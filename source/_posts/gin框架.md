---
title: gin框架
date: 2023-01-25 10:56:42
tags: gin
categories: go
---

# 环境搭建

创建项目目录，进入目录后执行`go mod init 项目名`
然后用golang打开，设置下载代理https://goproxy.cn，创建入口文件main.go，并放置main方法
```
package main

func main() {

}
```
直接可以引入依赖，比如：`import "github.com/gin-gonic/gin"`
此时依赖标红，然后下载依赖，同时会在go.mod里面显示增加的依赖，代码中也没有报错
然后代码为：
```
package main
import "github.com/gin-gonic/gin"

func main() {       
    r := gin.Default()       
    r.GET("/ping", func(c *gin.Context) {              
        c.JSON(200, gin.H{ 
            "message": "pong",              
        })       
    })       
    r.Run() // listen and serve on 0.0.0.0:8080 (for windows "localhost:8080")
}
```
执行`go run .\main.go`，如果不出错，且可以访问，那么基本就可以了。
# gin路由

## 基本路由

gin 框架中采用的路由库是基于[httprouter](https://github.com/julienschmidt/httprouter)做的。

## API参数

可以通过Context的Param方法来获取API参数
例如：localhost:8000/xxx/zhangsan
```
package main

import (
    "net/http"
    "strings"
    "github.com/gin-gonic/gin")

func main() {
    r := gin.Default()
    r.GET("/user/:name/*action", func(c *gin.Context) {
        name := c.Param("name")
        action := c.Param("action")
        //截取/
        action = strings.Trim(action, "/")
        c.String(http.StatusOK, name+" is "+action)
    })
    //默认为监听8080端口
    r.Run(":8000")
}
```

## URL参数

URL参数可以通过DefaultQuery()或Query()方法获取，其中DefaultQuery()若参数不存在，返回默认值，Query()若不存在，返回空串
格式为：API?name=zs
```
func main() {
    r := gin.Default()
    r.GET("/user", func(c *gin.Context) {
        //指定默认值
        //http://localhost:8080/user 才会打印出来默认的值
        name := c.DefaultQuery("name", "枯藤")
        c.String(http.StatusOK, fmt.Sprintf("hello %s", name))
    })
    r.Run()
}
```
## 表单参数

表单传输为post请求，http常见的传输格式为四种：

* application/json
* application/x-www-form-urlencoded
* application/xml
* multipart/form-data

表单参数可以通过PostForm()方法获取，该方法默认解析的是x-www-form-urlencoded或from-data格式的参数
```
func main() {
    r := gin.Default()
    r.POST("/form", func(c *gin.Context) {
        types := c.DefaultPostForm("type", "post")
        username := c.PostForm("username")
        password := c.PostForm("userpassword")
        // c.String(http.StatusOK, fmt.Sprintf("username:%s,password:%s,type:%s", username, password, types))
        c.String(http.StatusOK, fmt.Sprintf("username:%s,password:%s,type:%s", username, password, types))
    })
    r.Run()
}
```
## 上传单个文件

multipart/form-data格式用于文件上传
gin文件上传与原生的net/http方法类似，不同在于gin把原生的request封装到c.Request中
```
func main() {
    r := gin.Default()
    //限制上传最大尺寸
    r.MaxMultipartMemory = 8 << 20
    r.POST("/upload", func(c *gin.Context) {
        file, err := c.FormFile("file")
        if err != nil {
            c.String(500, "上传图片出错")
        }
        // c.JSON(200, gin.H{"message": file.Header.Context})
        c.SaveUploadedFile(file, file.Filename)
        c.String(http.StatusOK, file.Filename)
    })
    r.Run()
}
```

### 上传特定文件

有的用户上传文件需要限制上传文件的类型以及上传文件的大小，但是gin框架暂时没有这些函数(也有可能是我没找到)，因此基于原生的函数写法自己写了一个可以限制大小以及文件类型的上传函数
```
func main() {
    r := gin.Default()
    r.POST("/upload", func(c *gin.Context) {
        _, headers, err := c.Request.FormFile("file")
        if err != nil {
            log.Printf("Error when try to get file: %v", err)
        }
        //headers.Size 获取文件大小
        if headers.Size > 1024*1024*2 {
            fmt.Println("文件太大了")
            return
        }
        //headers.Header.Get("Content-Type")获取上传文件的类型
        if headers.Header.Get("Content-Type") != "image/png" {
            fmt.Println("只允许上传png图片")
            return
        }
        c.SaveUploadedFile(headers, "./video/"+headers.Filename)
        c.String(http.StatusOK, headers.Filename)
    })
    r.Run()
}
```

## 上传多个文件
```
func main() {
   // 1.创建路由
   // 默认使用了2个中间件Logger(), Recovery()
   r := gin.Default()
   // 限制表单上传大小 8MB，默认为32MB
   r.MaxMultipartMemory = 8 << 20
   r.POST("/upload", func(c *gin.Context) {
      form, err := c.MultipartForm()
      if err != nil {
         c.String(http.StatusBadRequest, fmt.Sprintf("get err %s", err.Error()))
      }
      // 获取所有图片
      files := form.File["files"]
      // 遍历所有图片
      for _, file := range files {
         // 逐个存
         if err := c.SaveUploadedFile(file, file.Filename); err != nil {
            c.String(http.StatusBadRequest, fmt.Sprintf("upload err %s", err.Error()))
            return
         }
      }
      c.String(200, fmt.Sprintf("upload ok %d files", len(files)))
   })
   //默认端口号是8080
   r.Run(":8000")
}
```

## routes group

routes group是为了管理一些相同的URL
```
package main

import (
   "github.com/gin-gonic/gin"
   "fmt")

// gin的helloWorld

func main() {
   // 1.创建路由
   // 默认使用了2个中间件Logger(), Recovery()
   r := gin.Default()
   // 路由组1 ，处理GET请求
   v1 := r.Group("/v1")
   // {} 是书写规范
   {
      v1.GET("/login", login)
      v1.GET("submit", submit)
   }
   v2 := r.Group("/v2")
   {
      v2.POST("/login", login)
      v2.POST("/submit", submit)
   }
   r.Run(":8000")}

func login(c *gin.Context) {
   name := c.DefaultQuery("name", "jack")
   c.String(200, fmt.Sprintf("hello %s\n", name))}

func submit(c *gin.Context) {
   name := c.DefaultQuery("name", "lily")
   c.String(200, fmt.Sprintf("hello %s\n", name))
}
```
## 路由拆分与注册

### 基本的路由注册

下面最基础的gin路由注册方式，适用于路由条目比较少的简单项目或者项目demo。
```
package main

import (
    "net/http"

    "github.com/gin-gonic/gin")

func helloHandler(c *gin.Context) {
    c.JSON(http.StatusOK, gin.H{
        "message": "Hello www.topgoer.com!",
    })
}

func main() {
    r := gin.Default()
    r.GET("/topgoer", helloHandler)
    if err := r.Run(); err != nil {
        fmt.Println("startup service failed, err:%v\n", err)
    }
}
```

### 路由拆分成单独文件或包

当项目的规模增大后就不太适合继续在项目的main.go文件中去实现路由注册相关逻辑了，我们会倾向于把路由部分的代码都拆分出来，形成一个单独的文件或包：
我们在routers.go文件中定义并注册路由信息：
```
package main

import (
    "net/http"

    "github.com/gin-gonic/gin")

func helloHandler(c *gin.Context) {
    c.JSON(http.StatusOK, gin.H{
        "message": "Hello www.topgoer.com!",
    })
}

func setupRouter() *gin.Engine {
    r := gin.Default()
    r.GET("/topgoer", helloHandler)
    return r
}
```
此时main.go中调用上面定义好的setupRouter函数：
```
func main() {
    r := setupRouter()
    if err := r.Run(); err != nil {
        fmt.Println("startup service failed, err:%v\n", err)
    }
}
```

此时的目录结构：
```
gin_demo
├── go.mod
├── go.sum
├── main.go
└── routers.go
```
把路由部分的代码单独拆分成包的话也是可以的，拆分后的目录结构如下：
```
gin_demo
├── go.mod
├── go.sum
├── main.go
└── routers
    └── routers.go
```
routers/routers.go需要注意此时setupRouter需要改成首字母大写：
```
package routers

import (
    "net/http"

    "github.com/gin-gonic/gin")

func helloHandler(c *gin.Context) {
    c.JSON(http.StatusOK, gin.H{
        "message": "Hello www.topgoer.com",
    })
}

// SetupRouter 配置路由信息
func SetupRouter() *gin.Engine {
    r := gin.Default()
    r.GET("/topgoer", helloHandler)
    return r
}
```
main.go文件内容如下：
```
package main

import (
    "fmt"
    "gin_demo/routers")

func main() {
    r := routers.SetupRouter()
    if err := r.Run(); err != nil {
        fmt.Println("startup service failed, err:%v\n", err)
    }
}
```
### 路由拆分成多个文件

当我们的业务规模继续膨胀，单独的一个routers文件或包已经满足不了我们的需求了，
```
func SetupRouter() *gin.Engine {
    r := gin.Default()
    r.GET("/topgoer", helloHandler)
  r.GET("/xx1", xxHandler1)
  ...
  r.GET("/xx30", xxHandler30)
    return r
}
```

因为我们把所有的路由注册都写在一个SetupRouter函数中的话就会太复杂了。我们可以分开定义多个路由文件，例如：
```
gin_demo
├── go.mod
├── go.sum
├── main.go
└── routers
    ├── blog.go
    └── shop.go
```
routers/shop.go中添加一个LoadShop的函数，将shop相关的路由注册到指定的路由器：
```
func LoadShop(e *gin.Engine)  {
    e.GET("/hello", helloHandler)
  e.GET("/goods", goodsHandler)
  e.GET("/checkout", checkoutHandler)
  ...
}
```
routers/blog.go中添加一个LoadBlog的函数，将blog相关的路由注册到指定的路由器：
```
func LoadBlog(e *gin.Engine) {
    e.GET("/post", postHandler)
  e.GET("/comment", commentHandler)
  ...
}
```
在main函数中实现最终的注册逻辑如下：
```
func main() {
    r := gin.Default()
    routers.LoadBlog(r)
    routers.LoadShop(r)
    if err := r.Run(); err != nil {
        fmt.Println("startup service failed, err:%v\n", err)
    }
}
```

### 路由拆分到不同的APP

有时候项目规模实在太大，那么我们就更倾向于把业务拆分的更详细一些，例如把不同的业务代码拆分成不同的APP。
因此我们在项目目录下单独定义一个app目录，用来存放我们不同业务线的代码文件，这样就很容易进行横向扩展。大致目录结构如下：
```
gin_demo
├── app
│   ├── blog
│   │   ├── handler.go
│   │   └── router.go
│   └── shop
│       ├── handler.go
│       └── router.go
├── go.mod
├── go.sum
├── main.go
└── routers
    └── routers.go
```
其中app/blog/router.go用来定义post相关路由信息，具体内容如下：
```
func Routers(e *gin.Engine) {
    e.GET("/post", postHandler)
    e.GET("/comment", commentHandler)
}
```
app/shop/router.go用来定义shop相关路由信息，具体内容如下：
```
func Routers(e *gin.Engine) {
    e.GET("/goods", goodsHandler)
    e.GET("/checkout", checkoutHandler)
}
```
routers/routers.go中根据需要定义Include函数用来注册子app中定义的路由，Init函数用来进行路由的初始化操作：
```
type Option func(*gin.Engine)

var options = []Option{}

// 注册app的路由配置
func Include(opts ...Option) {
    options = append(options, opts...)
}

// 初始化
func Init() *gin.Engine {
    r := gin.New()
    for _, opt := range options {
        opt(r)
    }
    return r
}
```
main.go中按如下方式先注册子app中的路由，然后再进行路由的初始化：
```
func main() {
    // 加载多个APP的路由配置
    routers.Include(shop.Routers, blog.Routers)
    // 初始化路由
    r := routers.Init()
    if err := r.Run(); err != nil {
        fmt.Println("startup service failed, err:%v\n", err)
    }
}
```
# gin数据解析与绑定

## Json 数据解析和绑定

### 客户端传参，后端接收并解析到结构体
```
package main

import (
   "github.com/gin-gonic/gin"
   "net/http")

// 定义接收数据的结构体
type Login struct {
   // binding:"required"修饰的字段，若接收为空值，则报错，是必须字段
   User    string `form:"username" json:"user" uri:"user" xml:"user" binding:"required"`
   Pssword string `form:"password" json:"password" uri:"password" xml:"password" binding:"required"`}

func main() {
   // 1.创建路由
   // 默认使用了2个中间件Logger(), Recovery()
   r := gin.Default()
   // JSON绑定
   r.POST("loginJSON", func(c *gin.Context) {
      // 声明接收的变量
      var json Login
      // 将request的body中的数据，自动按照json格式解析到结构体
      if err := c.ShouldBindJSON(&json); err != nil {
         // 返回错误信息
         // gin.H封装了生成json数据的工具
         c.JSON(http.StatusBadRequest, gin.H{"error": err.Error()})
         return
      }
      // 判断用户名密码是否正确
      if json.User != "root" || json.Pssword != "admin" {
         c.JSON(http.StatusBadRequest, gin.H{"status": "304"})
         return
      }
      c.JSON(http.StatusOK, gin.H{"status": "200"})
   })
   r.Run(":8000")}
```
### 表单数据解析和绑定
```
package main

import (
    "net/http"

    "github.com/gin-gonic/gin")

// 定义接收数据的结构体
type Login struct {
    // binding:"required"修饰的字段，若接收为空值，则报错，是必须字段
    User    string `form:"username" json:"user" uri:"user" xml:"user" binding:"required"`
    Pssword string `form:"password" json:"password" uri:"password" xml:"password" binding:"required"`}

func main() {
    // 1.创建路由
    // 默认使用了2个中间件Logger(), Recovery()
    r := gin.Default()
    // JSON绑定
    r.POST("/loginForm", func(c *gin.Context) {
        // 声明接收的变量
        var form Login
        // Bind()默认解析并绑定form格式
        // 根据请求头中content-type自动推断
        if err := c.Bind(&form); err != nil {
            c.JSON(http.StatusBadRequest, gin.H{"error": err.Error()})
            return
        }
        // 判断用户名密码是否正确
        if form.User != "root" || form.Pssword != "admin" {
            c.JSON(http.StatusBadRequest, gin.H{"status": "304"})
            return
        }
        c.JSON(http.StatusOK, gin.H{"status": "200"})
    })
    r.Run(":8000")
}
```

### URI数据解析和绑定
```
package main

import (
    "net/http"

    "github.com/gin-gonic/gin")

// 定义接收数据的结构体
type Login struct {
    // binding:"required"修饰的字段，若接收为空值，则报错，是必须字段
    User    string `form:"username" json:"user" uri:"user" xml:"user" binding:"required"`
    Pssword string `form:"password" json:"password" uri:"password" xml:"password" binding:"required"`}

func main() {
    // 1.创建路由
    // 默认使用了2个中间件Logger(), Recovery()
    r := gin.Default()
    // JSON绑定
    r.GET("/:user/:password", func(c *gin.Context) {
        // 声明接收的变量
        var login Login
        // Bind()默认解析并绑定form格式
        // 根据请求头中content-type自动推断
        if err := c.ShouldBindUri(&login); err != nil {
            c.JSON(http.StatusBadRequest, gin.H{"error": err.Error()})
            return
        }
        // 判断用户名密码是否正确
        if login.User != "root" || login.Pssword != "admin" {
            c.JSON(http.StatusBadRequest, gin.H{"status": "304"})
            return
        }
        c.JSON(http.StatusOK, gin.H{"status": "200"})
    })
    r.Run(":8000")
}
```
# gin 渲染

## 各种数据格式的响应

json、结构体、XML、YAML类似于java的properties、ProtoBuf
```
package main

import (
    "github.com/gin-gonic/gin"
    "github.com/gin-gonic/gin/testdata/protoexample")

// 多种响应方式func main() {
    // 1.创建路由
    // 默认使用了2个中间件Logger(), Recovery()
    r := gin.Default()
    // 1.json
    r.GET("/someJSON", func(c *gin.Context) {
        c.JSON(200, gin.H{"message": "someJSON", "status": 200})
    })
    // 2. 结构体响应
    r.GET("/someStruct", func(c *gin.Context) {
        var msg struct {
            Name    string
            Message string
            Number  int
        }
        msg.Name = "root"
        msg.Message = "message"
        msg.Number = 123
        c.JSON(200, msg)
    })
    // 3.XML
    r.GET("/someXML", func(c *gin.Context) {
        c.XML(200, gin.H{"message": "abc"})
    })
    // 4.YAML响应
    r.GET("/someYAML", func(c *gin.Context) {
        c.YAML(200, gin.H{"name": "zhangsan"})
    })
    // 5.protobuf格式,谷歌开发的高效存储读取的工具
    // 数组？切片？如果自己构建一个传输格式，应该是什么格式？
    r.GET("/someProtoBuf", func(c *gin.Context) {
        reps := []int64{int64(1), int64(2)}
        // 定义数据
        label := "label"
        // 传protobuf格式数据
        data := &protoexample.Test{
            Label: &label,
            Reps:  reps,
        }
        c.ProtoBuf(200, data)
    })

    r.Run(":8000")
}
```

## HTML模板渲染

* gin支持加载HTML模板, 然后根据模板参数进行配置并返回相应的数据，本质上就是字符串替换
* LoadHTMLGlob()方法可以加载模板文件

```
package main

import (
    "net/http"

    "github.com/gin-gonic/gin")

func main() {
    r := gin.Default()
    r.LoadHTMLGlob("tem/*")
    r.GET("/index", func(c *gin.Context) {
        c.HTML(http.StatusOK, "index.html", gin.H{"title": "我是测试", "ce": "123456"})
    })
    r.Run()
}
```

如果你需要引入静态文件需要定义一个静态文件目录
```
 r.Static("/assets", "./assets")
```

## 重定向
```
package main

import (
    "net/http"

    "github.com/gin-gonic/gin")

func main() {
    r := gin.Default()
    r.GET("/index", func(c *gin.Context) {
        c.Redirect(http.StatusMovedPermanently, "http://www.5lmh.com")
    })
    r.Run()
}
```

## 同步异步

* goroutine机制可以方便地实现异步处理
* 另外，在启动新的goroutine时，不应该使用原始上下文，必须使用它的只读副本

```
package main

import (
    "log"
    "time"

    "github.com/gin-gonic/gin")

func main() {
    // 1.创建路由
    // 默认使用了2个中间件Logger(), Recovery()
    r := gin.Default()
    // 1.异步
    r.GET("/long_async", func(c *gin.Context) {
        // 需要搞一个副本
        copyContext := c.Copy()
        // 异步处理
        go func() {
            time.Sleep(3 * time.Second)
            log.Println("异步执行：" + copyContext.Request.URL.Path)
        }()
    })
    // 2.同步
    r.GET("/long_sync", func(c *gin.Context) {
        time.Sleep(3 * time.Second)
        log.Println("同步执行：" + c.Request.URL.Path)
    })

    r.Run(":8000")
}
```

# gin 中间件

## 全局中间件

所有请求都经过此中间件
```
package main

import (
    "fmt"
    "time"

    "github.com/gin-gonic/gin")

// 定义中间
func MiddleWare() gin.HandlerFunc {
    return func(c *gin.Context) {
        t := time.Now()
        fmt.Println("中间件开始执行了")
        // 设置变量到Context的key中，可以通过Get()取
        c.Set("request", "中间件")
        status := c.Writer.Status()
        fmt.Println("中间件执行完毕", status)
        t2 := time.Since(t)
        fmt.Println("time:", t2)
    }}

func main() {
    // 1.创建路由
    // 默认使用了2个中间件Logger(), Recovery()
    r := gin.Default()
    // 注册中间件
    r.Use(MiddleWare())
    // {}为了代码规范
    {
        r.GET("/ce", func(c *gin.Context) {
            // 取值
            req, _ := c.Get("request")
            fmt.Println("request:", req)
            // 页面接收
            c.JSON(200, gin.H{"request": req})
        })

    }
    r.Run()
}
```

## Next()方法
```
func main() {       
    r := gin.Default()       
    m1 := func(c *gin.Context) {              
        fmt.Println("m1 start")              
        //c.Next()会跳过当前中间件后续的逻辑，类似defer，最后再执行c.Next后面的逻辑             
        //多个c.Next()谁在前面谁后执行，跟defer很像，类似先进后出的栈              
        c.Next()              
        fmt.Println("m1 end")      
    }       
    m2 := func(c *gin.Context) {              
        fmt.Println("m2 start")              
        //该方法会阻止业务逻辑以及该中间件后面中间件执行，但是不会阻止该中间件后面的逻辑执行包括c.Next()             
        //c.Abort()              
        c.Next()              
        fmt.Println("m2 end")      
    }       
    m3 := func(c *gin.Context) {              
        fmt.Println("m3 start")              
        c.Next()              
        fmt.Println("m3 end")       
    }       
    r.Use(m1, m2, m3)       
    r.GET("/", func(context *gin.Context {              
        context.Next()              
        context.JSON(http.StatusOK, gin.H{                      
        "message": "demo",              
        })              
        fmt.Println("hello world!!")       
    })       
    r.Run(":8081")}
```
输出：
```
[GIN-debug] Listening and serving HTTP on :8081 
m1 start 
m2 start 
m3 start 
hello world!! 
m3 end 
m2 end 
m1 end 
[GIN] 2021/03/26 - 12:40:40 |?[97;42m 200 ?[0m| 1.5781ms | 127.0.0.1 |?[97;44m GET ?[0m "/"
```

## 局部中间件
```
package main

import (
    "fmt"
    "time"

    "github.com/gin-gonic/gin")

// 定义中间
func MiddleWare() gin.HandlerFunc {
    return func(c *gin.Context) {
        t := time.Now()
        fmt.Println("中间件开始执行了")
        // 设置变量到Context的key中，可以通过Get()取
        c.Set("request", "中间件")
        // 执行函数
        c.Next()
        // 中间件执行完后续的一些事情
        status := c.Writer.Status()
        fmt.Println("中间件执行完毕", status)
        t2 := time.Since(t)
        fmt.Println("time:", t2)
    }
}

func main() {
    // 1.创建路由
    // 默认使用了2个中间件Logger(), Recovery()
    r := gin.Default()
    //局部中间键使用
    r.GET("/ce", MiddleWare(), func(c *gin.Context) {
        // 取值
        req, _ := c.Get("request")
        fmt.Println("request:", req)
        // 页面接收
        c.JSON(200, gin.H{"request": req})
    })
    r.Run()
}
```

## 中间件推荐

1. RestGate - REST API端点的安全身份验证
2. staticbin - 用于从二进制数据提供静态文件的中间件/处理程序
3. gin-cors - CORS杜松子酒的官方中间件
4. gin-csrf - CSRF保护
5. gin-health - 通过gocraft/health报告的中间件
6. gin-merry - 带有上下文的漂亮 打印 错误的中间件
7. gin-revision - 用于Gin框架的修订中间件
8. gin-jwt - 用于Gin框架的JWT中间件
9. gin-sessions - 基于mongodb和mysql的会话中间件
10. gin-location - 用于公开服务器的主机名和方案的中间件
11. gin-nice-recovery - 紧急恢复中间件，可让您构建更好的用户体验
12. gin-limit - 限制同时请求；可以帮助增加交通流量
13. gin-limit-by-key - 一种内存中的中间件，用于通过自定义键和速率限制访问速率。
14. ez-gin-template - gin简单模板包装
15. gin-hydra - gin中间件Hydra
16. gin-glog - 旨在替代Gin的默认日志
17. gin-gomonitor - 用于通过Go-Monitor公开指标
18. gin-oauth2 - 用于OAuth2
19. static gin框架的替代静态资产处理程序。
20. xss-mw - XssMw是一种中间件，旨在从用户提交的输入中“自动删除XSS”
21. gin-helmet - 简单的安全中间件集合。
22. gin-jwt-session - 提供JWT / Session / Flash的中间件，易于使用，同时还提供必要的调整选项。也提供样品。
23. gin-template - 用于gin框架的html / template易于使用。
24. gin-redis-ip-limiter - 基于IP地址的请求限制器。它可以与redis和滑动窗口机制一起使用。
25. gin-method-override - method受Ruby的同名机架启发而被POST形式参数覆盖的方法
26. gin-access-limit - limit-通过指定允许的源CIDR表示法的访问控制中间件。
27. gin-session - 用于Gin的Session中间件
28. gin-stats - 轻量级和有用的请求指标中间件
29. gin-statsd - 向statsd守护进程报告的Gin中间件
30. gin-health-check - check-用于Gin的健康检查中间件
31. gin-session-middleware - 一个有效，安全且易于使用的Go Session库。
32. ginception - 漂亮的例外页面
33. gin-inspector - 用于调查http请求的Gin中间件。
34. gin-dump - Gin中间件/处理程序，用于转储请求和响应的标头/正文。对调试应用程序非常有帮助。
35. go-gin-prometheus - Gin Prometheus metrics exporter
36. ginprom - Gin的Prometheus指标导出器
37. gin-go-metrics - Gin middleware to gather and store metrics using rcrowley/go-metrics
38. ginrpc - Gin 中间件/处理器自动绑定工具。通过像beego这样的注释路线来支持对象注册

# 会话控制

## Cookie的使用

测试服务端发送cookie给客户端，客户端请求时携带cookie
```
package main

import (
   "github.com/gin-gonic/gin"
   "fmt")

func main() {
   // 1.创建路由
   // 默认使用了2个中间件Logger(), Recovery()
   r := gin.Default()
   // 服务端要给客户端cookie
   r.GET("cookie", func(c *gin.Context) {
      // 获取客户端是否携带cookie
      cookie, err := c.Cookie("key_cookie")
      if err != nil {
         cookie = "NotSet"
         // 给客户端设置cookie
         //  maxAge int, 单位为秒
         // path,cookie所在目录
         // domain string,域名
         //   secure 是否智能通过https访问
         // httpOnly bool  是否允许别人通过js获取自己的cookie
         c.SetCookie("key_cookie", "value_cookie", 60, "/",
            "localhost", false, true)
      }
      fmt.Printf("cookie的值是： %s\n", cookie)
   })
   r.Run(":8000")
}
```

## Sessions

gorilla/sessions为自定义session后端提供cookie和文件系统session以及基础结构。
主要功能是：

* 简单的API：将其用作设置签名（以及可选的加密）cookie的简便方法。
* 内置的后端可将session存储在cookie或文件系统中。
* Flash消息：一直持续读取的session值。
* 切换session持久性（又称“记住我”）和设置其他属性的便捷方法。
* 旋转身份验证和加密密钥的机制。
* 每个请求有多个session，即使使用不同的后端也是如此。
* 自定义session后端的接口和基础结构：可以使用通用API检索并批量保存来自不同商店的session。

```
package main

import (
    "fmt"
    "net/http"

    "github.com/gorilla/sessions")

// 初始化一个cookie存储对象// something-very-secret应该是一个你自己的密匙，只要不被别人知道就行var store = sessions.NewCookieStore([]byte("something-very-secret"))

func main() {
    http.HandleFunc("/save", SaveSession)
    http.HandleFunc("/get", GetSession)
    err := http.ListenAndServe(":8080", nil)
    if err != nil {
        fmt.Println("HTTP server failed,err:", err)
        return
    }
}

func SaveSession(w http.ResponseWriter, r *http.Request) {
    // Get a session. We're ignoring the error resulted from decoding an
    // existing session: Get() always returns a session, even if empty.

    //　获取一个session对象，session-name是session的名字
    session, err := store.Get(r, "session-name")
    if err != nil {
        http.Error(w, err.Error(), http.StatusInternalServerError)
        return
    }

    // 在session中存储值
    session.Values["foo"] = "bar"
    session.Values[42] = 43
    // 保存更改
    session.Save(r, w)
}

func GetSession(w http.ResponseWriter, r *http.Request) {
    session, err := store.Get(r, "session-name")
    if err != nil {
        http.Error(w, err.Error(), http.StatusInternalServerError)
        return
    }
    foo := session.Values["foo"]
    fmt.Println(foo)
}
```
删除session的值：
```
    // 删除
    // 将session的最大存储时间设置为小于零的数即为删除
    session.Options.MaxAge = -1
    session.Save(r, w)
```
# 参数验证

## 结构体验证

用gin框架的数据验证，可以不用解析数据，减少if else，会简洁许多。
```
package main

import (
    "fmt"
    "time"

    "github.com/gin-gonic/gin")

//Person 
type Person struct {
    //不能为空并且大于10
    Age      int       `form:"age" binding:"required,gt=10"`
    Name     string    `form:"name" binding:"required"`
    Birthday time.Time `form:"birthday" time_format:"2006-01-02" time_utc:"1"`
}

func main() {
    r := gin.Default()
    r.GET("/5lmh", func(c *gin.Context) {
        var person Person
        if err := c.ShouldBind(&person); err != nil {
            c.String(500, fmt.Sprint(err))
            return
        }
        c.String(200, fmt.Sprintf("%#v", person))
    })
    r.Run()
}
```

## 自定义验证
```
package main

import (
    "net/http"
    "reflect"
    "github.com/gin-gonic/gin"
    "github.com/gin-gonic/gin/binding"
    "gopkg.in/go-playground/validator.v8")

/*
    对绑定解析到结构体上的参数，自定义验证功能
    比如我们要对 name 字段做校验，要不能为空，并且不等于 admin ，类似这种需求，就无法 binding 现成的方法
    需要我们自己验证方法才能实现 官网示例（https://godoc.org/gopkg.in/go-playground/validator.v8#hdr-Custom_Functions）
    这里需要下载引入下 gopkg.in/go-playground/validator.v8
*/
type Person struct {
    Age int `form:"age" binding:"required,gt=10"`
    // 2、在参数 binding 上使用自定义的校验方法函数注册时候的名称
    Name    string `form:"name" binding:"NotNullAndAdmin"`
    Address string `form:"address" binding:"required"`}

// 1、自定义的校验方法
func nameNotNullAndAdmin(v *validator.Validate, topStruct reflect.Value, currentStructOrField reflect.Value, field reflect.Value, fieldType reflect.Type, fieldKind reflect.Kind, param string) bool {

    if value, ok := field.Interface().(string); ok {
        // 字段不能为空，并且不等于  admin
        return value != "" && !("5lmh" == value)
    }

    return true
}

func main() {
    r := gin.Default()

    // 3、将我们自定义的校验方法注册到 validator中
    if v, ok := binding.Validator.Engine().(*validator.Validate); ok {
        // 这里的 key 和 fn 可以不一样最终在 struct 使用的是 key
        v.RegisterValidation("NotNullAndAdmin", nameNotNullAndAdmin)
    }

    /*
        curl -X GET "http://127.0.0.1:8080/testing?name=&age=12&address=beijing"
        curl -X GET "http://127.0.0.1:8080/testing?name=lmh&age=12&address=beijing"
        curl -X GET "http://127.0.0.1:8080/testing?name=adz&age=12&address=beijing"
    */
    r.GET("/5lmh", func(c *gin.Context) {
        var person Person
        if e := c.ShouldBind(&person); e == nil {
            c.String(http.StatusOK, "%v", person)
        } else {
            c.String(http.StatusOK, "person bind err:%v", e.Error())
        }
    })
    r.Run()
}
```
```
package main

import (
    "net/http"
    "reflect"
    "time"

    "github.com/gin-gonic/gin"
    "github.com/gin-gonic/gin/binding"
    "gopkg.in/go-playground/validator.v8")

// Booking contains binded and validated data.
type Booking struct {
    //定义一个预约的时间大于今天的时间
    CheckIn time.Time `form:"check_in" binding:"required,bookabledate" time_format:"2006-01-02"`
    //gtfield=CheckIn退出的时间大于预约的时间
    CheckOut time.Time `form:"check_out" binding:"required,gtfield=CheckIn" time_format:"2006-01-02"`
}

func bookableDate(
    v *validator.Validate, topStruct reflect.Value, currentStructOrField reflect.Value,
    field reflect.Value, fieldType reflect.Type, fieldKind reflect.Kind, param string,) bool {
    //field.Interface().(time.Time)获取参数值并且转换为时间格式
    if date, ok := field.Interface().(time.Time); ok {
        today := time.Now()
        if today.Unix() > date.Unix() {
            return false
        }
    }
    return true
}

func main() {
    route := gin.Default()
    //注册验证
    if v, ok := binding.Validator.Engine().(*validator.Validate); ok {
        //绑定第一个参数是验证的函数第二个参数是自定义的验证函数
        v.RegisterValidation("bookabledate", bookableDate)
    }

    route.GET("/5lmh", getBookable)
    route.Run()
}

func getBookable(c *gin.Context) {
    var b Booking
    if err := c.ShouldBindWith(&b, binding.Query); err == nil {
        c.JSON(http.StatusOK, gin.H{"message": "Booking dates are valid!"})
    } else {
        c.JSON(http.StatusBadRequest, gin.H{"error": err.Error()})
    }
}
```
# 其他
## 日志
```
package main

import (
    "io"
    "os"

    "github.com/gin-gonic/gin")

func main() {
    gin.DisableConsoleColor()

    // Logging to a file.
    f, _ := os.Create("gin.log")
    gin.DefaultWriter = io.MultiWriter(f)

    // 如果需要同时将日志写入文件和控制台，请使用以下代码。
    // gin.DefaultWriter = io.MultiWriter(f, os.Stdout)
    r := gin.Default()
    r.GET("/ping", func(c *gin.Context) {
        c.String(200, "pong")
    })
    r.Run()
}
```

## Air实时加载

Air能够实时监听项目的代码文件，在代码发生变更之后自动重新编译并执行，大大提高gin框架项目的开发效率。

## 权限管理

Casbin是用于Golang项目的功能强大且高效的开源访问控制库。
Casbin的作用：

* 以经典{subject, object, action}形式或您定义的自定义形式实施策略，同时支持允许和拒绝授权。
* 处理访问控制模型及其策略的存储。
* 管理角色用户映射和角色角色映射（RBAC中的角色层次结构）。
* 支持内置的超级用户，例如root或administrator。超级用户可以在没有显式权限的情况下执行任何操作。
* 多个内置运算符支持规则匹配。例如，keyMatch可以将资源键映射/foo/bar到模式/foo* 。

Casbin不执行的操作：

* 身份验证（又名验证username以及password用户登录时）
* 管理用户或角色列表。我相信项目本身管理这些实体会更方便。用户通常具有其密码，而Casbin并非设计为密码容器。但是，Casbin存储RBAC方案的用户角色映射。


在Casbin中，基于PERM元模型（策略，效果，请求，匹配器）将访问控制模型抽象为CONF文件。因此，切换或升级项目的授权机制就像修改配置一样简单。您可以通过组合可用的模型来定制自己的访问控制模型。例如，您可以在一个模型中同时获得RBAC角色和ABAC属性，并共享一组策略规则。Casbin中最基本，最简单的模型是ACL。ACL的CONF模型为：
```
＃请求定义
[request_definition]
r = sub, obj, act

[policy_definition]
p = sub, obj, act

[role_definition]
g = _, _

[policy_effect]
e = some(where (p.eft == allow))

[matchers]
m = g(r.sub, p.sub) && r.obj == p.obj && r.act == p.act
```
安装
```
go get github.com/casbin/casbin
```
示例：
```
package main

import (
    "fmt"
    "log"

    "github.com/casbin/casbin"
    xormadapter "github.com/casbin/xorm-adapter"
    "github.com/gin-gonic/gin"
    _ "github.com/go-sql-driver/mysql")

func main() {
    // 要使用自己定义的数据库rbac_db,最后的true很重要.默认为false,使用缺省的数据库名casbin,不存在则创建
    a, err := xormadapter.NewAdapter("mysql", "root:root@tcp(127.0.0.1:3306)/goblog?charset=utf8", true)
    if err != nil {
        log.Printf("连接数据库错误: %v", err)
        return
    }
    e, err := casbin.NewEnforcer("./rbac_models.conf", a)
    if err != nil {
        log.Printf("初始化casbin错误: %v", err)
        return
    }
    //从DB加载策略
    e.LoadPolicy()

    //获取router路由对象
    r := gin.New()

    r.POST("/api/v1/add", func(c *gin.Context) {
        fmt.Println("增加Policy")
        if ok, _ := e.AddPolicy("admin", "/api/v1/hello", "GET"); !ok {
            fmt.Println("Policy已经存在")
        } else {
            fmt.Println("增加成功")
        }
    })
    //删除policy
    r.DELETE("/api/v1/delete", func(c *gin.Context) {
        fmt.Println("删除Policy")
        if ok, _ := e.RemovePolicy("admin", "/api/v1/hello", "GET"); !ok {
            fmt.Println("Policy不存在")
        } else {
            fmt.Println("删除成功")
        }
    })
    //获取policy
    r.GET("/api/v1/get", func(c *gin.Context) {
        fmt.Println("查看policy")
        list := e.GetPolicy()
        for _, vlist := range list {
            for _, v := range vlist {
                fmt.Printf("value: %s, ", v)
            }
        }
    })
    //使用自定义拦截器中间件
    r.Use(Authorize(e))
    //创建请求
    r.GET("/api/v1/hello", func(c *gin.Context) {
        fmt.Println("Hello 接收到GET请求..")
    })

    r.Run(":9000") //参数为空 默认监听8080端口
}

//拦截器
func Authorize(e *casbin.Enforcer) gin.HandlerFunc {

    return func(c *gin.Context) {

        //获取请求的URI
        obj := c.Request.URL.RequestURI()
        //获取请求方法
        act := c.Request.Method
        //获取用户的角色
        sub := "admin"

        //判断策略中是否存在
        if ok, _ := e.Enforce(sub, obj, act); ok {
            fmt.Println("恭喜您,权限验证通过")
            c.Next()
        } else {
            fmt.Println("很遗憾,权限验证没有通过")
            c.Abort()
        }
    }
}
```
rbac_models.conf里面的内容如下：
```
[request_definition]
r = sub, obj, act

[policy_definition]
p = sub, obj, act

[role_definition]
g = _, _

[policy_effect]
e = some(where (p.eft == allow))

[matchers]
m = g(r.sub, p.sub) && r.obj == p.obj && r.act == p.act
```

配置链接数据库不需要手动创建数据库，系统自动创建casbin_rule表

# 部署
基本流程：

1. build出一个可执行文件
`go build main.go`，则生成了一个新文件main，并赋予可执行权限即可
2. 写一个sh文件，用来执行这个文件
vim run.sh
```
#!/bin/bash 
# 设置为 release 生产模式 
export GIN_MODE=release 
# 切换到路径下，这样才能够使用和开发时候一样的相对路径 
cd main文件所在的绝对路径 
# 启动 build 后的可执行文件 
./main
```
3. 配置service
输入命令创建：
vim /lib/systemd/system/mpgo.service
其中mpgo为服务名称，以后启动都是这个名称。里面写这样的内容：
```
[Unit] 
Description=mpgo

[Service] 
Type=simple 
Restart=always 
RestartSec=3s 
ExecStart=run.sh文件的完整路径 
[Install] 
WantedBy=multi-user.target
```
说明如下：

* Description是对这个服务的描述
* Restart=always服务异常退出时会重启
* RestartSec=3s设置重启间隔为3秒
* ExecStart=run.sh文件的完整路径这个服务会执行这个文件
* WantedBy=multi-user.target所有用户都可以执行
4. 启动
`service mpgo start/stop/restart/status`
