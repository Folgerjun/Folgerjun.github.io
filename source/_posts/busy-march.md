---
title: 忙碌三月（2019）
date: 2019-04-03 09:10:26
categories: [生活,杂侃]
tags: []
---

> 三月头到三月底算是在这公司至今最忙的一段时间了


从三月初去昆明之后回来又去临港来回三趟，之后又是世博文化中心项目急得要死，加班赶项目还花了两周末。

还不错，今年公司五月去海岛旅游，该加紧锻炼了！

---

## 昆明
说来惭愧，昆明这趟是我的“处女飞”，去的时候坐的是吉祥航空经济舱，体验是真特么差，便宜啊便宜。回来坐的是昆明航空经济舱，贵是贵了，体验也确实好很多，不知什么时候能体验下头等舱的魅力。

去了昆明长水机场，那确实是偏了点，坐了两个多小时才到了目的地。之后在昆明医科大学吃了顿饭，附近找了个宾馆，乖乖，全程微信沟通，就过来送了个发票。

![昆明路上拍摄的黄金龙头](https://raw.githubusercontent.com/Folgerjun/materials/master/blog/img/dragon.jpg)

你说这是“凤凰”？没差，龙凤呈祥！

去的时候那边温度比上海要高个 10℃，宾馆中都不带空调的，听说是四季如春啊，蓝天白云真不错。虽然没有大都市那么繁华，也很安谧，宜居。

晚上没事干搜了附近，去了家汗蒸店，结果一进去发现全是女的，尴尬。附近学校多嘛，女同学经常会来汗蒸放松放松，我就找了个角落闭目凝神静气。大概蒸了一个半小时，全身真的蒸透了，很舒服。

## 临港
临港创新科技城，规模不小设计造型独特，几年后估计又是一片天地。临港现在没有像市区那么繁华，街道上也几乎没什么人，打个车也没那么方便，不过看这发展的样子，未来也是个不错的地方，如果生活工作都在临港，那绝对幸福感不会低，看了下，均价三万一平吧。

去临港三月去了三趟，过去要 8 号线 -> 2 号线 -> 16 号线，将近三小时的路程。不过还好，每次都待了个几天不然一天来回估计脑袋够呛。

公司接的项目都这么高级，着实厉害。

原先我们都是现场用 485 串口服务器直连现场服务器进行数据采集，包括这个临港项目也是。之后准备用 485 转 GPRS 透传功能直接与公司外网 IP 暴露出去的端口进行数据传输，这样就不用在现场布置服务器了，直接在公司服务器部署采集即可。不过这样必然对现场工人们的技术要求提高些，不光要保证传感器的完好还要保证采集设备的完好还要过程中接线的正确性，由于现场技术人员不在就需要他们要有一定的问题排查能力。有利有弊吧，不过总体是方便了不少。

## 世博
上海世博文化中心项目最近要收尾了，公司干活人数有限，之前催死了。这项目还是用的现场部署服务器进行数据采集，不过用的是 4G 路由器，无固定外网 IP，所以现场能访问公司服务器，而外部访问不了，这就需要之前我总结过的[使用 ssh 反向隧道穿透 NAT 访问 Linux 内网主机](http://putop.top/2018/11/03/ssh-nat-ubuntu/)。然后还有一个问题就是数据需要同步到公司服务器，不然无法给业主实时查看。针对业务需求，通过一系列的资料查找，最终选择阿里开源的项目 otter，具体可看[记录 otter 数据同步](https://blog.csdn.net/ffj0721/article/details/88836302)。

加了几天班，终于也是告一段落了。

还没缓过来，松江云廊屋盖项目也要来了，今年真是项目都堆积一起了。

---

期待五月的海岛游（听说是塞班岛，俺土鳖，哪都成！）
