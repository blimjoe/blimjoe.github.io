---
title: Ubuntu/Debian上使用clash
tags: 运维
---  
### Ubuntu / Debian上使用clash  
1. clash core  
下载clash core（https://github.com/Dreamacro/clash/releases）  
```  
$ wget https://github.com/Dreamacro/clash/releases/download/v1.11.12/clash-linux-amd64-v1.11.12.gz  
$ gunzip clash-linux-amd64-v1.11.12.gz  
$ sudo chmod +x clash-linux-amd64-v1.11.12
$ sudo mv clash-linux-amd64-v1.11.12 /usr/bin/clash  
```  
2. 增加订阅  
``` 
$ mkdir /etc/clash  
// 增加config.yaml和Country.mmdb  
```  
3. 设置systemd  
```   
$ sudo touch /etc/systemd/system/clash.service
```  
内容如下： 
> [Unit]
> Description=clash daemon
> 
> [Service]
> Type=simple
> User=root
> ExecStart=/usr/bin/clash -d /etc/clash/
> Restart=on-failure
> 
> [Install]
> WantedBy=multi-user.target 

```  
$ sudo systemctl daemon-reload
$ sudo systemctl start clash.service  
```  
4. 配置clash-dashboard  
```  
$ git clone -b gh-pages --depth 1 https://github.com/Dreamacro/clash-dashboard  /etc/clash  
# 修改/etc/clash/config.yaml中的external-ui为
external-ui: '/etc/clash/clash-dashboard'  
```  
5. 访问http://127.0.0.1:9090/ui  
6. 配置proxychains  
```  
socks5 127.0.0.1 7891  
```  
