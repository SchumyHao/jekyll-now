---
published: true
category: homeassistant
tags:
  - homeassistant
  - rokid
  - nodjs
  - docker
  - rhass
---
## 背景

### rokid pebble

[Rokid](https://www.rokid.com/)是一家做智能音箱的企业. 现在有两代产品: **alien**和**pebble**.

在一个智能家居的群上我第一次听到了这个公司和当时的第一代产品**alien**. 当时在群上面更多的是比较她和**小智**音箱, 那个时候好像叮咚音箱还没怎么拓展市场, 国内也不是现在这样*智能音箱井喷*的时代. 所以她给我第一印象还是很深刻的, *唤醒准确*, *智能联动*. 不过第一代产品alien的外观和价格, 让我没有狠心剁手.

![]({{site.baseurl}}/images/homeassistant/bridge_to_rokid/pebble.jpg)

这是第二代产品**pebble**. 外观和价格都是我能够接受的范围之内, 所以我就果断买了.

### homeassistant

[homeassistant](https://home-assistant.io/)是我近半年时间一直在工作之余玩耍的对象, 是一个**本地化**, **开源**的智能家居平台, 可以自由的将智能家居设备加入到这个平台中, 然后用**统一**的方式控制, 实现**不同厂家设备的联动**. 同时这个平台将设备的数据可以保存到**本地**数据库中, 保证了数据的**安全性**, 也为以后**AI算法**的加入提供了数据支持.

## 目的

将homeassistant的设备加入到rokid pebble平台[homebase](https://rokid.github.io/rokid-homebase-docs/)上, 使用语音控制homeassistant平台上的设备

## 能做什么

- 2017.07.06
    - 能够将homeassistant中*switch*, *light*, *media_player*, *fan*这四类设备自动的加入homebase上.
    - 加入homebase中的设备可以进行*开关*的操作.
    - 自动识别homeassistant的friendly_name, 并设置为homebase中设备的tag.

## 开始之前

- 学习使用rokid的app打开[远程调试驱动](https://rokid.github.io/rokid-homebase-docs/tools/developer-driver.html). 现在还没有验证过使用**SSDP**进行自动检测, 所以最稳妥的方式是使用远程调试驱动手动添加设备.
- 会使用[docker](https://hub.docker.com/), 或者在宿主机上安装[nodejs8.x.x](https://nodejs.org/en/). 我本地是在**群晖**上使用docker的方式运行的, 且没有验证过别的方式. 但是原则上能够运行nodejs7.9.x的设备, 都能够胜任此项任务.

## 宿主机安装

### Docker安装

1.搜索并下载`schumyhao/homebase-hass-bridge-docker`docker image.

```
docker pull schumyhao/homebase-hass-bridge-docker
```


2.创建容器:

- 设置网络, 将9999端口映射到宿主机端口上, 或者直接设置网络为**host**模式.
- 设置ENV值**HASS_IP**为局域网内homeassistant的IP地址.
- 如果homeassistant的port**不是默认的8123**的话, 设置ENV值**HASS_PORT**为homeassistant的port.
- 如果homeassistant有设置**登录密码**的话, 设置ENV值**HASS_PASSWD**为你的登录密码.

![]({{site.baseurl}}/images/homeassistant/bridge_to_rokid/创建容器2.jpg)

![]({{site.baseurl}}/images/homeassistant/bridge_to_rokid/创建容器3.jpg)


### nodejs安装

1.确定自己的nodejs版本为7.9.x, 如果版本过低, 请升级nodejs版本
```
# node --version
v8.1.3
```

2.使用npm安装包**homebase-hass-bridge**.
```
npm install -g homebase-hass-bridge
```

3.设置环境变量, 设置homeassistant的IP地址, 登录密码, 如果homeassistant的port不是默认的8123的话, 同样要设置port
```
export HASS_IP=YOUR_HASS_IP
export HASS_PORT=YOUR_HASS_PORT
export HASS_PASSWD=YOUR_PASSWD
```

4.启动
```
rhass
```

## 手机App配置

1.打开app的[远程调试驱动](https://rokid.github.io/rokid-homebase-docs/tools/developer-driver.html).

![]({{site.baseurl}}/images/homeassistant/bridge_to_rokid/手机配置1.png)

2.在配置项中URL输入**上述运行rhass**机器的URL.端口是9999. 例如`http://192.168.1.1:9999`. 注意**http://**不能少

3.userId和userToken不需要填写.

![]({{site.baseurl}}/images/homeassistant/bridge_to_rokid/手机配置2.png)

## 结束

正常情况下, 上述操作完成后就可以在rokid的app中扫描到homeassistant中现在支持的设备了. 下一步可以自行对每个设备的tag进行定义.享受使用rokid控制家中设备的乐趣.


## 已知问题
- 步使用SSDP进行自动发现, 免去了手机端的配置.
