# zhihu_login
模拟新版知乎登录，解决两种验证码。---小白学习
前文：本人为爬虫新手，此程序纯粹为了学习而用，欢迎大家一起讨论！
感谢：github address:https://github.com/zkqiang

原理
利用Fiddler抓取网页信息，找到登录API+获取验证码API，通过python urllib2、requests包模拟登录，获取cookie信息保存。

'https://www.zhihu.com/api/v3/oauth/sign_in' 登录

'https://www.zhihu.com/signup' 获取_xsrf_token

需要注意的是，新版知乎在登录之前，即在 www.zhihu.com 便建立cookie - _xsrf来防 Xsrf 跨站的 Token 认证，访问首页时从`Response Headers`的`Set-Cookie`字段中可以找到。获取到xsrftoken后，我们的目标就变为构建POST验证信息所需要的headers和Form-Data信息。

在Python 2.7 中使用get，无法获取全部的cookie，原因未知，所以在原作者的基础上修改获取方法。
由于Form-Data现在已经完全加密。

所以感谢这位大哥将以前的form-data截图发出来。如下：

可以看到，client_id、grant_type、source、lang为静态，只需添加timestamp、signature、username、password、captcha至字典中。

timestamp:13位时间戳 int(time() *1000)

signature: Hmac算法对几个固定值和时间戳进行加密。（可以全局搜索页面js文件查找）



captcha:验证码
此时问题转为如何获取验证码，知乎的验证码分为 普通验证码 和 点击反转验证

`captcha`验证码，是通过 GET 请求单独的 API 接口返回是否需要验证码（无论是否需要，都要请求一次），如果是 True 则需要再次 PUT 请求获取图片的 base64 编码。

本小白查了下，HTTP的提交方式还有这么多，我们了解的还是不多。
OPTIONS,GET,HEAD,POST,PUT,DELETE,TRACE,CONNECT



通过我们人工操作输入验证码，此时所需要的所有参数已经集齐，调用API，post data，获取返回数据，cookie存入文件。显示登录成功！


