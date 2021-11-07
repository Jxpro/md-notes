## 一、安装

Go 语言开发包
国外：[https://golang.org/dl/](https://golang.org/dl/)
国内(推荐)： [https://golang.google.cn/dl/](https://golang.google.cn/dl/)

## 二、测试安装

```bash
# 任意目录下输入go version
go version go1.15.2 darwin/amd64

# 查看环境配置
go env
set GO111MODULE=
set GOARCH=amd64
...
```

## 三、修改相关配置

```bash
# 设置GOPROXY，将Go Module下载请求重定向到GOPROXY指向的缓存库
go env -w GO111MODULE=on
go env -w GOPROXY=https://goproxy.cn,direct

# Go 1.13设置了默认的GOSUMDB=sum.golang.org
# 是用来验证包的有效性。这个网址由于墙的原因可能无法访问，所以可以使用下面命令来关闭：
go env -w GOSUMDB=off

# 修改GOPATH默认路径，报错直接修改环境变量
# warning: go env -w GOPATH=... 
# does not override conflicting OS environment variable
go env -w GOPATH=D:\TASK\Program\Go
```

## 四、GOPATH目录结构

```bash
GOROOT
	--src                 <<--- Go 语言自带的源代码
	--pkg                 <<--- 编译的中间文件放在此文件夹
	--bin                 <<--- 编译的目标文件放在此文件夹
GOPATH
	--src                 <<--- 项目源代码放置在此文件夹。
	                            !!!警告：一个常犯的错误是把 GOPATH 指向此处!!!
		--HelloWorld      <<--- 我们项目源代码所在的文件夹。
		                        !!!警告：一个常犯的错误是把 GOPATH 指向此处!!!
		--vendor          <<--- 第三方开源代码文件夹
			--github.com
				--...
	--pkg                 <<--- 编译的中间文件放在此文件夹，Go编译器自动生成此文件夹
	--bin                 <<--- 编译的目标文件放在此文件夹，Go编译器自动生成此文件夹
	
```

## 五、 第一个Golang程序

-   文件位置：D:\TASK\Program\Go\src\helloworld\app.go

```go
package main

import "fmt"

func main() {
  fmt.Println("hello world")
}
```

-   编译运行

```bash
D:/TASK/Program/Go/src/helloworld > go run .\app.go
hello world

D:/TASK/Program/Go/src/helloworld > go build .\app.go
D:/TASK/Program/Go/src/helloworld > .\app.exe
hello world
```

## 六、参考文章

-   [Golang开发环境搭建](https://zhuanlan.zhihu.com/p/299095514)
-   [Golang环境搭建](https://www.cnblogs.com/leokale-zz/p/14651078.html)
-   [Go 语言 GOPATH 设置](https://blog.csdn.net/quicmous/article/details/80360126)

## 七、问题一

1.   报错详情

```bash
go.mod file not found in current directory or any parent directory; see 'go help modules'
```

2.   原因分析

     go环境设置存在问题
     
     具体搜索`go modules`
     
3.   解决办法

     -   ( 过了很长一段时间... )重新安装...
     -   以下方法本人尝试后无效:

     ```bash
     go env -w GO111MODULE=auto
     ```

4.   参考文章：[CSDN](https://blog.csdn.net/weixin_40179091/article/details/117163598)

## 八、问题二

1.   报错详情

```go
Error running 'go build awesomeProject (1)': Cannot run program "C:\Users\erbin\AppData\Local\Temp\___go_build_awesomeProject__1_.exe" (in directory "G:\Language Proctice\Goworks\src\awesomeProject"): CreateProcess error=216, 该版本的 %1 与你运行的 Windows 版本不兼容。请查看计算机的系统信息，然后联系软件发布者。
```

```go
// 此时的代码为
package awesomeProject

import "fmt"

func main(){
     
	fmt.Println("Hello World!")
}
```

2.   原因分析

     在go中，应用程序的入口包要为main，而编译源码没有main包时，将无法编译输出可执行的文件，也就会导致这里的错误

3.   解决办法

     将main函数所在的包名改为main，改之后如下：

```go
// 代码成功运行
package main

import "fmt"

func main(){
     
	fmt.Println("Hello World!")
}
```

