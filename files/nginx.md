1.下载nginx稳定版，zip文件（html是存放静态访问资源／log是启动和运行日志／conf里面配置nginx.conf文件／其他无需介绍／nginx.exe启动一闪而过）
2.nginx配置反向代理：修改nginx.conf文件夹的server属性，每配置一个server_name就要配置一个location（location里面属性名很多不用记住），这
样一对K-V配置表示nginx配置一个虚拟主机，每一个虚拟主机配合host文件（也就是DNS配置）设置ip重转发，运用反向代理，实现IP真实地址隐藏。
