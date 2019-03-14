1.下载nginx稳定版，zip文件（html是存放静态访问资源／log是启动和运行日志／conf里面配置nginx.conf文件／其他无需介绍／nginx.exe启动一闪而过）

2.nginx配置反向代理：修改nginx.conf文件夹的server属性，每配置一个server_name就要配置一个location（location里面属性名很多不用记住），这
样一对K-V配置表示nginx配置一个虚拟主机，每一个虚拟主机配合host文件（也就是DNS配置,实际对外网开发种不需要配置DNS）设置ip重转发，运用反向代理，实现IP真实地址隐藏。
下面为nginx的反向代理配置信息
```
        listen       8090;
        server_name  127.0.0.1;  	  
        location / {
            proxy_pass  http://www.baidu.com;
            index  index.html index.htm;
        }

```

3.nginx的负载均衡：有算法，轮循，ip绑定，权重，分别表示含义为  根据算法选择访问，根据次数轮流访问，根据电脑ip不同电脑选择性访问，根据设置比例选择访问；下面为配置代码：
```
1.轮询（默认） 每个请求按时间顺序逐一分配到不同的后端服务器，如果后端服务器down掉，能自动剔除。 
upstream backserver {  server 192.168.0.14;  server 192.168.0.15;  } 
2、指定权重 指定轮询几率，weight和访问比率成正比，用于后端服务器性能不均的情况。 
upstream backserver {  server 192.168.0.14 weight=10;  server 192.168.0.15 weight=10;  } 
3、IP绑定 ip_hash 每个请求按访问ip的hash结果分配，这样每个访客固定访问一个后端服务器，可以解决session的问题。 
upstream backserver {  ip_hash;  server 192.168.0.14:88;  server 192.168.0.15:80;  } 

然后将proxy_pass后面改为http://backserver；  就ok了
```
4.nginx配置服务器宕机容错机制（高可用）：一主一备或者多主多备。集群nginx，或者集群nginx代理的ip主机服务器（nginx默认有宕机容错轮训机制），如果nginx备用主机还是宕机，使用keepalive自动重启nginx脚本，n次还是宕机就发送邮箱给运维人员；
配置代码如下：
```
宕机轮训配置规则

    server {
        listen       80;
        server_name  www.itmayiedu.com;
        location / {
	    proxy_pass  http://backserver;
	    index  index.html index.htm;
            # 时间超出一秒之后就自动轮训下一台ip服务器
	    proxy_connect_timeout 1;
            proxy_send_timeout 1;
            proxy_read_timeout 1;
        }
		
	    }
```
淘宝架构案例：发送新版本时访问备用主机；大公司一般都是一主一备，其中tomcat发布版本时，session会失效怎么解决？存放到redis中；

5.nginx解决跨域问题：ajax跨域问题是因为-->请求url的所在域名和ajax请求访问的域名不一致导致不能访问，nginx实现跨域是虚拟出两个同域名主机，请求参数不同来实现的，代码如下：
```
server {
        listen       80;
        server_name  www.itmayiedu.com;
        location /A {
	    proxy_pass  http://www.a.com;
	    index  index.html index.htm;
        }
	location /B {
	    proxy_pass  http://www.b.com;
	    index  index.html index.htm;
        }
		
}
```
这样它们的域名都是 www. itmayiedu.com 了！ ok
