* [nginx](http://tengine.taobao.org/book/chapter_02.html)
* 多进程模式 
* 支持手动关闭后台模式，让nginx在前台运行，通过配置取消master进程，使nginx以单进程方式运行
* nginx启动后，会有一个master进程和多个worker进程
* master进行主要用来管理worker进程
	* 包含：接收来自外界的信号，向各worker进程发送信号，监控worker进程的运行状态，当worker进程退出后(异常情况下)，会自动重新启动新的worker进程
* 网络事件放在worker进程中
* 多个worker进程对等，同等竞争来自客户端的请求，各进程互相之间是独立的
* 一个请求只能在一个worker进程中处理
* 一个worker进程不能处理其他进程的请求
* 当前请求的worker进程异常退出，没有可供使用的进程，是直接返回失败还是等待？？？
* worker进程数可以设置，一般设置为与机器cpu核数相同
* nginx从容重启：首先master进程在接到信号后，会先重新加载配置文件，然后再启动新的worker进程，并向所有老的worker进程发送信号，告诉他们可以光荣退休了。新的worker在启动后，就开始接收新的请求，而老的worker在收到来自master的信号后，就不再接收新的请求，并且在当前进程中的所有未处理完的请求处理完成后，再退出
* 服务不中断？
* `nginx -s reload`
* nginx进程模型
	* 每个worker进程fork master进程(在master进程完成建立好listen的socket之后)
	* 为保证只有一个worker进程处理这个请求，在worker注册listenfd事件前抢accept_mutex,抢到的注册事件
	* 读事件里调用accept接受该链接，读取请求，解析请求，处理请求，产生数据并返回，断开连接
* 该模型好处
	* 独立进程不加锁，省事
	* 相互之间不影响，服务不中断
* 处理事件
	* 异步非阻塞方式处理进程
		* 非阻塞：事件没有准备好，马上返回EAGAIN，告诉你，事件还没准备好呢，你慌什么，过会再来吧。好吧，你过一会，再来检查一下事件，直到事件准备好了为止，在这期间，你就可以先去做其它事情，然后再来看看事件好了没

* 事件的三种类型：网络事件、信号、定时器

## nginx基础概念
### connection
* 一个nginx的最大连接数 worker_connections * worker_processes
* 对于HTTP请求本地资源来说，能够支持的最大并发数量是worker_connections * worker_processes
* HTTP作为反向代理来说，最大并发数量应该是worker_connections * worker_processes/2 （客户端的链接和后端服务的链接）
* `accept_mutex` 选项：获得了`accept_mutex`的进程才会去添加accept事件
	* 控制进程是否添加accept时间
	* 避免有的进程有空闲链接，没有处理机会，有的进程没有空闲链接，链接被丢弃
	* `ngx_accept_disabled`的变量来控制是否去竞争`accept_mutex`锁

### request
* `ngx_http_request_t`是对一个http请求的封装，用来保存解析请求与输出响应相关的数据
* 从`ngx_http_init_request`开始，这个函数设置读事件`ngx_http_process_request_line`来处理请求行，通过`ngx_http_read_request_header`来处理请求头
	* 以请求行中的host域来查找虚拟机
* 解析到的数据会存储在`ngx_http_request_t`结构中
* 