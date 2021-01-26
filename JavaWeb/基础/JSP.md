# JSP

Java Server Page

## 特点

- 是一种动态资源（不要将JSP等同于HTML，jsp实际并不是静态的页面文件）
- 运行在服务端
- 本质是Servlet

## 由来

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

## 本质

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

## 使用

JSP页面中，可同时编写HTML标签，Java代码，以及一些JSP的代码。下面对JSP的使用进行基本介绍

### JSP指令

又叫JSP编译指令，采用`<%@ code %>`包裹，一般放在JSP页面最前面，它的作用是，通知JSP引擎，告诉JSP引擎，在解析本JSP文件时，需要对何种属性进行何种设置。

包含了3个指令，**page**，**include**，**taglib**

#### page

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

#### include

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

#### taglib

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

### JSP脚本

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



### JSP内置对象

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

### JSP作用域

在9大内置对象中，有4个对象可以用来存储数据，这4个对象又称为4大**作用域对象**，它们分别是

- `pageContext`
- `request`
- `session`
- `application`

这些对象中都有`setAttribute`和`getAttribute`方法，可以往对象中设置数据

EL表达式可以很方便的从域对象中获取数据

### EL表达式

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

#### EL内置对象

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

#### EL取值方式

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

### JSP动作元素

JSP指令是在JSP编译时执行，而JSP动作元素是在处理请求时执行。可以用JSP动作元素动态导入页面文件，获取JavaBean，进行页面转发等

- `<jsp:forward>`：页面转发
- `<jsp:include>`：页面动态导入
- `<jsp: useBean>`：寻找或实例化一个JavaBean
- `<jsp:setProperty>`：设置JavaBean中的属性
- `<jsp:getProperty>`：获取JavaBean中的属性
- ... 

其他一些已经不怎么使用的元素就不一一列出



### JSTL标签库

当需要在JSP页面中，对EL表达式编写一些逻辑判断时，若直接嵌入Java代码，则使得逻辑代码和页面展示耦合在一起，代码可读性变差，JSTL标签库就是用来改善这种问题的。

JSTL是对EL的扩展，下面对JSTL的使用做简单说明。首先需要进行JSTL的相关配置。

#### 配置

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

4. 在JSP页面，通过`<%@taglib %>`来引入JSTL标签，引入方式如下

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

#### 使用

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

