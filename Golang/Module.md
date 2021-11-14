# Golang依赖管理

## 一、为什么需要依赖管理

最早的时候，Go所依赖的所有的第三方库都放在`GOPATH`这个目录下面。这就导致了同一个库只能保存一个版本的代码。如果不同的项目依赖同一个第三方的库的不同版本，应该怎么解决？

## 二、godep

Go语言从**v1.5**开始开始引入`vendor`模式，如果项目目录下有`vendor`目录，那么**go工具链**会优先使用`vendor`内的包进行**编译、测试**等

`godep`是一个通过`vender`模式实现的Go语言的**第三方依赖管理工具**，类似的还有由社区维护准官方包管理工具`dep`

### 2.1 安装

执行以下命令安装`godep`工具

```bash
go get github.com/tools/godep
```

### 2.2 基本命令

安装好`godep`之后，在终端输入`godep`查看支持的**所有命令**

```bash
godep save     将依赖项输出并复制到Godeps.json文件中
godep go       使用保存的依赖项运行go工具
godep get      下载并安装具有指定依赖项的包
godep path     打印依赖的GOPATH路径
godep restore  在GOPATH中拉取依赖的版本
godep update   更新选定的包或go版本
godep diff     显示当前和以前保存的依赖项集之间的差异
godep version  查看版本信息
```

使用`godep help \\[command\\]`可以看看**具体命令**的帮助信息

### 2.3 使用godep

在项目目录下执行`godep save`命令，会在当前项目中创建`Godeps`和`vender`两个文件夹

其中`Godeps`文件夹下有一个`Godeps.json`的文件，里面记录了项目所依赖的**包信息**

`vender`文件夹下是项目依赖的包的**源代码**文件

### 2.4 vender机制

**Go1.5**版本之后开始支持，能够控制Go语言程序编译时依赖包搜索路径的优先级

例如查找项目的某个依赖包，首先会在项目根目录下的`vender`文件夹中查找，如果没有找到就会去`$GOAPTH/src`目录下查找

### 2.5 godep开发流程

1.  保证程序能够正常编译
2.  执行`godep save`保存当前项目的所有**第三方依赖的版本信息和代码**
3.  提交Godeps目录和vender目录到代码库
4.  如果要更新依赖的版本，可以直接修改`Godeps.json`文件中的对应项

## 三. go module

`go module`是**Go1.11版本**之后官方推出的版本管理工具，并且从Go1.13版本开始，`go module`将是Go语言默认的依赖管理工具

### 3.1 GO111MODULE

要启用`go module`支持首先要设置环境变量`GO111MODULE`，通过它可以开启或关闭模块支持，它有三个可选值：`off`、`on`、`auto`，默认值是`auto`

-   `GO111MODULE=off`禁用模块支持，编译时会从`GOPATH`和`vendor`文件夹中查找包

-   `GO111MODULE=on`启用模块支持，编译时会忽略`GOPATH`和`vendor`文件夹，只根据 `go.mod`下载依赖

-   `GO111MODULE=auto`，当**项目在`$GOPATH/src`外**且项目根目录有`go.mod`文件时，开启模块支持

使用`go module`管理依赖后会在项目根目录下生成两个文件`go.mod`和`go.sum`

简单来说，设置`GO111MODULE=on`之后就可以使用`go module`了，以后就没有必要在`GOPATH`中创建项目了，并且还能够很好的管理项目依赖的第三方包信息

### 3.2 GOPROXY

**Go1.11之后**设置GOPROXY命令为：

```bash
export GOPROXY=https://goproxy.cn
```

**Go1.13之后**`GOPROXY`默认值为`https://proxy.golang.org`，在国内是无法访问的，所以十分建议大家设置GOPROXY，这里我推荐使用`goproxy.cn`

```bash
go env -w GOPROXY=https://goproxy.cn,direct
```

### 3.3 go mod命令

常用的`go mod`命令如下：

````bash
go mod download    下载依赖的module到本地cache（默认为$GOPATH/pkg/mod目录）
go mod edit        编辑go.mod文件
go mod graph       打印模块依赖图
go mod init        初始化当前文件夹, 创建go.mod文件
go mod tidy        增加缺少的module，删除无用的module
go mod vendor      将依赖复制到vendor下
go mod verify      校验依赖
go mod why         解释为什么需要依赖
````

### 3.4 go.mod

`go.mod`文件记录了项目所有的依赖信息，其结构大致如下：

```go
module github.com/Q1mi/studygo/blogger

go 1.12

require (
	github.com/DeanThompson/ginpprof v0.0.0-20190408063150-3be636683586
	github.com/gin-gonic/gin v1.4.0
	github.com/go-sql-driver/mysql v1.4.1
	github.com/jmoiron/sqlx v1.2.0
	github.com/satori/go.uuid v1.2.0
	google.golang.org/appengine v1.6.1 // indirect
)
```

其中：

-   `module`用来定义**包名**
-   `require`用来定义**依赖包及版本**
-   `indirect`表示**间接引用**

### 3.4 依赖的版本

`go mod`支持**语义化版本号**，比如`go get foo@v1.2.3`

也可以跟git的**分支或tag**，比如`go get foo@master`

还可以跟git提交**哈希**，比如`go get foo@e3702bed2`

### 3.5 replace


在国内访问`golang.org/x`的各个包都需要翻墙，你可以在`go.mod`中使用`replace`替换成`github`上对应的库

```go
replace (
	golang.org/x/crypto v0.0.0-20180820150726-614d502a4dac => github.com/golang/crypto v0.0.0-20180820150726-614d502a4dac
	golang.org/x/net v0.0.0-20180821023952-922f4815f713 => github.com/golang/net v0.0.0-20180826012351-8a410e7b638d
	golang.org/x/text v0.3.0 => github.com/golang/text v0.3.0
)
```

### 3.6 go get


在项目中执行`go get`命令可以下载依赖包，并且还**可以指定下载的版本**

-   运行`go get -u`将会升级到最新的次要版本或者修订版本(x.y.z, z是修订版本号， y是次要版本号)

-   运行`go get -u=patch`将会升级到最新的修订版本

-   运行`go get package@version`将会升级到指定的版本号version


如果下载所有依赖可以使用`go mod download`命令

### 3.7 整理依赖


我们在代码中删除依赖代码后，相关的依赖库并不会在go.mod文件中自动移除。这种情况下我们可以使用`go mod tidy`命令**更新go.mod中的依赖关系**

## 四、go mod edit


### 4.1 格式化


因为我们可以手动修改go.mod文件，所以有些时候需要**格式化**该文件。Go提供了一下命令：

```bash
go mod edit -fmt
```

### 4.2 添加依赖项


```bash
go mod edit -require=golang.org/x/text
```

### 4.3 移除依赖项

```bash
go mod edit -droprequire=golang.org/x/text
```

关于go mod edit的更多用法可以通过`go help mod edit`查看

## 五、项目中使用go module


### 5.1 既有项目


如果需要对一个已经存在的项目启用go module，可以按照以下步骤操作：

1.   在项目目录下执行`go mod init`，生成一个`go.mod`文件
2.   执行`go get`，查找并记录当前项目的依赖
3.   同时生成一个`go.sum`记录每个依赖库的版本和哈希值


### 5.2 新项目

对于一个新创建的项目，我们可以在项目文件夹下按照以下步骤操作：

1.   执行`go mod init`项目名命令，在当前项目文件夹下创建一个`go.mod`文件
2.   手动编辑`go.mod`中的`require`依赖项或执行`go get`自动发现、维护依赖