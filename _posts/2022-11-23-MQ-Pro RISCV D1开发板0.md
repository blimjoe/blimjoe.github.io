### ESP32-CAM-MB初尝试  
最近有无线监控和AP的需求，网上卖的设备又有点贵，所以想尝试用ESP32-CAM自制。  
>  ESP322-CAM是安信可最新推出一款开发板模组，只需要一个ESP32模组和摄像头即可组成一个摄像头系统，尺寸仅为2740.54.5mm，可广泛应用于各种物联网场合，适用于家庭智能设备、工业无线控制、无线监控、QR无线识别，无线定位系统信号以及其它物联网应用，是物联网应用的理想解决方案。  

---  
> 使用的环境是Debian及Ubuntu  
1. 安装基础环境  
```
$ sudo apt-get install git wget flex bison gperf python3 python3-venv python3-setuptools cmake ninja-build ccache libffi-dev libssl-dev dfu-util libusb-1.0-0  
```  
2. 将主板和底板连接pc之后查看设备  
在linux上的设备为/dev/ttyUSBX设备  

3. 获取ESP-IDF  
```  
$ mkdir ~/data/idf && cd ~/data/idf  
$ git clone --recursive https://github.com/espressif/esp-idf.git  
# recursive的使用，将submodule一起clone下来。  
