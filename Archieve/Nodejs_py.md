# 在node中执行python脚本

## Node.js多进程基础

`Node.js` 是以单线程的模式运行的，但它使用的是事件驱动来处理并发。这样有助于我们在多核 cpu 的系统上创建多个子进程，从而提高性能。

`Node`提供了 `child_process` 模块来创建子进程，方法有：

-   `child_process.exec`使用子进程执行命令，缓存子进程的输出，并将子进程的输出以回调函数的形式返回
-   `child_process.spawn`使用指定的命令行参数创建进程
-   `child_process.fork`是`spawn()`的特殊形式，用于在子进程中运行模块，与`spawn`方法不同的是，`fork`会在父进程与子进程之间建立一个通信管道用于进程之间的通信

每个子进程总是带有三个流对象：`child.stdin, child.stdout 和child.stderr`，他们可能会共享父进程的 `stdio` 流，或者也可以是独立的被导流的流对象。

### 1、exec() 方法

`child_process.exec(command[, options], callback)`

```python
# support.py
# -*- coding: utf-8 -*-
import sys
print("进程 " +sys.argv[1] +" 执行。") 
```

```javascript
// master.js
const fs = require('fs');
const child_process = require('child_process');
 
for(var i=0; i<3; i++) {
//创建三个子进程
    var workerProcess = child_process.exec('python support.py '+i, function (error, stdout, stderr) {
        if (error) {
            console.log(error.stack);
            console.log('Error code: '+error.code);
            console.log('Signal received: '+error.signal);
        }
        console.log('stdout: ' + stdout);
        console.log('stderr: ' + stderr);
    });
 
    workerProcess.on('exit', function (code) {
        console.log('子进程已退出，退出码 '+code);
    });
}
```

![img](https://gitee.com/jxprog/PicBed/raw/master/md/2021/11/11-175407)

### 2、spawn()方法

`child_process.spawn(command[, args][, options])`

```python
# support.py
# -*- coding: utf-8 -*-
import sys
print("进程 " +sys.argv[1] +" 执行。") 
```

```javascript
// master.js
const fs = require('fs');
const child_process = require('child_process');
 
for(var i=0; i<3; i++) {
   var workerProcess = child_process.spawn('python', ['support.py', i]);
 
   workerProcess.stdout.on('data', function (data) {
      console.log('stdout: ' + data);
   });
 
   workerProcess.stderr.on('data', function (data) {
      console.log('stderr: ' + data);
   });
 
   workerProcess.on('close', function (code) {
      console.log('子进程已退出，退出码 '+code);
   });
}
```

![img](https://gitee.com/jxprog/PicBed/raw/master/md/2021/11/11-175413)

### 3、fork()方法

`child_process.fork(modulePath[, args][, options])`
`modulePath： String`，将要在子进程中运行的模块

```javascript
// support.js
console.log("进程 " + process.argv[2] + " 执行。" );
```

```javascript
// master.js
const fs = require('fs');
const child_process = require('child_process');
 
for(var i=0; i<3; i++) {
   var worker_process = child_process.fork("support.js", [i]);    
 
   worker_process.on('close', function (code) {
      console.log('子进程已退出，退出码 ' + code);
   });
}
```

![img](https://gitee.com/jxprog/PicBed/raw/master/md/2021/11/11-175421)

## node调用python

```javascript
require('child_process').exec('python app.py');
```

```javascript
const child = require('child_process');
child.exec('regedit /s disableProxy.reg', () => {
    child.exec('python xywlogin.py', () => {
        child.exec('regedit /s enableProxy.reg');
    });
});
```

### 比较exec和spawn

>   1、`exec`将子进程输出结果暂放在`buffer`中，在结果完全返回后，再将输出一次性的以回调函数返回。如果`exec`的`buffer`体积设置的不够大，它将会以一个“`maxBuffer exceeded`”错误失败告终。而spawn在子进程开始执行后，就不断的将数据从子进程返回给主进程，它没有回调函数，它通过流的方式发数据传给主进程，从而实现了多进程之间的数据交换。这个功能的直接用应用场景就是“系统监控”。
>   2、书写上，exec更方便一些,将整个命令放在第一个参数中，而spawn需要拆分。
>   `child_process.spawn('python', ['support.py', i])`
>   `child_process.exec('python support.py '+i, callback)`