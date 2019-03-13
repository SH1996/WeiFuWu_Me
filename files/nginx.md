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
