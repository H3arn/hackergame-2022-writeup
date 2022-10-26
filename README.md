# 水银 2022 7-Day Trial

说了只做签到，然后心痒痒，就想着继续做吧。

然后发现我就是个啥都不会的跳梁小丑，真是丢人现眼。

内含大量吐槽，请见源码中注释掉的部分。



## 签到

先随便提交一次，然后会蹦出错误

注意 URL 变化，修改 `?result=????` 为 `?result=2022`

成功



## 举办猫咪问答喵谢谢喵

\*\*\*，真难找，我~~只找到三个~~ upd：六个答案已经都搞到了。

1. 搜了一下 Google 上的结果，第一次出现这个战队是在 https://cybersec.ustc.edu.cn/2019/0624/c15751a387753/page.htm , 但这并没有什么用。然后估计一下也不可能在 2012 年以前成立，那就和第六问一样，把答案枚举出来就行~~，反正最多才 96 种可能，连第六问都不如~~。

```diff
-days = [0,31,28,31,20,31,30,31,31,30,31,30,31] # to make datetime lib happy
-year = 2003
-while year < 2004:
+day = 1 # to make datetime.datetime() happy
+year = 2012
+while year < 2021:

-        day = 1
-        while day <= days[month]:

-                    'q1': "",
+                    'q1': date.strftime("%Y-%m"), # yyyy-mm

-                    'q6': date.strftime("%Y-%m-%d") # yyyy-mm-dd
+                    'q6': "" 

-            day = day + 1

```



2. 在 https://lug.ustc.edu.cn/wiki/lug/events/sfd/ 找到 陶柯宇 闪电演讲：《GNOME Wayland 使用体验：一个普通用户的视角》 slide 第 15 页，应该就是我们要找的程序。看起来似乎是一个影视后期处理软件，而且提到是 KDE 程序，那就前往 https://apps.kde.org/ 看看。好像有个叫 Kdenlive 的，一看长得一模一样，那就是了。



3. 直接翻译成英文搜，得到最后的大版本是 12.



4. 在 https://github.com/torvalds/linux.git 中的 commit message 里搜索 `CVE-2021-4034` 即可得知唯一符合的 commit hash 为 `dcd46d897adb70d63e025f175a00a89797d31a43`



5. 提示说是 1996 年注册的域名，而且有且只有六个字母，就想到 Z 佬的 GPG uid 里有个 sdf.org 的号，结合当时和群友聊到这个站的历史和会员的难以获取，就瞎猜了一个 sdf.org 居然对了



6. 浅浅找了下妮可网络通的内容也只有 https://netfee.ustc.edu.cn/faq/index.html#fee , 然后在 wayback machine 上也没有找到 2012 年以前的记录。心一冷，直接谷歌搜 “网络通 ustc” 然后把时间调到 2009-01-01 和 2011-01-01 之间。好家伙，给我碰上了 https://www.ustc.edu.cn/info/1057/4931.htm . 其中提到的 “网字〔2003〕1号《关于实行新的网络费用分担办法的通知》” 没法在公网搜索引擎上找到了，似乎。

   想到大狐狸苏卡卡酱以前打 hg 的绝招，我也来试试枚举吧，拿着做 Xcaptcha 时候写的机器人改两笔就行了。从 2003-01-01 到 2003-12-31 总共才 365 个可能，这种数据规模还不到市面常见 CC 攻击的零头。

```python
# works on Python 3.10.8

import requests 
import re 
import time
import datetime

# set the URL
url = 'http://202.38.93.111:10002'  
# set a valid cookie
cookies = {
    'DOKU_PREFS': 'list%23thumbs',
    'session': 'SESSION_VALUE'
} 

# only try one targeted question, as is seen in form_data
regexp = re.compile("(你只答对了 1 题喵，不够喵！)") 

days = [0,31,28,31,20,31,30,31,31,30,31,30,31] # to make datetime lib happy
year = 2003
while year < 2004:
    month = 1
    while month < 13:
        day = 1
        while day <= days[month]:
            # establish a session
            time.sleep(1)
            with requests.session() as session: 
                date = datetime.datetime(year, month, day)
                # fill the form data
                form_data = {
                    'q1': "",
                    'q2': "",
                    'q3': "",
                    'q4': "",
                    'q5': "",
                    'q6': date.strftime("%Y-%m-%d") # yyyy-mm-dd
                } 
                # for debug purpose
                print(form_data)

                # send the data 
                response = session.post(url, data=form_data, cookies=cookies) 
                # Error handler
                if (response.status_code!=200):
                    print("HTTP ", response.status_code)
                    exit(1)
                # Here comes the flag
                match = re.findall(regexp, response.text)
                if len(match):
                    print(match)
                    exit(0) # exit on success
            day = day + 1
        month = month + 1
    year = year + 1

```



## 偷家

解压，得到 `user` 家目录。然后用 vscode 打开文件夹，直接搜索 "flag". 

<!--其实是找了大半天才找到 rclone 那个，然后才想到用 vscode（-->

<!--真是找死我了（吐血-->



### Vscode

搜索结果中有一个 "DUGV.c", 注释第五行可以看到 flag. 



### Rclone

在 `user/.config/rclone/rclone.conf` 出现 "flag2", 其中有被 obscure 过的 pass.

丢进 [这个](https://go.dev/play/p/IcRYDip3PnE) 里面的程序，改掉 `YOUR PSEUDO-ENCRYPTED PASSWORD HERE` 就能得到结果。

考虑到原链接随时可能失效，拷贝程序源码如下：

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



## 真甜

<!--发现自己不会用正则表达式去替换，笑死，那就-->直接替换 `|` 为 `]=a[`

难看，但这 py 语法葡萄糖真甜~~，除杂都省了，直接饮用~~。



## 我是机器人

上来就跳转是怎么回事？草

~~手速拉满，~~换了 noscript 终于不跳了，总算看到了里面有个一秒就提交的 `<script>`, 还有一个 `<form>` 用来放三个计算题。但还是没啥用，1 秒的限制在后端也有。

<!--用 curl ~~浏览器~~被拦住了，忘了还有 cookie. 也不想折腾 shell script 了，-->那就干脆 py request 模拟~~，这样够 bot 了吧（~~<!--（咱安装 request 都被 pip 毒打，还是早点紫菜吧）-->

<!--继续开 noscript, 开 F12 去 network 里拿个 cookie 格式，然后随便提交一个答案，看一下 form data 格式。-->

然后我一个根本不会写代码的，开始搭建 python 危房。

<!--~~这里窝借用一下[某位 PGP 算号神人的骚话](https://www.douban.com/note/763978955)：~~ -->

<!--~~ > JavaScript 就这个好，给点星星之火就可以燎原。不光野，还浪，容易上手，以至于不管是谁都能上去霍霍两下 ~~ -->

<!--~~对于 py 大概也是如此（（~~ -->

<!--尝试 py 的时候才发现原来用无头浏览器（比如 py requests / curl）就不怕 script 了。flag 里也这么说（（-->

三个计算题包装在 `<label>` 中，格式如下：

```html
<label for="captcha2">258033923847200268802989441771128447507+193408310580629933415510529078909027659 的结果是？</label>
```

掏出[正则模拟器](https://regex101.com)随便糊个表达式，用 Match Group 把那俩数字拎出来。

```regex
\<label for\=\"captcha(\d)\"\>(\d+)\+(\d+)(.*)\<\/label\>
```

然后转换格式、计算、送走（

访问题目时需要使用 cookie, 也即填写此处 `session` 的值。用浏览器开一下题目，F12 就能拿到。

完整代码如下：

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
    # find all matches (actually 3)
    match = re.findall(regexp, r_text) 
    # print(match, "\n")

    # define a list to store answers
    ans = {}
    for i in range(0,3):
        # (forcibly) convert data type and calculate the answer. 
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

搞定，成功证明自己是 bot. 

~~果然 bot 的活还是得交给 bot, 想到最近的 AI 画画，心情复杂（（~~

![](http://202.38.93.111:10047/static/xcaptcha-success.png)



## 我草 恶俗啊

### 照片分析

用 gwenview 或者 `jhead -v` 得到 exif 信息

```
Exif Version:       2.31
Manufacturer:       Xiaomi
ISO Speed Ratings:  84
Date and Time:      2022/05/14 18:23:35
Flash:              No, complusory (=> OFF)
```



### 社工实践

放大照片，可看见前方的圆形建筑有字 ZOZOMARINE STADIUM 和 CHIBA LOTTE MARINE. 搜索后可知与“千葉海洋球場”有关。从拍摄角度能推测拍摄者所在建筑相对于球场的方向，顺带对比图中的道路，估计其位于  APA度假飯店東京灣幕張  ，邮政编号 261-0021.

已知该机器是小米的，搜索 gsmarena 对比各代机型<!--（对不起我真的认不得那么多的机器）-->猜测为 Redmi 9T, 屏幕分辨率 2340x1080.

航班的话，观察照片中飞机的姿态，结合拍摄者所在位置和拍摄角度，判断这架飞机大致是朝北方飞行。尚能目视，那么高度不算太高。且附近也没有其他飞机。

想回溯那么久以前的航班只能靠 flightradar24 了。先骗个 7-day gold trial, 然后回溯一下照片拍摄时的空域情况。注意，exif 中还有时区信息 +0900, 之前的时间是当地时间，需要换算成 UTC. 调至 2022-05-14 09:23:35 UTC 不难得知航班为 NH683 (HND -> HIJ)

拿到 flag, 别忘了把 gold subscription 取消掉。



## 胶衣 PLAY

### 纯文本

咕咕搜索 "Latex include other file in the file system"

然后在[这里](https://tex.stackexchange.com/questions/246/when-should-i-use-input-vs-include)看到了大佬们的指点。于是：

```latex
\input{/flag1}
```

然后输出了第一个 flag. 记得补上花括号。



### 特殊字符混入

混进去的两个都是 escape char, 一时有点头大。

搜了 regex, 没有；搜到 verbatim, 不行。

脚本里面有 `-no-shell-escape` 没法干坏事。真麻了。

在搜 escape char 的时候，[摸到](https://tex.stackexchange.com/questions/543565/setting-the-backslashs-catcode) `\catcode` 这个东西。心灰意冷之下试了试居然能用。

又在学习胶衣基本语法的时候看到了 [\\\\ 的用法](https://www.overleaf.com/learn/latex/Learn_LaTeX_in_30_minutes#Paragraphs_and_new_lines)。拿出来试了下还真能用，那就可以在 form data 里面塞入多行内容了。

那就好办了。

把 `#` 和 `_` 的 catcode 改成 12 就行了。

```latex
\catcode`\_=12 \\ \catcode`\#=12 \\ \input{/flag2} 
```

补上花括号，搞定。



## 垃圾运维

<!--~~revisions 关了却能看 diff 是怎么回事~~-->

又没什么登录的线索，只好简单学习了一下 dokuwiki 的官方 wiki, 发现除了 revision 还有 [diff](https://www.dokuwiki.org/diff) 这个好东西。

<!--（什么垃圾运维，换我直接把源站下线）-->

在 URL 末尾追加 `&do=diff` 即可打开 diff 页面，然后就能查看修改历史了。



## 工程1.psd

大概是个 gerber file. 

用 gerbv 导入所有设计图，逐个查看后发现 `ebaz_sdr-F_Cu` 好像有类似 flag 的玩意印在上面，后面被一些东西挡住了。鼠标选中直接删掉就能看到 flag. 



## 浇窝 WebGL 好不好嘛

折腾了半天也不是很懂。<!--还把我的电脑跑崩了好几次，这就是现代科技吗（-->

网页结构很简单，<!--很适合在本地跑，我也确实是这样做的。-->咱猜是要修改那四个 js. 

WebGL 确实也看不大懂，搜了下里面的函数名字，大概是用光线追踪写的玩具。

但里面最让我感到奇怪的就是五个 SDF. 前四个那么大那么沉，而最后那个又那么短，结合图像中固定的 flag 字样，还有那个模糊的后半段，很难不联想到前四个 SDF 就是把字母画出来，最后那个负责画一个矩形挡住 flag. 

看了下只有 `sceneSDF` 中调用过这五个函数。

直接把其中的 t5 被赋值/调用的地方改成随便一个浮点数（比如 1e1 之类的常量，或者其他 float 变量）就行了。

重新渲染，就能看到 flag 了。

