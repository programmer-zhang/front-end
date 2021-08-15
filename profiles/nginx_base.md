> Nginx 是一款轻量级的Web 服务器/反向代理服务器及电子邮件（IMAP/POP3）代理服务器，目前国内好很多公司都在使用，本篇带来Nginx 基础介绍，后期还会有更为详细的介绍，敬请关注。

## 阅读本文您将收获
*  Nginx  的作用
*  Nginx  基础信息，包括架构、基础配置、基本概念

##   Nginx 的作用
* webserver
* 反向代理服务器
* 负载均衡

## 初探 Nginx 架构
* 多进程模式 
* 支持手动关闭后台模式，让 Nginx 在前台运行，通过配置取消 master 进程，使 Nginx 以单进程方式运行
*  Nginx 启动后，会有一个 master 进程和多个 worker 进程
* master 进行主要用来管理 worker 进程
	* 包含：接收来自外界的信号，向各 worker 进程发送信号，监控 worker 进程的运行状态，当 worker 进程退出后(异常情况下)，会自动重新启动新的 worker 进程
* 网络事件放在 worker 进程中
* 多个 worker 进程对等，同等竞争来自客户端的请求，各进程互相之间是独立的
* 一个请求只能在一个 worker 进程中处理
* 一个 worker 进程不能处理其他进程的请求
* worker 进程数可以设置，一般设置为与机器cpu核数相同
* `nginx -s reload`nginx从容重启：首先 master 进程在接到信号后，会先重新加载配置文件，然后再启动新的 worker 进程，并向所有老的 worker 进程发送信号，告诉他们可以光荣退休了。新的 worker 在启动后，就开始接收新的请求，而老的 worker 在收到来自 master 的信号后，就不再接收新的请求，并且在当前进程中的所有未处理完的请求处理完成后，再退出。

### Nginx 进程模型
* 每个 worker 进程 fork master 进程(在 master 进程完成建立好 listen 的 socket 之后)
* 为保证只有一个worker进程处理这个请求，在 worker 注册 listenfd 事件前抢 accept_mutex,抢到的注册事件
* 读事件里调用 accept 接受该链接，读取请求，解析请求，处理请求，产生数据并返回，断开连接
* 该模型好处
	* 独立进程不加锁，省事
	* 相互之间不影响，服务不中断
* 处理事件
	* 异步非阻塞方式处理进程
		* 非阻塞：事件没有准备好，马上返回 EAGAIN，告诉你，事件还没准备好呢，你慌什么，过会再来吧。好吧，你过一会，再来检查一下事件，直到事件准备好了为止，在这期间，你就可以先去做其它事情，然后再来看看事件好了没。

* 事件的三种类型：网络事件、信号、定时器

##  Nginx 基础概念
### connection
* 一个 Nginx 的最大连接数 `worker_connections * worker_processes`
* 对于HTTP请求本地资源来说，能够支持的最大并发数量是 `worker_connections * worker_processes`
* HTTP 作为反向代理来说，最大并发数量应该是 `worker_connections * worker_processes/2` （客户端的链接和后端服务的链接）
* `accept_mutex` 选项：获得了`accept_mutex`的进程才会去添加accept事件
	* 控制进程是否添加 accept 时间
	* 避免有的进程有空闲链接，没有处理机会，有的进程没有空闲链接，链接被丢弃
	* `ngx_accept_disabled` 的变量来控制是否去竞争 `accept_mutex` 锁

### request
![nginx处理网络请求的生命周期](../images/nginx-manage-scoket.png)

* `ngx_http_request_t`是对一个http请求的封装，用来保存解析请求与输出响应相关的数据
* 从 `ngx_http_init_request` 开始，这个函数设置读事件`ngx_http_process_request_line` 来处理请求行，通过`ngx_http_read_request_header` 来处理请求头
	* 以请求行中的host域来查找虚拟机
* 解析到的数据会存储在 `ngx_http_request_t` 结构中
* nginx解析到两个回车换行符表示请求头结束，调用 `ngx_http_process_request` 来处理请求
* `ngx_http_process_request` 设置处理函数为 `ngx_http_request_handler`
* 然后调用 `ngx_http_handler` 来真正地处理一个完整的HTTP请求

### keepalive
* 除了 `http1.0` 不带 `content-length` 以及 `http1.1` 非chunked不带 `content-length` 外，body的长度是可知的
* 客户端的请求头中的 `connection` 为 `close`，则表示客户端需要关掉长连接
* 如果为 `keep-alive` ，则客户端需要打开长连接
* 如果客户端的请求中没有 `connection` 这个头，那么根据协议，如果是 `http1.0`，则默认为`close`，如果是 `http1.1` ，则默认为 `keep-alive`
* 如果服务端最后决定 `keepalive` 打开，响应头中会包含 `connection:keep-alive`,否则就是 `connection:close`
* `keepalive` 开启的好处:客户端一次访问需要多次访问同一个 `server` 时，会减少大量 `time-wait` 的数量

### pipe
* `pipeline` 流水线作业，是 `keepalive` 的一种升华
* 与 `keepalive` 相同也是基于长连接
* 利用一个连接做多次请求
* 与 `keepalive` 的区别
	* `keepalive`： 第二个请求必须等到第一个请求的响应接受完全后才能发起
	* `pipeline`:  Nginx 对 `pipeline` 中的多个请求处理不是并行的，依然一个请求一个请求的处理，只是在处理第一个请求的时候，客户端就可以发起第二个请求
* 实现原理： Nginx 读取数据时，会将读取的数据放到一个 `buffer` 里，，处理完前一个请求后，如果发现 `buffer` 里还有数据，就会认为是下一个请求的开始，然后处理下一个请求，否则设置`keepalive`

### `lingering_close`
* 延迟关闭，当 Nginx 关闭连接时，先关闭 `tcp` 的写，等待一段时间再关闭连接的读
* 保持更好的客户端兼容性。需要消耗更多的额外资源(比如连接会被一直占用)

## nginx的配置系统
* 由一个主配置文件和其他一些辅助配置文件构成
* 这些文件都是纯文本文件
* 全部位于 Nginx 安装目录的 `conf` 目录下
* 只有主配置文件 `nginx.conf` 是在任何情况下都会被使用
* `nginx.conf` 中，若干配置项由配置指令和指令参数两个部分构成

### 指令
* 配置指令是一个字符串
* 可以用单引号或者双引号括起来，也可以不
* 如果配置指令包含空格，一定要引起来。

### 指令参数
* 指令参数使用一个或多个空格或者TAB字符与指令隔开
* 指令参数有一个或多个TOKEN串组成，TOKEN串之间由空格或者TAB键分隔
* 简单配置项：`error_page   500 502 503 504  /50x.html;`
* 复杂配置项：

	```
	location / {
	    root   /home/jizhao/nginx-book/build/html;
	    index  index.html index.htm;
	}
	```

### 指令上下文
* `nginx.conf` 中的配置信息根据逻辑上的意义，进行分类，分成了多个作用域(配置指令上下文)
* 不同的作用域含有一个或者多个配置项
* nginx支持的几个指令上下文

指令上下文|含义
:--:|:--
main|nginx在运行时与具体业务功能（比如http服务或者email服务代理）无关的一些参数，比如工作进程数，运行的身份等
http|与提供http服务相关的一些配置参数。例如：是否使用keepalive啊，是否使用gzip进行压缩等
server|http服务上支持若干虚拟主机。每个虚拟主机一个对应的server配置项，配置项里面包含该虚拟主机相关的配置。在提供mail服务的代理时，也可以建立若干server.每个server通过监听的地址来区分
location|http服务中，某些特定的URL对应的一系列配置项
mail|实现email相关的SMTP/IMAP/POP3代理时，共享的一些配置项（因为可能实现多个代理，工作在多个监听地址上）

## nginx的模块化体系结构
* nginx的内部结构是由核心部分和一系列的功能模块所组成
* 好处：使每个模块的功能相对简单，便于开发，同时便于对系统进行功能扩展
### 模块概述
* 将各功能模块组织成一个链，有请求到达时，请求依次经过这条链上的部分或者全部模块，进行处理。
* 有两个模块比较特殊，他们居于nginx core和各功能模块的中间。这两个模块就是http模块和mail模块。
* http模块和mail模块在nginx core之上实现了另外一层抽象，处理与HTTP协议和email相关协议（SMTP/POP3/IMAP）有关的事件，并且确保这些事件能被以正确的顺序调用其他的一些功能模块

### 模块的分类

类型|功能
:--:|:--
event module|搭建了独立于操作系统的事件处理机制的框架，及提供了各具体事件的处理。包括ngx_events_module， ngx_event_core_module和ngx_epoll_module等。nginx具体使用何种事件处理模块，这依赖于具体的操作系统和编译选项
phase handler|此类型的模块也被直接称为handler模块。主要负责处理客户端请求并产生待响应内容，比如ngx_http_static_module模块，负责客户端的静态页面请求处理并将对应的磁盘文件准备为响应内容输出
output filter|也称为filter模块，主要是负责对输出的内容进行处理，可以对输出进行修改。例如，可以实现对输出的所有html页面增加预定义的footbar一类的工作，或者对输出的图片的URL进行替换之类的工作
upstream|upstream模块实现反向代理的功能，将真正的请求转发到后端服务器上，并从后端服务器上读取响应，发回客户端。upstream模块是一种特殊的handler，只不过响应内容不是真正由自己产生的，而是从后端服务器上读取的
load-balancer|负载均衡模块，实现特定的算法，在众多的后端服务器中，选择一个服务器出来作为某个请求的转发服务器

### 请求的处理流程
* 初始化 `HTTP Request`（读取来自客户端的数据，生成 `HTTP Request` 对象，该对象含有该请求所有的信息）
* 处理请求头
* 处理请求体
* 如果有的话，调用与此请求（URL或者Location）关联的 `handler`
* 依次调用各 `phase handler` 进行处理
* 通常情况下，一个 `phase handler` 对这个 `request` 进行处理，并产生一些输出。通常`phase handler` 是与定义在配置文件中的某个 `location` 相关联的
* 一个 `phase handler` 通常执行以下几项任务：
	* 获取 `location` 配置
	* 产生适当的响应
	* 发送 `response header`
	* 发送 `response body`
