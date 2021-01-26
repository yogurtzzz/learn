[TOC]

## Servlet

### 简介

Servlet = Server Applet，翻译过来就是服务端小程序，它是JavaWeb的基石，大部分基于Java开发的Web应用，底层都是它。擒贼先擒王，把Servlet搞懂，对JavaWeb以后的学习是大有裨益的。

Servlet是一种用于开发动态web资源（[关于动态资源与静态资源](#dynamicResource)）的技术规范。简而言之，**它就是一个规范**。打开JAVA官方的API文档，地址https://docs.oracle.com/javaee/7/api/，找到`javax.servlet`包，这个包中就是Servlet规范的相关内容

![image-20201231162902423](https://img-blog.csdnimg.cn/20210107152740751.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZjajEwMDk3ODQ4MTQ=,size_16,color_FFFFFF,t_70)

打开开发工具IDEA，从tomcat的安装目录中导入`servlet-api.jar`，可以看到Servlet就是一个在JavaEE定义好的接口规范。

![image-20201231165121159](https://img-blog.csdnimg.cn/20210107152827760.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZjajEwMDk3ODQ4MTQ=,size_16,color_FFFFFF,t_70)

查看文档的注释，便可了解Servlet是个什么东西了

![image-20201231165301014](https://img-blog.csdnimg.cn/20210107152918743.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZjajEwMDk3ODQ4MTQ=,size_16,color_FFFFFF,t_70)

注释中说，一个Servlet是一个运行在Web服务器中的小型Java程序，它可以从Web客户端那里接收请求，并返回响应，通常使用HTTP协议进行通信。在开发中，一个实现了`javax.servlet.Servlet`接口的Java类，就可称为一个Servlet。

### 基本使用

1. 新建一个JavaWeb项目

2. 导入`servlet-api.jar`依赖

3. 创建一个普通Java文件，使其实现`javax.servlet.Servlet`接口（即新建一个Servlet）

4. 通常不同的URL请求，会分发给不同的Servlet进行处理，故需要配置Servlet的url映射关系

   可通过两种方式配置

   - xml：在`web.xml`中进行配置
   - 注解：在Java类中使用`@WebServlet`进行配置（Servlet 3.0 以后才支持注解）

5. 部署到Tomcat，在浏览器通过对应的url发送请求，即可观察到对应Servlet的`doService()`方法被调用

### 初体验

下面，对Servlet进行一个初步的使用

我们使用IDEA创建一个简单的Web项目

- 创建项目

  点击左上角的File -> New -> Project

  ![image-20201231165739452](https://img-blog.csdnimg.cn/20210107153015181.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZjajEwMDk3ODQ4MTQ=,size_16,color_FFFFFF,t_70)

  勾选Web Application即可，然后点击Next，直到Finish

  ![image-20201231165844757](https://img-blog.csdnimg.cn/20210107153058146.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZjajEwMDk3ODQ4MTQ=,size_16,color_FFFFFF,t_70)

- 导入依赖

  导入`servlet-api.jar`这个jar包

  左上角 File -> Project Structure

  ![image-20201231170155305](https://img-blog.csdnimg.cn/2021010715312640.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZjajEwMDk3ODQ4MTQ=,size_16,color_FFFFFF,t_70)

  打开后，选择 Modules -> Dependencies -> 点击 `+`号 -> 选择第一个

  ![image-20201231170510568](https://img-blog.csdnimg.cn/20210107153153120.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZjajEwMDk3ODQ4MTQ=,size_16,color_FFFFFF,t_70)

  找到`servlet-api.jar`（[如何下载该jar包](#tomcat)），点击OK，点击Apply

  ![image-20201231170559968](https://img-blog.csdnimg.cn/20210107153215508.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZjajEwMDk3ODQ4MTQ=,size_16,color_FFFFFF,t_70)

- 创建一个Servlet

  新建一个Java类，使其实现Servlet接口，则这个Java类就是一个Servlet，在其`service()`方法中随便写一些代码

  ```java
  import javax.servlet.*;
  import java.io.IOException;
  
  public class MyServlet implements Servlet {
  
  	@Override
  	public void init(ServletConfig servletConfig) throws ServletException {
  
  	}
  
  	@Override
  	public ServletConfig getServletConfig() {
  		return null;
  	}
  
  	@Override
  	public void service(ServletRequest servletRequest, ServletResponse servletResponse) throws ServletException, IOException {
  		System.out.println("我收到请求了哈哈哈");
  	}
  
  	@Override
  	public String getServletInfo() {
  		return null;
  	}
  
  	@Override
  	public void destroy() {
  
  	}
  }
  ```

- 配置Servlet

  在`web.xml`中进行配置

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
           version="4.0">
      <servlet>
          <servlet-name>myServlet</servlet-name>
          <servlet-class>MyServlet</servlet-class>
      </servlet>
      
      <servlet-mapping>
          <servlet-name>myServlet</servlet-name>
          <url-pattern>/</url-pattern>
      </servlet-mapping>
  </web-app>
  ```

  注：若使用注解配置，在`MySerlvet`的类名上加上`@WebServlet`注解即可，如下

  ```java
  import javax.servlet.*;
  import java.io.IOException;
  
  
  @WebServlet(name = "myServlet", urlPatterns = "/")
  public class MyServlet implements Servlet {
      // 省略代码
  }
  ```

  项目的目录结构如下

  ![image-20201231171511270](https://img-blog.csdnimg.cn/20210107153312421.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZjajEwMDk3ODQ4MTQ=,size_16,color_FFFFFF,t_70)

- 配置Tomcat服务器

  点击右上角的Add Configuration

  ![image-20201231171543456](https://img-blog.csdnimg.cn/20210107153336986.png)

  添加一个Tomcat服务器

  ![image-20201231171700871](https://img-blog.csdnimg.cn/20210107153357256.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZjajEwMDk3ODQ4MTQ=,size_16,color_FFFFFF,t_70)

  随后看到如下页面，进行配置

  ![image-20201231172635549](https://img-blog.csdnimg.cn/20210107153428178.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZjajEwMDk3ODQ4MTQ=,size_16,color_FFFFFF,t_70)

  点击红框右侧的Fix，将当前项目部署到Tomcat，点击Apply

  ![image-20201231171913900](https://img-blog.csdnimg.cn/20210107153451632.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZjajEwMDk3ODQ4MTQ=,size_16,color_FFFFFF,t_70)

  随后在右上角就能看到有Tomcat啦，点击绿色的▶按钮，启动服务器

  ![image-20201231171959765](https://img-blog.csdnimg.cn/20210107153515703.png)

- 访问页面

  打开浏览器，输入`localhost:8080`，就能在控制台看到打印出了日志。

  ![image-20201231172732639](https://img-blog.csdnimg.cn/20210107153534852.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZjajEwMDk3ODQ4MTQ=,size_16,color_FFFFFF,t_70)

  说明请求成功被MyServlet处理，并且处理时调用的是service方法



### Servlet生命周期

在`javax.servlet.Servlet`这个接口中，只定义了几个方法，其中有3个与Servlet的生命周期相关，它们分别是

- `init()`
- `service()`
- `destroy()`

根据文档中的注释，`init()`方法会在Servlet创建时被调用，在Servlet创建完毕后，每收到一个请求，就会调用`service()`方法对请求进行处理，而`destroy()`方法则是在Servlet销毁时被调用。

简单来说即是：Servlet出生（`init`），Servlet干活（`service`），Servlet死亡（`destroy`）

Servlet的运行需要依赖于一个支持Servlet规范的环境，即需要一个容器。Tomcat实现了Servlet规范，所以它可以被称为Servlet容器。

我们在开发Web应用时，基本没有操心过应用是如何接收请求和返回响应的，我们只用创建Java类，实现Servlet接口，编写其中的`service()`方法，即可对请求进行处理，并返回响应。我们看到`service()`方法有2个入参，如下

![](https://img-blog.csdnimg.cn/20210108091504967.png)

在service方法中我们可以直接使用这两个参数，但是这两个参数是哪里来的呢？为什么Servlet处理请求时就执行的是`service()`方法呢？是谁负责调用这个`service()`方法的呢？答案是，Servlet容器。Servlet容器会按照Servlet规范办事。Servlet的规范文档，可以在Oracle的官方网站上找到，参见[此处](https://www.oracle.com/java/technologies/javaee/javaeetechnologies.html#javaee7)

简单来说就是，JAVA中定义了一个规范，这个规范中说，有一种牛逼的技术叫Servlet，它必须有5个方法，分别是12345，Servlet初始化时需要执行方法1，Servlet干活时执行方法2，Servlet死掉时执行方法3，方法4和方法5则可以获取一些配置信息。

于是我们开发时，就是创建各种不同的Servlet，实现12345这些方法，然后给每个Servlet配置一个url路径。再把这些Servlet一股脑扔进一个Servlet容器里。它们就准备就绪， 可以开始干活了。Servlet容器负责的是什么呢？Servlet容器，顾名思义，就是负责容纳和管理各个Servlet。容器的运行流程可以用如下的伪代码形式进行简单说明（基于HTTP通信）

```
// 服务启动时
1. 扫描项目的文件，加载Servlet和各项配置，并调用每个Servlet的init方法
// 服务启动后
循环:
	2. 监听服务器的某个端口
	3. 收到HTTP请求
	4. 对请求数据进行解析，封装
	5. 根据请求的URL，找到对应的Servlet
	6. 调用对应Servlet的service方法,传入封装好的request, response对象
	7. Servlet执行完毕后，取出response对象缓冲区中的数据，组装HTTP响应报文，并发送
```

其实，Servlet的加载时机，可以在服务器刚启动时加载，也可以在Servlet第一次被请求时加载（懒加载）。这可以通过`load-on-startup`参数进行配置。可以在`web.xml`中配置

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">
    <servlet>
        <servlet-name>myServlet</servlet-name>
        <servlet-class>MyServlet</servlet-class>
        <load-on-startup>0</load-on-startup>
    </servlet>

    <servlet-mapping>
        <servlet-name>myServlet</servlet-name>
        <url-pattern>/demo</url-pattern>
    </servlet-mapping>
</web-app>
```

也可以通过注解配置

```java
@WebServlet(name = "myServlet", urlPatterns = "/demo", loadOnStartup = 0)
public class MyServlet extends HttpServlet {
	// 省略代码...
}
```

`load-on-startup`是一个可选的配置项，关于其说明，在`web.xml`中，按住`Ctrl`，在`<load-on-startup>`标签上点击鼠标左键，即可看到其定义。如下

![](https://img-blog.csdnimg.cn/20210108095644742.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZjajEwMDk3ODQ4MTQ=,size_16,color_FFFFFF,t_70)

可以知道，这个参数是用来控制Servlet的加载顺序的。对于某个Servlet，若不配置`load-on-starup`，或者配置为负数时，Servlet容器可以自由选择何时加载这个Servlet（这种情况一般都会选择懒加载，即用到这个Servlet的时候才进行加载）。当这个参数的值为0，或者正数时，Servlet容器必须在应用启动时就立刻加载这个Servlet。并且`load-on-startup`的值也决定了Servlet的加载顺序，值越小的越先加载。比如Servlet A配置了`load-on-startup`为1，Servlet B 配置了2，则应用启动时，先加载Servlet A，再加载Servlet B。

简单记为：

1. 值为负数，Servlet会立刻加载，值为0或正数，会懒加载
2. 值越小的，越先加载



一般来说，我们开发的web应用都是基于HTTP通信的，所以创建一个Servlet时，我们可以直接继承`javax.servlet.HttpServlet`。这个类也是`javax.servlet.Servlet`的子类。HttpServlet中已经实现了`service()`方法，在`service()`中会根据HTTP的请求方法，来决定是调用`doGet()`，还是`doPost()`，或者其他的`doXxx()`。一般来说只会用到`GET`和`POST`，所以一般我们只需要实现`doGet`和`doPost`方法即可。创建一个Servlet的示例如下

```java
public class MyServlet extends HttpServlet {
	protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
	System.out.println("收到一个HTTP请求");
	}

	protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		doPost(request, response);
	}
}
```

在IDEA中，可以很方便的创建一个Servlet，操作如下

![](https://img-blog.csdnimg.cn/20210108164838181.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZjajEwMDk3ODQ4MTQ=,size_16,color_FFFFFF,t_70)

### request与response对象

在开发时，我们最需要关心的便是，请求是什么，又需要给出怎样的响应。所以就需要了解Servlet容器为我们封装好的两个重要对象：`request`和`response`。Servlet规范中定义了一个类`javax.servlet.ServletRequest`，用来承载请求数据，而响应数据则由`javax.servlet.ServletResponse`承载。

在开发中，多以HTTP协议进行通信，所以了解它们的子类`javax.servlet.HttpServletRequest`和`javax.servlet.HttpServletResponse`这两个类即可。

#### HttpServletRequest

Servlet容器，将HTTP请求报文中的数据，封装成`HttpServletRequest`对象，下面列举这个类的常用方法

- `getRequestURL()`：获取客户端请求的完整URL
- `getRequestURI()`：获取客户端请求的资源名部分
- `getQueryString()`：获取URL后面携带的查询参数
- `getMethod()`：获取请求方法
- `getScheme()`：获取请求使用的协议名称，如`HTTP`
- `getProtocol()`：获取请求使用的协议，如`HTTP/1.1`
- `getHeader(String name)`：获取请求头中，name对应的value（若有多个value，则返回第一个）
- `getHeaders(String name)`：获取请求头中，name对应的全部value，返回值是一个Enumeration对象
- `getCookies()`：获取请求中携带的Cookie（返回的是一个Cookie数组）
- `getSession()`：获取与该请求关联的Session，若Session不存在，则创建一个
- `getParameter(String name)`：获取请求参数，对于HTTP，参数指的是query string中的参数或者post提交的表单数据
- `getInputStream()`：获取请求的InputStream
- `getReader()`：获取请求的Reader
- `getAttribute(String name)`：获取request对象中的属性
- `setAttribute(String name, Object o)`：往request对象中设置属性
- `remoteAttribute(String name)`：删除request对象中的属性

比较常用的就是上面列举的这些，完整的方法列表可以自行查看`HttpServletRequest`及`ServletRequest`类。建议下载源码包，便可以看到其中的JavaDoc注释，注释可以说是比较官方，也比较完整的学习材料了，源码包的下载最好通过maven来进行，这个在以后的maven笔记中再说。

下面使用Postman发起一个请求，用Fiddle抓包，并在应用的控制台中进行观察。

Servlet的代码如下

```java
@WebServlet(urlPatterns = "/demo")
public class MyServlet extends HttpServlet {
	@Override
	protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
		doPost(req, resp);
	}

	@Override
	protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
		System.out.println("requestURL = " + req.getRequestURL().toString());
		System.out.println("requestURI = " + req.getRequestURI());
        System.out.println("method = " + req.getMethod());
		System.out.println("queryString = " + req.getQueryString());
		System.out.println("scheme = " + req.getScheme());
		System.out.println("protocol = " + req.getProtocol());
		Enumeration<String> headerNames = req.getHeaderNames();
		while (headerNames.hasMoreElements()) {
			String headerName = headerNames.nextElement();
			String headerValue = req.getHeader(headerName);
			System.out.println("headerName = " + headerName + ", headerValue = " + headerValue);
		}
		Enumeration<String> parameterNames = req.getParameterNames();
		while (parameterNames.hasMoreElements()) {
			String parameterName = parameterNames.nextElement();
			String parameterValue = req.getParameter(parameterName);
			System.out.println("paramName = " + parameterName + ", paramValue = " + parameterValue);
		}
	}
}
```

Postman发起的请求如下

![image-20210108145135359](https://img-blog.csdnimg.cn/20210108145141381.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZjajEwMDk3ODQ4MTQ=,size_16,color_FFFFFF,t_70)

Fiddler的抓包情况如下

![](https://img-blog.csdnimg.cn/20210108145417245.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZjajEwMDk3ODQ4MTQ=,size_16,color_FFFFFF,t_70)

应用的控制台输出的信息如下

![](https://img-blog.csdnimg.cn/20210108145718741.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZjajEwMDk3ODQ4MTQ=,size_16,color_FFFFFF,t_70)

上面的示例无法看到请求体的处理情况，下面用一个登录的案例来演示带有请求体的POST请求。

login.jsp代码如下

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    <form action="login" method="post">
        用户：<input type="text" name="user"/>
        密码：<input type="text" name="password">
        <input type="submit" value="登录"/>
    </form>
</body>
</html>
```

负责处理的Servlet代码如下

```java
@WebServlet(urlPatterns = "/login")
public class LoginServlet extends HttpServlet {
	protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		BufferedReader reader = request.getReader();
		String line;
		while ((line = reader.readLine()) != null) {
			System.out.println(line);
		}
	}

	protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		doPost(request, response);
	}
}
```

浏览器访问login.jsp

![](https://img-blog.csdnimg.cn/20210108151930148.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZjajEwMDk3ODQ4MTQ=,size_16,color_FFFFFF,t_70)

点击登录，Fiddler抓包情况如下，可以看到请求报文中带有了请求体。

![](https://img-blog.csdnimg.cn/20210108152043465.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZjajEwMDk3ODQ4MTQ=,size_16,color_FFFFFF,t_70)

应用的控制台输出如下

![image-20210108152234448](https://img-blog.csdnimg.cn/20210108152239759.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZjajEwMDk3ODQ4MTQ=,size_16,color_FFFFFF,t_70)

由此可见，使用`getReader`方法能够获取到请求体中的数据。当然，`getInputStream`方法也是能够获取到请求体的。只是`getReader`获取到的是字符流，而`getInputStream`获取到的是字节流。

由于平时开发中，使用POSTMAN调试接口的比较常见，故再次用POSTMAN演示一下POST请求

![](https://img-blog.csdnimg.cn/20210108152804168.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZjajEwMDk3ODQ4MTQ=,size_16,color_FFFFFF,t_70)

此时抓包结果为

![](https://img-blog.csdnimg.cn/20210108152844525.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZjajEwMDk3ODQ4MTQ=,size_16,color_FFFFFF,t_70)

控制台输出为

![](https://img-blog.csdnimg.cn/20210108152927681.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZjajEwMDk3ODQ4MTQ=,size_16,color_FFFFFF,t_70)

可以看到，用POST提交表单数据（表单数据在请求头中表现为`Content-Type: application/x-www-form-urlencoded `），数据是放在请求体中的。这种表单数据，同样可以使用`getParameter()`方法来获取。当通过POST提交表单数据，注意不能同时使用`getReader()`和`getParameter()`，因为**请求体的数据流只能读取一次**。若先调用了`getReader()`或者`getInputStream()`获取了请求体的数据流，则之后调用`getParameter()`则无法获取到任何参数；同理，若先调用`getParameter()`，则随后调用`getReader()`或者`getInputStream()`也无法获取到任何数据。这都是因为**请求体的数据流只能读取一次**。关于如何解决只能读取一次这个问题，可以通过`HttpServletRequestWrapper`这个类来实现，这个后续有机会再说。（在对一个web应用的接口调用进行记录时，需要使用诸如AOP或者Filter来拦截request，取出request中的各项数据，记录在日志中，当在进行日志记录时，可能就已经读取了request中的流，导致后续进入到Spring框架中，由于request中的流已经被读取了，导致Spring获取不到请求体，参数解析失败）

#### HttpServletResponse

Servlet完成对请求的处理之后，该返回响应了。响应数据全部承载于`HttpServletResponse`这个对象，Servlet中可以对这个对象进行设置，不外乎设置3类数据：响应码，响应头，响应体。当Servlet的`service()`方法执行完毕后，会由Servlet容器取出`HttpServletResponse`对象中的数据，组装成HTTP响应报文，发送给请求者。

下面列举这个类的常用方法

- `addHeader(String key, String value)`：添加响应头信息
- `addCookie(Cookie cookie)`：添加Cookie（Cookie是一种特殊的响应头）
- `setStatus(int sc)`：设置响应状态码（通常用于设置2xx这一类成功的响应）
- `sendError(int sc)`：设置响应状态码（通常用于设置错误类的响应，如4xx，或者5xx）
- `sendRedirect(String location)`：发送一个临时重定向的响应，并给出重定向地址。（响应状态码302，浏览器在收到这种状态码的响应时，会取出响应头中的Location属性的值，并重定向到Location指定的地址）
- `getWriter()`：获取response的字符输出流，可用于向响应体中写入数据。
- `getOutputStream()`：获取response的字节输出流，可用于向响应体中写入数据。

下面对`HttpServletResponse`的使用做一个简单的介绍

创建一个如下的Servlet

```java
@WebServlet(urlPatterns = "/resp")
public class ResponseServlet extends HttpServlet {
	protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		response.addHeader("RemainTimes", "5");  // 添加响应头
		response.addHeader("Content-Type","text/html;charset=utf-8"); //设置字符集为utf-8,解决中文乱码
		response.addCookie(new Cookie("account","av123")); // 添加一个Cookie
		response.setStatus(202); // 设置响应状态码
		response.getWriter().write("人脑是单线程的CPU"); // 向响应体中写入数据
	}

	protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		doPost(request, response);
	}
}
```

打开浏览器，发起请求

![](https://img-blog.csdnimg.cn/20210108163211202.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZjajEwMDk3ODQ4MTQ=,size_16,color_FFFFFF,t_70)

查看抓包情况

![](https://img-blog.csdnimg.cn/20210108163313630.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZjajEwMDk3ODQ4MTQ=,size_16,color_FFFFFF,t_70)

可以看到，HTTP响应报文中的数据就是我们在Servlet中设置的数据。当然，有一些额外的响应头没有在Servlet中进行设置，如`Content-Length`和`Date`，则可能是由Servlet容器帮忙添加的。可以看到，设置Cookie，表现在HTTP响应报文中，仅仅是在响应头中设置了`Set-Cookie`，浏览器看到这个特殊的响应头后，会将Cookie的值暂时保存起来，以便在之后的请求中带上Cookie。（可以看到请求报文中有请求头`Cookie`）

#### 请求转发

当一个请求需要转发到新的地址去处理时，需要用到请求转发。Servlet中有两种方式

##### 服务端转发

通过request对象中的`getRequestDispatcher()`，获取一个`RequestDispatcher`对象（请求转发器）

使用示例如下，定义2个Servlet

```java
@WebServlet(urlPatterns = "/first")
public class FirstServlet extends HttpServlet {
	protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		System.out.println("这是First");
        response.getWriter().write("First Servlet Say Hi");
		request.getRequestDispatcher("/second").forward(request, response); //转发
	}
}
```

```java
@WebServlet(urlPatterns = "/second")
public class SecondServlet extends HttpServlet {
	protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		System.out.println("这是Second");
        response.getWriter().write("Second Servlet Say Hi");
	}
}
```

访问

![](https://img-blog.csdnimg.cn/20210108170729455.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZjajEwMDk3ODQ4MTQ=,size_16,color_FFFFFF,t_70)

应用控制台输出

![](https://img-blog.csdnimg.cn/20210108170808420.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZjajEwMDk3ODQ4MTQ=,size_16,color_FFFFFF,t_70)

抓包信息如下，只有一次HTTP请求

![](https://img-blog.csdnimg.cn/20210108170946635.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZjajEwMDk3ODQ4MTQ=,size_16,color_FFFFFF,t_70)

确实是转发过去了，但注意到，在FirstServlet中向响应体添加的字符串`First Servlet Say Hi`不见了。经过Debug，我发现在用forward转发时，会对response的缓冲区进行重。Debug情况如下

可以看到response的缓冲区开始时是有值的。

![](https://img-blog.csdnimg.cn/20210108171303168.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZjajEwMDk3ODQ4MTQ=,size_16,color_FFFFFF,t_70)

执行完`response.resetBuffer()`之后，缓冲区被重置，先前FirstServlet设置的字符串也就没有了

![](https://img-blog.csdnimg.cn/20210108171522636.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZjajEwMDk3ODQ4MTQ=,size_16,color_FFFFFF,t_70)



这就是forward转发，它发生在服务端，相当于方法调用，客户端对此是没有感知的。

##### 客户端转发

服务端返回302临时重定向，并在响应头`Location`中给出重定向的地址。浏览器收到302的HTTP响应状态码时，会自动去响应头中取出`Location`的值，并跳转过去。示例如下

新建一个Servlet

```java
@WebServlet(name = "RedirectServlet", urlPatterns = "/redirect")
public class RedirectServlet extends HttpServlet {
	protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		System.out.println("This is Redirect Servlet");
		response.sendRedirect("/welcome.jsp");
	}
}
```

新建一个`welcome.jsp`

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
</head>
<body>
    <h1>welcome</h1>
</body>
</html>
```

在浏览器访问`/redirect`

![](https://img-blog.csdnimg.cn/2021010817242857.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZjajEwMDk3ODQ4MTQ=,size_16,color_FFFFFF,t_70)

注意，观察到浏览器地址栏发生了变化

查看抓包情况

![](https://img-blog.csdnimg.cn/20210108172551678.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZjajEwMDk3ODQ4MTQ=,size_16,color_FFFFFF,t_70)

能够看到发生了2次HTTP请求，第一次请求`/redirect`时，响应报文中的状态码是302，且响应头中有`Location`属性

随后浏览器根据302的响应，又发起一次请求，自动重定向到了`/welcome.jsp`

![](https://img-blog.csdnimg.cn/20210108172704969.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZjajEwMDk3ODQ4MTQ=,size_16,color_FFFFFF,t_70)

##### 对比小结

| 区别           | 服务端转发                   | 客户端转发                                |
| -------------- | ---------------------------- | ----------------------------------------- |
| 发生的位置     | 发生在服务端                 | 发生在客户端（浏览器）                    |
| 原理           | 服务端内部的方法调用         | 发送302的HTTP响应，浏览器自动进行页面跳转 |
| 请求次数       | 1次                          | 2次                                       |
| 是否能共享数据 | 是（同一组request/response） | 否（两组request/response）                |
| 浏览器地址栏   | 不变                         | 改变                                      |
| 其他           | 只能转发到本服务内的资源     | 能跳转到任意URL                           |



### 会话技术

在之前的HTTP协议笔记中提到过，HTTP是无状态的。在某些场景下，需要在通信双方之间，维护一些状态，这时就要用到会话技术。

维护状态，即存储一些信息。Cookie采用的即是在客户端（浏览器）中存储这些信息。

#### Cookie

原理：当HTTP响应报文中带有这样一个响应头部时，`Set-Cookie:userId=123`，浏览器接收到该响应报文，则会将`userId=123`这个信息，以键值对的形式存储在浏览器内存中。在下一次访问服务器时，浏览器会自动将该信息添加到HTTP请求报文中，具体体现是，请求报文头部多了一项：`Cookie:userId=123`。

**发送Cookie**

服务端若需要向客户端发送一个Cookie，则只需要在响应头中设置`Set-Cookie`即可，Cookie的内容是键值对的形式。

```java
Cookie cookie = new Cookie("userId", "123");
response.addCookie(cookie);
```

**接收Cookie**

服务端若想要解析客户端请求中携带的Cookie，则只需要解析请求头中的`Cookie`即可。

```java
Cookie[] cookies = request.getCookies();
```

代码示例

```java
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.ResponseBody;

import javax.servlet.http.Cookie;
import javax.servlet.http.HttpServletResponse;

@RestController
public class CookieController {

	@GetMapping("/cookie")
	public String cookie(HttpServletResponse response) {
		Cookie cookie = new Cookie("userId", "123");
		response.addCookie(cookie);
		return "success";
	}
    
    @GetMapping("/demo")
	public void demo(HttpServletRequest request) {
		Cookie[] cookies = request.getCookies();
		if (cookies != null) {
			for (Cookie c : cookies) {
				System.out.print("name = " + c.getName());
				System.out.println(", value = " + c.getValue());
			}
		}
	}
}
```

请求抓包情况

![](https://img-blog.csdnimg.cn/20210117215104385.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZjajEwMDk3ODQ4MTQ=,size_16,color_FFFFFF,t_70)

再次发起一个请求，请求`/demo`，抓包可见，请求头中自动带上了`Cookie:userId=123`

![](https://img-blog.csdnimg.cn/20210117215449716.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZjajEwMDk3ODQ4MTQ=,size_16,color_FFFFFF,t_70)

查看应用控制台，可见接收到了Cookie

![](https://img-blog.csdnimg.cn/20210117220550565.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZjajEwMDk3ODQ4MTQ=,size_16,color_FFFFFF,t_70)

详解：

Cookie本质就是一个存储在浏览器端的键值对信息，除了基本的name-value，它还有一些其他属性

- domain

  `setDomain(String pattern)`

  指定Cookie生效的域。如，百度有多台服务器部署不在不同的域名下（有相同的父域名），如`www.baidu.com`，`tieba.baidu.com`，`fanyi.baidu.com`。若想要在这些域名共享某一Cookie，则可以设置该Cookie的domain为`.baidu.com`。

  若不设置domain，则Cookie默认只会返回给发送它的服务器。（谁发送的这个Cookie，下次访问谁时，就会携带这个Cookie，而访问其他服务，则不会携带该Cookie）

- path

  `setPath(String uri)`

  指定Cookie生效的资源路径。如，设置path为`/demo`，则下次访问时，只有当请求路径为`/demo`目录或者其子目录，才会携带该Cookie。若需要在某个服务内全局共享，设置为`/`即可，我测试了一下，如不进行设置，默认为`/`。

- maxAge

  `setMaxAge(int expiry)`

  Cookie默认情况是临时存储在浏览器内存中的，浏览器关闭，cookie就失效了。若想要Cookie长期有效，则需要将Cookie持久化到磁盘。这可以通过设置maxAge来实现。

  - 设为正数：Cookie将被持久化到磁盘，在达到Cookie的最大存活时间（单位秒）之前，关闭浏览器也不会使其失效
  - 设为0：通知浏览器，删除该Cookie
  - 设为负数：Cookie将不被持久化，浏览器退出后Cookie将被删除（和默认情况一样）

- httpOnly

  `setHttpOnly(boolean httpOnly)`

  设置为true，则可以避免通过JS脚本窃取该Cookie

- secure

  `setSecure(boolean flag)`

  设置为true，则告知浏览器，该Cookie只有当使用安全的通信协议时（如HTTPS或SSL），才会被发送

#### Session

Session是将信息存储在服务端。

可以在用户发送不同请求的时候，进行数据共享。

Session的本质仍然是Cookie。

Session的默认有效时间为30分钟。

可以用`request.getSession()`来获取一个Session，随后，可以往Session中设置属性（`setAttribute`），它有点像一个Map。

代码示例如下

```java
	@GetMapping("/session")
	public String session(HttpServletRequest request) {
		HttpSession session = request.getSession();
		session.setAttribute("name","yogurt");
		session.setAttribute("age", 17);
		session.setAttribute("school","sysu");
		return "success";
	}

	@GetMapping("/parseSession")
	public String parseSession(HttpServletRequest request) {
		HttpSession session = request.getSession();
		Enumeration<String> attributeNames = session.getAttributeNames();
		while (attributeNames.hasMoreElements()) {
			String name = attributeNames.nextElement();
			Object value = session.getAttribute(name);
			System.out.println("name = " + name + ", value = " + value);
		}
		return "success";
	}
```

访问`/session`，可以看到响应报文中，出现了`Set-Cookie`头部

![](https://img-blog.csdnimg.cn/20210117224752541.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZjajEwMDk3ODQ4MTQ=,size_16,color_FFFFFF,t_70)

随后，再次访问`/parseSession`接口，可以看到自动在请求头中携带了这个Cookie

![](https://img-blog.csdnimg.cn/20210117224844241.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZjajEwMDk3ODQ4MTQ=,size_16,color_FFFFFF,t_70)

查看应用控制台输出

![](https://img-blog.csdnimg.cn/20210117224917857.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZjajEwMDk3ODQ4MTQ=,size_16,color_FFFFFF,t_70)

原理：通过`request.getSession()`获取session时，若是第一次请求，则会创建一个session。第一次响应时，会通过在响应头上添加`Set-cookie:JSESSIONID=xxxx`。随后在浏览器发起的请求，会带上这个cookie，表现为在请求头上添加`Cookie:JSESSIONID=xxxx`，然后服务器根据这个id，找到自己内存中的session对象。如下图所示

![](https://img-blog.csdnimg.cn/20210118115705553.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZjajEwMDk3ODQ4MTQ=,size_16,color_FFFFFF,t_70)

Session的方法

- `setMaxInactiveInterval(int interval)`：设置session最大有效时长（单位：秒）
- `invalidate()`：使得Session失效
- `setAttribute(String name, Object value)`：往Session对象中设置属性
- `getAttribute(String name)`：从Session对象中获取属性

其他说明：

- 浏览器关闭，服务端不关闭，两次获取到的Session是否为同一个？

  不是同一个。因为Cookie默认在浏览器关闭后失效。若希望获取到的是同一个，则手动创建一个Cookie，使其name为`JSESSIONID`，并为其设置maxAge

- 浏览器不关闭，服务器关闭，两次获取到的Session是否为同一个？

  不是同一个。因为服务器关闭后，Session对象就销毁了。若希望获取到的是同一个，则可以配置Session的**钝化**和**活化**策略。

  **钝化**：在服务器关闭之前，将session序列化，存储到服务器的磁盘中（tomcat的work工作目录下）

  **活化**：在服务器启动时，从磁盘中反序列化session文件，创建session对象，加载到内存中

  session的钝化和活化是Tomcat默认支持的。需要在Tomcat中启动，才有效。如果在IDEA中启动项目，则只会钝化session，但重启后因为会删除work工作目录，故无法活化session。注意，若Session需要钝化，则Session中存储的对象，必须要实现Serializable接口，才能够成功被序列化。在服务器关闭时，还未超过有效时长的Session，才会被钝化为文件，当Session超时，或调用了Session的invalidate方法，或服务器非正常关闭，都不会钝化Session。所有的Session对象，钝化时会被保存在同一个文件`SESSION.ser`中，这个文件会一直存在，直到下次服务器启动时消失。

  钝化后的Session存储如下图

  ![](https://img-blog.csdnimg.cn/20210117231218323.png)

  

  

#### Cookie和Session的对比

|          | Cookie                                                       | Session                    |
| -------- | ------------------------------------------------------------ | -------------------------- |
| 存储未知 | 存储在浏览器端（易被篡改，不安全）                           | 存储在服务端               |
| 大小     | 一般存储少量，不太敏感的数据                                 | 可以存储很多数据，各种类型 |
| 限制     | 浏览器对单个Cookie大小有限制（一般是4kb），对一个域名下的Cookie总数也有限制 | 无限制                     |
| 有效性   | 默认在浏览器关闭后失效，可以设置maxAge，Cookie即可被持久化   | 默认有效时长为30分钟       |



### ServletContext

Cookie和Session可以用来解决相同用户在不同请求之间的数据共享问题。那么，不同用户之间的数据共享问题要如何解决呢？答案就是ServletContext。

什么是ServletContext？

- 运行在JVM上的每一个**web应用程序**，都有一个与之对应的上下文环境，这个上下文环境，即ServletContex
- ServletContext对象可以被该**web应用程序**中的所有Servlet访问

可以简单理解为，一个web项目，只有一个ServletContext对象。这个ServletContext，被所有Servlet，用户所共享。它的一些常用接口如下

- `setAttribute(String name, Object value)`
- `getAttribute(String name)`
- `removeAttribute(String name)`
- `getInitParameter(String param)`
- ...

如何获取ServletContext对象

- 在Servlet中直接调用`getServletContext()`
- 在`HttpServletRequest`对象上直接调用`getServletContext()`
- 在`ServletConfig`对象上直接调用`getServletContext()`
- 在`HttpSession`对象上直接调用`getServletContext()`

可以在web.xml中添加`context-param`，然后在ServletContext中获取，

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">
    <context-param>
        <param-name>name</param-name>
        <param-value>yogurt</param-value>
    </context-param>

    <context-param>
        <param-name>age</param-name>
        <param-value>24</param-value>
    </context-param>
</web-app>
```

```java
ServletContext context = .....;
String name = context.getInitParameter("name");
```

如果有多个属性，使用多个`context-param`标签



ServletContext的应用

实现网站访问量计数器，每有用户访问一次网站，就加一

```java
@WebServlet(name = "WebSiteServlet", urlPatterns = "/num")
public class WebSiteServlet extends HttpServlet {
   protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
      request.setCharacterEncoding("utf-8");
      response.setContentType("text/html;charset=utf-8");

      ServletContext servletContext = getServletContext();
      Integer num = (Integer) servletContext.getAttribute("num");
      if (num == null) {
         servletContext.setAttribute("num", 1);
      } else {
         servletContext.setAttribute("num", ++num);
      }
      PrintWriter writer = response.getWriter();
      writer.write("<html>");
      writer.write("<body>");
      writer.write("用户访问的次数为：" + servletContext.getAttribute("num") + "次");
      writer.write("</body>");
      writer.write("</html>");

   }

   protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
      doPost(request, response);
   }
}
```

### ServletConfig

`ServletContext`对应的是`web.xml`全局配置对象，而`ServletConfig`则是针对某一具体Servlet的配置对象

针对某一具体Servlet的配置，如

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">
    <servlet>
        <servlet-name>ConfigServlet</servlet-name>
        <servlet-class>com.yogurt.web.servlet.ConfigServlet</servlet-class>
        <init-param>
            <param-name>namespace</param-name>
            <param-value>Yogurtzz3</param-value>
        </init-param>
        <init-param>
            <param-name>location</param-name>
            <param-value>D:\Web\</param-value>
        </init-param>
    </servlet>

    <servlet-mapping>
        <servlet-name>ConfigServlet</servlet-name>
        <url-pattern>/config</url-pattern>
    </servlet-mapping>
</web-app>
```

可以在Servlet中获取配置信息

```java
package com.yogurt.web.servlet;

import javax.servlet.ServletConfig;
import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.util.Enumeration;

public class ConfigServlet extends HttpServlet {
	protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		ServletConfig servletConfig = getServletConfig();
		Enumeration<String> initParameterNames = servletConfig.getInitParameterNames();
		while (initParameterNames.hasMoreElements()) {
			String name = initParameterNames.nextElement();
			String value = servletConfig.getInitParameter(name);
			System.out.println("servlet init param, name = " + name + ", value = " + value);
		}
	}

	protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		doPost(request, response);
	}
}

```

运行服务后，访问`/config`，可以看到控制台打印出该Servlet的initParams信息

```
servlet init param, name = namespace, value = Yogurtzz3
servlet init param, name = location, value = D:\Web\
```



Servlet规范中，除了Servlet组件本身，还有其他一些组件，如Filter和Listener。它们并称为JavaWeb三大组件。（Servlet，Filter，Listener）。下面对Filter和Listener进行介绍。

### Filter

#### 原理

即过滤器。它可以在请求处理之前，和之后，做一些额外的操作。其工作流程如下图。

![](https://img-blog.csdnimg.cn/20210118144503143.png)

过滤器可以配置多个，若使用多个过滤器，则它们就形成了一条过滤器链

![](https://img-blog.csdnimg.cn/20210118144406239.png)

Filter包含了如下3个接口

- `javax.servlet.Filter`
- `javax.servlet.FilterChain`
- `javax.servlet.FilterConfig`

#### 使用

定义一个Filter，并配置该Filter需要生效的urlPattern，示例如下

```java
import javax.servlet.*;
import javax.servlet.annotation.WebFilter;
import java.io.IOException;

@WebFilter(filterName = "demoFilter", urlPatterns = "/demo") // 加上注解后，就不必在web.xml中进行配置
public class DemoFilter implements Filter {
	@Override
	public void init(FilterConfig filterConfig) throws ServletException {
		System.out.println("demo filter init");
	}

	@Override
	public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
		System.out.println("this is filter for url /demo");
		filterChain.doFilter(servletRequest, servletResponse);
	}

	@Override
	public void destroy() {
		System.out.println("demo filter destroy");
	}
}
```

对Filter的配置中，只有urlPattern参数是必须的，当只有urlPattern属性被设置时，可以直接将其设置为value属性，此时可以省略参数名，如下

```java
@WebFilter("/demo")
public class DemoFilter implements Filter {
    //代码略...
}
```

关于`javax.serlvet.Filter`接口中的几个方法（这也是Filter的生命周期）

- `init(FilterConfig filterConfig)`：在Filter对象，进行初始化时被调用
- `doFilter(..)`：每次当一组request/response经过某一个Filter时，该Filter的doFilter将被调用
- `destroy()`：当Filter对象被销毁时被调用

#### 注意

关于FilterChain，当对某一urlPattern配置了多个过滤器时，这些过滤器连在一起，成为一个过滤器链（FilterChain）。一次请求到来时，一组request/response对象，在这条链中进行传递，经过链上的每一个过滤器处理，然后最终到达**web资源**（可以是静态的页面资源，也可以是动态的servlet资源）。web资源处理完毕后， 再沿着这条链，经过层层处理，最后形成响应，返回给客户端。

在编写一个Filter时，一般来说只需要编写`doFilter()`方法即可，如下代码所示

```java
	@Override
	public void doFilter(ServletRequest req, ServletResponse resp, FilterChain chain) throws IOException, ServletException {
		System.out.println("before request is handled");
		chain.doFilter(req, resp);
        System.out.println("after request is handled");
	}
```

需要注意的是，Filter，不只是能在request被处理前，进行一些前置操作，也可以在request被处理完毕，已经组装好response对象后，进行一些后置操作。并且要特别注意，在Filter的`doFilter`方法中，一定要调用`FilterChain`对象上面的`doFilter`方法，才能将这一组request/response对象在链上传递下去，最终到达要请求的**web资源**

#### 应用场景

Filter的常见应用场景列举如下

- 进行日志记录（记录被调用的接口，请求参数，以便进行问题排查，数据统计等工作）

- 统一设置编码格式（如处理中文乱码问题等）
- 过滤敏感词汇
- 数据压缩
- 数据加密
- 身份认证

### Listener

监听器。用于监听一些重要事件的发生。Listener对象可以在事件发生之前，之后，做一些额外的操作。

实现Servlet API中提供的一系列Listener接口，即可监听到对应的事件。

Servlet API中针对如下对象，提供了监听器（主要是监听对象的创建，销毁，和对象中属性的更改）

- `ServletContext`对象

  `ServletContextListener`，`ServletContextAttributeListener`

- `HttpSession`对象

  `HttpSessionListener`，`HttpSessionAttributeListener`，`HttpSessionIdListener`

- `ServletRequest`对象

  `ServletRequestListener`，`ServletRequestAttributeListener`

比如，可以监听`HttpSession`对象的创建和销毁，来实现一个网站在线人数统计的功能（若用户请求的是一个JSP页面，JSP页面在默认情况下，会为每一个新的请求创建一个session对象，这是下面进行人数统计的基础，这个默认行为，可以通过JSP指令`<%@ page session="false" %>`来取消。注：session对象是JSP页面的9大内置对象之一，正是因为JSP默认为每个请求生成了一个session对象，所以才能在JSP页面中直接使用这个session对象）

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





### JSP

Java Server Page

#### 特点

- 是一种动态资源（不要将JSP等同于HTML，jsp实际并不是静态的页面文件）
- 运行在服务端
- 本质是Servlet

#### 由来

为什么有JSP？

在早期的JavaWeb开发时，程序员需要在Servlet中手动拼接HTML标签和动态的数据，如下

```java
@WebServlet(urlPatterns = "/page")
public class PageServlet extends HttpServlet {
	protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		response.setCharacterEncoding("utf-8");
		PrintWriter writer = response.getWriter();
		String data = "你好";
		writer.write("<html>\n");
		writer.write(" <head>\n");
		writer.write("   <body>\n");
		writer.write("    <h1>\n");
		writer.write(data);
		writer.write("    </h1>\n");
		writer.write("   </body>\n");
		writer.write(" </head>\n");
		writer.write("</html>\n");
	}

	protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		doPost(request, response);
	}
}
```

可谓非常反人类了，这也导致了当时很多java阵营的web开发，投入了php，asp等动态语言的怀抱

#### 本质

于是，Java官方推出了JSP这一技术，声称可以直接在HTML页面中书写Java代码，这就避免了原先的需要手动拼接HTML标签的麻烦。比如，可以有如下的JSP文件

```jsp
<!-- demo.jsp -->
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    <%
        String user = request.getParameter("user");
        String password = request.getParameter("password");
        // 执行数据库查询
    	// 得到账户余额
        int remainMoney = 100;
    %>
<h1>你好, <%= user%>, 你账户上还剩 <%= remainMoney%> 元</h1>
</body>
</html>
```

当访问上面这个`demo.jsp`时，会由JSP引擎，针对该JSP文件，生成一个Servlet类，在Servlet类中完成处理，并返回响应。用浏览器访问，结果如下

![](https://img-blog.csdnimg.cn/20210118163138276.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZjajEwMDk3ODQ4MTQ=,size_16,color_FFFFFF,t_70)

此时，查看IDEA的tomcat工作目录（位置是：`C:\Users\用户名\.IntelliJIdeaxxx\system\tomcat\项目名\work\`），可以看到，针对`demo.jsp`，生成了一个java类`demo_jsp`

![](https://img-blog.csdnimg.cn/20210118163254750.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZjajEwMDk3ODQ4MTQ=,size_16,color_FFFFFF,t_70)

查看这个类

![](https://img-blog.csdnimg.cn/20210118163435774.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZjajEwMDk3ODQ4MTQ=,size_16,color_FFFFFF,t_70)

可以看到核心的代码，还是进行了HTML标签的拼接，只不过这个拼接过程是程序自动完成的而已。

我们可以看到，这个类继承了`HttpJspBase`

![](https://img-blog.csdnimg.cn/20210118163538918.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZjajEwMDk3ODQ4MTQ=,size_16,color_FFFFFF,t_70)

而`HttpJspBase`这个类，在tomcat的lib下的`jasper.jar`这个包下

![](https://img-blog.csdnimg.cn/20210118163821600.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZjajEwMDk3ODQ4MTQ=,size_16,color_FFFFFF,t_70)

可以看到这个`HttpJspBase`实现了`HttpServlet`，即它也是个Servlet。所以我们说，JSP的本质就是Servlet。

当我们在浏览器访问`http://localhost:8080/demo.jsp`时，注意，实际展示的并不是`demo.jsp`这个页面，而是根据`demo.jsp`这个urlPattern，匹配到了tomcat中默认的一个Servlet。我们打开tomcat的conf目录下的`web.xml`，可以看到有如下的配置

![](https://img-blog.csdnimg.cn/20210118164205431.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZjajEwMDk3ODQ4MTQ=,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20210118164121469.png)

可以看到，对于以`.jsp`或`.jspx`结尾的url，会交给一个名为`jsp`的servlet处理，这个servlet是`org.apache.jasper.servlet.JspServlet`对象。这个对象，会解析jsp文件，并生成了对应的Java类（Servlet），并调用这个Servlet完成对请求的处理。这就是JSP的本质。

**小结**：

JSP的本质是：当请求一个JSP文件时，由tomcat内置的JSP引擎（`JspServlet`），解析该JSP文件，生成一个对应的Servlet类，并执行这个Servlet类，完成对请求的响应。

#### 使用

JSP页面中，可同时编写HTML标签，Java代码，以及一些JSP的代码。下面对JSP的使用进行基本介绍

##### JSP指令

又叫JSP编译指令，采用`<%@ code %>`包裹，一般放在JSP页面最前面，它的作用是，通知JSP引擎，告诉JSP引擎，在解析本JSP文件时，需要对何种属性进行何种设置。

包含了3个指令，**page**，**include**，**taglib**

###### page

这个指令，主要用于在JSP转换为servlet类时，进行一些属性配置，支持对如下属性进行配置

- contentType：等同于response.setContentType
- language：指定JSP页面所使用的脚本语言（默认是java，目前也只支持java，= =！汗）
- import：导入需要的java类
- pageEncoding：页面的编码格式
- session：用来控制请求jsp页面时是否会自动创建session对象
- isErrorPage：标识该jsp是否是错误页面
- errorPage：当前jsp执行java代码出现异常后，需要跳转到的页面

在IDEA里可以查看page支持的所有属性

![](https://img-blog.csdnimg.cn/20210118164916281.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZjajEwMDk3ODQ4MTQ=,size_16,color_FFFFFF,t_70)

若遇到JSP页面的中文乱码，可以通过如下page指令进行编码设置

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
```

###### include

用于在当前JSP页面中，导入另外的JSP页面，使用方法如下

```jsp
<!-- demo.jsp中 -->
<%@ include file="index.jsp" %>
```

注意，这是静态导入。这也就是说，当用户请求主JSP页面时，不会对导入的从JSP另外生成一个servlet，而是会将从JSP的内容，合并到主JSP生成的servlet中。以上面的代码为例，当用户请求`demo.jsp`时，tomcat只会为`demo.jsp`生成一个servlet，而`index.jsp`中的内容，会并入到`demo.jsp`对应的servlet中。这样做，好处是，运行效率高；坏处是，若主jsp文件和从jsp文件中定义了相同名称的变量，方法等，则会产生冲突，报错，增大了维护jsp的成本。示例如下，现有一个`my.jsp`，内容如下

```jsp
<!-- my.jsp -->
<%@page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
</head>
<body>
    This is my.jsp
<%@include file="static.jsp"%>
</body>
</html>
```

其中`static.jsp`的内容如下

```jsp
<!-- static.jsp -->
<%@page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
</head>
<body>
我是静态导入页面
</body>
</html>
```

访问my.jsp，能够看到如下页面

![](https://img-blog.csdnimg.cn/20210118165908473.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZjajEwMDk3ODQ4MTQ=,size_16,color_FFFFFF,t_70)

找到IDEA的tomcat的work目录，能够看到只生成了my.jsp对应的servlet文件（这也就是说，不会对导入的static.jsp另外生成一个servlet）

![](https://img-blog.csdnimg.cn/20210118165947466.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZjajEwMDk3ODQ4MTQ=,size_16,color_FFFFFF,t_70)

`my.jsp`对应的servlet代码如下

![](https://img-blog.csdnimg.cn/20210118170021489.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZjajEwMDk3ODQ4MTQ=,size_16,color_FFFFFF,t_70)

既然有静态导入，那么相对应的，就有动态导入，动态导入是通过JSP动作标签`<jsp:include>`来进行的，如下所示

```jsp
<jsp:include page="dynamic.jsp"/>
```

动态导入，主JSP文件和从JSP文件，都会分别生成属于自己的servlet文件。2个servlet一前一后，分别执行，页面在最终显示的时候是合并的。若采用动态导入，则两个JSP页面完全独立，若定义了相同名称的变量，方法等，则不会发生冲突。动态导入的优点是，2个JSP页面没有耦合，可维护性好，至于缺点，由于要分别生成2个servlet，自然性能要略差于**静态导入**。

演示如下

有一个`my.jsp`文件，其内容如下

```jsp
<!-- my.jsp -->
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
</head>
<body>
This is my.jsp
<jsp:include page="dynamic.jsp"/>
</body>
</html>
```

其中导入的`dynamic.jsp`的内容如下

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
</head>
<body>
我是动态导入页面
</body>
</html>
```

请求`my.jsp`，效果如下

![](https://img-blog.csdnimg.cn/20210118170442568.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZjajEwMDk3ODQ4MTQ=,size_16,color_FFFFFF,t_70)

找到IDEA的tomcat的work目录，可以看到生成了2个servlet

![](https://img-blog.csdnimg.cn/20210118170508950.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZjajEwMDk3ODQ4MTQ=,size_16,color_FFFFFF,t_70)

查看`my.jsp`对应的servlet，内容如下

![](https://img-blog.csdnimg.cn/20210118170609668.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZjajEwMDk3ODQ4MTQ=,size_16,color_FFFFFF,t_70)

动态导入和静态导入对比

|               | 动态导入                                  | 静态导入                           |
| ------------- | ----------------------------------------- | ---------------------------------- |
| 使用方法      | <jsp:include page="dynamic.jsp"/>         | <%@include file="static.jsp"%>     |
| 运行效率      | 略差                                      | 略优                               |
| JSP页面耦合度 | 耦合度低，JSP页面之间相互独立，可维护性好 | 耦合度高，可维护性差               |
| servlet个数   | 生成2个servlet                            | 只生成1个servlet                   |
| 同名变量      | 2个jsp页面中可以有同名变量，方法等        | 2个jsp页面中不能有同名变量，方法等 |

###### taglib

用于引入自定义标签库，如引入JSTL标签库

```jsp
<%@taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core"%>
```

随后在JSP页面中，即可以`c`为前缀，使用JSTL的核心标签库，如

```jsp
<c:set var="a" value="10"/>
<c:if test="${a > 5}" var="result" scope="request">
    <h1>a = ${a} , JSTL is easy</h1>
</c:if>
```

##### JSP脚本

上面说到，JSP页面中，也可以编写Java代码。

JSP中的Java代码片段，称为JSP脚本，目前JSP脚本支持的语言只有Java。

JSP中的Java代码片段，需要用`<% %>`进行包裹，共分为3种

- `<% code %>`：普通Java代码。
- `<%= code %>`：Java表达式
- `<%! code %>`：用于定义类的成员变量和成员方法等

用一个实际的示例来进行说明，假设有如下的jsp文件`demo.jsp`

```jsp
<%@ page import="java.time.format.DateTimeFormatter" %>
<%@ page import="java.time.LocalDateTime" %>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
<%!
    private static final DateTimeFormatter dtf;
    
    static {
        dtf = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss");
    }
    
    public static String now() {
    	return dtf.format(LocalDateTime.now());
    }
%>

<%
    String user = request.getParameter("user");
    String password = request.getParameter("password");
    // 执行数据库查询
    // 得到账户余额
    int remainMoney = 100;
%>
<h1>你好, 当前时间是: <%= now()%> , 你的账户名是 <%= user%>, 你账户上还剩 <%= remainMoney%> 元</h1>
</body>
</html>
```

上面的JSP文件包含了3种类型的Java代码片段。我们用浏览器访问`demo.jsp`，结果如下

![](https://img-blog.csdnimg.cn/20210120103947813.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZjajEwMDk3ODQ4MTQ=,size_16,color_FFFFFF,t_70)

查看`demo.jsp`编译生成的servlet代码，如下（注意查看其中的注释，注释为手动添加）

```java
/*
 * Generated by the Jasper component of Apache Tomcat
 * Version: Apache Tomcat/8.5.47
 * Generated at: 2021-01-20 02:38:55 UTC
 * Note: The last modified time of this file was set to
 *       the last modified time of the source file after
 *       generation to assist with modification tracking.
 */
package org.apache.jsp;

import javax.servlet.*;
import javax.servlet.http.*;
import javax.servlet.jsp.*;
import java.time.format.DateTimeFormatter; // 这里是 page 指令设置import属性, 导入的java类
import java.time.LocalDateTime;   // 这里是 page 指令设置import属性, 导入的java类

public final class demo_jsp extends org.apache.jasper.runtime.HttpJspBase
    implements org.apache.jasper.runtime.JspSourceDependent,
                 org.apache.jasper.runtime.JspSourceImports {

    /** <!% code %> 中的Java代码，会被添加到定义类的成员的地方, 也就是这里 **/
    private static final DateTimeFormatter dtf;

    static {
        dtf = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss");
    }

    public static String now() {
    	return dtf.format(LocalDateTime.now());
    }
    /** <!% code %> 中的Java代码，会被添加到定义类的成员的地方, 也就是这里 **/

  private static final javax.servlet.jsp.JspFactory _jspxFactory =
          javax.servlet.jsp.JspFactory.getDefaultFactory();

  private static java.util.Map<java.lang.String,java.lang.Long> _jspx_dependants;

  private static final java.util.Set<java.lang.String> _jspx_imports_packages;

  private static final java.util.Set<java.lang.String> _jspx_imports_classes;

  static {
    _jspx_imports_packages = new java.util.HashSet<>();
    _jspx_imports_packages.add("javax.servlet");
    _jspx_imports_packages.add("javax.servlet.http");
    _jspx_imports_packages.add("javax.servlet.jsp");
    _jspx_imports_classes = new java.util.HashSet<>();
    _jspx_imports_classes.add("java.time.format.DateTimeFormatter");
    _jspx_imports_classes.add("java.time.LocalDateTime");
  }

  private volatile javax.el.ExpressionFactory _el_expressionfactory;
  private volatile org.apache.tomcat.InstanceManager _jsp_instancemanager;

  public java.util.Map<java.lang.String,java.lang.Long> getDependants() {
    return _jspx_dependants;
  }

  public java.util.Set<java.lang.String> getPackageImports() {
    return _jspx_imports_packages;
  }

  public java.util.Set<java.lang.String> getClassImports() {
    return _jspx_imports_classes;
  }

  public javax.el.ExpressionFactory _jsp_getExpressionFactory() {
    if (_el_expressionfactory == null) {
      synchronized (this) {
        if (_el_expressionfactory == null) {
          _el_expressionfactory = _jspxFactory.getJspApplicationContext(getServletConfig().getServletContext()).getExpressionFactory();
        }
      }
    }
    return _el_expressionfactory;
  }

  public org.apache.tomcat.InstanceManager _jsp_getInstanceManager() {
    if (_jsp_instancemanager == null) {
      synchronized (this) {
        if (_jsp_instancemanager == null) {
          _jsp_instancemanager = org.apache.jasper.runtime.InstanceManagerFactory.getInstanceManager(getServletConfig());
        }
      }
    }
    return _jsp_instancemanager;
  }

  public void _jspInit() {
  }

  public void _jspDestroy() {
  }

  public void _jspService(final javax.servlet.http.HttpServletRequest request, final javax.servlet.http.HttpServletResponse response)
      throws java.io.IOException, javax.servlet.ServletException {

    final java.lang.String _jspx_method = request.getMethod();
    if (!"GET".equals(_jspx_method) && !"POST".equals(_jspx_method) && !"HEAD".equals(_jspx_method) && !javax.servlet.DispatcherType.ERROR.equals(request.getDispatcherType())) {
      response.sendError(HttpServletResponse.SC_METHOD_NOT_ALLOWED, "JSPs only permit GET POST or HEAD");
      return;
    }

    final javax.servlet.jsp.PageContext pageContext;
    javax.servlet.http.HttpSession session = null;
    final javax.servlet.ServletContext application;
    final javax.servlet.ServletConfig config;
    javax.servlet.jsp.JspWriter out = null;
    final java.lang.Object page = this;
    javax.servlet.jsp.JspWriter _jspx_out = null;
    javax.servlet.jsp.PageContext _jspx_page_context = null;


    try {
      response.setContentType("text/html;charset=UTF-8"); // 这里就是 page 指令中设置的Content-Type属性
      pageContext = _jspxFactory.getPageContext(this, request, response,
      			null, true, 8192, true);
      _jspx_page_context = pageContext;
      application = pageContext.getServletContext();
      config = pageContext.getServletConfig();
      session = pageContext.getSession();
      out = pageContext.getOut();
      _jspx_out = out;

      out.write("\n");
      out.write("\n");
      out.write("\n");
      out.write("<html>\n");
      out.write("<head>\n");
      out.write("    <title>Title</title>\n");
      out.write("</head>\n");
      out.write("<body>\n");
      out.write('\n');
      out.write('\n');

    /** 下面就是 <% code %> 中的 Java 代码 **/
    String user = request.getParameter("user");
    String password = request.getParameter("password");
    // 执行数据库查询
    // 得到账户余额
    int remainMoney = 100;
    /** 上面就是 <% code %> 中的 Java 代码 **/

      out.write("\n");
      out.write("<h1>你好, 当前时间是: ");
      out.print( now());   /** 这里就是 <%= code %> 中的内容, 是个Java表达式，会以这种形式追加到HTML数据中 **/
      out.write(" , 你的账户名是 ");
      out.print( user);   /** 这里就是 <%= code %> 中的内容, 是个Java表达式，会以这种形式追加到HTML数据中 **/
      out.write(", 你账户上还剩 ");
      out.print( remainMoney);  /** 这里就是 <%= code %> 中的内容, 是个Java表达式，会以这种形式追加到HTML数据中 **/
      out.write(" 元</h1>\n");
      out.write("</body>\n");
      out.write("</html>");
    } catch (java.lang.Throwable t) {
      if (!(t instanceof javax.servlet.jsp.SkipPageException)){
        out = _jspx_out;
        if (out != null && out.getBufferSize() != 0)
          try {
            if (response.isCommitted()) {
              out.flush();
            } else {
              out.clearBuffer();
            }
          } catch (java.io.IOException e) {}
        if (_jspx_page_context != null) _jspx_page_context.handlePageException(t);
        else throw new ServletException(t);
      }
    } finally {
      _jspxFactory.releasePageContext(_jspx_page_context);
    }
  }
}
```

对于需要通过Java代码来控制是否需要展示的HTML标签，可以像下面这样写（可见非常不方便，要反复的使用`<% %>`）

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<body>
<%
    String str = request.getParameter("score");
    int score = Integer.parseInt(str);
%>
<h3>你的成绩是: <%= score%></h3>
<%
    if (score > 90) {
%>
<h1>优秀！</h1>
<% } %>
</body>
</html>
```

访问：

![](https://img-blog.csdnimg.cn/20210120105852787.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZjajEwMDk3ODQ4MTQ=,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20210120105918620.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZjajEwMDk3ODQ4MTQ=,size_16,color_FFFFFF,t_70)



##### JSP内置对象

观察上面JSP文件转化成的servlet文件，我们会发现servlet文件中自动帮我们创建了一些对象。

下面截取一个自动生成的servlet文件中的`_jspService`方法（这个方法其实就相当于Servlet中的`service`方法，具体原理可以查看tomcat的lib目录中`jasper.jar`中的`org.apache.jasper.runtime.HttpJspBase`，所有JSP文件转化成的servlet，都是继承自这个类）

```java
public void _jspService(final javax.servlet.http.HttpServletRequest request, final javax.servlet.http.HttpServletResponse response)
      throws java.io.IOException, javax.servlet.ServletException {

    final java.lang.String _jspx_method = request.getMethod();
    if (!"GET".equals(_jspx_method) && !"POST".equals(_jspx_method) && !"HEAD".equals(_jspx_method) && !javax.servlet.DispatcherType.ERROR.equals(request.getDispatcherType())) {
      response.sendError(HttpServletResponse.SC_METHOD_NOT_ALLOWED, "JSPs only permit GET POST or HEAD");
      return;
    }

    final javax.servlet.jsp.PageContext pageContext;
    javax.servlet.http.HttpSession session = null;
    final javax.servlet.ServletContext application;
    final javax.servlet.ServletConfig config;
    javax.servlet.jsp.JspWriter out = null;
    final java.lang.Object page = this;
    javax.servlet.jsp.JspWriter _jspx_out = null;
    javax.servlet.jsp.PageContext _jspx_page_context = null;


    try {
      response.setContentType("text/html;charset=UTF-8"); // 这里就是 page 指令中设置的Content-Type属性
      pageContext = _jspxFactory.getPageContext(this, request, response,
      			null, true, 8192, true);
      _jspx_page_context = pageContext;
      application = pageContext.getServletContext();
      config = pageContext.getServletConfig();
      session = pageContext.getSession();
      out = pageContext.getOut();
      _jspx_out = out;
      // 剩余代码略...
    }
}
```

可以看到，在`service`方法中，为我们自动生成了不少对象，比如`pageContext`，`session`，`application`等，这也就使得，我们可以在JSP页面中，直接使用这些对象，如下图

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<body>
<%
    request.setAttribute("user", "Ezreal");
    pageContext.setAttribute("game", "League of Legends");
    session.setAttribute("ult", "精准弹幕");
    response.addCookie(new Cookie("tips", "我是一块小饼干"));
%>
</body>
</html>
```

这些自动生成的对象，称为JSP内置对象，共有9个，在JSP生成的servlet中，它们的变量名称及对应的类分别如下

- `request`：`HttpServletRequest`对象，封装了请求的数据
- `response`：`HttpServletResponse`对象，封装了响应的数据
- `session`：`HttpSession`对象
- `pageContext`：`PageContext`对象，当前JSP页面上下文
- `application`：`ServletContext`对象，web应用全局上下文
- `out`：`JspWriter`对象，用于将数据输出到页面
- `config`：`ServletConfig`对象，当前servlet的配置
- `page`：相当于当前servlet的this指针
- `exception`：异常对象，当当前JSP页面被标记为错误页面时（设置isErrorPage=true），才有该对象

可以自己尝试使用page命令对JSP页面进行配置，然后观察生成的servlet类。比如，设置

```jsp
<%@page session="false"%>
```

可以观察到生成的servlet类中，就不会自动创建`HttpSession`对象了

##### JSP作用域

在9大内置对象中，有4个对象可以用来存储数据，这4个对象又称为4大**作用域对象**，它们分别是

- `pageContext`
- `request`
- `session`
- `application`

这些对象中都有`setAttribute`和`getAttribute`方法，可以往对象中设置数据

EL表达式可以很方便的从域对象中获取数据

##### EL表达式

Express Language ，它是一种表达式语言，其作用是为了方便JSP页面从域对象中获取数据

下面分别展示了使用原始方式，和使用EL表达式方式来从域对象中取值。可见，EL表达式更加的简便

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<body>
<% request.setAttribute("location","广州"); %>
原始方式：<br/>
name: <%= request.getParameter("name")%>  <!-- 取HTTP请求中的参数 -->
pwd: <%= request.getParameter("pwd")%>
location: <%= request.getAttribute("location")%> <!-- 取request域对象中的location属性 -->
<br/>
EL表达式：<br/>
name: ${param.name}  <!-- 取HTTP请求中的参数 -->
pwd: ${param.pwd}
location: ${location} <!-- 取域对象中的location属性 -->
</body>
</html>
```

效果是一样的

![](https://img-blog.csdnimg.cn/20210120142548937.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZjajEwMDk3ODQ4MTQ=,size_16,color_FFFFFF,t_70)

###### EL内置对象

与JSP类似，EL表达式也有一些内置对象可以直接拿来使用，它们都是

- **pageScope**：JSP中的`pageContext`域对象

- **requestScope**：JSP中的`request`域对象

- **sessionScope**：JSP中的`session`域对象

- **applicationScope**：JSP中的`application`域对象

- **param**：是一个`Map<String,String>`，里面存储了request中的全部参数。如`${param.user}`相当于`request.getParameter("user")`

- **paramValues**：和param类似，不过它是一个`Map<String,String[]>`，假设查询参数`name`有多个值，则可以通过paramValues来获取多个值，param则只会取第一个值

  ```jsp
  <%@ page contentType="text/html;charset=UTF-8" language="java" %>
  <html>
  <body>
  param: ${param.name}
  <br/>
  paramValues: ${paramValues.name[0]}, ${paramValues.name[1]}
  </body>
  </html>
  ```

  ![](https://img-blog.csdnimg.cn/20210120145320527.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZjajEwMDk3ODQ4MTQ=,size_16,color_FFFFFF,t_70)

- **initParam**：获取初始化参数，相当于`ServletContext.getInitParameter`

  示例如下

  `web.xml`中配置初始化参数

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
           version="4.0">
      <context-param>
          <param-name>name</param-name>
          <param-value>jack</param-value>
      </context-param>
  </web-app>
  ```

  JSP中获取初始化参数

  ```jsp
  <%@ page contentType="text/html;charset=UTF-8" language="java" %>
  <html>
  <body>
  initParam: ${initParam.name}
  </body>
  </html>
  ```

  访问

  ![](https://img-blog.csdnimg.cn/20210120145934202.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZjajEwMDk3ODQ4MTQ=,size_16,color_FFFFFF,t_70)

- **header**：请求头，是一个`Map<String,String>`

  示例如下

  ```jsp
  <%@ page contentType="text/html;charset=UTF-8" language="java" %>
  <html>
  <body>
  accept: ${header.accept} <br/>
  user-agent: ${header["user-agent"]} <br/>
  </body>
  </html>
  ```

  访问

  ![](https://img-blog.csdnimg.cn/20210120150351789.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZjajEwMDk3ODQ4MTQ=,size_16,color_FFFFFF,t_70)

- **headerValues**：与header类似，不过它是个`Map<String, String[]>`，当某一请求头部有多个值时，可用该对象获取

- **cookie**：获取请求中携带的Cookie

  示例如下

  JSP文件

  ```jsp
  <%@ page contentType="text/html;charset=UTF-8" language="java" %>
  <html>
  <body>
  <% response.addCookie(new Cookie("rookie", "我这块小饼干叫rookie"));%>
  cookieName: ${cookie.rookie.name} <br/>
  cookieValue: ${cookie.rookie.value}
  </body>
  </html>
  
  ```

  第一次访问时，没有Cookie

  ![](https://img-blog.csdnimg.cn/20210120151606583.png)

  

  第二次访问时，会携带第一次访问得到的Cookie

  ![](https://img-blog.csdnimg.cn/20210120151717934.png)

  第二次访问时的抓包

  ![](https://img-blog.csdnimg.cn/20210120151920433.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZjajEwMDk3ODQ4MTQ=,size_16,color_FFFFFF,t_70)

  

- **pageContext**：与JSP的`pageContext`对象是同一个，可以用它来获取JSP的其他对象，如`request`，`response`，`session`，`servletContext`，`servletConfig`等对象。注意，EL中只有`pageContext`内置对象，其他那些对象只能通过`pageContext`来获取，比如`${pageContext.request.contextPath}`

###### EL取值方式

EL表达式进行取值时，会先尝试匹配内置对象，若没有匹配到内置对象，则再从4大作用域对象中取值，示例如下

```jsp
${param.name} <!-- 相当于request.getParameter("name") -->
```

```jsp
${hi} <!-- 内置对象中没有叫hi的，故尝试从作用域对象中取值 -->
```

从作用域对象中取值时，先从作用域范围小的对象中取，若没取到，再去作用域范围更大的对象中取。4大作用域对象的范围从小到大依次是：`pageContext`，`request`，`session`，`application`

也可以明确指定取某个域对象中的属性，比如`${sessionScope.userId}`。注意，JSP中的4大域对象的名称，和EL中的有所不同

下面是用于验证的JSP代码

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" pageEncoding="utf-8" %>
<html>
<body>
<!-- 使用el表达式获取数据的顺序 -->
<%
    pageContext.setAttribute("hi","hi, pageContext");
    request.setAttribute("hi","hi, request");
    session.setAttribute("hi","hi, session");
    application.setAttribute("hi","hi, application");
%>
hi : ${hi}<br/> <!-- 会取到pageContext中的hi属性 -->

pageContext: ${pageScope.hi}<br/> <!-- 明确指定取 pageContext域对象中的hi --> 
request : ${requestScope.hi}<br/> <!-- 明确指定取 request域对象中的hi -->
session: ${sessionScope.hi}<br/>
application: ${applicationScope.hi}<br/>
</body>
</html>

```

访问

![](https://img-blog.csdnimg.cn/20210120154542730.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZjajEwMDk3ODQ4MTQ=,size_16,color_FFFFFF,t_70)

域对象中的属性，也可以设置为Java对象，比如，有如下的类`User`和`Address`

```java
public class User {
	private String name;
	private Address address;
    // 省略构造函数, get/set函数，toString函数等
}
```

```java
public class Address {
	private String province;
	private String city;
	private String town;
    // 省略构造函数, get/set函数，toString函数等
}
```

JSP中进行如下设置

```jsp
<%@ page import="po.User" %>
<%@ page import="po.Address" %>
<%@ page contentType="text/html;charset=UTF-8" language="java" pageEncoding="utf-8" %>
<html>
<body>
<%
    Address address = new Address("四川","绵阳","江油");
    User user = new User("yogurt", address);
    request.setAttribute("user", user);
%>

name : ${user.name} <br/> <!-- 也可以用 ${user["name"]} -->
address: <br/>
&nbsp;&nbsp;- province: ${user.address.province} <br/> <!-- 也可以用 ${user["address"]["province"]} -->
&nbsp;&nbsp;- city: ${user.address.city} <br/>
&nbsp;&nbsp;- town: ${user.address.town} <br/>
</body>
</html>
```

访问

![](https://img-blog.csdnimg.cn/20210120155207619.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZjajEwMDk3ODQ4MTQ=,size_16,color_FFFFFF,t_70)

取JavaBean中的属性，可以用点号`.`，后面接属性的名称，当然也可以用`[]`，括号内是属性的名称（用双引号括起来）

取Map中的值也是如此，只是传递的不是属性名称，而是key。

取数组或者List中的值，则是用`[]`加上元素下标

除了支持取值，EL表达式还能进行一些基本的算术和逻辑运算，比如

```jsp
<%@ page contentType="text/html;charset=UTF-8" pageEncoding="utf-8" %>
<html>
<body>
${1+2}<br/>
${1-2}<br/>
${1*2}<br/>
${1/2}<br/>
${1%2}<br/>
${1>2}<br/>
${1>2?"南":"铝"}<br/>
${1+"2"}<br/>  <!-- 会自动将字符串2转为数字，进行运算，而不是做字符串拼接 -->
${true && false}  <!-- 支持逻辑运算 -->
</body>
</html>
```

![](https://img-blog.csdnimg.cn/20210120161740844.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZjajEwMDk3ODQ4MTQ=,size_16,color_FFFFFF,t_70)

##### JSP动作元素

JSP指令是在JSP编译时执行，而JSP动作元素是在处理请求时执行。可以用JSP动作元素动态导入页面文件，获取JavaBean，进行页面转发等

- `<jsp:forward>`：页面转发
- `<jsp:include>`：页面动态导入
- `<jsp: useBean>`：寻找或实例化一个JavaBean
- `<jsp:setProperty>`：设置JavaBean中的属性
- `<jsp:getProperty>`：获取JavaBean中的属性
- ... 

其他一些已经不怎么使用的元素就不一一列出



##### JSTL标签库

当需要在JSP页面中，对EL表达式编写一些逻辑判断时，若直接嵌入Java代码，则使得逻辑代码和页面展示耦合在一起，代码可读性变差，JSTL标签库就是用来改善这种问题的。

JSTL是对EL的扩展，下面对JSTL的使用做简单说明。首先需要进行JSTL的相关配置。

###### 配置

1. 首先，下载JSTL标签库依赖，下载地址：http://archive.apache.org/dist/jakarta/taglibs/standard/binaries/，拉到页面最下方，下载最新的版本即可，或者直接使用http://archive.apache.org/dist/jakarta/taglibs/standard/binaries/jakarta-taglibs-standard-1.1.2.zip

2. 解压后，在lib目录中找到`jstl.jar`和`standard.jar`

   ![](https://img-blog.csdnimg.cn/20210120162141332.png)

3. 将这两个jar包添加到项目的依赖项中

   ![](https://img-blog.csdnimg.cn/20210120162239393.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZjajEwMDk3ODQ4MTQ=,size_16,color_FFFFFF,t_70)

   ![](https://img-blog.csdnimg.cn/20210120162251844.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZjajEwMDk3ODQ4MTQ=,size_16,color_FFFFFF,t_70)

   ![](https://img-blog.csdnimg.cn/20210120162304681.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZjajEwMDk3ODQ4MTQ=,size_16,color_FFFFFF,t_70)

   ![](https://img-blog.csdnimg.cn/20210120162318249.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZjajEwMDk3ODQ4MTQ=,size_16,color_FFFFFF,t_70)

   在项目左侧的External Library 中看到有如下2个包，则引入成功

   ![](https://img-blog.csdnimg.cn/20210120162334904.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZjajEwMDk3ODQ4MTQ=,size_16,color_FFFFFF,t_70)

3. 在JSP页面，通过`<%@taglib %>`来引入JSTL标签，引入方式如下

   ```jsp
   <%@taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
   ```

   引入后，即可在JSP页面中，用前缀c，来使用JSTL标签，如下（前缀c是自定义的，不是固定为c，可以为其他任何命名，通常命名为c，表示core，JSTL的核心库，JSTL除了core核心库，还有一些其他库）

   ```jsp
   <%@ page contentType="text/html;charset=UTF-8" language="java" %>
   <%@taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
   <html>
   <head>
       <title>Title</title>
   </head>
   <body>
   <c:out value="xixi"/>
   </body>
   </html>
   
   ```

   如果在IDEA中，发现`<%@taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>`

   这一行代码标红，则添加如下配置

   左上角 File -> Settings ->  Language & Framework  ->  Schemas and DTDs  -> 点击 `+` 号

   ![](https://img-blog.csdnimg.cn/20210120162713614.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZjajEwMDk3ODQ4MTQ=,size_16,color_FFFFFF,t_70)

   ![](https://img-blog.csdnimg.cn/20210120162752178.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZjajEwMDk3ODQ4MTQ=,size_16,color_FFFFFF,t_70)

   URI填写 JSTL的DTD地址，File选择先前下载的 jakarta-taglibs-standard包中的tld目录下的`c.tld`

   ![](https://img-blog.csdnimg.cn/20210120162837808.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZjajEwMDk3ODQ4MTQ=,size_16,color_FFFFFF,t_70)

   点击Apply

   ![](https://img-blog.csdnimg.cn/20210120162921881.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZjajEwMDk3ODQ4MTQ=,size_16,color_FFFFFF,t_70)

   若启动程序后，访问JSP页面，发现如下报错

   ![](https://img-blog.csdnimg.cn/20210120163021906.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZjajEwMDk3ODQ4MTQ=,size_16,color_FFFFFF,t_70)

   其实是部署的web项目中，没有standara.jar包中的文件

   我们点击左上角 File -> Project Structure -> Artifacts ，能看到提示说 standard.jar is missing

   ![](https://img-blog.csdnimg.cn/20210120163125161.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZjajEwMDk3ODQ4MTQ=,size_16,color_FFFFFF,t_70)

   我们直接点击右侧的 Fix，选择第二个，将所有缺失的依赖jar包添加进去即可

   ![](https://img-blog.csdnimg.cn/2021012016321487.png)

   最后点击 Apply，展开WEB-INF下的lib目录，看到已经将 `jstl.jar`和`standard.jar`添加了进去

   ![](https://img-blog.csdnimg.cn/20210120163305684.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZjajEwMDk3ODQ4MTQ=,size_16,color_FFFFFF,t_70)

   再次启动服务，访问对应页面，成功

   ![](https://img-blog.csdnimg.cn/20210120163359445.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZjajEwMDk3ODQ4MTQ=,size_16,color_FFFFFF,t_70)

   至此，JSTL标签配置完成，下面可以进行JSTL的开发

###### 使用

JSTL的标签主要有以下几类

- **核心标签库**
- 格式化标签库
- 函数标签库
- XML标签库
- SQL标签库

其中，**核心标签库**，**格式化标签库**是最常用，其余的了解即可

下面对核心标签库中的标签进行简要介绍

- `<c:out>`

  输出标签。有2个属性

  - value：输出的值
  - default：输出值缺失的情况下的默认值

  示例：`<c:out value="xixi" default="default"/>`

- `<c:set>`

  向域对象中添加属性。有3个属性

  - var：属性的key
  - value：属性的值
  - scope：添加到哪个域对象（若不指定，默认添加到pageContext）

  示例：`<c:set var="name" value="yogurt" scope="request"/>`

  ```jsp
  <c:set var="name" value="yogurt"/>  <!-- 相当于 pageContext.setAttribute("name","yogurt") -->
  <% request.setAttribute("name","amber"); %>
  <c:out value="${name}"/> 
  <!-- 这里输出的应该是 yogurt 还是 amber 呢
   => 是 yogurt, 因为 c:set 标签当不指定 scope 时，默认是向 pageContext作用域对象中设置属性, 
   pageContext域对象范围小于request, 所以EL表达式优先取到pageContext中的值 
   -->
  ```

- `<c:remove>`

  从域对象中删除属性。有2个属性

  - var：需要删除的属性的key
  - scope：删除的是哪个域对象中的属性（若不指定，则默认删除所有域对象中的该属性）

  示例：`<c:remove var="userId" scope="request"/>`

- `<c:if>`

  相当于Java中的if代码块。有3个属性

  - test：条件表达式。当该表达式为true时，会执行`<c:if>`标签体里面的内容
  - var：通过该属性，可以将条件表达式的计算结果保存在某一个变量中
  - scope：与var一起使用，条件表达式结果保存在哪个域对象的属性中

  示例：

  ```jsp
  <c:set var="a" value="10"/>
  <c:if test="${a > 5}" var="result" scope="request">
      <h1>a = ${a} ,test is true, JSTL is easy</h1>
  </c:if>
  <% out.print("上面的c:if标签的条件表达式的结果是" + request.getAttribute("result")); %>
  ```

- `<c:choose>`

  相当于Java中的switch。这个标签有2个子标签`<c:when>`，`<c:otherwise>`

  示例：

  ```jsp
  <c:set var="age" value="15"/>
  <c:choose>
      <c:when test="${age < 10}">
          <h1>小孩</h1>
      </c:when>
      <c:when test="${age < 20}">
          <h1>少年</h1>
      </c:when>
      <c:when test="${age < 30}">
          <h1>青年</h1>
      </c:when>
      <c:when test="${age < 40}">
          <h1>中年</h1>
      </c:when>
      <c:otherwise>
          <h1>不知道</h1>
      </c:otherwise>
  </c:choose>
  ```

- `<c:forEach>`

  循环标签。该标签运用较多。它有如下几个属性

  - begin：起始位置下标
  - end：结束位置下标（inclusive）
  - step：步长
  - varStatus：循环状态
  - var：该轮循环的元素
  - items：用来遍历的集合

  示例

  ```jsp
  <!-- 循环标签 -->
  <%
      List<String> list = new ArrayList<>();
      list.add("a1");
      list.add("a2");
      list.add("a3");
      list.add("a4");
      list.add("a5");
      list.add("a6");
      request.setAttribute("list", list);
  %>
  <c:forEach begin="0" end="3" step="1" varStatus="status" var="element" items="${list}">
      <!--
      begin 起始下标
      end   终止下标(inclusive)
      step  步长
      如上面 begin=0 , end=3 , step=1, 其实相当于 java 中的 for(int i = 0 ; i <= 3; i++)
      -->
      <!-- varStatus 表示循环的状态, 其中有这么些值 -->
      index=${status.index} ---
      count=${status.count} ---
      step=${status.step} ---
      begin=${status.begin} ---
      end=${status.end} ---
      current=${status.current} ---
      first=${status.first} ---
      last=${status.last}
  
      element = ${element} <br/>
      <!-- items 表示要迭代的集合 -->
      <!-- var 表示的是集合中某一次迭代的元素 -->
      <!-- 通常进行循环只需要使用 items 指定要迭代的集合, 以及 var 存放每一轮迭代的元素, 即可, 若要获取某一轮迭代的相关变量, 如下标, 则可以用 varStatus, 而其他的属性如 begin, end, step 不是很常用 -->
  </c:forEach>
  ```

下面展示一个综合示例，使用JSTL标签进行动态表格绘制

```jsp
<%
    Map<String, Student> student = new HashMap<>();
    Student s1 = new Student("Yogurt", "24", "2", "Excellent");
    Student s2 = new Student("Dick", "27", "3", "Good");
    Student s3 = new Student("Bob", "26", "4", "Medium");
    Student s4 = new Student("Amber", "25", "2", "Excellent");
    student.put(s1.name, s1);
    student.put(s2.name, s2);
    student.put(s3.name, s3);
    student.put(s4.name, s4);
    request.setAttribute("student", student);
%>
<c:if test="${student != null}">
    <table border="1px">
        <tr>
            <th>name</th>
            <th>age</th>
            <th>grade</th>
            <th>level</th>
        </tr>
        <c:forEach items="${student}" var="one">
            <tr>
                <td>${one.value.name}</td> <!-- 注意, 这里获取 Student 中的 name 属性, 是通过 getName 方法去获取的, 下面获取其他属性同理, 如不添加 get 方法, 访问该 JSP 时, 会报错 -->
                <td>${one.value.age}</td>
                <td>${one.value.grade}</td>
                <td>${one.value.level}</td>
            </tr>
        </c:forEach>
    </table>
</c:if>
```



### 附录

#### 动态资源与静态资源<a id="dynamicResource">

- 静态资源

  所有用户访问该资源，得到的结果都是相同的，这个特点正如其名，静态。不会因为是不同用户访问，就返回不同的结果。

  静态资源的例子：文本，图片，音视频，HTML，CSS，JavaScript等

- 动态资源

  动态，顾名思义，是会变动的。不同的用户，访问该资源，得到的结果是不同的。或者不同的时间，访问该资源，得到的结果是不同的。简单来讲，动态资源=静态页面+动态数据。举个简单例子，如果有个资源，其内容为`你好，公子`。用户A请求该资源，得到的结果是`你好，公子`，用户B请求该资源，得到的也是`你好，公子`。这就是静态资源，它是始终不变的，所有用户请求都得到的是相同的结果。如果有另一个资源，其内容为`你好，${lastName}公子`，其中的`${lastName}`只是个占位符，具体数据需要从数据库动态查询。假设用户A姓黄，A请求该资源，得到的是`你好，黄公子`，假设用户B姓白，B请求该资源，得到的是`你好，白公子`。可见动态资源=静态部分+动态数据。

  动态资源的例子：JSP，Servlet，php等



#### Tomcat下载<a id="tomcat">

https://tomcat.apache.org/download-80.cgi

选择下载WINDOWS的ZIP包。下载完成后解压即可。Servlet，JSP等相关的依赖包，都能在tomcat的lib目录中找到

![image-20201231170847375](https://img-blog.csdnimg.cn/20210107153602692.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3ZjajEwMDk3ODQ4MTQ=,size_16,color_FFFFFF,t_70)

