---
title: SpringBoot
date: 2019-04-03 12:19:47
categories:
tags:
---


> # 入门 #

> ## 启动 ##

[官网创建](https://start.spring.io/)

> ### 第一个例子 ###

> #### 编辑pom.xml ####

```xml
<!-- springBoot的启动器 -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>

```

> #### 编写controller ####

```java

/**
 * SpringBoot HelloWorld
 * @author Administrator
 *
 */
@Controller
public class HelloWorld {

	@RequestMapping("/hello")
	@ResponseBody
	public Map<String, Object> showHelloWorld(){
		Map<String, Object> map = new HashMap<>();
		map.put("msg", "HelloWorld");
		return map;
	}
}

```

> #### 编写启动类 ####

```java
package com.bjsxt.app;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

/**
 * SpringBoot 启动类
 * @author Administrator
 *
 */
@SpringBootApplication
public class App {

	public static void main(String[] args) {
		SpringApplication.run(App.class, args);
	}
}

```

> ### 打包 ###

进入到项目目录下，终端运行

```
mvn clean package -Dmaven.test.skip=true

```

> ### 运行 ###

```
java -jar demo_springboot-0.0.1-SNAPSHOT.jar

```

> # 整合Web开发 #

> ## 整合Servlet ##

> ### 注解扫描注册 ###

通过注解扫描完成`Servlet`组件的注册

> #### 编写Servlet ####

```java


/**
 *SpringBoot整合Servlet方式一
 *
 *<servlet>
 *	<servlet-name>FirstServlet</servlet-name>
 *	<servlet-class>com.bjsxt.servlet.FirstServlet</servlet-class>
 *</servlet>
 *
 *<servlet-mapping>
 * <servlet-name>FirstServlet</servlet-name>
 * <url-pattern>/first</url-pattern>
 *</servlet-mapping>
 *
 */

@WebServlet(name="FirstServlet",urlPatterns="/first")
public class FirstServlet extends HttpServlet {

	@Override
	protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
		System.out.println("FirstServlet............");
	}
}

```

> #### 编写启动类 ####

```java


/**
 * SpringBoot整合Servlet方式一
 *
 *
 */
@SpringBootApplication
//在springBoot启动时会扫描@WebServlet，并将该类实例化
@ServletComponentScan 
public class App {

	public static void main(String[] args) {
		SpringApplication.run(App.class, args);
	}

}

```

> ### 方法注册 ###

通过方法完成`Servlet`组件的注册

> #### 编写Servlet ####

```java

/**
 *SpringBoot整合Servlet方式二
 *
 */

public class SecondServlet extends HttpServlet {

	@Override
	protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
		System.out.println("SecondServlet..........");
	}
	
}

```

> #### 编写启动类 ####

```java


/**
 * SpringBoot整合Servlet方式二
 *
 *
 */
@SpringBootApplication
public class App2 {

	public static void main(String[] args) {
		SpringApplication.run(App2.class, args);
	}
	
	@Bean
	public ServletRegistrationBean getServletRegistrationBean(){
		ServletRegistrationBean bean = new ServletRegistrationBean(new SecondServlet());
		bean.addUrlMappings("/second");
		return bean;
	}
}

```


> ## 整合Filter ##

> ### 注解扫描注册 ###

> #### 编写Filter ####

```java


/**
 *SpringBoot整合Filter 方式一
 *<filter>
 *	<filter-name>FirstFilter</filter-name>
 *	<filter-class>com.bjsxt.filter.FirstFilter</filter-class>
 *</filter>
 *<filter-mapping>
 *	<filter-name>FirstFilter</filter-name>
 *	<url-pattern>/first</url-pattern>
 *</filter-mapping>
 */
//@WebFilter(filterName="FirstFilter",urlPatterns={"*.do","*.jsp"})
@WebFilter(filterName="FirstFilter",urlPatterns="/first")
public class FirstFilter implements Filter {

	@Override
	public void destroy() {
		// TODO Auto-generated method stub

	}
	@Override
	public void doFilter(ServletRequest arg0, ServletResponse arg1, FilterChain arg2)
			throws IOException, ServletException {
		System.out.println("进入Filter");
		arg2.doFilter(arg0, arg1);
		System.out.println("离开Filter");
	}

	@Override
	public void init(FilterConfig arg0) throws ServletException {
		// TODO Auto-generated method stub
	}
}

```

> #### 编写启动类 ####

```java


/**
 *SpringBoot整合Filter 方式一
 *
 */
@SpringBootApplication
@ServletComponentScan
public class App {

	public static void main(String[] args) {
		SpringApplication.run(App.class, args);

	}

}

```

> ### 方法注册 ###

> #### 编写Filter ####

```java

/**
 * 
 *SpringBoot整合Filter 方式二
 *
 */
public class SecondFilter implements Filter {
	@Override
	public void destroy() {
		// TODO Auto-generated method stub
	}
	@Override
	public void doFilter(ServletRequest arg0, ServletResponse arg1, FilterChain arg2)
			throws IOException, ServletException {
		System.out.println("进入SecondFilter");
		arg2.doFilter(arg0, arg1);
		System.out.println("离开SecondFilter");
	}

	@Override
	public void init(FilterConfig arg0) throws ServletException {
		// TODO Auto-generated method stub
	}
}

```

> #### 编写启动类 ####

```java


/**
 * SpringBoot整合Filter方式二
 *
 *
 */
@SpringBootApplication
public class App2 {

	public static void main(String[] args) {
		SpringApplication.run(App2.class, args);
	}
	
	
	/**
	 * 注册Filter
	 */
	@Bean
	public FilterRegistrationBean getFilterRegistrationBean(){
		FilterRegistrationBean bean = new FilterRegistrationBean(new SecondFilter());
		//bean.addUrlPatterns(new String[]{"*.do","*.jsp"});
		bean.addUrlPatterns("/second");
		return bean;
	}
}

```

> ## 整合Listener ##

> ### 注解扫描注册 ###

> #### 编写Listener ####

```java
package com.bjsxt.listener;

import javax.servlet.ServletContextEvent;
import javax.servlet.ServletContextListener;
import javax.servlet.annotation.WebListener;
/**
 * springBoot整合Listener
 *
 *<listener>
 *	<listener-class>com.bjsxt.listener.FirstListener</listener-class>
 *</listener>
 */
@WebListener
public class FirstListener implements ServletContextListener {

	@Override
	public void contextDestroyed(ServletContextEvent arg0) {
		// TODO Auto-generated method stub

	}

	@Override
	public void contextInitialized(ServletContextEvent arg0) {
		System.out.println("Listener...init......");

	}

}

```

> #### 编写启动类 ####

```java
package com.bjsxt;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.boot.web.servlet.ServletComponentScan;

/**
 * springBoot整合Listener方式一
 *
 *
 */
@SpringBootApplication
@ServletComponentScan
public class App {

	public static void main(String[] args) {
		SpringApplication.run(App.class, args);
	}

}

```

> ### 方法注册 ###

> #### 编写Listener ####

```java
package com.bjsxt.listener;

import javax.servlet.ServletContextEvent;
import javax.servlet.ServletContextListener;
/**
 * springBoot整合Listener方式二。
 *
 *
 */
public class SecondListener implements ServletContextListener {

	@Override
	public void contextDestroyed(ServletContextEvent arg0) {
		// TODO Auto-generated method stub
	}

	@Override
	public void contextInitialized(ServletContextEvent arg0) {
		System.out.println("SecondListener..init.....");
	}

}

```

> #### 编写启动类 ####

```java

/**
 * SpringBoot整合Listener方式二
 *
 *
 */
@SpringBootApplication
public class App2 {

	public static void main(String[] args) {
		SpringApplication.run(App2.class, args);

	}
	/**
	 * 注册listener
	 */
	@Bean
	public ServletListenerRegistrationBean<SecondListener> getServletListenerRegistrationBean(){
		ServletListenerRegistrationBean<SecondListener> bean= new ServletListenerRegistrationBean<SecondListener>(new SecondListener());
		return bean;
	}
}

```

> ## 访问静态资源 ##

> ### 从`classpath/static`的目录下读取 ###

> ### 从`ServletContext`根目录下读取,即`webapp`目录 ###

> ## 文件上传 ##


> ### 编写Controller ###

```java

/**
 * SpringBoot文件上传
 *
 *
 */
//@Controller
@RestController //表示该类下的方法的返回值会自动做json格式的转换
public class FileUploadController {

	/*
	 * 处理文件上传
	 */
	@RequestMapping("/fileUploadController")
	public Map<String, Object> fileUpload(MultipartFile filename)throws Exception{
		System.out.println(filename.getOriginalFilename());
		filename.transferTo(new File("e:/"+filename.getOriginalFilename()));
		Map<String, Object> map = new HashMap<>();
		map.put("msg", "ok");
		return map;
	}
}

```


> ### 编写启动类 ###

```java
package com.bjsxt;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

/**
 * SpringBoot文件上传
 *
 *
 */
@SpringBootApplication
public class App {

	public static void main(String[] args) {
		SpringApplication.run(App.class, args);

	}

}

```

> ### 编写文件上传表单 ###

进入`src/main/resources/static`目录下,新增`upload.html`

```html
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>文件上传</title>
</head>
<body>
	<form action="fileUploadController" method="post" enctype="multipart/form-data">
		上传文件：<input type="file" name="filename"/><br/>
		<input type="submit"/>
	</form>
</body>
</html>

```

> ### 设置上传文件大小的默认值 ###

进入`/src/main/resources`目录，编辑`application.properties`文件

```
spring.http.multipart.maxFileSize=200MB
spring.http.multipart.maxRequestSize=200MB

```

> ## 整合jsp ##

> ### 编辑pom.xml ###

```xml
  <!-- jdk1.7 -->
  <properties>
  	<java.version>1.7</java.version>
  </properties>
  
  <dependencies>
  <!-- springBoot的启动器 -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
	    <!-- jstl -->
	<dependency>
		<groupId>javax.servlet</groupId>
		<artifactId>jstl</artifactId>
	</dependency>
	<!-- jasper -->
	<dependency>
		<groupId>org.apache.tomcat.embed</groupId>
		<artifactId>tomcat-embed-jasper</artifactId>
		<scope>provided</scope>
	</dependency>
</dependencies>

```

> ### 创建`application.properties` ###

进入`src/main/resources`目录，新增编辑`application.properties`文件

```
spring.mvc.view.prefix=/WEB-INF/jsp/
spring.mvc.view.suffix=.jsp

```

> ### 编写Controller ###

```java

/**
 * SpringBoot整合jsp
 *
 *
 */
@Controller
public class UserController {
	/*
	 * 处理请求，产生数据
	 */
	@RequestMapping("/showUser")
	public String showUser(Model model){
		List<Users> list = new ArrayList<>();
		list.add(new Users(1,"张三",20));
		list.add(new Users(2,"李四",22));
		list.add(new Users(3,"王五",24));
		
		//需要一个Model对象
		model.addAttribute("list", list);
		//跳转视图
		return "userList";
	}
}

```

> ### 创建jsp ###

进入`src/main/webapp/WEB-INF/jsp`目录,新增`userList.jsp`

```html
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c"%>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
<title>Insert title here</title>
</head>
<body>
	<table border="1" align="center" width="50%">
		<tr>
			<th>ID</th>
			<th>Name</th>
			<th>Age</th>
		</tr>
		<c:forEach items="${list }" var="user">
			<tr>
				<td>${user.userid }</td>
				<td>${user.username }</td>
				<td>${user.userage }</td>	
			</tr>
		</c:forEach>
	</table>
</body>
</html>

```

> ### 创建启动类 ###

```java
package com.bjsxt;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

/**
 * SpringBoot启动类
 *
 *
 */
@SpringBootApplication
public class App {

	public static void main(String[] args) {
		SpringApplication.run(App.class, args);
	}
}

```

> ## 整合freemarker ##

> ### 编辑pom.xml ###

```xml
<properties>
	<java.version>1.7</java.version>
</properties>

<dependencies>
<!-- springBoot的启动器 -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
<!-- freemarker启动器的坐标 -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-freemarker</artifactId>
</dependency>
</dependencies>

```
> ### 创建 Controller ###

```java


/**
 * SpringBoot整合jsp
 *
 *
 */
@Controller
public class UserController {
	/*
	 * 处理请求，产生数据
	 */
	@RequestMapping("/showUser")
	public String showUser(Model model){
		List<Users> list = new ArrayList<>();
		list.add(new Users(1,"张三",20));
		list.add(new Users(2,"李四",22));
		list.add(new Users(3,"王五",24));
		
		//需要一个Model对象
		model.addAttribute("list", list);
		//跳转视图
		return "userList";
	}
}

```

> ### 编写前端页面 ###

`springBoot`要求模板形式的视图层技术的文件必须要放到`src/main/resources`目录下必
须要一个名称为**`templates`**

进入到`src/main/resources/templates`目录,新增`userList.ftl`文件

```html
<html>
	<head>
		<title>展示用户数据</title>
		<meta charset="utf-9"></meta>
	</head>
	
	<body>
		
		<table border="1" align="center" width="50%">
			
			<tr>
				
				<th>ID</th>
				<th>Name</th>
				<th>Age</th>
			</tr>
			
			<#list list as user >
				<tr>
					<td>${user.userid}</td>
					<td>${user.username}</td>
					<td>${user.userage}</td>
				</tr>
			</#list>	
		</table>
	</body>
</html>

```


> ### 创建启动类 ###

```java
package com.bjsxt;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

/**
 * SpringBoot启动类
 *
 *
 */
@SpringBootApplication
public class App {

	public static void main(String[] args) {
		SpringApplication.run(App.class, args);
	}
}

```

> ## 整合Thymeleaf ##

> ### 编辑pom.xml ###

```xml
<properties>
<java.version>1.7</java.version>
<thymeleaf.version>3.0.2.RELEASE</thymeleaf.version>
<thymeleaf-layout-dialect.version>2.0.4</thymeleaf-layout-dialect.version>
</properties>

<dependencies>
	<!-- springBoot的启动器 -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
<!-- springBoot的启动器 -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-thymeleaf</artifactId>
</dependency>
</dependencies>

```

> ### 编辑前端页面 ###

进入`src/main/resources/templates`,`templates`目录是安全的。意味着该目录下的内容是不允许外界直接访问的。

```html
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Thymeleaf入门</title>
</head>
<body>
	<span th:text="Hello"></span>
	<hr/>
	<span th:text="${msg}"></span>
</body>
</html>

```

> ### 编辑Controller ###

```java
package com.bjsxt.controller;

import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.RequestMapping;

/**
 * Thymeleaf入门案例
 *
 *
 */
@Controller
public class DemoController {
	@RequestMapping("/show")
	public String showInfo(Model model){
		model.addAttribute("msg", "Thymeleaf 第一个案例");
		return "index";
	}
}

```

> ### 编辑启动类 ###

```java
package com.bjsxt;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

/**
 * 
 *Thymeleaf入门案例
 *
 */
@SpringBootApplication
public class App {

	public static void main(String[] args) {
		SpringApplication.run(App.class, args);
	}
}

```

> ### 坑 ###

> #### `org.xml.sax.SAXParseException:元素类型 "meta" 必须由匹配的结束标记"</meta>"终止` ####

解决

方式一

让html的标记按照严禁的语法编写

```html
<meta charset="UTF-8"/>

```

方式二

`thymeleaf.jar`更新为3.0以上的版本

`thymeleaf-layout-dialect.jar`更新为2.0以上的版本

```xml
<properties>
    <java.version>1.7</java.version>
    <thymeleaf.version>3.0.2.RELEASE</thymeleaf.version>
    <thymeleaf-layout-dialect.version>2.0.4</thymeleaf-layout-dialect.version>
</properties>

```

> ## Thymeleaf语法 ##

> ### 变量输出与字符串操作 ###

调用内置对象一定要用**#**

大部分的内置对象都以**s**结尾`strings、numbers、dates`

> #### 后台数据 ####

```java
package com.bjsxt.controller;

@Controller
public class DemoController {
	@RequestMapping("/show")
	public String showInfo(HttpServletRequest request,Model model){
		model.addAttribute("msg", "Thymeleaf 第一个案例");
		model.addAttribute("key", new Date());
		return "index";
	}
}

```


> #### th:text ####

```html
<span th:text="Hello"></span>
<hr/>
<span th:text="${msg} "></span>
<hr/>

```

> #### th:value ####

```html
<span th:text="${msg} "></span>

```

> #### 判断字符串是否为空 ####

```html
<span th:text="${#strings.isEmpty(msg)}"></span>

```

> #### 判断字符串是否包含指定的子串 ####

```html
<span th:text="${#strings.contains(msg,'9')}"></span>
<span th:text="${#strings.contains(msg,'T')}"></span>

```

> #### 判断当前字符串是否以子串开头 ####

```html
<span th:text="${#strings.startsWith(msg,'a')}"></span>
<span th:text="${#strings.startsWith(msg,'T')}"></span>

```

> #### 判断当前字符串是否以子串结尾 ####

```html
<span th:text="${#strings.endsWith(msg,'a')}"></span>
<span th:text="${#strings.endsWith(msg,'案例')}"></span>

```

> #### 返回字符串的长度 ####

```html
<span th:text="${#strings.length(msg)}"></span>

```
> #### 查找子串的位置 ####

返回该子串的下标，如果没找到则返回-1

```html
<span th:text="${#strings.indexOf(msg,'h')}"></span>

```

> #### 截取子串 ####

```html
<span th:text="${#strings.substring(msg,13)}"></span>
<span th:text="${#strings.substring(msg,13,14)}"></span>

```

> #### 字符串转大小写 ####

```html
<span th:text="${#strings.toUpperCase(msg)}"></span>
<span th:text="${#strings.toLowerCase(msg)}"></span>

```

> ### 日期格式化处理 ###

> #### 后台数据 ####

```java
package com.bjsxt.controller;

@Controller
public class DemoController {
	@RequestMapping("/show")
	public String showInfo(HttpServletRequest request,Model model){
		model.addAttribute("key", new Date());
		return "index";
	}
	
}

```

> #### 默认格式日期 ####

```html
<span th:text="${#dates.format(key)}"></span>

```

> #### 按照自定义的格式做日期转换 ####

```html
<span th:text="${#dates.format(key,'yyy/MM/dd')}"></span>

```

> #### 取年 ####

```html
<span th:text="${#dates.year(key)}"></span>

```
> #### 取月 ####

```html
<span th:text="${#dates.month(key)}"></span>

```

> #### 取日 ####

```html
<span th:text="${#dates.day(key)}"></span>

```

> ### 条件判断 ###

> #### 后台数据 ####

```java
package com.bjsxt.controller;

@Controller
public class DemoController {
	@RequestMapping("/show2")
	public String showInfo2(Model model){
		model.addAttribute("sex", "女");
		model.addAttribute("id","1");
		return "index2";
	}
}


```
> #### th:if ####

```html
<span th:if="${sex} == '男'">
	性别：男
</span>
<span th:if="${sex} == '女'">
	性别：女
</span>

```

> #### th:switch ####

```html
<div th:switch="${id}">
	<span th:case="1">ID为1</span>
	<span th:case="2">ID为2</span>
	<span th:case="3">ID为3</span>
</div>

```

> ### 迭代遍历 ###

> #### 后台数据 ####

```java
package com.bjsxt.controller;

@Controller
public class DemoController {
	
	@RequestMapping("/show3")
	public String showInfo3(Model model){
		List<Users> list = new ArrayList<>();
		list.add(new Users(1,"张三",20));
		list.add(new Users(2,"李四",22));
		list.add(new Users(3,"王五",24));
		model.addAttribute("list", list);
		return "index3";
	}
	
	@RequestMapping("/show4")
	public String showInfo4(Model model){
		Map<String, Users> map = new HashMap<>();
		map.put("u1", new Users(1,"张三",20));
		map.put("u2", new Users(2,"李四",22));
		map.put("u3", new Users(3,"王五",24));
		model.addAttribute("map", map);
		return "index4";
	}
	
}

```

> #### th:each迭代list ####

```html
<tr th:each="u : ${list}">
	<td th:text="${u.userid}"></td>
	<td th:text="${u.username}"></td>
	<td th:text="${u.userage}"></td>
</tr>

```
> #### th:each状态变量 ####

`index`当前迭代器的索引从0开始
`count`当前迭代对象的计数从1开始
`size`被迭代对象的长度
`even/odd`布尔值,当前循环是否是偶数/奇数从0开始
`first`布尔值，当前循环的是否是第一条,如果是返回`true`否则返回`false`
`last`布尔值，当前循环的是否是最后一条,如果是则返回`true`否则返回`false`

```html
<tr th:each="u,var : ${list}">
	<td th:text="${u.userid}"></td>
	<td th:text="${u.username}"></td>
	<td th:text="${u.userage}"></td>
	<td th:text="${var.index}"></td>
	<td th:text="${var.count}"></td>
	<td th:text="${var.size}"></td>
	<td th:text="${var.even}"></td>
	<td th:text="${var.odd}"></td>
	<td th:text="${var.first}"></td>
	<td th:text="${var.last}"></td>
</tr>

```

> #### th:each迭代Map ####

```html
<tr th:each="maps : ${map}">
	<td th:each="entry:${maps}" th:text="${entry.value.userid}" ></td>
	<td th:each="entry:${maps}" th:text="${entry.value.username}"></td>
	<td th:each="entry:${maps}" th:text="${entry.value.userage}"></td>
</tr>

```
> ### 域对象操作 ###

> #### 后台数据 ####

```java
package com.bjsxt.controller;

@Controller
public class DemoController {
	
	@RequestMapping("/show5")
	public String showInfo5(HttpServletRequest request,Model model){
		request.setAttribute("req", "HttpServletRequest");
		request.getSession().setAttribute("sess", "HttpSession");
		request.getSession().getServletContext().setAttribute("app", "Application");
		return "index5";
	}
	
}

```

> #### HttpServletRequest ####

```html
Request:<span th:text="${#httpServletRequest.getAttribute('req')}"></span><br/>

```

> #### HttpSession ####

```html
Session:<span th:text="${session.sess}"></span><br/>

```

> #### ServletContext ####

```html
Application:<span th:text="${application.app}"></span>

```

> ### URL表达式 ###

基本语法`@{}`

> #### 后台数据 ####

```java
package com.bjsxt.controller;

@Controller
public class DemoController {
	
	@RequestMapping("/{page}")
	public String showInfo(@PathVariable String page,Integer id,String name){
		System.out.println(id+"--"+name);
		return page;
	}
	
}

```

> #### 绝对路径 ####

```html
<a th:href="@{http://www.baidu.com}">绝对路径</a><br/>
<a href="http://www.baidu.com">绝对路径2</a>

```


> #### 相对路径 ####

```html
<a th:href="@{/show}">相对路径</a>
<hr/>
<a th:href="@{~/project2/resourcename}">相对于服务器的根</a>
<hr/>
<a th:href="@{/show(id=1,name=zhagnsan)}">相对路径-传参</a>
<hr/>
<a th:href="@{/path/{id}/show(id=1,name=zhagnsan)}">相对路径-传参-restful</a>

```

> # 整合MyBatis #

> ## 编辑pom.xml ##

```xml
<!-- Mybatis启动器 -->
<dependency>
	<groupId>org.mybatis.spring.boot</groupId>
	<artifactId>mybatis-spring-boot-starter</artifactId>
	<version>1.1.1</version>
</dependency>
<!-- mysql数据库驱动 -->
<dependency>
	<groupId>mysql</groupId>
	<artifactId>mysql-connector-java</artifactId>
</dependency>
<!-- druid数据库连接池 -->
<dependency>
	<groupId>com.alibaba</groupId>
	<artifactId>druid</artifactId>
	<version>1.0.9</version>
</dependency>

```


> ## 编辑`application.properties` ##

进入`src/main/resources`目录,编辑`application.properties`

```
spring.datasource.driverClassName=com.mysql.jdbc.Driver
spring.datasource.url=jdbc:mysql://localhost:3306/ssm
spring.datasource.username=root
spring.datasource.password=root

spring.datasource.type=com.alibaba.druid.pool.DruidDataSource

mybatis.type-aliases-package=com.bjsxt.pojo

```

> ## 数据库表设计 ##

```sql
CREATE TABLE `users` (
	`id` INT (11) NOT NULL AUTO_INCREMENT,
	`name` VARCHAR (255) DEFAULT NULL,
	`age` INT (11) DEFAULT NULL,
	PRIMARY KEY (`id`)
) ENGINE = INNODB DEFAULT CHARSET = utf8;

```

> ## 添加用户 ##

> ### 创建实体类 ###

```java
package com.bjsxt.pojo;

public class Users {
	private Integer id;
	private String name;
	private Integer  age;
	public Integer getId() {
		return id;
	}
	public void setId(Integer id) {
		this.id = id;
	}
	public String getName() {
		return name;
	}
	public void setName(String name) {
		this.name = name;
	}
	public Integer getAge() {
		return age;
	}
	public void setAge(Integer age) {
		this.age = age;
	}
	
}

```

> ### 创建mapper接口以及映射配置文件 ###

```java
package com.bjsxt.mapper;

import com.bjsxt.pojo.Users;

public interface UsersMapper {
	
	void insertUser(Users users);
}


```

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.bjsxt.mapper.UsersMapper">
	<insert id="insertUser" parameterType="users">
		insert into users(name,age) values(#{name},#{age})
	</insert>
</mapper>

```

> ### 创建业务层 ###

所有方法开启事务`@Transactional`标注在类上

```java

@Service
@Transactional
public class UsersServiceImpl implements UsersService {
	
	@Autowired
	private UsersMapper usersMapper;
	
	@Override
	public void addUser(Users users) {
		this.usersMapper.insertUser(users);
	}
}

```

> ### 创建Controller ###

前端请求`http://localhost:8080/users/input`跳转到`input.html`页面

```java

@Controller
@RequestMapping("/users")
public class UsersController {

	@Autowired
	private UsersService usersService;
	
	/**
	 * 页面跳转
	 */
	@RequestMapping("/{page}")
	public String showPage(@PathVariable String page){
		return page;
	}
	
	/**
	 * 添加用户
	 */
	@RequestMapping("/addUser")
	public String addUser(Users users){
		this.usersService.addUser(users);
		return "ok";
	}
}

```

> ### 编写页面 ###

使用`thymeleaf`模板,页面必须放在`src/main/resources/templates`目录下

添加用户页面`input.html`

```html
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>添加用户</title>
</head>
<body>
	
	<form th:action="@{/users/addUser}" method="post">
		用户姓名：<input type="text" name="name"/><br/>
		用户年龄：<input type="text" name="age"/><br/>
		<input type="submit" value="确定"/><br/>
	</form>
</body>
</html>

```
添加成功页面`ok.html`

```html
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>操作提示页面</title>
</head>
<body>
	操作成功！！！
</body>
</html>

```

> ### 创建启动类 ###

用户扫描`MyBatis`的`Mapper`接口,使用`@MapperScan("com.bjsxt.mapper")`注解

```java
package com.bjsxt;

import org.mybatis.spring.annotation.MapperScan;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
@MapperScan("com.bjsxt.mapper") //@MapperScan 用户扫描MyBatis的Mapper接口
public class App {

	public static void main(String[] args) {
		SpringApplication.run(App.class, args);
	}
}

```

> ## 查询用户 ##

> ### 修改`mapper`接口中以及映射配置文件 ###

```java
List<Users> selectUsersAll();

```

```xml
<select id="selectUsersAll" resultType="users">
	select id,name,age from users
</select>

```

> ### 修改业务层 ###

添加查询方法

```java
@Override
public List<Users> findUserAll() {
	return this.usersMapper.selectUsersAll();
}

```

> ### 修改Controller ###

浏览器请求`http://localhost:8080/findUserAll`,跳转到`showUsers.html`

```java
/**
 * 查询全部用户
 */
@RequestMapping("/findUserAll")
public String findUserAll(Model model){
	List<Users> list = this.usersService.findUserAll();
	model.addAttribute("list", list);
	return "showUsers";
}

```


> ### 创建页面 ###

查询用户页面`showUsers.html`

```html

<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>展示用户数据</title>
</head>
<body>
	<table border="1" style="width:300px;">
		<tr>
			<th>用户ID</th>
			<th>用户姓名</th>
			<th>用户年龄</th>
		</tr>
		<tr th:each="user : ${list}">
			<td th:text="${user.id}"></td>
			<td th:text="${user.name}"></td>
			<td th:text="${user.age}"></td>
		</tr>
	</table>
</body>
</html>

```

> ## 更新用户 ##

> ### 数据回显 ###

更新用户之前的查询，将数据在页面中回显


> #### 修改mapper接口以及映射配置文件 ####

```java
Users selectUsersById(Integer id);

```

```xml
<select id="selectUsersById" resultType="users">
	select id,name,age from users where id = #{value}
</select>

```

> #### 修改业务层代码 ####

```java
@Override
public Users findUserById(Integer id) {
	return this.usersMapper.selectUsersById(id);
}

```


> #### 修改Controller ####

浏览器请求`http://localhost:8080/findUserById`,跳转到`updateUser.html`页面，将数据用户数据回显

```java
/**
 * 根据用户id查询用户
 */
@RequestMapping("/findUserById")
public String findUserById(Integer id,Model model){
	Users user = this.usersService.findUserById(id);
	model.addAttribute("user", user);
	return "updateUser";
}

```

> #### 编写页面 ####

更新用户页面`updateUser.html`进行数据回显,使用`th:field`取得`controller`中的模型数据

```html
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
</head>
<body>
	<form th:action="@{/users/editUser}" method="post">
			<input type="hidden" name="id" th:field="${user.id}"/>
			用户姓名：<input type="text" name="name" th:field="${user.name}"/><br/>
			用户年龄：<input type="text" name="age" th:field="${user.age}"/><br/>
			<input type="submit" value="确定"/><br/>
	</form>
</body>
</html>

```

> ### 更新 ###

> #### 修改mapper接口以及映射配置文件 ####

```java
void updateUser(Users users);

```

```xml
<update id="updateUser" parameterType="users">
	update users set name=#{name} ,age=#{age} where id=#{id}
</update>

```

> #### 修改业务层代码 ####

```java
@Override
public void updateUser(Users users) {
	this.usersMapper.updateUser(users);
}

```

> #### 修改Controller ####

更新用户页面`updateUser.html`点击提交，浏览器请求`http://localhost:8080/editUser`,跳转到修改成功页面`ok.html`

```java
/**
 * 更新用户
 */
@RequestMapping("/editUser")
public String editUser(Users users){
	this.usersService.updateUser(users);
	return "ok";
}
```

> ## 删除用户 ##

> ### 修改mapper接口以及映射配置文件 ###

```java
void deleteUserById(Integer id);

```

```xml
<delete id="deleteUserById">
	delete from users where id = #{value}
</delete>

```

> ### 修改业务层 ###

```java
@Override
public void deleteUserById(Integer id) {
	this.usersMapper.deleteUserById(id);
}

```

> ### 修改Controller ###

浏览器请求`http://localhost:8080/delUser`重定向请求`/users/findUserAll`，跳转到查询用户页面`showUsers.html`

```java
/**
 * 删除用户
 */
@RequestMapping("/delUser")
public String delUser(Integer id){
	this.usersService.deleteUserById(id);
	return "redirect:/users/findUserAll";
}
```

> ### 修改页面 ###

修改查询用户页面`showUsers.html`,增加删除按钮链接

```html
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>展示用户数据</title>
</head>
<body>
	<table border="1" style="width:500px;">
		<tr>
			<th>用户ID</th>
			<th>用户姓名</th>
			<th>用户年龄</th>
			<th>操作</th>
		</tr>
		<tr th:each="user : ${list}">
			<td th:text="${user.id}"></td>
			<td th:text="${user.name}"></td>
			<td th:text="${user.age}"></td>
			<td>
				<a th:href="@{/users/findUserById(id=${user.id})}">更新用户</a>
				<a th:href="@{/users/delUser(id=${user.id})}">删除用户</a>
			</td>
		</tr>
	</table>
</body>
</html>

```

> # 服务端表单数据校验 #

> ## 表单做数据校验 ##

> ### 修改实体类 ###

非空校验使用`@NotBlank`注解标注在属性上

```java
package com.bjsxt.pojo;

import org.hibernate.validator.constraints.NotBlank;

public class Users {
	@NotBlank //非空校验
	private String name;
	@NotBlank //密码非空校验
	private String password;
	private Integer age;
	public String getName() {
		return name;
	}
	public void setName(String name) {
		this.name = name;
	}
	public String getPassword() {
		return password;
	}
	public void setPassword(String password) {
		this.password = password;
	}
	public Integer getAge() {
		return age;
	}
	public void setAge(Integer age) {
		this.age = age;
	}
	@Override
	public String toString() {
		return "Users [name=" + name + ", password=" + password + ", age=" + age + "]";
	}
	
}

```

> ### 修改Controller ###

浏览器请求`http://localhost:8080/addUser`,跳转到添加用户页面`add.html`,点击提交按钮浏览器请求`http://localhost:8080/save`,如果校验通过跳转到成功页面`ok.html`,校验不通过跳转到添加用户页面`add.html`

开启校验使用`@Valid`注解
封装了校验的结果`BindingResult`

```java


/**
 * SpringBoot 表单数据校验
 *
 *
 */
@Controller
public class UsersController {

	@RequestMapping("/addUser")
	public String showPage(){
		return "add";
	}
	
	/**
	 * 完成用户添加
	 *@Valid 开启对Users对象的数据校验
	 *BindingResult:封装了校验的结果
	 */
	@RequestMapping("/save")
	public String saveUser(@Valid Users users,BindingResult result){
		if(result.hasErrors()){
			return "add";
		}
		System.out.println(users);
		return "ok";
	}
}

```

> ### 修改页面 ###

添加用户页面`add.html`,使用`th:errors`取得校验信息

```html
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>添加用户</title>
</head>
<body>
	<form th:action="@{/save}" method="post">
		用户姓名：<input type="text" name="name"/><font color="red" th:errors="${users.name}"></font><br/>
		用户密码：<input type="password" name="password" /><font color="red" th:errors="${users.password}"></font><br/>
		用户年龄：<input type="text" name="age" /><font color="red" th:errors="${users.age}"></font><br/>
		<input type="submit" value="OK"/>
	</form>
</body>
</html>

```

> ## 坑 ##

> ### java.lang.IllegalStateException: Neither BindingResult nor plain target object for bean name 'users' available as request attribute ###


> #### 描述 ####

浏览器请求`http://localhost:8080/addUser`方法，该方法并没有传递`Users`对象，跳转到添加用户页面`add.html`取不到数据所以报错

> #### 解决 ####

> #####  方式一 #####

**跳转页面注入对象**

可以在浏览器请求`http://localhost:8080/addUser`方法中注入一个`Uesrs`对象
由于`springmvc`会将`Uesrs`对象放入到`Model`模型中传递,使用驼峰命名规则,**参数的变量名需要与对象的名称相同,将首字母小写**


修改Controller

```java


/**
 * SpringBoot 表单数据校验
 *
 *
 */
@Controller
public class UsersController {
	/**
	 * 
	 * 如果想为传递的对象更改名称，可以使用@ModelAttribute("aa")这表示当前传递的对象的key为aa。
	 * 那么我们在页面中获取该对象的key也需要修改为aa
	 * @param users
	 * @return
	 */
	@RequestMapping("/addUser")
	public String showPage(Users users){
		return "add";
	}
	
	/**
	 * 完成用户添加
	 *@Valid 开启对Users对象的数据校验
	 *BindingResult:封装了校验的结果
	 */
	@RequestMapping("/save")
	public String saveUser(@Valid Users users,BindingResult result){
		if(result.hasErrors()){
			return "add";
		}
		System.out.println(users);
		return "ok";
	}
}

```


> ##### 方式二 #####

**设置参数名称可变**

如果想为传递的对象更改名称,可以使用`@ModelAttribute("aa")`这表示当前传递的对象的`key`为`aa`
那么我们在页面中获取该对象的`key`也需要修改为`aa`

修改Controller

```java


/**
 * SpringBoot 表单数据校验
 *
 *
 */
@Controller
public class UsersController {
	/**
	 * 
	 * 如果想为传递的对象更改名称，可以使用@ModelAttribute("aa")这表示当前传递的对象的key为aa。
	 * 那么我们在页面中获取该对象的key也需要修改为aa
	 * @param users
	 * @return
	 */
	@RequestMapping("/addUser")
	public String showPage(@ModelAttribute("aa") Users users){
		return "add";
	}
	
	/**
	 * 完成用户添加
	 *@Valid 开启对Users对象的数据校验
	 *BindingResult:封装了校验的结果
	 */
	@RequestMapping("/save")
	public String saveUser(@ModelAttribute("aa") @Valid Users users,BindingResult result){
		if(result.hasErrors()){
			return "add";
		}
		System.out.println(users);
		return "ok";
	}
}

```

修改页面

添加用户页面`add.html`,`${aa.name}`需要和请求`http://localhost:8080/addUser`方法中设置的`@ModelAttribute("aa")`一样

```html
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>添加用户</title>
</head>
<body>
	<form th:action="@{/save}" method="post">
		用户姓名：<input type="text" name="name"/><font color="red" th:errors="${aa.name}"></font><br/>
		用户密码：<input type="password" name="password" /><font color="red" th:errors="${aa.password}"></font><br/>
		用户年龄：<input type="text" name="age" /><font color="red" th:errors="${aa.age}"></font><br/>
		用户邮箱：<input type="text" name="email" /><font color="red" th:errors="${aa.email}"></font><br/>
		<input type="submit" value="OK"/>
	</form>
</body>
</html>

```
> ## 其他校验规则 ##

`@NotBlank`判断字符串是否为`null`或者是空串(去掉首尾空格)。
`@NotEmpty`判断字符串是否`null`或者是空串。
`@Length`判断字符的长度(最大或者最小)
`@Min`判断数值最小值
`@Max`判断数值最大值
`@Email`判断邮箱是否合法

> ### 修改实体类 ###

```java

public class Users {
	@NotBlank(message="用户名不能为空") //非空校验
	@Length(min=2,max=6,message="最小长度为2位，最大长度为6位")
	private String name;
	@NotEmpty
	private String password;
	@Min(value=15)
	private Integer age;
	@Email
	private String email;
	
	public String getEmail() {
		return email;
	}
	public void setEmail(String email) {
		this.email = email;
	}
	public String getName() {
		return name;
	}
	public void setName(String name) {
		this.name = name;
	}
	public String getPassword() {
		return password;
	}
	public void setPassword(String password) {
		this.password = password;
	}
	public Integer getAge() {
		return age;
	}
	public void setAge(Integer age) {
		this.age = age;
	}
	@Override
	public String toString() {
		return "Users [name=" + name + ", password=" + password + ", age=" + age + "]";
	}
	
}

```

> # 异常处理 #

`SpringBoot`中对于异常处理提供了五种处理方式

> ## 自定义错误页面 ##

默认的处理异常的机制

一旦程序中出现了异常`SpringBoot`会向`/error`发送请求。在`springBoot`中提供了一个
叫`BasicExceptionController`类来处理`/error`请求，然后跳转到默认显示异常的页面来展示异常
信息

> ### 修改Controller ###

浏览器请求`http://localhost:8080/show`和`http://localhost:8080/show2`发生了异常,并不会跳转到`index.html`页面，而是会有默认异常页面`error.html`来显示异常信息

```java
package com.bjsxt.controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;

/**
 * SpringBoot处理异常方式一：自定义错误页面
 *
 *
 */
@Controller
public class DemoController {
	
	@RequestMapping("/show")
	public String showInfo(){
		String str = null;
		str.length();
		return "index";
	}
	
	@RequestMapping("/show2")
	public String showInfo2(){
		int a = 10/0;
		return "index";
	}
	
	
}

```


> ### 效果 ###

![](默认异常页面.png)


> ### 修改页面 ###

如果我们需要将所有的异常同一跳转到自定义的错误页面需要在`src/main/resources/templates`目录下创建`error.html`页面,名称必须叫`error.html`

```html
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>错误提示页面</title>
</head>
<body>
	出错了，请与管理员联系。。。
	<span th:text="${exception}"></span>
</body>
</html>

```



> ### 弊端 ###

对所有异常都显示到异常页面`error.html`,不能处理对不同的异常跳转到不同的异常显示页面

> ## @ExceptionHandle ##

> ### 修改Controller ###

使用`@ExceptionHandler`注解标注在方法上分别对不同的异常进行处理

使用`ModelAndView`可以指定异常信息及异常视图页面

```java

/**
 * SpringBoot处理异常方式二：@ExceptionHandle注解处理异常
 *
 *
 */
@Controller
public class DemoController {
	
	@RequestMapping("/show")
	public String showInfo(){
		String str = null;
		str.length();
		return "index";
	}
	
	@RequestMapping("/show2")
	public String showInfo2(){
		int a = 10/0;
		return "index";
	}
	
	/**
	 * java.lang.ArithmeticException
	 * 该方法需要返回一个ModelAndView：目的是可以让我们封装异常信息以及视图的指定
	 * 参数Exception e:会将产生异常对象注入到方法中
	 */
	@ExceptionHandler(value={java.lang.ArithmeticException.class})
	public ModelAndView arithmeticExceptionHandler(Exception e){
		ModelAndView mv = new ModelAndView();
		mv.addObject("error", e.toString());
		mv.setViewName("error1");
		return mv;
	}
	
	/**
	 * java.lang.NullPointerException
	 * 该方法需要返回一个ModelAndView：目的是可以让我们封装异常信息以及视图的指定
	 * 参数Exception e:会将产生异常对象注入到方法中
	 */
	@ExceptionHandler(value={java.lang.NullPointerException.class})
	public ModelAndView nullPointerExceptionHandler(Exception e){
		ModelAndView mv = new ModelAndView();
		mv.addObject("error", e.toString());
		mv.setViewName("error2");
		return mv;
	}
	
	
	
}

```

> ### 修改页面 ###

进入`src/main/resources/templates`目录下，创建空指针和算术异常页面


> #### 算术异常页面`error1.html` ####

```html
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>错误提示页面-ArithmeticException</title>
</head>
<body>
	出错了，请与管理员联系。。。
	<span th:text="${error}"></span>
</body>
</html>

```

> #### 空指针异常页面`error2.html` ####

```html
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>错误提示页面-NullPointerException</title>
</head>
<body>
	出错了，请与管理员联系。。。
	<span th:text="${error}"></span>
</body>
</html>

```

> ### 效果 ###

![](@ExceptionHandle注解处理异常.png)


> ### 弊端 ###

只有标记了`@ExceptionHandle`注解的`Controller`类才能处理跳转到自定义异常页面，而其它的`Controller`类中的方法则还是跳转到`springboot`默认机制的异常处理


> ## @ControllerAdvice+@ExceptionHandler ##

> ### 修改Controller ###

全局异常控制类`GlobalException.java`,浏览器请求`Controller`，如果发生了异常，则会走全局异常控制类`GlobalException.java`,该类可以对不同的异常进行不同的处理(指定异常信息、视图页面)

该方式优于`@ExceptionHandle`注解处理异常

```java

/**
 * 全局异常处理类
 *
 *
 */
@ControllerAdvice
public class GlobalException {
	/**
	 * java.lang.ArithmeticException
	 * 该方法需要返回一个ModelAndView：目的是可以让我们封装异常信息以及视图的指定
	 * 参数Exception e:会将产生异常对象注入到方法中
	 */
	@ExceptionHandler(value={java.lang.ArithmeticException.class})
	public ModelAndView arithmeticExceptionHandler(Exception e){
		ModelAndView mv = new ModelAndView();
		mv.addObject("error", e.toString());
		mv.setViewName("error1");
		return mv;
	}
	
	/**
	 * java.lang.NullPointerException
	 * 该方法需要返回一个ModelAndView：目的是可以让我们封装异常信息以及视图的指定
	 * 参数Exception e:会将产生异常对象注入到方法中
	 */
	@ExceptionHandler(value={java.lang.NullPointerException.class})
	public ModelAndView nullPointerExceptionHandler(Exception e){
		ModelAndView mv = new ModelAndView();
		mv.addObject("error", e.toString());
		mv.setViewName("error2");
		return mv;
	}
	
}

```


> ### 效果 ###

![](@ControllerAdvice+@ExceptionHandler注解处理异常.png)


> ## SimpleMappingExceptionResolver ##

比起`@ControllerAdvice+@ExceptionHandler`处理异常,通过`SimpleMappingExceptionResolver`做全局异常处理,方式更优雅,但是有个缺点即在异常页面不能输出异常信息

> ### 修改Controller ###

```java

/**
 * 通过SimpleMappingExceptionResolver做全局异常处理
 *
 *
 */
@Configuration
public class GlobalException {
	
	/**
	 * 该方法必须要有返回值。返回值类型必须是：SimpleMappingExceptionResolver
	 */
	@Bean
	public SimpleMappingExceptionResolver getSimpleMappingExceptionResolver(){
		SimpleMappingExceptionResolver resolver = new SimpleMappingExceptionResolver();
		
		Properties mappings = new Properties();
		
		/**
		 * 参数一：异常的类型，注意必须是异常类型的全名
		 * 参数二：视图名称
		 */
		mappings.put("java.lang.ArithmeticException", "error1");
		mappings.put("java.lang.NullPointerException","error2");
		
		//设置异常与视图映射信息的
		resolver.setExceptionMappings(mappings);
		
		return resolver;
	}
	
}

```

> ### 效果 ###

不能输出异常信息

![](SimpleMappingExceptionResolver类处理异常.png)

> ### 弊端 ###

不能在异常页面输出异常信息，`SimpleMappingExceptionResolver`类处理异常只能针对不同的异常映射不同的视图逻辑

> ## 自定义HandlerExceptionResolver ## 



> ### 修改Controller ###

定义全局异常控制类必须要实现`HandlerExceptionResolver`接口,该种方式可以针对不同异常跳转不同的异常页面，并且在异常异页面可以输出异常信息，但是平常异常信息不会直接输出在异常页，避免异常数据被人窃取

推荐使用


```java

/**
 * 通过实现HandlerExceptionResolver接口做全局异常处理
 *
 *
 */
@Configuration
public class GlobalException implements HandlerExceptionResolver {

	@Override
	public ModelAndView resolveException(HttpServletRequest request, HttpServletResponse response, Object handler,
			Exception ex) {
		ModelAndView mv = new ModelAndView();
		//判断不同异常类型，做不同视图跳转
		if(ex instanceof ArithmeticException){
			mv.setViewName("error1");
		}
		
		if(ex instanceof NullPointerException){
			mv.setViewName("error2");
		}
		mv.addObject("error", ex.toString());
		
		return mv;
	}
}

```

> # 单元测试 #

> ## 修改pom.xml ##

```xml
<!-- 添加junit环境的jar包 -->
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-test</artifactId>
</dependency>

```


> ## 创建dao ##

```java
package com.bjsxt.dao;

import org.springframework.stereotype.Repository;

@Repository
public class UserDaoImpl {

	public void saveUser(){
		System.out.println("insert into users.....");
	}
}

```

> ## 创建service ##

```java
package com.bjsxt.service;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import com.bjsxt.dao.UserDaoImpl;

@Service
public class UserServiceImpl {

	@Autowired
	private UserDaoImpl userDaoImpl;
	
	public void addUser(){
		this.userDaoImpl.saveUser();
	}
}

```

> ## 创建启动类 ##

```java
package com.bjsxt;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class App {

	public static void main(String[] args) {
		SpringApplication.run(App.class, args);
	}

}

```

> ## 整合Junit做单元测试 ##

`@RunWith`启动器`SpringJUnit4ClassRunner.class`让`junit`与`spring`环境进行整合

`@SpringBootTest(classes={App.class})`说明当前类为`springBoot`的测试类加载`SpringBoot`启动类`App.class`

```java
package com.bjsxt.test;

import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;
import com.bjsxt.App;
import com.bjsxt.service.UserServiceImpl;

/**
 * SpringBoot测试类
 *@RunWith:启动器 
 *SpringJUnit4ClassRunner.class：让junit与spring环境进行整合
 *
 *@SpringBootTest(classes={App.class}) 1,当前类为springBoot的测试类
 *@SpringBootTest(classes={App.class}) 2,加载SpringBoot启动类。启动springBoot
 *
 *junit与spring整合 @Contextconfiguartion("classpath:applicationContext.xml")
 */
@RunWith(SpringJUnit4ClassRunner.class) 
@SpringBootTest(classes={App.class})
public class UserServiceTest {

	@Autowired
	private UserServiceImpl userServiceImpl;
	
	@Test
	public void testAddUser(){
		this.userServiceImpl.addUser();
	}
}

```

> # 热部署 #

> ## SpringLoader插件 ##

> ### 创建controller ###

```java
package com.bjsxt.controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;

/**
 * SpringBoot----SpringLoader
 *
 *
 */
@Controller
public class UsersController {

	@RequestMapping("/show")
	public String showPage(){
		System.out.println("ShowPage......");
		return "index";
	}
}

```

> ### 创建页面 ###

进入`/src/main/resources/templates`目录下，创建热部署前端测试页面

```html
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
</head>
<body>
	<span th:text="Hello...."></span>
</body>
</html>

```

> ### 创建启动类 ###

```java
package com.bjsxt;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class App {
	public static void main(String[] args) {
		SpringApplication.run(App.class, args);
	}
}

```

> ### 运行 ###


> #### 方式一 ####

> ##### 修改pom.xml #####

```xml
<!-- springloader插件 -->
<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
            <dependencies>
                <dependency>
                    <groupId>org.springframework</groupId>
                    <artifactId>springloaded</artifactId>
                    <version>1.2.5.RELEASE</version>
                </dependency>
            </dependencies>
        </plugin>
    </plugins>
</build>

```

> ##### maven命令启动 #####

![](maven命令启用热部署.png)


> ##### 弊端 #####

这种方式的缺点是`Springloader`热部署程序是在系统后台以进程的形式来运行,需要手动关闭该进程
对`Java`代码做部署处理,但是对页面无能为力


> #### 方式二 ####

在项目中直接使用`jar`包的方式

启动命令`VM Options`参数`-javaagent:.\lib\springloaded-1.2.5.RELEASE.jar -noverify`

> ## DevTools工具 ##

> ### 修改pom.xml ###

```xml
<!-- DevTools的坐标 -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-devtools</artifactId>
    <optional>true</optional>
</dependency>

```

> ### SpringLoader与DevTools的区别 ###

`SpringLoader`在部署项目时使用的是热部署的方式

`DevTools`在部署项目时使用的是重新部署的方式


> # 整合Ehcache #

> ## 修改pom.xml ##

```xml
<dependencies>
	<!-- springBoot的启动器 -->
	<dependency>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-web</artifactId>
	</dependency>
	<!-- springBoot的启动器 -->
	<dependency>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-thymeleaf</artifactId>
	</dependency>

	<!-- springBoot的启动器 -->
	<dependency>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-data-jpa</artifactId>
	</dependency>

	<!-- 测试工具的启动器 -->
	<dependency>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-test</artifactId>
	</dependency>

	<!-- mysql -->
	<dependency>
		<groupId>mysql</groupId>
		<artifactId>mysql-connector-java</artifactId>
	</dependency>

	<!-- druid连接池 -->
	<dependency>
		<groupId>com.alibaba</groupId>
		<artifactId>druid</artifactId>
		<version>1.0.9</version>
	</dependency>

	<!-- Spring Boot缓存支持启动器 -->
	<dependency>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-cache</artifactId>
	</dependency>

	<!-- Ehcache坐标 -->
	<dependency>
		<groupId>net.sf.ehcache</groupId>
		<artifactId>ehcache</artifactId>
	</dependency>

</dependencies>
```


> ## 创建Ehcache的配置文件 ##

进入`src/main/resources/`目录下,创建`ehcache.xml`

```xml
<ehcache xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="../config/ehcache.xsd">

    <diskStore path="java.io.tmpdir"/>

  <!--defaultCache:echcache的默认缓存策略  -->
    <defaultCache
            maxElementsInMemory="10000"
            eternal="false"
            timeToIdleSeconds="120"
            timeToLiveSeconds="120"
            maxElementsOnDisk="10000000"
            diskExpiryThreadIntervalSeconds="120"
            memoryStoreEvictionPolicy="LRU">
        <persistence strategy="localTempSwap"/>
    </defaultCache>
    <!-- 自定义缓存策略 -->
    <cache name="users"
            maxElementsInMemory="10000"
            eternal="false"
            timeToIdleSeconds="120"
            timeToLiveSeconds="120"
            maxElementsOnDisk="10000000"
            diskExpiryThreadIntervalSeconds="120"
            memoryStoreEvictionPolicy="LRU">
        <persistence strategy="localTempSwap"/>
    </cache>
</ehcache>

```
> ## 修改application.properties文件 ##

进入`src/main/resources/`目录下,修改`application.properties`

```
spring.datasource.driverClassName=com.mysql.jdbc.Driver
spring.datasource.url=jdbc:mysql://localhost:3306/ssm
spring.datasource.username=root
spring.datasource.password=root

spring.datasource.type=com.alibaba.druid.pool.DruidDataSource

spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true

spring.cache.ehcache.cofnig=ehcache.xml

```

> ## 创建service ##

使用缓存策略`@Cacheable(value="users")`

```java
package com.bjsxt.service.impl;

import java.util.List;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.cache.annotation.Cacheable;
import org.springframework.data.domain.Page;
import org.springframework.data.domain.Pageable;
import org.springframework.stereotype.Service;

import com.bjsxt.dao.UsersRepository;
import com.bjsxt.pojo.Users;
import com.bjsxt.service.UsersService;
/**
 * UsersService接口实现类
 *
 *
 */
@Service
public class UsersServiceImpl implements UsersService {

	@Autowired
	private UsersRepository usersRepository;
	
	@Override
	public List<Users> findUserAll() {
		return this.usersRepository.findAll();
	}

	@Override
	//@Cacheable:对当前查询的对象做缓存处理
	@Cacheable(value="users")
	public Users findUserById(Integer id) {
		return this.usersRepository.findOne(id);
	}

	@Override
	public Page<Users> findUserByPage(Pageable pageable) {
		return this.usersRepository.findAll(pageable);
	}

	@Override
	public void saveUsers(Users users) {
		this.usersRepository.save(users);
	}

}

```

> ## 修改实体类 ##

```java
package com.bjsxt.pojo;

import java.io.Serializable;

import javax.persistence.Column;
import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;
import javax.persistence.Table;

@Entity
@Table(name="t_users")
public class Users implements Serializable {

	@Id
	@GeneratedValue(strategy=GenerationType.IDENTITY)
	@Column(name="id")
	private Integer id;
	
	@Column(name="name")
	private String name;
	
	@Column(name="age")
	private Integer age;
	
	@Column(name="address")
	private String address;
	

	public Integer getId() {
		return id;
	}

	public void setId(Integer id) {
		this.id = id;
	}

	public String getName() {
		return name;
	}

	public void setName(String name) {
		this.name = name;
	}

	public Integer getAge() {
		return age;
	}

	public void setAge(Integer age) {
		this.age = age;
	}

	public String getAddress() {
		return address;
	}

	public void setAddress(String address) {
		this.address = address;
	}

	@Override
	public String toString() {
		return "Users [id=" + id + ", name=" + name + ", age=" + age + ", address=" + address + "]";
	}
}

```

> ## 创建启动类 ##

开启缓存使用`@EnableCaching`注解

```java
package com.bjsxt;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cache.annotation.EnableCaching;


@SpringBootApplication
@EnableCaching
public class App {

	public static void main(String[] args) {
		SpringApplication.run(App.class, args);
	}
}

```

> ## 测试 ##

```java
package com.bjsxt.test;

import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;

import com.bjsxt.App;
import com.bjsxt.service.UsersService;

/**
 * UsersService测试
 *
 *
 */
@RunWith(SpringJUnit4ClassRunner.class)
@SpringBootTest(classes=App.class)
public class UsersServiceTest {

	@Autowired
	private UsersService usersService;
	
	@Test
	public void testFindUserById(){
		//第一次查询
		System.out.println(this.usersService.findUserById(1));
		
		//第二次查询
		System.out.println(this.usersService.findUserById(1));
	}
}

```

> ## 效果 ##

后台输出执行了一次`sql`查询，第二条结果是从缓存中取出来的

```
Hibernate: select users0_.id as id1_0_0_, users0_.address as address2_0_0_, users0_.age as age3_0_0_, users0_.name as name4_0_0_ from t_users users0_ where users0_.id=?
Users [id=1, name=shenlibing, age=18, address=haikoou]
Users [id=1, name=shenlibing, age=18, address=haikoou]
2019-04-08 15:28:17.262  INFO 1320 --- [       Thread-4] o.s.w.c.s.GenericWebApplicationContext   : Closing org.springframework.web.context.support.GenericWebApplicationContext@5b94b04d: startup date [Mon Apr 08 15:28:11 CST 2019]; root of context hierarchy
2019-04-08 15:28:17.265  INFO 1320 --- [       Thread-4] j.LocalContainerEntityManagerFactoryBean : Closing JPA EntityManagerFactory for persistence unit 'default'
2019-04-08 15:28:17.270  INFO 1320 --- [       Thread-4] com.alibaba.druid.pool.DruidDataSource   : {dataSource-1} closed

```

> ## @Cacheable ##

把方法的返回值添加到`Ehcache`中做缓存

`Value`属性：指定一个`Ehcache`配置文件中的缓存策略，如果么有给定`value`，则
表示使用默认的缓存策略

`Key`属性：给存储的值起个名称。在查询时如果有名称相同的，那么则知己从缓存中将
数据返回

> ### 修改service ###

```java
package com.bjsxt.service.impl;

import java.util.List;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.cache.annotation.Cacheable;
import org.springframework.data.domain.Page;
import org.springframework.data.domain.Pageable;
import org.springframework.stereotype.Service;

import com.bjsxt.dao.UsersRepository;
import com.bjsxt.pojo.Users;
import com.bjsxt.service.UsersService;
/**
 * UsersService接口实现类
 *
 *
 */
@Service
public class UsersServiceImpl implements UsersService {

	@Autowired
	private UsersRepository usersRepository;
	
	@Override
	public List<Users> findUserAll() {
		return this.usersRepository.findAll();
	}

	@Override
	//@Cacheable:对当前查询的对象做缓存处理
	@Cacheable(value="users")
	public Users findUserById(Integer id) {
		return this.usersRepository.findOne(id);
	}

	@Override
	@Cacheable(value="users",key="#pageable.pageSize")
	public Page<Users> findUserByPage(Pageable pageable) {
		return this.usersRepository.findAll(pageable);
	}

	@Override
	public void saveUsers(Users users) {
		this.usersRepository.save(users);
	}

}

```

> ### 修改测试 ###

```java
package com.bjsxt.test;

import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.data.domain.PageRequest;
import org.springframework.data.domain.Pageable;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;

import com.bjsxt.App;
import com.bjsxt.service.UsersService;

/**
 * UsersService测试
 *
 *
 */
@RunWith(SpringJUnit4ClassRunner.class)
@SpringBootTest(classes=App.class)
public class UsersServiceTest {

	@Autowired
	private UsersService usersService;
	
	@Test
	public void testFindUserByPage(){
		Pageable pageable = new PageRequest(0, 2);
		//第一次查询
		System.out.println(this.usersService.findUserByPage(pageable).getTotalElements());
		
		//第二次查询
		System.out.println(this.usersService.findUserByPage(pageable).getTotalElements());
		
		//第三次查询
		pageable = new PageRequest(1, 2);
		System.out.println(this.usersService.findUserByPage(pageable).getTotalElements());
	}
}

```

> ### 效果 ###

后台输出执行了一次`sql`查询，后面两次是从缓存取出来的

因为`service`层使用了缓存策略`@Cacheable(value="users",key="#pageable.pageSize")`且指定了`key`为分页的每页条数
在测试查询的时候虽然第三次查询重新`new PageRequest(1, 2)`，但是`size`还是为2，所以还是从缓存中取

```
Hibernate: select users0_.id as id1_0_, users0_.address as address2_0_, users0_.age as age3_0_, users0_.name as name4_0_ from t_users users0_ limit ?
Hibernate: select count(users0_.id) as col_0_0_ from t_users users0_
5
5
5
2019-04-08 15:45:08.451  INFO 20352 --- [       Thread-4] o.s.w.c.s.GenericWebApplicationContext   : Closing org.springframework.web.context.support.GenericWebApplicationContext@192c3f1e: startup date [Mon Apr 08 15:45:02 CST 2019]; root of context hierarchy
2019-04-08 15:45:08.457  INFO 20352 --- [       Thread-4] j.LocalContainerEntityManagerFactoryBean : Closing JPA EntityManagerFactory for persistence unit 'default'
2019-04-08 15:45:08.464  INFO 20352 --- [       Thread-4] com.alibaba.druid.pool.DruidDataSource   : {dataSource-1} closed

```

> ## @CacheEvict ##

清除缓存,在对频繁查询的数据可以从缓存中取,然而一旦有新的数据写入数据库,必须重新刷新缓存,即清除缓存


> ### 修改service ###

```java

@Override
//@CacheEvict(value="users",allEntries=true) 清除缓存中以users缓存策略缓存的对象
@CacheEvict(value="users",allEntries=true)
public void saveUsers(Users users) {
	this.usersRepository.save(users);
}

```
> ### 修改测试 ###

```java
package com.bjsxt.test;

import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.data.domain.PageRequest;
import org.springframework.data.domain.Pageable;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;

import com.bjsxt.App;
import com.bjsxt.pojo.Users;
import com.bjsxt.service.UsersService;

/**
 * UsersService测试
 *
 *
 */
@RunWith(SpringJUnit4ClassRunner.class)
@SpringBootTest(classes=App.class)
public class UsersServiceTest {

	@Autowired
	private UsersService usersService;
	
	@Test
	public void testFindAll(){
		//第一次查询
		System.out.println(this.usersService.findUserAll().size());
		
		Users users = new Users();
		users.setAddress("南京");
		users.setAge(43);
		users.setName("朱七");
		this.usersService.saveUsers(users);
		
		//第二次查询
		System.out.println(this.usersService.findUserAll().size());
	}
}

```

效果

后台输出第一次查询和第二次查询之间插入了一条新数据，第一次查询结果是5条，在插入数据方法`saveUsers`上开启了清除缓存，所以插入数据之后再一次查询的数据也就同步过来更新为6条

```
Hibernate: select users0_.id as id1_0_, users0_.address as address2_0_, users0_.age as age3_0_, users0_.name as name4_0_ from t_users users0_
5
Hibernate: insert into t_users (address, age, name) values (?, ?, ?)
Hibernate: select users0_.id as id1_0_, users0_.address as address2_0_, users0_.age as age3_0_, users0_.name as name4_0_ from t_users users0_
6
2019-04-08 15:54:13.046  INFO 19660 --- [       Thread-4] o.s.w.c.s.GenericWebApplicationContext   : Closing org.springframework.web.context.support.GenericWebApplicationContext@192c3f1e: startup date [Mon Apr 08 15:54:08 CST 2019]; root of context hierarchy
2019-04-08 15:54:13.049  INFO 19660 --- [       Thread-4] j.LocalContainerEntityManagerFactoryBean : Closing JPA EntityManagerFactory for persistence unit 'default'
2019-04-08 15:54:13.053  INFO 19660 --- [       Thread-4] com.alibaba.druid.pool.DruidDataSource   : {dataSource-1} closed

```

> # 整合Redis #

> ## 修改pom.xml ##

```xml
<!-- Spring Data Redis的启动器 -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>

```

> ## 创建Redis配置类 ##

```java
package com.bjsxt.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.data.redis.connection.jedis.JedisConnectionFactory;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.data.redis.serializer.StringRedisSerializer;

import redis.clients.jedis.JedisPoolConfig;

/**
 * 完成对Redis的整合的一些配置
 *
 *
 */
@Configuration
public class RedisConfig {

	/**
	 * 1.创建JedisPoolConfig对象。在该对象中完成一些链接池配置
	 *
	 */
	@Bean
	public JedisPoolConfig jedisPoolConfig(){
		JedisPoolConfig config = new JedisPoolConfig();
		//最大空闲数
		config.setMaxIdle(10);
		//最小空闲数
		config.setMinIdle(5);
		//最大链接数
		config.setMaxTotal(20);
		
		return config;
	}
	
	/**
	 * 2.创建JedisConnectionFactory：配置redis链接信息
	 */
	@Bean
	public JedisConnectionFactory jedisConnectionFactory(JedisPoolConfig config){
		JedisConnectionFactory factory = new JedisConnectionFactory();
		//关联链接池的配置对象
		factory.setPoolConfig(config);
		//配置链接Redis的信息
		//主机地址
		factory.setHostName("192.168.1.104");
		//端口
		factory.setPort(6379);
		
		return factory;
	}
	
	/**
	 * 3.创建RedisTemplate:用于执行Redis操作的方法
	 */
	@Bean
	public RedisTemplate<String,Object> redisTemplate(JedisConnectionFactory factory){
		RedisTemplate<String, Object> template = new RedisTemplate<>();
		//关联
		template.setConnectionFactory(factory);
		
		//为key设置序列化器
		template.setKeySerializer(new StringRedisSerializer());
		//为value设置序列化器
		template.setValueSerializer(new StringRedisSerializer());
		
		return template;
	}
}

```

> ## 创建测试 ##

> ### 修改pom.xml ###

添加测试启动依赖坐标

```xml
<!-- Test的启动器 -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-test</artifactId>
</dependency>

```

> ### 测试 ###

```java
package com.bjsxt.test;

import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;

import com.bjsxt.App;

/**
 * Spring Data Redis测试
 *
 *
 */
@RunWith(SpringJUnit4ClassRunner.class)
@SpringBootTest(classes=App.class)
public class RedisTest {

	@Autowired
	private RedisTemplate<String, Object> redisTemplate;
	
	/**
	 * 添加一个字符串
	 */
	@Test
	public void testSet(){
		this.redisTemplate.opsForValue().set("key", "北京尚学堂");
	}
	
	/**
	 * 获取一个字符串
	 */
	@Test
	public void testGet(){
		String value = (String)this.redisTemplate.opsForValue().get("key");
		System.out.println(value);
	}
}

```

> ## 提取redis的配置信息 ##

> ### 修改application.properties ###

进入`src/main/resource/`目录,修改`application.properties`

```
spring.redis.pool.max-idle=10
spring.redis.pool.min-idle=5
spring.redis.pool.max-total=20

spring.redis.hostName=192.168.1.104
spring.redis.port=6379

```

> ### 修改redis配置类 ###

将前缀相同的内容创建一个实体使用`@ConfigurationProperties`注解

```java
package com.bjsxt.config;

import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.data.redis.connection.jedis.JedisConnectionFactory;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.data.redis.serializer.StringRedisSerializer;
import redis.clients.jedis.JedisPoolConfig;

/**
 * 完成对Redis的整合的一些配置
 *
 *
 */
@Configuration
public class RedisConfig {

	/**
	 * 1.创建JedisPoolConfig对象。在该对象中完成一些链接池配置
	 * @ConfigurationProperties:会将前缀相同的内容创建一个实体。
	 */
	@Bean
	@ConfigurationProperties(prefix="spring.redis.pool")
	public JedisPoolConfig jedisPoolConfig(){
		JedisPoolConfig config = new JedisPoolConfig();
		/*//最大空闲数
		config.setMaxIdle(10);
		//最小空闲数
		config.setMinIdle(5);
		//最大链接数
		config.setMaxTotal(20);*/
		System.out.println("默认值："+config.getMaxIdle());
		System.out.println("默认值："+config.getMinIdle());
		System.out.println("默认值："+config.getMaxTotal());
		return config;
	}
	
	/**
	 * 2.创建JedisConnectionFactory：配置redis链接信息
	 */
	@Bean
	@ConfigurationProperties(prefix="spring.redis")
	public JedisConnectionFactory jedisConnectionFactory(JedisPoolConfig config){
		System.out.println("配置完毕："+config.getMaxIdle());
		System.out.println("配置完毕："+config.getMinIdle());
		System.out.println("配置完毕："+config.getMaxTotal());
		
		JedisConnectionFactory factory = new JedisConnectionFactory();
		//关联链接池的配置对象
		factory.setPoolConfig(config);
		//配置链接Redis的信息
		//主机地址
		/*factory.setHostName("192.168.1.104");
		//端口
		factory.setPort(6379);*/
		return factory;
	}
	
	/**
	 * 3.创建RedisTemplate:用于执行Redis操作的方法
	 */
	@Bean
	public RedisTemplate<String,Object> redisTemplate(JedisConnectionFactory factory){
		RedisTemplate<String, Object> template = new RedisTemplate<>();
		//关联
		template.setConnectionFactory(factory);
		
		//为key设置序列化器
		template.setKeySerializer(new StringRedisSerializer());
		//为value设置序列化器
		template.setValueSerializer(new StringRedisSerializer());
		
		return template;
	}
}

```

> ### 效果 ###

```
2019-04-08 16:24:36.624  INFO 9340 --- [           main] .s.d.r.c.RepositoryConfigurationDelegate : Multiple Spring Data modules found, entering strict repository configuration mode!
默认值：8
默认值：0
默认值：8
配置完毕：10
配置完毕：5
配置完毕：20
2019-04-08 16:24:38.113  INFO 9340 --- [           main] s.w.s.m.m.a.RequestMappingHandlerAdapter : Looking for @ControllerAdvice: org.springframework.web.context.support.GenericWebApplicationContext@79d8407f: startup date [Mon Apr 08 16:24:35 CST 2019]; root of context hierarchy
2019-04-08 16:24:38.184  INFO 9340 --- [           main] s.w.s.m.m.a.RequestMappingHandlerMapping : Mapped "{[/error]}" onto public org.springframework.http.ResponseEntity<java.util.Map<java.lang.String, java.lang.Object>> org.springframework.boot.autoconfigure.web.BasicErrorController.error(javax.servlet.http.HttpServletRequest)
2019-04-08 16:24:38.185  INFO 9340 --- [           main] s.w.s.m.m.a.RequestMappingHandlerMapping : Mapped "{[/error],produces=[text/html]}" onto public org.springframework.web.servlet.ModelAndView org.springframework.boot.autoconfigure.web.BasicErrorController.errorHtml(javax.servlet.http.HttpServletRequest,javax.servlet.http.HttpServletResponse)
2019-04-08 16:24:38.221  INFO 9340 --- [           main] o.s.w.s.handler.SimpleUrlHandlerMapping  : Mapped URL path [/webjars/**] onto handler of type [class org.springframework.web.servlet.resource.ResourceHttpRequestHandler]
2019-04-08 16:24:38.221  INFO 9340 --- [           main] o.s.w.s.handler.SimpleUrlHandlerMapping  : Mapped URL path [/**] onto handler of type [class org.springframework.web.servlet.resource.ResourceHttpRequestHandler]
2019-04-08 16:24:38.269  INFO 9340 --- [           main] o.s.w.s.handler.SimpleUrlHandlerMapping  : Mapped URL path [/**/favicon.ico] onto handler of type [class org.springframework.web.servlet.resource.ResourceHttpRequestHandler]
2019-04-08 16:24:38.317  WARN 9340 --- [           main] org.thymeleaf.templatemode.TemplateMode  : [THYMELEAF][main] Template Mode 'HTML5' is deprecated. Using Template Mode 'HTML' instead.
2019-04-08 16:24:38.931  INFO 9340 --- [           main] com.bjsxt.test.RedisTest                 : Started RedisTest in 4.089 seconds (JVM running for 5.112)
北京尚学堂
2019-04-08 16:24:39.039  INFO 9340 --- [       Thread-2] o.s.w.c.s.GenericWebApplicationContext   : Closing org.springframework.web.context.support.GenericWebApplicationContext@79d8407f: startup date [Mon Apr 08 16:24:35 CST 2019]; root of context hierarchy

```

> ## 存储实体对象 ##

> ### 创建实体 ###

实体必须实现序列化接口

```java
package com.bjsxt.pojo;

import java.io.Serializable;

public class Users implements Serializable {
	
	private Integer id;
	private String name;
	private Integer age;
	public Integer getId() {
		return id;
	}
	public void setId(Integer id) {
		this.id = id;
	}
	public String getName() {
		return name;
	}
	public void setName(String name) {
		this.name = name;
	}
	public Integer getAge() {
		return age;
	}
	public void setAge(Integer age) {
		this.age = age;
	}
	@Override
	public String toString() {
		return "Users [id=" + id + ", name=" + name + ", age=" + age + "]";
	}
	
}

```


> ### 修改测试 ###

保存实体前时必须重新设置序列化器

```java
package com.bjsxt.test;


import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.data.redis.serializer.JdkSerializationRedisSerializer;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;
import com.bjsxt.App;
import com.bjsxt.pojo.Users;

/**
 * Spring Data Redis测试
 *
 *
 */
@RunWith(SpringJUnit4ClassRunner.class)
@SpringBootTest(classes=App.class)
public class RedisTest {

	@Autowired
	private RedisTemplate<String, Object> redisTemplate;
	
	/**
	 * 添加Users对象
	 */
	@Test
	public void testSetUesrs(){
		Users users = new Users();
		users.setAge(20);
		users.setName("张三丰");
		users.setId(1);
		//重新设置序列化器
		this.redisTemplate.setValueSerializer(new JdkSerializationRedisSerializer());
		this.redisTemplate.opsForValue().set("users", users);
	}
	
	/**
	 * 取Users对象
	 */
	@Test
	public void testGetUsers(){
		//重新设置序列化器
		this.redisTemplate.setValueSerializer(new JdkSerializationRedisSerializer());
		Users users = (Users)this.redisTemplate.opsForValue().get("users");
		System.out.println(users);
	}
	
}

```

> ### 效果 ###

```
2019-04-08 16:29:32.045  INFO 12720 --- [           main] .s.d.r.c.RepositoryConfigurationDelegate : Multiple Spring Data modules found, entering strict repository configuration mode!
默认值：8
默认值：0
默认值：8
配置完毕：10
配置完毕：5
配置完毕：20
2019-04-08 16:29:33.400  INFO 12720 --- [           main] s.w.s.m.m.a.RequestMappingHandlerAdapter : Looking for @ControllerAdvice: org.springframework.web.context.support.GenericWebApplicationContext@1e66f1f5: startup date [Mon Apr 08 16:29:31 CST 2019]; root of context hierarchy
2019-04-08 16:29:33.477  INFO 12720 --- [           main] s.w.s.m.m.a.RequestMappingHandlerMapping : Mapped "{[/error]}" onto public org.springframework.http.ResponseEntity<java.util.Map<java.lang.String, java.lang.Object>> org.springframework.boot.autoconfigure.web.BasicErrorController.error(javax.servlet.http.HttpServletRequest)
2019-04-08 16:29:33.478  INFO 12720 --- [           main] s.w.s.m.m.a.RequestMappingHandlerMapping : Mapped "{[/error],produces=[text/html]}" onto public org.springframework.web.servlet.ModelAndView org.springframework.boot.autoconfigure.web.BasicErrorController.errorHtml(javax.servlet.http.HttpServletRequest,javax.servlet.http.HttpServletResponse)
2019-04-08 16:29:33.518  INFO 12720 --- [           main] o.s.w.s.handler.SimpleUrlHandlerMapping  : Mapped URL path [/webjars/**] onto handler of type [class org.springframework.web.servlet.resource.ResourceHttpRequestHandler]
2019-04-08 16:29:33.518  INFO 12720 --- [           main] o.s.w.s.handler.SimpleUrlHandlerMapping  : Mapped URL path [/**] onto handler of type [class org.springframework.web.servlet.resource.ResourceHttpRequestHandler]
2019-04-08 16:29:33.564  INFO 12720 --- [           main] o.s.w.s.handler.SimpleUrlHandlerMapping  : Mapped URL path [/**/favicon.ico] onto handler of type [class org.springframework.web.servlet.resource.ResourceHttpRequestHandler]
2019-04-08 16:29:33.607  WARN 12720 --- [           main] org.thymeleaf.templatemode.TemplateMode  : [THYMELEAF][main] Template Mode 'HTML5' is deprecated. Using Template Mode 'HTML' instead.
2019-04-08 16:29:34.223  INFO 12720 --- [           main] com.bjsxt.test.RedisTest                 : Started RedisTest in 3.616 seconds (JVM running for 4.489)
Users [id=1, name=张三丰, age=20]
2019-04-08 16:29:34.352  INFO 12720 --- [       Thread-2] o.s.w.c.s.GenericWebApplicationContext   : Closing org.springframework.web.context.support.GenericWebApplicationContext@1e66f1f5: startup date [Mon Apr 08 16:29:31 CST 2019]; root of context hierarchy

```

> ### JdkSerializationRedisSerializer弊端 ###

实体对象通过`JdkSerializationRedisSerializer`序列化后存到`Redis`中占用空间大

> ## JSON格式存储实体对象 ##

**推荐使用**

> ### 修改测试 ###

重置序列化`Jackson2JsonRedisSerializer`

```java
package com.bjsxt.test;


import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.data.redis.serializer.Jackson2JsonRedisSerializer;
import org.springframework.data.redis.serializer.JdkSerializationRedisSerializer;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;
import com.bjsxt.App;
import com.bjsxt.pojo.Users;

/**
 * Spring Data Redis测试
 *
 *
 */
@RunWith(SpringJUnit4ClassRunner.class)
@SpringBootTest(classes=App.class)
public class RedisTest {

	@Autowired
	private RedisTemplate<String, Object> redisTemplate;
	
	/**
	 * 基于JSON格式存Users对象
	 */
	@Test
	public void testSetUsersUseJSON(){
		Users users = new Users();
		users.setAge(20);
		users.setName("李四丰");
		users.setId(1);
		this.redisTemplate.setValueSerializer(new Jackson2JsonRedisSerializer<>(Users.class));
		this.redisTemplate.opsForValue().set("users_json", users);
	}
	
	/**
	 * 基于JSON格式取Users对象
	 */
	@Test
	public void testGetUseJSON(){
		this.redisTemplate.setValueSerializer(new Jackson2JsonRedisSerializer<>(Users.class));
		Users users = (Users)this.redisTemplate.opsForValue().get("users_json");
		System.out.println(users);
	}
}

```

> ### 效果 ###

```
2019-04-08 16:41:53.694  INFO 19280 --- [           main] .s.d.r.c.RepositoryConfigurationDelegate : Multiple Spring Data modules found, entering strict repository configuration mode!
默认值：8
默认值：0
默认值：8
配置完毕：10
配置完毕：5
配置完毕：20
2019-04-08 16:41:55.094  INFO 19280 --- [           main] s.w.s.m.m.a.RequestMappingHandlerAdapter : Looking for @ControllerAdvice: org.springframework.web.context.support.GenericWebApplicationContext@1e66f1f5: startup date [Mon Apr 08 16:41:52 CST 2019]; root of context hierarchy
2019-04-08 16:41:55.177  INFO 19280 --- [           main] s.w.s.m.m.a.RequestMappingHandlerMapping : Mapped "{[/error]}" onto public org.springframework.http.ResponseEntity<java.util.Map<java.lang.String, java.lang.Object>> org.springframework.boot.autoconfigure.web.BasicErrorController.error(javax.servlet.http.HttpServletRequest)
2019-04-08 16:41:55.178  INFO 19280 --- [           main] s.w.s.m.m.a.RequestMappingHandlerMapping : Mapped "{[/error],produces=[text/html]}" onto public org.springframework.web.servlet.ModelAndView org.springframework.boot.autoconfigure.web.BasicErrorController.errorHtml(javax.servlet.http.HttpServletRequest,javax.servlet.http.HttpServletResponse)
2019-04-08 16:41:55.216  INFO 19280 --- [           main] o.s.w.s.handler.SimpleUrlHandlerMapping  : Mapped URL path [/webjars/**] onto handler of type [class org.springframework.web.servlet.resource.ResourceHttpRequestHandler]
2019-04-08 16:41:55.216  INFO 19280 --- [           main] o.s.w.s.handler.SimpleUrlHandlerMapping  : Mapped URL path [/**] onto handler of type [class org.springframework.web.servlet.resource.ResourceHttpRequestHandler]
2019-04-08 16:41:55.259  INFO 19280 --- [           main] o.s.w.s.handler.SimpleUrlHandlerMapping  : Mapped URL path [/**/favicon.ico] onto handler of type [class org.springframework.web.servlet.resource.ResourceHttpRequestHandler]
2019-04-08 16:41:55.311  WARN 19280 --- [           main] org.thymeleaf.templatemode.TemplateMode  : [THYMELEAF][main] Template Mode 'HTML5' is deprecated. Using Template Mode 'HTML' instead.
2019-04-08 16:41:55.957  INFO 19280 --- [           main] com.bjsxt.test.RedisTest                 : Started RedisTest in 3.767 seconds (JVM running for 4.678)
Users [id=1, name=李四丰, age=20]
2019-04-08 16:41:56.141  INFO 19280 --- [       Thread-2] o.s.w.c.s.GenericWebApplicationContext   : Closing org.springframework.web.context.support.GenericWebApplicationContext@1e66f1f5: startup date [Mon Apr 08 16:41:52 CST 2019]; root of context hierarchy

```