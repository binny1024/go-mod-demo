# go-mod-demo
- `GO111MODULE=off `禁用 go module，编译时会从 GOPATH 和 vendor 文件夹中查找包；
- `GO111MODULE=on `启用 go module，编译时会忽略 GOPATH 和 vendor 文件夹，只根据 go.mod下载依赖；
- `GO111MODULE=auto`（默认值），当项目在 GOPATH/src 目录之外，并且项目根目录有 go.mod 文件时，开启 go module。

MacOS 或者 Linux 下开启 GO111MODULE 的命令为：

```
export GO111MODULE=on 
```
或者 

```
export GO111MODULE=auto
```
# 常用的go mod命令
|命令|	作用|
|---|---|
|go mod download|	下载依赖包到本地（默认为 GOPATH/pkg/mod 目录）|
|go mod edit	|编辑 go.mod 文件|
|go mod graph	|打印模块依赖图|
|go mod init|	初始化当前文件夹，创建 go.mod 文件|
|go mod tidy|	增加缺少的包，删除无用的包|
|go mod vendor	|将依赖复制到 vendor 目录下|
|go mod verify	|校验依赖|
|go mod why	|解释为什么需要依赖|

# 代理
- `goproxy.io`
- `goproxy.cn`：（推荐）由国内的七牛云提供。

# go.mod 文件
go.mod 提供了 module、require、replace 和 exclude 四个命令：
- module 语句指定包的名字（路径）；
- require 语句指定的依赖项模块；
- replace 语句可以替换依赖项模块；
- exclude 语句可以忽略依赖项模块。

## 使用 replace 替换无法直接获取的 package
由于某些已知的原因，并不是所有的 package 都能成功下载，比如：golang.org 下的包。

modules 可以通过在 go.mod 文件中使用 replace 指令替换成 github 上对应的库，比如：


```
replace (
    golang.org/x/crypto v0.0.0-20190313024323-a1f597ede03a => github.com/golang/crypto v0.0.0-20190313024323-a1f597ede03a
)
```
或者

```
replace golang.org/x/crypto v0.0.0-20190313024323-a1f597ede03a => github.com/golang/crypto v0.0.0-20190313024323-a1f597ede03a
```


# 与` GOPATH`的不同
![](http://qiniu.binny.work/mweb/16006837605873.jpg)

由于 `go.mod` 会扫描`同工作目录`下所有 package 并且变更引入方法，必须将 hello 当成路径的前缀，也就是需要写成 `import hello/api`，以往 `GOPATH/dep `模式允许的 `import ./api `已经失效。
# 如何在项目中使用
## 第一步
新建项目:`mkdir hello`.

## 第二步
在项目根目录初始化`go.mod`文件.

## 第三步
编写源码,添加依赖包`main.go`.

```go
package main
import (
    "net/http"
    "github.com/labstack/echo"
)
func main() {
    e := echo.New()
    e.GET("/", func(c echo.Context) error {
        return c.String(http.StatusOK, "Hello, World!")
    })
    e.Logger.Fatal(e.Start(":1323"))
}
```
## 第四步
执行 `go run main.go`下载依赖.

`go module` 安装 `package` 的原则是先拉取最新的 `release tag`，若无 `tag` 则拉取最新的 `commit`，详见 [Modules 官方介绍](https://github.com/golang/go/wiki/Modules)。

go 会自动生成一个 go.sum 文件来记录 dependency tree。

再次执行脚本go run main.go发现跳过了检查并安装依赖的步骤。

# 依赖升级
可以使用命令`go list -m -u all`来检查可以升级的 package。

使用`go get -u need-upgrade-package`升级后会将新的依赖版本更新到 go.mod 

也可以使用`go get -u`升级所有依赖。



