---
title: "Golang相关知识"
date: 2023-07-19T14:42:41+08:00
tags: ["golang入门", "go基础知识"]
categories: ["编程入门知识", "golang相关"]
url: "/"
---
# Golang相关知识
---
## 1 Golang入门

> #### 1. 编写hello, world 
> ```go
> package main
>
> import "fmt"
>
> var aaa = "123456"
> 
> func main()  {
> 	var aa string = "hello world!"  //变量标准定义
>	var aa1 = "ssss"                //简写形式1
>	aa2 := "1111"                   //简写形式2
>	fmt.Println("hello world!", aa, aa1, aa2)
>}
> ```

> #### 2. 使用自带http库，写简单的web服务
> ```go
> package main
> 
> import (
> 	"net/http"
> )
> 
> func main() {
> 	//http.HandleFunc("/health", func(w http.ResponseWriter, r *http.Request) {
> 	//	w.Write([]byte("1234567"))
> 	//})
> 	//http.ListenAndServe(":8080", nil)
> 
> 	mux := http.NewServeMux()
> 	mux.HandleFunc("/health", func(w http.ResponseWriter, r *http.Request) {
> 			w.Write([]byte("1234567"))
> 	})
> 	http.ListenAndServe(":8080", mux)
> }
> ```

> #### 3. 使用第三方的web框架（Gin），写简单的web服务
> ```go
> package main
> 
> import (
> 	"github.com/gin-gonic/gin"
> )
> 
> func main() {
> 	engine := gin.Default()
> 	engine.Handle("GET", "/health", func(context *gin.Context) {
> 		name := context.Query("name")
> 		context.JSON(200, gin.H{"code": "0", "message": "ok", "data": name})
> 	})
> 	engine.Run(":8080")
> }
> ```

> #### 4. Golang中协程的使用
> ```go
> package main
> 
> import (
> 	"fmt"
> 	"strconv"
> )
> 
> func main() {
> 	for i:=1; i<10; i++ {
> 		it := i	        //内部变量接收i值
> 		go func() {     //go关键字 后面加上调用方法 就会创建一个协程
> 			fmt.Println("go 12345 " + strconv.Itoa(it))
> 		}()
> 	}
> }
> ```

## 2 协程知识讲解
> &emsp;&emsp;我们应该都听说过操作系统是以时间片轮转的方式来轮流执行已就绪的任务，使每个任务在CPU上执行一段时间，由于时间非常短，就会给人造成多个任务在同时运行的现象，这就是并发，在很短一段时间内CPU可以执行多个任务。
> 
> &emsp;&emsp;并行，是指在同一时刻，多个任务可以同时执行，这个就只有在多核CPU上才有的了。
> 
> &emsp;&emsp;由于有并发或者并行的需求，所以也就有了多线程的开发。
> 
> &emsp;&emsp;线程可分为内核线程和用户线程。  
> &emsp;&emsp;内核线程是操作系统可以调度的，给CPU来执行的。  
> &emsp;&emsp;现在大多数开发语言的多线程，都是基于内核线程的
> 
> &emsp;&emsp;用户线程是在操作系统用户层面抽象出来的线程，所以操作系统是不会直接调度的，不过最终用户线程还是得依托内核线程才能干事请，而这种用户线程也可以称之为协程、轻线程
，后面统一用协程来称呼
> 
> &emsp;&emsp;*协程相对于线程有什么优势呢*  
> &emsp;&emsp;其实从上面的介绍是看不出有什么优势的，反而它还得依托线程来干事，能有什么优势呢
>
> &emsp;&emsp;*它的优势体现在大量的并发的操作下*
>
> &emsp;&emsp;多线程的创建、以及线程间切换执行都会消耗大量CPU资源，虽然采用线程池可以改善线程创建的问题，但是线程间的切换依旧还是会消耗CPU，特别是在大量线程之间的来回切换
同时每个线程占用的内存空间也比较大
这都是相对于协程来说的
>
> &emsp;&emsp;而协程间的切换，是在用户态下进行的，消耗CPU资源比较少，并且协程的内存空间占用相对来讲会比线程少
>
> &emsp;&emsp;所以在大量并发的情况下，协程的优势才会体现出来
> #### Golang协程模型介绍
> GMP模型
>> G：go 协程  
>> M：系统线程  
>> P：逻辑处理器，负责提供相关的上下文环境，内存缓存的管理，Goroutine任务队列等  
>> Go 将一个全局队列拆成了多个本地队列，这个管理本地队列的结构被称作 P。
> ![GMP-Model](./resource/image/gmp-model.png)