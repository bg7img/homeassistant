# 在树莓派上安装homeassistant
准备工作：
a、安装树莓派系统2020-02-13-raspbian-buster版
b、树莓派SD卡拓展容量，先输入df -h查看一下。后输入sudo raspi-config打开配置文件，选择第7个，再选择第1个，回车，搞定。

1、检查python3环境
在命令行中输入python3 --version，查看python3的版本，3.5.3以上就可以用。

2、检查pip3工具
在命令行中输入，查看pip3的版本，9.0.1以上就可以
3、#创建名为 homeassistant 的用户和密码
   #sudo useradd -rm homeassistant
   #sudo passwd homeassistant

4、创建安装文件夹
   首先我们先输入pwd命令查看当前目录是否在pi的根目录下。
   #cd /srv
   sudo mkdir homeassistant
   cd homeassistant
   #sudo chown homeassistant:homeassistant homeassistant

5、创建虚拟环境
#sudo su -s /bin/bash homeassistant
#cd /srv/homeassistant
#python3 -m venv .
#source bin/activate

6、设置国内镜像源
修改 /etc/apt/sources.list


sudo nano /etc/apt/sources.list

         
原文deb http://mirrordirector.raspbian.org/raspbian/ stretch main contrib non-free rp， 加#注释掉

         
加入如下阿里源:
deb http://mirrors.aliyun.com/raspbian/raspbian/ stretch main contrib non-free rpi

修改/etc/apt/sources.list.d/raspi.list

       
sudo nano /etc/apt/sources.list.d/raspi.list

      
原文deb http://archive.raspberrypi.org/debian/ stretch main ui， 加#

      
加入如下科大源：
deb http://mirrors.ustc.edu.cn/archive.raspberrypi.org/debian/ stretch main ui

在命令行中输入
sudo nano /etc/pip.conf
然后把下面的内容复制粘贴进去
[global]
trusted-host=mirrors.aliyun.com
index-url=https://mirrors.aliyun.com/pypi/simple/
用光标把每行命令宿进和第一行对齐
用ctrl+x组合键保存并退出。

更新下系统：
sudo apt-get update
sudo apt-get upgrade -y

由于默认源piwheels.org有用于树莓派的armv7的whl文件，因此无需编译。但是换源后就不同了，由于大多数库没有armv7的whl，所以必须自行安装。这里就有一个大坑。
先安装一下这两个库就可以解决
sudo apt-get install libffi-dev libssl-dev


一、安装Home assistant：

7、安装Home assistant
在命令行中输入sudo pip3 install homeassistant
进行安装

8、启动Home Assistant,补全所需的组件
在命令行一条一条的输入以下命令：
sudo pip3 install sqlalchemy
sudo pip3 install aiohttp_cors
sudo pip3 install home-assistant-frontend
sudo pip3 install mutagen
sudo pip3 install distro
sudo pip3 install fuzzywuzzy
sudo pip3 install netdisco
sudo pip3 install xmltodict
安装完后启动Home Assistant输入一下命令
hass

9、树莓派homeassistant加入自启动

A、 查看hass安装路径：which hass


pi@raspberrypi:~ $ which hass

/usr/local/bin/hass


B、 编辑文本：

sudo nano -w /etc/systemd/system/home-assistant@pi.service



[Unit]
Description=Home Assistant
After=network-online.target

[Service]
Type=simple
User=%i
ExecStart=/usr/local/bin/hass -c "/home/pi/.homeassistant"

[Install]
WantedBy=multi-user.target

C、 重新加载systemd


sudo systemctl --system daemon-reload
 



D、加入启动项：


sudo systemctl enable home-assistant@pi.service
 



E、启动服务：


sudo systemctl start home-assistant@pi.service



a、查看服务：


sudo systemctl status home-assistant@pi.service

 



b、若查看系统启动服务可以用：


systemctl list-units --type service

二、安装EMQX:
先查看树莓派的系统版本
lsb_release -a
1.转到 emqx.io 或 github，选择Raspbian版本，然后下载要安装的 EMQ X Edge 版本的 .zip 文件。

2.解压压缩包，将下面的路径更改为下载 EMQ X Edge 软件包的路径。
$ $ unzip emqx-edge-raspbian10-v4.1.1.zip
解压后把emqx的文件夹移动到PI的根目录下。
重新启动树莓派
3.启动 EMQ X Edge
$ cd emqx
$ ./bin/emqx start
emqx 4.1.1 is started successfully!
$ ./bin/emqx_ctl status
Node 'emqx@127.0.0.1' is started
emqx v4.1.1 is running

打开浏览器在地址栏输入：127.0.0.1:18083能访问EMQx服务器就OK了。
4.EMQ X 启动成功，如何使用请参考官方文档。
