## Nginx配置upstream实现负载均衡
* nginx最吸引人的地方就在于他可以实现负载均衡，减轻服务器压力
* 代理多台服务器，通过upstream模块进行反向代理，并保持系统可用

### nginx配置upstream
* 在nginx http模块下加入upstream节点
	
```
upstream www { 
# www这个名字可以随便起，但是要尽量符合服务器的用途和域名
      server 10.0.6.108:8090; 
      server 10.0.0.85:8091; 
}
```
	
* 配置server节点下的location节点中的proxy_pass反向代理
	
```
location / { 
    proxy_pass http://www; 
}
```
	
* upstream依照轮询（默认）方式进行负载，每一个请求按时间顺序逐一分配到不同的后端服务器
* 若后端服务器down掉，能自己主动剔除

### upstream的分配策略
* 指定轮询几率(weight)，weight和访问比率成正比，用于后端服务器性能不均的情况

```
upstream linuxidc{ 
      server 10.0.0.77 weight=5; 
      server 10.0.0.88 weight=10; 
}
# 10.0.0.88的訪问比率要比10.0.0.77的访问比率高一倍
```
* 每一个请求按訪问ip的hash结果分配(ip_hash)。这样每一个訪客固定訪问一个后端服务器，能够解决session的问题

```
upstream favresin{ 
      ip_hash; 
      server 10.0.0.10:8080; 
      server 10.0.0.11:8080; 
}
```
* 按后端服务器的响应时间来分配请求(fair)。响应时间短的优先分配。与weight分配策略相似。

```
 upstream favresin{      
      server 10.0.0.10:8080; 
      server 10.0.0.11:8080; 
      fair; 
}
```
* 按訪问url的hash结果来分配请求，使每一个url定向到同一个后端服务器(url_hash)。后端服务器为缓存时比較有效

* 注意：在upstream中加入hash语句。server语句中不能写入weight等其他的參数，hash_method是使用的hash算法。

```
 upstream resinserver{ 
      server 10.0.0.10:7777; 
      server 10.0.0.11:8888; 
      hash $request_uri; 
      hash_method crc32; 
}
```

### upstream设备设置状态值
* down 表示单前的server临时不參与负载.
* weight 默认为1.weight越大，负载的权重就越大。
* max_fails ：同意请求失败的次数默认为1.当超过最大次数时，返回proxy_next_upstream 模块定义的错误.
* fail_timeout : max_fails次失败后。暂停的时间。
* backup： 其他全部的非backup机器down或者忙的时候，请求backup机器。所以这台机器压力会最轻。

```
upstream bakend{ 
#定义负载均衡设备的Ip及设备状态 
      ip_hash; 
      server 10.0.0.11:9090 down; 
      server 10.0.0.11:8080 weight=2; 
      server 10.0.0.11:6060; 
      server 10.0.0.11:7070 backup; 
}
```
