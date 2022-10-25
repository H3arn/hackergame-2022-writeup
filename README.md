# Hackergame 2022 7-Day Trial

我一个什么都不会的也来玩玩看好了。

说了只做签到，然后心痒痒，就想着继续做吧。

然后发现我就是个啥都不会的跳梁小丑，真是丢人现眼。



## 签到

先随便提交一次，然后会蹦出错误

注意 URL 变化，修改 `?result=????` 为 `?result=2022`

成功



## 举办猫咪问答喵谢谢喵

\*\*\*，真难找，我就找到三个。



2. 在 https://lug.ustc.edu.cn/wiki/lug/events/sfd/ 找到 陶柯宇 闪电演讲：《GNOME Wayland 使用体验：一个普通用户的视角》 slide 第 15 页，应该就是我们要找的程序。看起来似乎是一个影视后期处理软件，而且提到是 KDE 程序，那就前往 https://apps.kde.org/ 看看。好像有个叫 Kdenlive 的，一看长得一模一样，那就是了。



3. 直接翻译成英文搜，得到最后的大版本是 12.



4. 在 torvalds/linux.git 中的 commit message 里搜索 `CVE-2021-4034` 即可得知唯一符合的 commit hash 为 `dcd46d897adb70d63e025f175a00a89797d31a43`



## 偷家

解压，得到 `user` 家目录。然后用 vscode 打开文件夹，直接搜索 "flag". 



### Vscode

搜索结果中有一个 "DUGV.c", 注释第五行可以看到 flag. 



### Rclone

在 `user/.config/rclone/rclone.conf` 出现 "flag2", 其中有被 obscure 过的 pass.

丢进 [这个](https://go.dev/play/p/IcRYDip3PnE) 里面的程序，改掉 `YOUR PSEUDO-ENCRYPTED PASSWORD HERE` 就能得到结果。

防止原链接失效，拷贝程序源码如下：

```go
package main

import (
	"crypto/aes"
	"crypto/cipher"
	"crypto/rand"
	"encoding/base64"
	"errors"
	"fmt"
	"log"
)

// crypt internals
var (
	cryptKey = []byte{
		0x9c, 0x93, 0x5b, 0x48, 0x73, 0x0a, 0x55, 0x4d,
		0x6b, 0xfd, 0x7c, 0x63, 0xc8, 0x86, 0xa9, 0x2b,
		0xd3, 0x90, 0x19, 0x8e, 0xb8, 0x12, 0x8a, 0xfb,
		0xf4, 0xde, 0x16, 0x2b, 0x8b, 0x95, 0xf6, 0x38,
	}
	cryptBlock cipher.Block
	cryptRand  = rand.Reader
)

// crypt transforms in to out using iv under AES-CTR.
//
// in and out may be the same buffer.
//
// Note encryption and decryption are the same operation
func crypt(out, in, iv []byte) error {
	if cryptBlock == nil {
		var err error
		cryptBlock, err = aes.NewCipher(cryptKey)
		if err != nil {
			return err
		}
	}
	stream := cipher.NewCTR(cryptBlock, iv)
	stream.XORKeyStream(out, in)
	return nil
}

// Reveal an obscured value
func Reveal(x string) (string, error) {
	ciphertext, err := base64.RawURLEncoding.DecodeString(x)
	if err != nil {
		return "", fmt.Errorf("base64 decode failed when revealing password - is it obscured? %w", err)
	}
	if len(ciphertext) < aes.BlockSize {
		return "", errors.New("input too short when revealing password - is it obscured?")
	}
	buf := ciphertext[aes.BlockSize:]
	iv := ciphertext[:aes.BlockSize]
	if err := crypt(buf, buf, iv); err != nil {
		return "", fmt.Errorf("decrypt failed when revealing password - is it obscured? %w", err)
	}
	return string(buf), nil
}

// MustReveal reveals an obscured value, exiting with a fatal error if it failed
func MustReveal(x string) string {
	out, err := Reveal(x)
	if err != nil {
		log.Fatalf("Reveal failed: %v", err)
	}
	return out
}

func main() {
	fmt.Println(MustReveal("YOUR PSEUDO-ENCRYPTED PASSWORD HERE"))
}

```



## 我草 恶俗啊

### 照片分析

用 gwenview 或者 `jhead -v` 得到 exif 信息

Exif Version 2.31

Manufacturer Xiaomi

ISO Speed Ratings 84

Date and Time 2022/05/14 18:23:35

Flash No, complusory（强制关闭）



### 社工实践

放大照片，可看见前方的圆形建筑有字 ZOZOMARINE STADIUM 和 CHIBA LOTTE MARINE. 搜索后可知与“千葉海洋球場”有关。从拍摄角度能推测拍摄者所在建筑相对于球场的方向，顺带对比图中的道路，估计其位于  APA度假飯店東京灣幕張  ，邮政编号得到 261-0021

已知该机器是小米的，搜索 gsmarena 对比各代机型（对不起我真的认不得国内那么多的机器）猜测为 Redmi 9T, 屏幕分辨率得到 2340x1080

航班的话，观察照片中飞机的姿态，结合拍摄者所在位置和拍摄角度，判断这架飞机大致是朝北方飞行。尚能目视，那么高度不算太高。且附近也没有其他飞机。

想回溯那么久以前的航班只能靠 flightradar 了。先骗个 7-day gold trial, 然后回溯一下照片拍摄时的空域情况。注意，exif 中还有时区信息 +0900, 之前的时间是当地时间，需要换算成 UTC. 调至 2022-05-14 09:23:35 UTC 不难得知航班为 NH683 (HND -> HIJ)

拿到 flag, 把 gold subscription 取消掉。



## 查找 替换

直接正则替换 `|` 为 `]=a[`

难看，但是 py 语法糖，能跑



## 我是机器人

上来跳转是怎么回事？草

手速拉满，总算看到了里面有个 `<script>`, 还有一个 `<form>` 用来放三个计算题。换了 noscript 然后没啥用，1 秒的限制在后端也有。

用 curl 浏览器被拦住了，忘了还有 cookie. 我也不想折腾 shell script 了，那就干脆 py request 模拟。（连 request 都不会装，我紫菜吧）

继续开 noscript, 开 F12 去 network 里拿个 cookie 格式，然后随便提交一个答案，看一下 form 格式。

然后开始现学现卖 python 灵车。

三个计算题包装在 `<label>` 中，格式如下：

```html
<label for="captcha2">258033923847200268802989441771128447507+193408310580629933415510529078909027659 的结果是？</label>
```

用[正则模拟器](https://regex101.com)搞个表达式，套一下元素，拿到计算题。

```regex
\<label for\=\"captcha(\d)\"\>(\d+)\+(\d+)(.*)\<\/label\>
```

然后把得到的数据提取、转换格式、计算、输出、发送。

记得换掉 `SESSION_VALUE`

```python
# works on Python 3.10.8

import requests 
import re 

# set the URL
url = 'http://202.38.93.111:10047/xcaptcha'  
# set a valid cookie
cookies = {
    'DOKU_PREFS': 'list%23thumbs',
    'session': 'SESSION_VALUE'
} 

# establish a session
with requests.session() as session: 
    # get the challenge captcha
    r_text = session.get(url, cookies=cookies).text 
    # for debug purpose
    print(r_text,"\n\n\n") 

    # regular expression to match the HTML element. Group 2 and 3 are the target numbers. 
    regexp = re.compile("\<label for\=\"captcha(\d)\"\>(\d+)\+(\d+)(.*)\<\/label\>") 
    # find all matches
    match = re.findall(regexp, r_text) 
    # print(match, "\n")

    ans = {}
    for i in range(0,3):
        # convert data type and calculate the answer. 
        ans[i] = int(match[i][1]) + int(match[i][2]) 
        # print(int(match[i][0]), " ", ans[i], "\n")

    # fill the form data
    form_data = {
        'captcha1': ans[0],
        'captcha2': ans[1],
        'captcha3': ans[2]
    } 
    # for debug purpose
    print(form_data, "\n\n\n")

    # send the data to prove "I'm a robot"
    response = session.post(url, data=form_data) 
    # Here comes the flag
    print(response.text) 

```

搞定，成功证明自己是 bot. Flag 真搞。

![](http://202.38.93.111:10047/static/xcaptcha-success.png)



## 垃圾运维

~~revisions 关了却能看 diff 是怎么回事~~

简单学习了一下 dokuwiki 的官方 wiki, 发现除了 revision 还有 diff 这个好东西。

~~（换我直接把源站下线）~~

在 URL 末尾追加 `&do=diff` 即可打开 diff 页面，然后就能查看修改历史了。



## 线路板

大概是个 gerber file. 

用 gerbv 导入所有设计图，逐个查看后发现 `ebaz_sdr-F_Cu` 好像有类似 flag 的玩意印在上面，后面被一些东西挡住了。鼠标选中直接删掉就能看到 flag. 
