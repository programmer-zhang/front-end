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
	    proxy_pass http://tomcat:8080/index
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
	    proxy_pass http://tomcat:8080/
	}
	```

### ReWrite语法规则
* last – 基本上都用这个Flag，表示完成Rewrite
* break – 中止Rewirte，不在继续匹配
	* last和break的区别
		* last一般写在server和if中，而break一般使用在location中
		* last不终止重写后的url匹配，即新的url会再从server走一遍匹配流程，而break终止重写后的匹配
		* break和last都能组织继续执行后面的rewrite指令
* redirect – 返回临时重定向的HTTP状态302，地址栏显示跳转后的地址
* permanent – 返回永久重定向的HTTP状态301，地址栏显示跳转后的地址

### 常见全局变量
* `$args` ： 这个变量等于请求行中的参数，同$query_string
* `$content_length` ： 请求头中的Content-length字段。
* `$content_type` ： 请求头中的Content-Type字段。
* `$document_root` ： 当前请求在root指令中指定的值。
* `$host` ： 请求主机头字段，否则为服务器名称。
* `$http_user_agent` ： 客户端agent信息
* `$http_cookie` ： 客户端cookie信息
* `$limit_rate` ： 这个变量可以限制连接速率。
* `$request_method` ： 客户端请求的动作，通常为GET或POST。
* `$remote_addr` ： 客户端的IP地址。
* `$remote_port` ： 客户端的端口。
* `$remote_user` ： 已经经过Auth Basic Module验证的用户名。
* `$request_filename` ： 当前请求的文件路径，由root或alias指令与URI请求生成。
* `$scheme` ： HTTP方法（如http，https）。
* `$server_protocol` ： 请求使用的协议，通常是HTTP/1.0或HTTP/1.1。
* `$server_addr` ： 服务器地址，在完成一次系统调用后可以确定这个值。
* `$server_name` ： 服务器名称。
* `$server_port` ： 请求到达服务器的端口号。
* `$request_uri` ： 包含请求参数的原始URI，不包含主机名，如：”/foo/bar.php?arg=baz”。
* `$uri` ： 不带请求参数的当前URI，$uri不包含主机名，如”/foo/bar.html”。
* `$document_uri` ： 与$uri相同。
