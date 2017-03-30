.. playdocinjavazh documentation master file, created by
   sphinx-quickstart on Thu May  5 23:05:51 2016.
   You can adapt this file completely to your liking, but it should at least
   contain the root ``toctree`` directive.

***********************************
Play framework 2.5.x教程（For Java)
***********************************

* :ref:`genindex`

*********
Play 教程
*********

Play框架的文档说明了其可用的特性, 但是文档并不会说明如何从头到尾创建一个应用。所以我们就有了这个教程。

教程对于展示一个简单的应用是如何工作的十分有用，特别是当涉及到和其它系统比如数据库和其它的JavaScript框架一起工作时。

****************
Activator 模板
****************

许多Activator模板带有易懂的教程来指引你使用特定的被这个模板所使用的技术。

一个全面的列表可以在Activator Web Interface被找到。

此外，模板也被发布在Lightbend网站上，一个关于Play的包含有官方和社区的贡献的全面的列表能在这里被找到。

Lightbend同样维护了大量关于Activator的模板。你可以运行命令activator ui然后在浏览器里打开http://127.0.0.1:8888/并点击Tutorial（教程）选项卡，这样你就能看到一系列的内置教程了。

*******************
一些英文的视频教程
*******************

嗯，这一段文字原文是没有的，不过原文这里提供了一些英文的视频教程，假如你能够很好地听英语的话（能听看不懂哈哈哈）你就去官网https://www.playframework.com/documentation/2.5.x/Tutorials\*play-tutorials看吧。

*********************
一些英文的第三方教程
*********************

嗯，这一段文字原文也是没有的，一些英文书什么的，你们要是愿意看英文的第三方的还会来看我这蹩脚的翻译了么哈哈哈。官网上也有，就是上面那个链接，想看的点进去看吧哈哈哈。

****
术语
****

呃，这一段文字原文还是没有的哈哈哈，我比较懒，一些术语翻译过来的中文为了避免歧义，我都会单独放到这一节来

.. toctree::
   :maxdepth: 1

Action行为

Controller控制器

Result（直译为结果，不妥，故不翻译）

HT（HyperText）超文本

HTTP（超文本传输协议，不翻译）

HTML超文本标记语言

Session（不翻译，做WEB的人应该懂的）

Flash scope（不翻译，感觉难翻译）

Body块（儿）

WEB（这要翻译？）

Socket(不翻译，套接字)

SQL（不翻译，结构化查询语言）

ORM（不翻译，对象关系映射）

*****************
对Java的主要概念
*****************

着看起来像是官方的目录呢，由于我的翻译会有一个自动生成的目录，我就把官方的目录也单独翻译出来吧！

========
HTTP编程
========

行为、控制器、Result

HTTP路由

操作HTTP响应

Session和Flash scope（呵呵呵这个真不知道怎么翻似乎也没必要翻）

块解析器

行为的功能

HTTP请求处理器（HTTP Request Handler）、行为创建器（ActionCreator）

============
异步HTTP编程
============

处理异步结果

响应HTTP流

Comet

WebSockets

=============
Twirl模板引擎
=============

模板语法

通用案例

定制语法

===============
表单提交和验证
===============

定义表单

使用表单模板助手

防止跨站攻击

========
与XX工作
========

Json

XML

处理上传请求

=============
访问SQL数据库
=============

配置并使用JDBC

与JPA整合

使用Ebean ORM

========
使用缓存
========

使用缓存

===========
请求WEB服务
===========

Play WEB服务 API

连接到OpenID服务

访问使用OAuth保护的服务

=========
整合AKKA
=========

整合AKKA

======
国际化
======

国际化

========
依赖注入
========

使用Guice

========
应用部署
========

应用部署

基本行为（Essential Actions）

HTTP过滤

异常处理

全局设置

=============
调试你的应用
=============

编写测试

编写函数测试

使用Guice测试

数据库测试

WEB服务器客户端测试

=====
日志
=====

记录日志

********
HTTP编程
********

======================
行为、控制器、Result
======================

-----------
什么是行为
-----------

大部分Play接收到的请求都会交给Action处理

一个Action基本上就是一个处理请求的参数并且产生一个回应发送给客户端的Java方法。

::

  public Result index() {
    return ok("Got request " + request() + "!");
  }


一个Action返回一个 ``play.mvc.Result`` 类型的变量，表示被发送到客户端的HTTP响应。在这个例子里 ``ok`` 方法构造了一个 **200 OK** 回应包含了一个 **text\/plain** 回应块。

------
控制器
------

一个控制器是一个 ``play.mvc.Controller`` 的子类，它将一系列action组合起来了。

::

  package controllers;

  import play.*;
  import play.mvc.*;

  public class Application extends Controller {

    public Result index() {
        return ok("It works!");
    }

  }

上面这是最简的定义一个没有参数的Action方法返回一个 ``Result`` 变量的一个语法：

Action也可以拥有一些（个）参数

::

  public Result index(String name) {
    return ok("Hello " + name);
  }

这些参数将会被 ``Router`` 解析并被请求的URL里的变量所解析。这些参数能够被从URL地址或者URL字符串中解析。

-------
Results
-------

让我们从一些简单的Results开始：一个HTTP result有状态码，一系列HTTP头，以及一个正文发送到客户端。

这些results将会被定义为 ``play.mvc.Result`` ，并且 ``play.mvc.Result`` 类提供一系列小助手来产生一个标准的HTTP result，比如上面我们用到的 ``ok`` 方法：

::

  public Result index() {
    return ok("Hello world!");
  }

这里是许多创建各种各样的result的例子：

::

  Result ok = ok("Hello world!");
  Result notFound = notFound();
  Result pageNotFound = notFound("<h1>Page not found</h1>").as("text/html");
  Result badRequest = badRequest(views.html.form.render(formWithErrors));
  Result oops = internalServerError("Oops");
  Result anyStatus = status(488, "Strange response type");

大部分小助手能够在 ``play.mvc.Results`` 这个类中找到。

--------------------------
重定向也是简单的一种result
--------------------------

重定向浏览器到另一个URL只是另一种result。然而，这些result并没有返回正文。

这里是一些能够创建重定向result的小助手：

::

  public Result index() {
    return redirect("/user/home");
  }

这个重定向方法默认使用 ``303 SEE_OTHER`` 响应类型，但是你能够指定一个更特别的状态码进行回应。

::

  public Result index() {
    return temporaryRedirect("/user/home");
  }

=========
HTTP路由
=========

-------------
内置HTTP路由
-------------

路由是将每一个HTTP请求转发到一个行为请求（一个控制器类中的公有方法）的组成部分。

HTTP请求被看做MVC模型中的一个事件。这个事件包括两种主要的信息：

 请求部分（比如/clients/1542, /photos/list），包括查询字符串。

 HTTP请求方式（GET, POST, …）。

路由被定义在conf/routes文件里，它会被编译。这意味着你将会直接在浏览器里就能看见路由错误。

---------
依赖注入
---------

Play框架支持生成两种路由，一种是一个依赖注入路由，另一种则是静态路由。默认的路由就是依赖注入路由，因为我们想提醒你在控制器中使用依赖注入，所以我们在Play框架的Activator模板中有着案例。如果你需要使用静态控制器，你可以在build.sbt文件中添加以下的设置条目以选择静态路由生成器。

::

  routesGenerator := StaticRoutesGenerator
 
在Play框架的文档中的代码例子里假定假定你使用依赖注入路由生成器。如果你不使用静态路由，你就必须十分麻烦地改写代码为了你的静态路由生成器，或者通过在控制器调用前加上一个@符号，亦或是通过将每个你的行为方法都声明为静态。

------------
路由文件格式
------------

**conf\/routes** 是路由使用的配置文件。这个文件列出了所有的被应用程序使用的路由。每一个路由都包含一个HTTP请求方法以及URL格式接着一个要被调用的行为方法。
让我们来看看路由定义的样子吧：
 
::

  GET   /clients/:id          controllers.Clients.show(id: Long)
 
注意：在行为调用中，参数类型必须写在参数名之后，这点和Scala的语法比较像，而跟Java不太一样。
每一条路由都是从HTTP请求方法开始，并被URL格式所跟着。最后一个元素是路由的调用定义。
你也可以在路由文件中使用#号进行注释。

------------
HTTP请求方法
------------

HTTP请求方法可以是任何的被HTTP所支持的请求方法（GET, PATCH, POST, PUT, DELETE, HEAD, OPTIONS）。

------------
URL格式
------------

URL格式被定义在路由的请求部分。一些部分是可以被动态替换的。

------------
静态部分
------------

比如想要在请求中精确地匹配 ``GET /clients/all`` ，你可以定义这样一个路由：
 
::

  GET   /clients/all          controllers.Clients.list()
 
------------
动态部分
------------

如果你想要定义一个能够接受客户端id的路由，你需要添加一个动态部分：
 
::

  GET   /clients/:id          controllers.Clients.show(id: Long)
 
注意：一个URL格式最多只能含有一个动态部分
对于动态部分默认的匹配策略是一个用正则表达式[^/]+定义的，意味着任何被定义成类似:id的部分将会匹配到一个URL段。

------------------------
包含一些/的动态部分
------------------------

如果你需要一个包含不止一个部分的URI部分，被正斜杠所隔开，你可以定义一个使用*符号的部分，它使用.*正则表达式匹配：
 
::

  GET   /files/*name          controllers.Application.download(name)
 
这里，一些类似于 ``GET /files/images/logo.png`` 的请求，name动态部分会被 **images/logo.png** 所填充。

根据自定义表达式所定义的动态部分

你可以为动态部分定义你自己的正则表达式，使用 ``$id<regex>`` 的格式：
 
::

  GET   /items/$id<[0-9]+>    controllers.Items.show(id: Long)
 
------------------------
调用行为构建器方法
------------------------

路由定义的最后一部分是调用。这一部分必须定义一个有效的行为方法调用。
如果这个方法没有定义任何参数，只需要给出方法的全名即可：
 
::

  GET   /                     controllers.Application.homePage()
 
如果这个行为方法定义了一些参数，相应的参数会被从URI中找到，或是在URI部分里，亦或是在请求字符串之中。
 
::
 
  # Extract the page parameter from the path.
  # i.e. http://myserver.com/index
  GET   /:page                controllers.Application.show(page)
  # Extract the page parameter from the query string.
  # i.e. http://myserver.com/?page=index
  GET   /                     controllers.Application.show(page)
 
这里是对应show方法的定义，位于controllers.Application控制器中：

::

  public Result show(String page) {
      String content = Page.getContentOf(page);
      response().setContentType("text/html");
      return ok(content);
  }
 
------------
参数类型
------------

对于字符串类型的参数，参数类型是可选的。如果你想要Play框架转换参数为特殊Scala类型，你可以添加一个明确的类型：

::

  GET   /clients/:id          controllers.Clients.show(id: Long)
 
然后在行为方法的参数中使用同样的类型：

::

  public Result show(Long id) {
      Client client = clientService.findById(id);
      return ok(views.html.Client.show(client));
  }
 
注意：参数类型需要使用下标的时候。使用泛型的时候使用[]符号而不是和Java一样的<>。例如，List[String]是和Java类型List<String>一样的。

------------
确定值的参数
------------

有时候你希望使用一个有着确定值的参数：
 
::

  # Extract the page parameter from the path, or fix the value for /
  GET   /                     controllers.Application.show(page = "home")
  GET   /:page                controllers.Application.show(page)
 
------------------------
有默认值的参数
------------------------

有时候你希望提供一个默认的变量应对没有在请求中找到值的情况：
 
::

  # Pagination links, like /clients?page=3
  GET   /clients              controllers.Clients.list(page: Int ?= 1)
 
------------
可选参数
------------

你可以声明一个可选参数在并不一定需要参数的情况下：
 
::

  # The version parameter is optional. E.g. /api/list-all?version=3.0
  GET   /api/list-all         controllers.Api.list(version ?= null)
 
------------
路由优先级
------------

许多路由可能会匹配到同一个请求。如果这里出现了冲突，第一个路由（按声明顺序）会被使用。

------------
重定向路由
------------

路由可以被用于在Java调用中生成一个URL。这使得集中你所有的URI格式在一个很简单的配置文件中成为可能，这样你就能够更有自信地重构你的应用。

对于路由文件中用到的每一个控制器，路由将会在routes创建一个重定向控制器，拥有同样的行为方法，具有同样的签名，但是返回一个 ``play.mvc.Call`` 而不是 ``play.mvc.Result`` 。

``play.mvc.Call`` 定义了一个HTTP请求，并提供了HTTP请求方法和URI。

比如你可以创建一个这样的控制器：
 
::

  package controllers;
  import play.*;
  import play.mvc.*;
  public class Application extends Controller {
      public Result hello(String name) {
          return ok("Hello " + name + "!");
      }
  }
 
然后，你就可以在conf/routes文件里列出它：

::
  
  # Hello action
  GET   /hello/:name          controllers.Application.hello(name)
 
你可以重定向URL到hello方法，通过使用 ``controllers.routes.Application`` 重定向控制器：

::

  // Redirect to /hello/Bob
  public Result index() {
      return redirect(controllers.routes.Application.hello("Bob"));
  }
 
注意：每一个控制器包都拥有一个routes子包。所以行为 ``controllers.admin.Application.hello`` 能够通过 ``controllers.admin.routes.Application.hello`` 被重定向。

------------
高级路由
------------

参见文档中 `Routing DSL <https://www.playframework.com/documentation/2.5.x/JavaRoutingDsl>`_ 一节。

=========
控制应答
=========

------------------------------------
改变默认内容类型（Content-Type）
------------------------------------

最终的内容类型会从你指定为body的Java变量的类型自动推断出来。

例如：

::
 
  Result textResult = ok("Hello World!");
 

将会自动设置Content-Type为text/plain，而

::
 
  JsonNode json = Json.toJson(object);
  Result jsonResult = ok(json);
 

将会设置 **Content-Type** 为 **application/json**

这个功能很有用，但是有时你想要主动更改它，这时候你只需要在一个result上使用as(newContentTyoe)方法就能创建一个相似但具有不同类型的Content-Type的result。

::

  Result htmlResult = ok("<h1>Hello World!</h1>").as("text/html");
 
------------------------
设置HTTP回应头
------------------------

::

  public Result index() {
      response().setHeader(CACHE_CONTROL, "max-age=3600");
      response().setHeader(ETAG, "xxx");
      return ok("<h1>Hello World!</h1>").as("text/html");
  }
 

需要注意的是设置HTTP回应头会自动丢弃任何先前设置的值。

------------------------
设置与删除cookies
------------------------

Cookie只不过是一种特殊的HTTP头而已，但是Play提供了一系列的方法，让你能更方便地使用它。

你能够很容易地向HTTP回应头上添加一个cookie：

::

  response().setCookie("theme", "blue");
 

如果你需要更多的细节操作，例如包含一个作用域、期限，不论这是否安全，不论HTTP only flag是否应该被设置，你能通过这些重载方法够做到以上操作：



删除一个存储在浏览器里的cookie：

::

  response().discardCookie("theme");
 


如果你对你的cookie设置了一个作用域，确定你在删除cookie的时候设置了相同的作用域，因为浏览器只会删除作用域和名称都相符的cookie。

为文字result设置字符编码

对于一个基于文字的HTTP回应，处理文字编码是十分重要的。Play默认你使用utf-8编码。

编码被用来转换文字回应到正确的套接字字节，并添加;charset=xxx 表达式到Content-Type 头、

编码能够被在你创建Result变量的时候指定：

::
  
  public Result index() {
      return ok("<h1>Hello World!</h1>", "iso-8859-1").as("text/html; charset=iso-8859-1");
  }
 

=========================
Session和Flash scopes
=========================

------------------------------------
在Play框架中它们有什么不同？
------------------------------------

如果你必须在多个HTTP请求之中保持数据，你可以将它们保存在Session或Flash scope之中。存储在Session中的数据在整个用户会话的过程中都可用，而存储在Flash scope中数据只提供给下一个请求。

必须要知道的是，会话和Flash数据不存储在服务器中，而是被添加到每个随后的HTTP请求的Cookies之中。这意味着，这些数据的大小将会非常有限（最多4 KB），并且可以仅存储字符串类型的值。

由于Cookies使用了密钥签名，所以在客户端无法修改cookie数据（否则会失效）。Play的Session并不旨在被用作缓存。如果你需要缓存与特定的会话一些数据，你可以使用Play内置的缓存机制，并使用Session存储一个唯一的ID来将缓存数据与特定用户相关联。

注：对于Session并没有技术上的超时，当用户关闭浏览器时它就会过期。如果你在一个特别的应用中需要一个功能性的超时，你只需要储存一个时间戳到你用户的Session之中然后按照你的应用的需要来使用（例如，最长会话持续时间，最大不活动持续时间等）。您还可以设置会话cookie的最长存活时间通过配置在application.conf的键play.http.session.maxAge（毫秒），但是请注意，这并不能阻止攻击者保存和重新使用cookie在其过期之后。

------------------------
将数据存储到Session
------------------------

由于Session只是一个Cookie，因此它也只是一个HTTP头，但Play提供了一个辅助方法来存储Session值：

::
  
  public Result login() {
      session("connected", "user@gmail.com");
      return ok("Welcome!");
  }
 
使用同样的方法，你可以删除传入Session的任何值：

::
  
  public Result logout() {
      session().remove("connected");
      return ok("Bye");
  }


------------------------ 
读取Session值
------------------------
你可以从HTTP请求传入Session：

::

  public Result index() {
      String user = session("connected");
      if(user != null) {
          return ok("Hello " + user);
      } else {
          return unauthorized("Oops, you are not connected");
      }
  }

------------------------
丢弃整个Session
------------------------

如果你想丢弃整个Session，这里有一个简单的操作：

::
  
  public Result logout() {
      session().clear();
      return ok("Bye");
  }

------------------------
Flash scope
------------------------

Flash scope的作用几乎完全和Session一样，但有两点不同：

* 数据只为一次请求保存

* Flash scope使用的cookie没有签名，这使得它有可能被用户所修改。

重要提示：Flash scope应该仅用于为简单的非Ajax应用程序传输成功/错误信息。由于数据只为下一个请求保留，以及因为没有加护用于确保在复杂的Web应用程序的请求次序，所以Flash scope是受资源竞争制约的。

因此，例如，在存储一个项目后，您可能希望将用户重定向回索引页面，您可能希望在主页上显示一个异常：成功存储。在保存的动作之后，你也许想向Flash scope添加一个成功的消息：

::

  public Result save() {
      flash("success", "The item has been created");
      return redirect("/home");
  }
 
然后在index action中，你可以检查在Flash scope中是否存在成功消息，并且如果是这样，提示它：

::
  
  public Result index() {
      String message = flash("success");
      if(message == null) {
          message = "Welcome!";
      }
      return ok(message);
  }
 
Flash scope值在也Twirl模板引擎中也能被自动应用。例如：

::
  
  @if(flash.containsKey("success")) {
    @flash.get("success")
  } else {
    Welcome!
  }
