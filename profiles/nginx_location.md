## Nginx location配置
### Nginx location语法规则
* `location [=|~|~*|^~] /uri/ { … }`
	* = 开头表示精确匹配
	* ^~ 开头表示uri以某个常规字符串开头，理解为匹配 url路径即可，匹配符合后，不再向下匹配
	* ~ 开头表示区分大小写的正则匹配
	* ~* 开头表示不区分大小写的正则匹配
	* !~和!~*分别为区分大小写不匹配及不区分大小写不匹配 的正则
	* / 通用匹配，任何请求都会匹配到

### 实际使用建议
* 必选规则
	* 直接匹配网站根，通过域名访问网站首页比较频繁，使用这个会加速处理
	* 这里是直接转发给后端应用服务器了，也可以是一个静态首页

	```
	location = / {
	    proxy_pass http://xxxx.com/index
	}
	```
* 必选规则
	* 处理静态文件请求，这是nginx作为http服务器的强项
	* 有两种配置模式，目录匹配或后缀匹配,任选其一或搭配使用
	
	```
	location ^~ /static/ {
	    root /webroot/static/;
	}
	location ~* \.(gif|jpg|jpeg|png|css|js|ico)$ {
	    root /webroot/res/;
	}
	```
* 通用规则
	* 用来转发动态请求到后端应用服务器,可以在前端页面增加路由转发，增加一个共同的前缀
	* 非静态文件请求就默认是动态请求，自己根据实际把握
	* 毕竟目前的一些框架的流行，带.php,.jsp后缀的情况很少了
	
	```
	location /api/ {
	    proxy_pass http://xxxx.com/api/abc
	}
	```

### Rewrite语法规则
* last – 基本上都用这个Flag，表示完成Rewrite
* break – 中止Rewirte，不在继续匹配
	* last和break的区别
		* last一般写在server和if中，而break一般使用在location中
		* last不终止重写后的url匹配，即新的url会再从server走一遍匹配流程，而break终止重写后的匹配
		* break和last都能组织继续执行后面的rewrite指令
* redirect – 返回临时重定向的HTTP状态302，地址栏显示跳转后的地址
* permanent – 返回永久重定向的HTTP状态301，地址栏显示跳转后的地址
	* redirect 和 permanent
		* 临时重定向：对旧网址没有影响，但新网址不会有排名
		* 永久重定向：新网址完全继承旧网址，旧网址的排名等完全清零

### 常见全局变量
* `$args`： 这个变量等于请求行中的参数，同$query_string
* `$content_length`： 请求头中的Content-length字段。
* `$content_type`： 请求头中的Content-Type字段。
* `$document_root`： 当前请求在root指令中指定的值。
* `$host`： 请求主机头字段，否则为服务器名称。
* `$http_user_agent`： 客户端agent信息
* `$http_cookie`： 客户端cookie信息
* `$limit_rate`： 这个变量可以限制连接速率。
* `$request_method`： 客户端请求的动作，通常为GET或POST。
* `$remote_addr`： 客户端的IP地址。
* `$remote_port`： 客户端的端口。
* `$remote_user`： 已经经过Auth Basic Module验证的用户名。
* `$request_filename`： 当前请求的文件路径，由root或alias指令与URI请求生成。
* `$scheme`： HTTP方法（如http，https）。
* `$server_protocol`： 请求使用的协议，通常是HTTP/1.0或HTTP/1.1。
* `$server_addr`： 服务器地址，在完成一次系统调用后可以确定这个值。
* `$server_name`： 服务器名称。
* `$server_port`： 请求到达服务器的端口号。
* `$request_uri`： 包含请求参数的原始URI，不包含主机名，如：”/abc/index.html?token=1a”
* `$uri`： 不带请求参数的当前URI，$uri不包含主机名，如”/foo/bar.html”。
* `$document_uri`： 与$uri相同。

### 一个关于重定向的参数处理问题
* 问题描述：Nginx在进行rewrite后都会自动添加上旧地址中的参数部分，而这对于重定向到的新地址来说可能是多余
* 解决方案：
	* 转发规则添加 `? `后缀
* 问题实例：
	* 把 `http://xxx.com/test.html?params=xxx` 重定向到 `http://xxx.com/new.html`
	* 若按照默认的写法：`rewrite ^/test.html(.*) /new permanent;`
	* 重定向后的结果是：`http://xxx.com/new.html?params=xxx`
	* 如果改写成：`rewrite ^/test.html(.*) /new.html? permanent;`
	* 那结果就是：`http://xxx.com/new.html`
* 指定参数Rewirte
	* `rewrite  ^/test.html  /new.html  permanent;`       //重写向带参数的地址
	* `rewrite  ^/test.html  /new.html?  permanent;`      //重定向后不带参数
	* `rewrite  ^/test.html  /new.html?id=$arg_id?  permanent;`    //重定向后带指定的参数

### 消除 `proxy_pass` 转发 `/`的恐怖阴影
* 在配置Nginx过程中路由转发的一个小小 `/` 可能会给你带来很多麻烦，虽然只是一个小小的斜杠，但是转发的结果千差万别
* 两种情况
	* `proxy_pass` 不带`/`
	
	```
	location /alpha/ {
	    proxy_pass  http://192.168.xxx.xxx:80;
	}
	http://domain/alpha/ --> http://192.168.xxx.xxx:80/alpha/
	http://domain/alpha/beta/abc --> http://192.168.xxx.xxx:80/alpha/beta/abc
	```
	* `proxy_pass` 带`/`
	
	```
	location /alpha/ {
	    proxy_pass  http://192.168.xxx.xxx:80/;
	}
	http://domain/alpha/ --> http://192.168.xxx.xxx:80/
	http://domain/alpha/beta/abc --> http://192.168.xxx.xxx:80/beta/abc
	```