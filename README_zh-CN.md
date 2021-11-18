# NCraft-go 项目标准布局

NCraft-go 的项目目录标准参考: [Standard Go Project Layout](https://github.com/golang-standards/project-layout)，基于这个标准做了一些删减，并针对微服务的特定场景做了更细化的目录规定。

[TOC]

## 说明

### 概念

- Package（包）

  对应 mojo API 里 wand.mojo 中所定义的包。
- Project（项目）

  通常一个有独立Repo的Package也是一个项目，在这里概念与`Package`类似，只是从工程开发角度出发，进行开发管理而使用的概念，对应可以进行独立的`issue`管理或者发布管理。
- Interface（接口）

  服务的最小单位。通常对应同一种 `Resource` 的操作。

## Go 目录
### cmd
当前项目的**可执行文件**。cmd 目录下的每一个**子目录名称都应该匹配可执行文件**。在 cmd 目录下的子目录为微服务可执行文件，或者为控制台执行文件。
同样的，不要在 /cmd 目录中放置太多的代码，我们应该将公有代码放置到 /pkg 中，将私有代码放置到 /internal 中并在 /cmd 中引入这些包，**保证 main 函数中的代码尽可能简单和少**。

#### cmd/{interface-name}-server
当前项目中**指定接口的微服务可执行文件**。其中 `{interface-name}` 是指 kerabe-case 形式的 InterfaceDecl.name。
#### cmd/{package-name}-unified-server
当前项目中**合一的微服务可执行文件**。集成该包下所有`interface`定义的服务，在某些需要减少需要单独部署的微服务进程数量的场景下，可以使用。
### internal
**私有的**应用程序代码库。这些是不希望被其他人导入的代码。在本项目内，是微服务的实现代码。

#### internal/{interface-name}

指定接口相关的实现的业务逻辑代码。

#### internal/{interface-name}-server

指定接口的服务注册启动相关代码。

#### internal/model

SQL数据库的模型操作代码库，代码库的文件名采用 {struct-name}-model.go 的规范。每一个存在model的struct类型，单独一个文件。

#### internal/server

当前项目中**合一的服务注册启动代码**。

### pkg
**外部应用程序**可以使用的库代码。其他项目将会导入这些库来保证项目可以正常运行，所以在将代码放在这里前，一定要三四而行。请注意，internal 目录是一个更好的选择来确保项目私有代码不会被其他人导入，因为这是 Go 强制执行的。

#### pkg/{interface-name}-service

指定接口实现代码，调用internal的内部业务逻辑实现函数，根据接口进行统一的封装。

##### pkg/{interface-name}-service/handlers

是`Interface`中所定义的`method`的实现入口。脚手架代码生成后，开发者通过修改此处的代码完成服务的实现。

##### pkg/{interface-name}-service/svc

基于 [gokit](https://github.com/go-kit/kit) 的 `EndPoint` 的定义，以及`HTTP`、`gRPC` Transport 的实现。

## 通用应用程序目录
### build
**打包和持续集成**所需的文件。

- build/ci：存放持续集成的配置和脚本，如果持续集成平台对配置文件有路径要求，则可将其 link 到指定位置。
- build/package：存放 AMI、Docker、系统包（deb、rpm、pkg）的配置和脚本等。

### configs
配置文件模板或默认配置。
### deployments
IaaS，PaaS，系统和容器编排部署配置和模板（docker-compose，kubernetes/helm，mesos，terraform，bosh）。
### scripts
用于执行各种构建，安装，分析等操作的脚本。这些脚本使根级别的 Makefile 变得更小更简单。
### test
**外部测试应用程序和测试数据**。随时根据需要构建 /test 目录。对于较大的项目，有一个数据子目录更好一些。例如，如果需要 Go 忽略目录中的内容，则可以使用 /test/data  这样的目录名字。请注意，Go 还将忽略以“.”或“_”开头的目录或文件，因此可以更具灵活性的来命名测试数据目录。

## 其他目录
### assets
项目中使用的其他资源（图像、logo 等）。
### docs
**设计和用户文档**。

### githooks
Git 钩子。
### tools
此项目的支持工具。请注意，这些工具可以从 /pkg 和 /internal 目录导入代码。

## 其他文件
### Makefile
在任何一个项目中都会存在一些需要运行的脚本，这些脚本文件应该被放到 /scripts 目录中并**由 Makefile 触发**。
### README.md

项目的说明文件

### go.mod

golang的mod管理文件

