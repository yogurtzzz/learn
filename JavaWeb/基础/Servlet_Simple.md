# Servlet

## 概述

**狭义地说**，Servlet就是定义在JavaEE规范中的一个接口，`javax.servlet.Servlet`，参见JavaEE的[API文档](https://docs.oracle.com/javaee/7/api/)

```java
package javax.servlet;

import java.io.IOException;

public interface Servlet {
    void init(ServletConfig config) throws ServletException;

    ServletConfig getServletConfig();

    void service(ServletRequest req, ServletResponse res) throws ServletException, IOException;

    String getServletInfo();

    void destroy();
}
```

Servlet接口定义的是一套处理网络请求的规范。一个实现了Servlet接口的类（下面简称**Servlet类**），表明它是一个可以处理网络请求的类。Servlet类需要实现Servlet接口中的5个方法，其中最主要的3个是：

- `init()`
- `destroy()`
- `service()`

它们分别定义了，一个Servlet类，

- 在初始化时需要干什么
- 在销毁时需要干什么
- 在每次接收到请求时，要作何处理

实现了Servlet接口的类，就能处理网络请求了吗？

显然没这么简单。

我们只知道，一个Servlet类在处理请求时，执行的是它的`service()`方法，但`service()`方法是由谁调用的，方法入参中的`request`和`response`对象又是怎么来的，这显然不是Servlet类自己做的，而且Servlet中也没有诸如监听网络端口等和网络请求有关的操作。

所以，Servlet类要处理网络请求，还需要依赖一个东西——**Servlet容器**。这个容器要负责监听网络端口，封装网络请求数据，管理众多的Servlet类，进行请求的调度分发，返回响应数据等。最典型的Servlet容器就是Tomcat。

Servlet容器和Servlet类是如何协作处理一个HTTP请求的？

```
1. Servlet容器监听网络端口，接收到HTTP请求
2. 根据请求的url, 查找对应的Servlet类
3. 若对应的Servlet类不在容器中，则检索并创建该Servlet实例，并初始化(调Servlet的init方法)
4. 容器构建该次请求的request和response对象, 调用对应Servlet的service方法，传入这2个参数
5. Servlet类执行service方法, 对请求进行处理，并设置响应数据到response对象中
6. 容器取出response对象中的响应数据，封装好HTTP响应报文，返回给客户端
```

我们可以看到，在HTTP请求的整个处理过程中，干活儿最多的，其实是Servlet容器。Servlet容器把处理请求的整个流程，框架，都搭好了，只是在中间留了很小的一部分空间，给Servlet类。所以，Servlet类负责的，其实只是整个流程中很小的一环。

不过，这也是因为，在处理网络请求时，诸如监听网络端口，封装请求数据，响应请求数据，这些操作，都是共性的。也就是说，所有的处理网络请求的程序，都必须有这些相同的步骤，而唯一不同的地方，只是在于对请求的业务处理。于是就把这些相同的地方抽取出来，做成了**Servlet容器**，不同的地方，交给各个**Servlet类**。这也体现了编程领域中，**抽取共性，封装变化**的设计理念。试想，若没有Servlet容器，则每开发一个网络程序，都要自己完整实现一套处理网络请求和响应的流程，那开发的门槛就太高了，开发量也很大，并且都是重复的工作。



**广义地讲**，Servlet是一套规范体系，不仅仅是`javax.servlet.Servlet`接口，而是`javax.servlet`包下定义的全部规范。主要包括了JavaWeb三大组件，Servlet，Filter，Listener，以及其他一系列相关的接口。

下面，对Servlet的规范体系进行非常简要的归纳总结。



## 基本使用

构建一个最基本的web程序，只需要有Servlet类（实现了`javax.servlet.Servlet`接口的类）即可

1. 在IDEA中新建一个JavaWeb项目
2. 导入`servlet-api.jar`依赖
3. 创建一个实现`javax.servlet.Servlet`接口的类
4. 对该Servlet类进行配置（主要配置该Servlet对应的url），有2种配置方法
   1. 在`web.xml`中配置
   2. 使用注解`@WebServlet`进行配置
5. 配置并部署Tomcat



**Servlet配置参数**：

- `urlPattern`：该Servlet处理的url
- `initParams`：name - value，键值对形式，可通过`ServletConfig`获取
- `loadOnStartup`：值为一个整数。若是0或正数，容器启动时就立刻加载Servlet，数字越小越先加载。若是负数，容器可自由选择何时加载（一般会懒加载，即用到该Servlet时才加载）

**Servlet生命周期**：

- `init()`：容器加载Servlet实例，进行初始化时，调用执行Servlet的`init()`
- `destroy()`：容器销毁Servlet实例时，调用执行Servlet的`destroy()`
- `service()`：容器将请求交由一个Servlet处理时，调用执行Servlet的`service()`

`javax.servlet.Servlet`只是最原始的接口规范，`javax.servlet`包下还定义了一些基本的抽象子类，如`javax.servlet.GenericServlet`，`javax.servlet.http.HttpServlet`，类的层次结构图如下

![](https://img-blog.csdnimg.cn/20210121143721438.png)

由于构建的web应用几乎都是基于HTTP协议的，我们创建一个Servlet时，通常只需要继承`HttpServlet`，由于最常用的HTTP方法是GET和POST，通常我们只需要重写`doGet`和`doPost`方法即可。本文下面的内容，默认基于HTTP协议。



## 两个重要对象

在Servlet处理请求时，有两个最重要的对象——`request`和`response`。前者封装了HTTP请求数据，后者封装了HTTP响应数据。这两个对象分别是`HttpServletRequest`和`HttpServletResponse`类的实例。对于`request`，主要的操作是`get`，因为需要从其中获取请求数据，对于`response`，主要的操作是`set`，因为需要往里面设置响应数据

它们中的常用方法下面列举一二

`HttpServletRequest`：

- `getRequestURL`：获取请求URL
- `getMethod`：获取请求方法
- `getParameter(String s)`：获取请求参数
- `getHeader(String s)`：获取请求头
- `getInputStream() / getReader()`：获取请求体
- .....

`HttpServletResponse`

- `addHeader(String k, String v)`：添加响应头
- `getOutputStream() / getWriter()`：获取输出流，以便往响应体里写入数据
- `setStatus(int sc)`：设置HTTP响应状态码
- `sendRedirect(String s)`：设置重定向
- ....

## 请求转发

当一个请求，在一个Servlet中不能完成处理，需要进行请求转发时，有2种转发方式

- 服务端转发

  通过`request.getRequestDispatcher("/xx").forward(request,response)`

  转发到能处理`/xx`这种url的Servlet。发生在服务端内部，本质相当于方法调用，只能转发到服务端内部的资源。

- 客户端转发

  通过设置HTTP状态码为`3xx`（一般设置为`302`），并在HTTP响应头添加`Location`指定重定向的url地址，浏览器收到`3xx`的HTTP响应，会自动重定向到`Location`头部指定的url地址。实际发生了2次HTTP请求，可跳转到任意url。可以调用`response`对象的`sendRedirect`方法完成。

## 会话技术

众所周知，HTTP是无状态的协议，即每组HTTP请求/响应，都是互相独立的，HTTP协议本身**不具备记忆能力**。但有的场景需要在多次HTTP请求之间维护一些状态信息，此时，就轮到会话技术登场了。根据状态信息是保存在服务端，还是客户端，会话技术分为了2种：Cookie和Session

### Cookie

cookie是HTTP协议的扩展标准。本质就是一个简单的k-v键值对。当浏览器收到的响应报文中，包含了`Set-Cookie`头部时，会将该头部中的k-v键值对，保存在浏览器端（默认保存在浏览器内存，在浏览器关闭后失效），下次再发起请求时，会自动添加`Cookie`头部，携带先前保存下来的k-v键值对。

在Servlet中，通过`response`对象来向浏览器发送一个cookie，具体操作如下

```
Cookie cookie = new Cookie("userId","123");
response.addCookie(cookie);
```

HTTP响应报文如下（简化）

```http
HTTP/1.1 200 OK
Set-Cookie: userId=123
```

下次请求时的HTTP请求报文如下（简化）

```http
GET / HTTP/1.1
Host: www.baidu.com
Cookie: userId=123
```

在Servlet中，通过`request`对象获取请求中携带的cookie，具体操作如下

```java
Cookie[] cookies = request.getCookies(); // 获取请求携带的全部Cookie
for(Cookie c : cookies) {
    String name = c.getName();
    String value = c.getValue();
}
```

k-v键值对，是cookie最基本的信息。除此之外，cookie还可以设置以下属性

- domain：设置允许携带cookie的域
- path：设置允许携带cookie的资源路径
- maxAge：设置cookie最大存活时间，单位秒（若不设置，cookie默认在浏览器关闭时失效）
- httpOnly：设为true，可避免JS脚本窃取cookie
- secure：设为true，则只有使用HTTPS等安全的协议时，才携带Cookie

另：Cookie，翻译过来是**小饼干**的意思，这样的命名也意味着，它只能保存少量的信息（简单的k-v键值对），且浏览器一般对cookie的大小，数量等都有所限制，而由于将信息保存在了浏览器端，也就意味着信息容易遭到窃取或篡改，不够安全。

于是，将信息保存在服务端的Session技术，轮到它登场了。

### Session

session是依赖于cookie的。它的大概原理是：一次请求到来，服务器端在处理请求时，生成一个session对象（保存在服务端的内存中），以及一个对应的sessionId。当服务端处理完毕，返回响应时，在HTTP响应报文添加这样的头部`Set-Cookie:JSESSIONID=123`。浏览器端保存下这个name为`JSESSIONID`的cookie，在下次请求时，携带这个cookie。服务端根据`JSESSIONID`，找到自己内存中对应的session对象，这样，即可在多个HTTP请求之间共享数据。在tomcat的配置中，session的默认有效时间为30分钟。session对象中也可以添加k-v键值对，但**值**不仅仅是字符串，还可以是任意的Object对象，这就比cookie能携带的信息要大得多了。且session对象的大小，理论上只会受到服务器内存大小的限制。且由于存在服务端，安全性相对就高了许多。

在Servlet中，一个session对象是一个`HttpSession`类的实例，通过`request`对象来生成或获取一个session对象，如下

```java
// 1. 若此次请求中没有携带name为JSESSIONID的cookie, 则getSession会新创建一个session对象
//    并把对应的id添加到响应头
// 2. 若此次请求中有携带name为JSESSIONID的cookie, 则getSession会根据这个id的值
//    从内存中找到该id对应的session对象
HttpSession session = request.getSession();
```

session对象中常用的方法：

- `setAttribute(String s, Object o)`：往session对象中添加一组键值对数据
- `getAttribue(String s)`
- `setMaxInactiveInterval(int i)`：设置session对象最大有效时长，单位秒。（负数表示永不失效）
- `invalidate()`：使该session对象失效

另：

1. 由于session对象是保存在服务端内存，当服务器关闭时，内存中的数据就没了。所以session还有钝化和活化处理。这种处理在tomcat中是默认开启的。当**session未失效**，**且服务器正常关闭**时，内存中的session对象，会被序列化，存储到磁盘，文件名为`SESSIONS.ser`（这叫**钝化**）。下次服务器启动时，会自动读取并删除这个`SESSIONS.ser`，将session对象的数据从磁盘加载到内存（这叫**活化**）。由于session对象中可以保存任意Object对象，若要成功支持session钝化活化，则保存在session中的数据，都需要可序列化（实现`Serializable`接口）
2. 由于name为`JSESSIONID`的cookie，是自动设置到响应中的，所以这个cookie遵循默认的配置，在浏览器关闭后就会失效。若需要在浏览器关闭后不失效，则手动设置一个name为`JSESSIONID`的cookie，并设置其maxAge

### 对比小结

|              | Cookie                       | Session                        |
| ------------ | ---------------------------- | ------------------------------ |
| 数据存储位置 | 客户端（浏览器端）           | 服务端                         |
| 限制         | 少量数据，简单的字符串键值对 | 可存很多数据，任意Object类型   |
| 有效性       | 默认在浏览器关闭后失效       | 默认有效时长30分钟（tomcat中） |
| 安全性       | 低                           | 高                             |

## 其它相关对象

上面的会话技术，是在同一用户的多次请求之间进行数据共享。若要在整个web应用中共享数据，则可以通过`ServletContext`对象实现。

### ServletContext

一个Web应用，对应一个`ServletContext`对象，根据命名也能看出，这是整个web应用的上下文环境。这个对象中的常用方法，下面列举一二

- `setAttribue(String s, Object o)`：添加一组键值对数据
- `getAttribe(String s)`
- `getInitParameter(String s)`：获取web应用的全局初始化参数（`web.xml`中的`<context-param>`标签）
- ...

小应用：统计网站访问量。每收到一个请求，就对`ServletContext`中的一个属性进行累加操作

### ServletConfig

`ServletConfig`对象用于向Servlet传递一些参数，以便在Servlet初始化时使用。它主要包含了如下方法

- `getServletContext()`：获取web应用全局上下文
- `getInitParameter(String s)`：获取Servlet初始化参数（`web.xml`中的`<init-param>`标签，也可通过`@WebInitParam`注解进行设置）

## 过滤器

### 概述

Servlet，Filter，Listener并称JavaWeb三大组件。

其中的Filter，指的就是`javax.servlet.Filter`接口。它可以在**请求被处理**（请求的资源可以是Servlet，也可以是HTML等静态资源）的**之前**，**之后**，进行一些额外的操作。

它的工作流程如下图所示

![](https://img-blog.csdnimg.cn/20210118144503143.png)

过滤器可以配置不止一个，当有多个过滤器时，它们就形成了一个**链**，如下图

![](https://img-blog.csdnimg.cn/20210118144406239.png)

与Servlet的生命周期类似，Filter也有如下3个方法

- `init()`：过滤器被创建时，该方法被调用
- `destroy()`：过滤器被销毁时，该方法被调用
- `doFilter()`：过滤器对请求/响应进行过滤处理时，该方法被调用

Filter相关的一共有3个类

- `javax.servlet.Filter`：核心接口
- `javax.servlet.FilterConfig`：过滤器配置接口，可以通过该接口给过滤器传递一些初始化参数
- `javax.servlet.FilterChain`：过滤器链接口

### 使用

创建一个过滤器的步骤如下

1. 新建一个类，实现`javax.servlet.Filter`接口

2. 重写`init()`，`destroy()`，`doFilter()`方法

3. 配置过滤器

   - xml方式：`web.xml`

     ```xml
     	<filter>
             <filter-name>myFilter</filter-name>
             <filter-class>filter.LogFilter</filter-class>
         </filter>
         <filter-mapping>
             <filter-name>myFilter</filter-name>
             <url-pattern>/*</url-pattern>
         </filter-mapping>
     ```

   - 注解方式：`@WebFilter`

     ```java
     @WebFilter(servletNames = {"ElServlet"})
     public class LogFilter implements Filter {
     	// 代码略
     }
     ```



### 配置参数

只需要配置过滤器在何时起作用即可，可以指定其`urlPattern`，对满足某一格式的url进行拦截；也可以指定`servletNames`，对指定的Servlet进行拦截（二者选其一即可）

- `urlPattern`

- `servletNames`

如下

```java
@WebFilter(servletNames = {"ElServlet"})
public class LogFilter implements Filter {
	// 代码略
}
```

```java
@WebFilter(urlPatterns = "/*")
public class LogFilter implements Filter {
	// 代码略
}
```

### 注意

由于拦截某一请求的过滤器，可能有多个，这就形成一条过滤器链，当一个过滤器处理完毕后，应该调用链上的下一个过滤器，或者直接进入到资源处理（当该过滤器是链中的最后一个时）。

所以，在某个过滤器的`doFilter()`方法中，完成了处理后，应该调用`FilterChain`的`doFilter()`方法，将请求/响应在过滤器链上传递下去（若不调用，则请求不会到达最终的web资源）。在调用`FilterChain`的`doFilter()`方法之前，请求还未被处理，在其后，请求已完成处理。这就对应了先前说的，Filter可以在**请求被处理**的**之前**，**之后**，进行一些额外的操作。

```java
@WebFilter(urlPatterns = "/*")
public class LogFilter implements Filter {
	public void destroy() {
	}

	public void doFilter(ServletRequest req, ServletResponse resp, FilterChain chain) throws ServletException, IOException {
		System.out.println("before servlet");
		chain.doFilter(req, resp);
		System.out.println("after servlet");
	}

	public void init(FilterConfig config) throws ServletException {
	}
}
```

### 应用场景

Filter的常见应用场景列举如下

- 进行日志记录（记录被调用的接口，请求参数，以便进行问题排查，数据统计等工作）

- 统一设置编码格式（如处理中文乱码问题等）
- 过滤敏感词汇
- 数据压缩
- 数据加密
- 身份认证

## 监听器

JavaWeb三大组件的最后一个，Listener。

用于监听一些重要事件的发生。以便在事件发生时，能够做一些额外操作。

Servlet API中针对如下对象，提供了对应的监听器接口

- `ServletContext`对象

  `ServletContextListener`，`ServletContextAttributeListener`

- `HttpSession`对象

  `HttpSessionListener`，`HttpSessionAttributeListener`，`HttpSessionIdListener`

- `ServletRequest`对象

  `ServletRequestListener`，`ServletRequestAttributeListener`

比如，可以监听`HttpSession`对象的创建和销毁，来实现一个网站在线人数统计的功能（若用户请求的是一个JSP页面，JSP页面在默认情况下，会为每一个新的请求创建一个session对象，这是下面进行人数统计的基础，这个默认行为，可以通过JSP指令`<%@ page session="false" %>`来取消）

```java
import javax.servlet.ServletContext;
import javax.servlet.annotation.WebListener;
import javax.servlet.http.HttpSession;
import javax.servlet.http.HttpSessionEvent;
import javax.servlet.http.HttpSessionListener;
import java.time.LocalDateTime;

/**
 * 统计网线在线人数
 * Session创建, 就全局人数+1, Session销毁, 就全局人数-1
 * **/
@WebListener // 加上注解后，就不必在web.xml中进行配置
public class OnLineCounter implements HttpSessionListener {

	private final String COUNT = "count";

	@Override
	public void sessionCreated(HttpSessionEvent httpSessionEvent) {
		HttpSession session = httpSessionEvent.getSession();
		System.out.println(LocalDateTime.now() + ", 新来了一个人, id = " + session.getId());
		ServletContext servletContext = session.getServletContext();
		Object o = servletContext.getAttribute(COUNT);
		int count = o == null ? 0 : (int) o;
		servletContext.setAttribute(COUNT, ++count);
	}

	@Override
	public void sessionDestroyed(HttpSessionEvent httpSessionEvent) {
		HttpSession session = httpSessionEvent.getSession();
		System.out.println(LocalDateTime.now() + ", 走了一个人, id = " + session.getId());
		ServletContext servletContext = session.getServletContext();
		int count = (int) servletContext.getAttribute(COUNT);
		servletContext.setAttribute(COUNT, --count);
	}
}
```