---
title: Cookie和Session、SessionID
tags: 计算机网络
---

[toc]

## 一、Cookie的定义

> ​    指某些网站为了辨别用户身份、进行session跟踪而存储在用户本地终端上的数据（通常经过加密）。也就是说如果知道一个用户的Cookie，并且在Cookie有效的时间内，就可以利用Cookie以这个用户的身份登录这个网站。

> **会话cookie和持久cookie的区别？**
>
> ​    如果不设置过期时间，则表示这个cookie生命周期为浏览器会话期间，只要关闭浏览器窗口，cookie就消失了。这种生命期为浏览会话期的cookie被称为会话cookie。会话cookie一般不保存在硬盘上而是保存在内存里。
>  　　如果设置了过期时间，浏览器就会把cookie保存到硬盘上，关闭后再次打开浏览器，这些cookie依然有效直到超过设定的过期时间。
>  　　存储在硬盘上的cookie可以在不同的浏览器进程间共享，比如两个IE窗口。而对于保存在内存的cookie，不同的浏览器有不同的处理方式。

## 二、session

**1、session在不同环境下的不同含义**

> ​     session，中文经常翻译为会话，其本来的含义是指有始有终的一系列动作/消息，比如打电话是从拿起电话拨号到挂断电话这中间的一系列过程可以称之为一个session。然而当session一词与网络协议相关联时，它又往往隐含了“面向连接”和/或“保持状态”这样两个含义。
>
> ​    session在Web开发环境下的语义又有了新的扩展，它的含义是指一类用来在客户端与服务器端之间保持状态的解决方案。有时候Session也用来指这种解决方案的存储结构。

**2、session的机制（下面会细说）**

> ​     session机制是一种服务器端的机制，服务器使用一种类似于散列表的结构(也可能就是使用散列表)来保存信息。但程序需要为某个客户端的请求创建一个session的时候，服务器首先检查这个客户端的请求里是否包含了一个session标识 —— 称为session id。
>
> ​      如果已经包含一个session id则说明以前已经为此客户创建过session，服务器就按照session id把这个session检索出来使用(如果检索不到，可能会新建一个，这种情况可能出现在服务端已经删除了该用户对应的session对象，但用户人为地在请求的URL后面附加上一个JSESSION的参数)。
>
> ​    如果客户请求不包含session id，则为此客户创建一个session并且生成一个与此session相关联的session id，这个session id将在本次响应中返回给客户端保存。

**3、cookie机制和session机制的区别**

> cookie机制采用的是在客户端保持状态的方案，而session机制采用的是在服务器端保持状态的方案。 

## 三、Cookie和Session、SessionID的关系

> ​    sessionid 是一个会话的 key，浏览器第一次访问服务器会在服务器端生成一个 session，有一个  sessionID 和它对应，并返回给浏览器，这个 sessionID 会被保存在浏览器的会话 cookie 中。tomcat 生成的  sessionID 叫做 jsessionID。

> ​    sessionID 在访问 tomcat 服务器 HttpServletRequest 的  getSession(true) 的时候创建，tomcat 的 ManagerBase 类提供创建sessionID  的方法：随机数+时间+jvmid。Tomcat 的 StandardManager 类将 session 存储在内存中，也可以持久化到  file，数据库，memcache，redis等。

> ​    客户端只保存 sessionID 到 cookie 中，而不会保存 session。session 不会因为浏览器的关闭而删除，只能通过程序调用 HttpSession.invalidate() 或超时才能销毁。

> **session 的 id 是从哪里来的，sessionID 是如何使用的？**
>
> ​    当客户端第一次请求 session 对象时候，服务器会为客户端创建一个 session，并将通过特殊算法算出一个 session 的 ID，用来标识该 session 对象。

> **session 存放在哪里？**
>
> ​    服务器端的内存中。不过 session 可以通过特殊的方式做持久化管理（memcache，redis）。

> **session在下列情况下被删除：**
>
> 1. 程序调用HttpSession.invalidate()
> 2. 距离上一次收到客户端发送的session id时间间隔超过了session的最大有效时间
> 3. 服务器进程被停止
>
> 注意：
>
> - 客户端只保存 sessionID 到 cookie 中，而不会保存 session。
> - 关闭浏览器只会使存储在客户端浏览器内存中的session cookie失效，不会使服务器端的session对象失效，同样也不会使已经保存到硬盘上的持久化cookie消失。

> **session cookie和session对象的生命周期是一样的吗？**
>
>    当用户关闭了浏览器虽然session cookie已经消失，但session对象仍然保存在服务器端。

> **Session 在何时创建呢？**
>
> ​     一个常见的错误是以为session在有客户端访问时就被创建，然而事实是直到某server端程序(如Servlet)调用HttpServletRequest.getSession(true)这样的语句时才会被创建。      
>
> ​    在创建了Session的同时，服务器会为该Session生成唯一的Session  id，而这个Session id在随后的请求中会被用来重新获得已经创建的Session；在Session被创建之后，就可以调用Session相关的方法往Session中增加内容了，而这些内容只会保存在服务器中，发到客户端的只有Session id；当客户端再次发送请求的时候，会将这个Session id带上，服务器接受到请求之后就会依据Session id找到相应的Session，从而再次使用之。

> **打开两个浏览器窗口访问应用程序会使用同一个session还是不同的session？**
>
> ​    通常session cookie是不能跨窗口使用的，当你新开了一个浏览器窗口进入相同页面时，系统会赋予你一个新的session id，这样我们信息共享的目的就达不到了。
>
> ​     此时我们可以先把session id保存在persistent cookie中(通过设置session的最大有效时间)，然后在新窗口中读出来，就可以得到上一个窗口的session id了，这样通过session cookie和persistent cookie的结合我们就可以实现了跨窗口的会话跟踪。 

## 四、客户端用cookie保存了sessionID时

> 　　客户端用cookie保存了sessionID，当我们请求服务器的时候，会把这个sessionID一起发给服务器，服务器会到内存中搜索对应的sessionID，如果找到了对应的  sessionID，说明我们处于登录状态，有相应的权限；如果没有找到对应的sessionID，这说明：要么是我们把浏览器关掉了（后面会说明为什么），要么session超时了（没有请求服务器超过20分钟），session被服务器清除了，则服务器会给你分配一个新的sessionID。你得重新登录并把这个新的sessionID保存在cookie中。

> ​     在没有把浏览器关掉的时候（这个时候假如已经把sessionID保存在cookie中了）这个sessionID会一直保存在浏览器中，每次请求的时候都会把这个sessionID提交到服务器，所以服务器认为我们是登录的；当然，如果太长时间没有请求服务器，服务器会认为我们已经所以把浏览器关掉了，这个时候服务器会把该sessionID从内存中清除掉，这个时候如果我们再去请求服务器，sessionID已经不存在了，所以服务器并没有在内存中找到对应的 sessionID，所以会再产生一个新的sessionID，这个时候一般我们又要再登录一次。 

## 五、客户端没有用cookie保存sessionID时

> 　　这个时候如果我们请求服务器，因为没有提交sessionID上来，服务器会认为你是一个全新的请求，服务器会给你分配一个新的sessionID，这就是 为什么我们每次打开一个新的浏览器的时候（无论之前我们有没有登录过）都会产生一个新的sessionID（或者是会让我们重新登录）。

> ​     当我们一旦把浏览器关掉后，再打开浏览器再请求该页面，它会让我们登录，这是为什么？我们明明已经登录了，而且还没有超时，sessionID肯定还在服务器上的，为什么现在我们又要再一次登录呢？这是因为我们关掉浏览再请求的时候，我们提交的信息没有把刚才的sessionID一起提交到服务器，所以服务器不知道我们是同一个人，所以这时服务器又为我们分配一个新的sessionID，打个比方：浏览器就好像一个要去银行开户的人，而服务器就好比银行，  这个要去银行开户的人这个时候显然没有帐号（sessionID），所以到银行后，银行工作人员问有没有帐号，他说没有，这个时候银行就会为他开通一个帐号（sessionID）。所以可以这么说，每次打开一个新的浏览器去请求的一个页面的时候，服务器都会认为，这是一个新的请求，他为你分配一个新的sessionID。