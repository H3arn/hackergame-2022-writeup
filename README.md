# hackergame-2022-trial

什么都不会的垃圾来玩玩看好了，说了只做签到，然后心痒痒就继续做吧



## 签到

先提交一次，然后会蹦出错误

注意URL变化，修改 `?result=????` 为 `?result=2022`

成功



## 偷家

### Rclone

在 `user/.config/rclone/rclone.conf` 有被 obscure 过的 pass

丢进[这个](https://go.dev/play/p/IcRYDip3PnE)里面的程序，改掉 `YOUR PSEUDO-ENCRYPTED PASSWORD HERE` 就能得到结果



## 旅行照片

### 照片分析

用 gwenview 或者 `jhead -v` 得到 exif 信息

Exif Version 2.31

Manufacturer Xiaomi

ISO Speed Ratings 84

Date and Time 2022/05/14 18:23:35

Flash No, complusory（强制关闭）



### 社工实践

放大照片可看见前方的圆形建筑有字 

ZOZOMARINE STADIUM 和 CHIBA LOTTE MARINE

搜索后可知与“千葉海洋球場”有关

从拍摄角度能推测拍摄者所在建筑相对于球场的方向，顺带对比图中的道路，估计其位于  APA度假飯店東京灣幕張  。

邮政编号得到 261-0021

已知该机器是小米的，搜索 gsmarena 对比各代机型（对不起我真的认不得国内那么多的机器）

猜测为 Redmi 9T, 屏幕分辨率得到 2340x1080

航班的话，观察照片中飞机的姿态，结合拍摄者所在位置，判断这架飞机大致是朝北方飞行。尚能目视，那么高度不算太高。且附近也没有其他飞机。

想回溯那么久以前的航班只能靠 flightradar 了。先骗个 7-day gold trial, 然后回溯一下照片拍摄时的空域情况。注意，exif 中还有时区信息 +0900, 之前的时间是当地时间，需要换算成 UTC. 调至 2022-05-14 09:23:35 UTC 不难得知航班为 NH683 (HND->HIJ)

然后记得把 gold subscription 取消掉。

