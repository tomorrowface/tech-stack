# csrf是什么
>CSRF（Cross-site request forgery），中文名称：跨站请求伪造，是一种广泛存在于网站中的安全漏洞，缩写为：CSRF/XSRF。
# 原理
CSRF攻击能劫持终端用户在已登录的Web站点上执行本意操作。简单的说，攻击者透过盗用用户身份悄悄发送一个请求，或执行某些恶意操作，CSRF的过程非常隐秘，受害人甚至无法察觉。
产生CSRF漏洞的原因主要有两点：一方面是开发者不够谨慎，编写的Web应用程序存在漏洞导致恶意利用：另一方面，是因为Web浏览器对于Cookie和HTTP身份验证的回话信息的处理存在一定的缺陷。

![csrf攻击流程](https://raw.githubusercontent.com/familyld/CSRF-Attack/master/graph/image7.png)

# 实践

现在有一个页面<http://A.com/me>，是一个个人中心页面，这里放了用户收藏的资源，

可以进行取消收藏的操作，对应的请求是<http://A.com/cancelfavor?eid=44ce2275eff9aef8951e0669>，如果攻击者利用页面漏洞注入了代码，使用户点击某处进入有恶意代码的页面<http://B.com>，在B页面发送这个取消收藏的请求就可以删除用户收藏的资源。

![mycenter](https://github.com/tomorrowface/tech-stack/blob/master/images/mycenter.png?raw=true)

以下是在B页面发送的请求，可以通过js或img的方式构造请求，为了能看到响应数据，采用了js构造的方法：![csrf-result](https://github.com/tomorrowface/tech-stack/blob/master/images/csrf-result2.png?raw=true)

看接口返回true，可知道成功删除，再看页面里真的少了一个资源。

![mycenter](https://github.com/tomorrowface/tech-stack/blob/master/images/csrf-result.png?raw=true)

以下这张图是成功的关键，这些cookie都是A站点的cookie，在B页面中请求A站点接口，会附带上A站点的cookie，cookie里包含用户重要的信息，服务器接收到这些信息后误以为是用户本人发送，执行取消收藏操作，结果用户收藏的资源就被恶意删除了。

![cookie](https://github.com/tomorrowface/tech-stack/blob/master/images/cookie.png?raw=true)

# 解决

对于网站内一些重要的请求，涉及修改、删除、保存操作的，都是要做csrf防御的。

可以从以下方面着手：

- referrer判断，服务器端通过referrer判断请求的来源，如果不是指定站点，则极有可能是csrf。
- token，一开始访问页面时，服务器生成一个token并存在浏览器cookie，以后的请求在header里加上这个token。
- 适合使用验证码的场景使用验证码，修改密码时提示输入原密码
