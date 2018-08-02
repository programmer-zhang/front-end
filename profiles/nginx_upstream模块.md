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
	
* 