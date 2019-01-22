
---
title: 网易邮箱服务器发送电子邮件_搭建James邮件服务器发送电子邮件
date: 2019-01-22 12:58:55
tags:
---

1、网易邮箱发送电子邮件

为了安全，网易邮箱开启pop3授权码验证，该授权码可以让客户端进行登录

![](https://i.imgur.com/MepAaVz.png)

![](https://i.imgur.com/BzfesnI.png)

qq开启授权码

[https://service.mail.qq.com/cgi-bin/help?subtype=1&&no=1001256&&id=28](https://service.mail.qq.com/cgi-bin/help?subtype=1&&no=1001256&&id=28)




实现：
JavaMailAPI使用比较麻烦，这里采用的是Apache Commons Email

导入依赖

```

	<!-- https://mvnrepository.com/artifact/org.apache.commons/commons-email -->
	<dependency>
	    <groupId>org.apache.commons</groupId>
	    <artifactId>commons-email</artifactId>
	    <version>1.4</version>
	</dependency>


```


测试：
A simple text email

```

	@Test
    public void test01() throws Exception {
        SimpleEmail email = new SimpleEmail();
        // 设置主机名，远程服务器的主机名
        email.setHostName("smtp.163.com");
        email.setSmtpPort(25);
        // 设置登陆远程服务器的账号和pop3授权码
        email.setAuthentication("15501892660@163.com", "xxxxxxx");

        // 编写一个邮件
        // 设置发送给谁
        email.addTo("782125244@qq.com");
        // 设置这个邮件来源于哪里
        email.setFrom("15501892660@163.com");
        // 设置邮件主题
        email.setSubject("163发给qq的测试邮件 ");
        // 设置邮件内容
        email.setMsg("测试邮件");
        // 邮件发送
        email.send();

    }

```


参考：

[http://commons.apache.org/proper/commons-email/userguide.html](http://commons.apache.org/proper/commons-email/userguide.html)


2、搭建James邮件服务器

下载

[https://archive.apache.org/dist/james/server/](https://archive.apache.org/dist/james/server/)

quick-start

[http://james.apache.org/server/quick-start.html](http://james.apache.org/server/quick-start.html)


解压

D:\devsoft\apache-james-3.0-beta4

![](https://i.imgur.com/nR79WWy.png)

建库

![](https://i.imgur.com/kj3faWO.png)


配置数据源

![](https://i.imgur.com/9l952KR.png)

![](https://i.imgur.com/1PeU0yW.png)


启动

```

	D:\devsoft\apache-james-3.0-beta4\bin>run.bat
	INFO  09:28:46,331 | org.apache.james.container.spring.context.JamesServerApplicationContext | Refreshing org.apache.james.container.spring.context.JamesServerApplicationContext@153f5a29: startup date [Thu Jan 03 09:28:46 CST 2019]; root of context hierarchy
	Exception in thread "main" org.springframework.beans.factory.BeanDefinitionStoreException: Failed to create the JAXB binder; nested exception is javax.xml.bind.JAXBException: Provider com.sun.xml.internal.bind.v2.ContextFactory could not be instantiated: com.sun.xml.internal.bind.v2.runtime.IllegalAnnotationsException: 1 counts of IllegalAnnotationExceptions
	类的两个属性具有相同名称 "outputs"
	        this problem is related to the following location:
	                at public java.util.List org.apache.camel.model.ResequenceDefinition.getOutputs()
	                at org.apache.camel.model.ResequenceDefinition
	        this problem is related to the following location:
	                at private java.util.List org.apache.camel.model.ResequenceDefinition.outputs
	                at org.apache.camel.model.ResequenceDefinition
	
	 - with linked exception:
	[com.sun.xml.internal.bind.v2.runtime.IllegalAnnotationsException: 1 counts of IllegalAnnotationExceptions
	类的两个属性具有相同名称 "outputs"
	        this problem is related to the following location:
	                at public java.util.List org.apache.camel.model.ResequenceDefinition.getOutputs()
	                at org.apache.camel.model.ResequenceDefinition
	        this problem is related to the following location:
	                at private java.util.List org.apache.camel.model.ResequenceDefinition.outputs
	                at org.apache.camel.model.ResequenceDefinition
	]
	        at org.apache.camel.spring.handler.CamelNamespaceHandler$CamelContextBeanDefinitionParser.doParse(CamelNamespaceHandler.java:258)
	        at org.springframework.beans.factory.xml.AbstractSingleBeanDefinitionParser.parseInternal(AbstractSingleBeanDefinitionParser.java:85)
	        at org.springframework.beans.factory.xml.AbstractBeanDefinitionParser.parse(AbstractBeanDefinitionParser.java:59)
	        at org.springframework.beans.factory.xml.NamespaceHandlerSupport.parse(NamespaceHandlerSupport.java:73)
	        at org.springframework.beans.factory.xml.BeanDefinitionParserDelegate.parseCustomElement(BeanDefinitionParserDelegate.java:1419)
	        at org.springframework.beans.factory.xml.BeanDefinitionParserDelegate.parseCustomElement(BeanDefinitionParserDelegate.java:1409)
	        at org.springframework.beans.factory.xml.DefaultBeanDefinitionDocumentReader.parseBeanDefinitions(DefaultBeanDefinitionDocumentReader.java:184)
	        at org.springframework.beans.factory.xml.DefaultBeanDefinitionDocumentReader.doRegisterBeanDefinitions(DefaultBeanDefinitionDocumentReader.java:140)
	        at org.springframework.beans.factory.xml.DefaultBeanDefinitionDocumentReader.registerBeanDefinitions(DefaultBeanDefinitionDocumentReader.java:111)
	        at org.springframework.beans.factory.xml.XmlBeanDefinitionReader.registerBeanDefinitions(XmlBeanDefinitionReader.java:493)
	        at org.springframework.beans.factory.xml.XmlBeanDefinitionReader.doLoadBeanDefinitions(XmlBeanDefinitionReader.java:390)
	        at org.springframework.beans.factory.xml.XmlBeanDefinitionReader.loadBeanDefinitions(XmlBeanDefinitionReader.java:334)
	        at org.springframework.beans.factory.xml.XmlBeanDefinitionReader.loadBeanDefinitions(XmlBeanDefinitionReader.java:302)
	        at org.springframework.beans.factory.support.AbstractBeanDefinitionReader.loadBeanDefinitions(AbstractBeanDefinitionReader.java:174)
	        at org.springframework.beans.factory.support.AbstractBeanDefinitionReader.loadBeanDefinitions(AbstractBeanDefinitionReader.java:209)
	        at org.springframework.beans.factory.support.AbstractBeanDefinitionReader.loadBeanDefinitions(AbstractBeanDefinitionReader.java:180)
	        at org.springframework.beans.factory.support.AbstractBeanDefinitionReader.loadBeanDefinitions(AbstractBeanDefinitionReader.java:243)
	        at org.springframework.context.support.AbstractXmlApplicationContext.loadBeanDefinitions(AbstractXmlApplicationContext.java:127)
	        at org.springframework.context.support.AbstractXmlApplicationContext.loadBeanDefinitions(AbstractXmlApplicationContext.java:93)
	        at org.springframework.context.support.AbstractRefreshableApplicationContext.refreshBeanFactory(AbstractRefreshableApplicationContext.java:131)
	        at org.springframework.context.support.AbstractApplicationContext.obtainFreshBeanFactory(AbstractApplicationContext.java:522)
	        at org.springframework.context.support.AbstractApplicationContext.refresh(AbstractApplicationContext.java:436)
	        at org.springframework.context.support.ClassPathXmlApplicationContext.<init>(ClassPathXmlApplicationContext.java:139)
	        at org.springframework.context.support.ClassPathXmlApplicationContext.<init>(ClassPathXmlApplicationContext.java:93)
	        at org.apache.james.container.spring.context.JamesServerApplicationContext.<init>(JamesServerApplicationContext.java:39)
	        at org.apache.james.app.spring.JamesAppSpringMain.init(JamesAppSpringMain.java:61)
	        at org.apache.james.app.spring.JamesAppSpringMain.main(JamesAppSpringMain.java:42)
	Caused by: javax.xml.bind.JAXBException: Provider com.sun.xml.internal.bind.v2.ContextFactory could not be instantiated: com.sun.xml.internal.bind.v2.runtime.IllegalAnnotationsException: 1 counts of IllegalAnnotationExceptions
	类的两个属性具有相同名称 "outputs"
	        this problem is related to the following location:
	                at public java.util.List org.apache.camel.model.ResequenceDefinition.getOutputs()
	                at org.apache.camel.model.ResequenceDefinition
	        this problem is related to the following location:
	                at private java.util.List org.apache.camel.model.ResequenceDefinition.outputs
	                at org.apache.camel.model.ResequenceDefinition
	
	 - with linked exception:
	[com.sun.xml.internal.bind.v2.runtime.IllegalAnnotationsException: 1 counts of IllegalAnnotationExceptions
	类的两个属性具有相同名称 "outputs"
	        this problem is related to the following location:
	                at public java.util.List org.apache.camel.model.ResequenceDefinition.getOutputs()
	                at org.apache.camel.model.ResequenceDefinition
	        this problem is related to the following location:
	                at private java.util.List org.apache.camel.model.ResequenceDefinition.outputs
	                at org.apache.camel.model.ResequenceDefinition
	]
	        at javax.xml.bind.ContextFinder.newInstance(ContextFinder.java:146)
	        at javax.xml.bind.ContextFinder.find(ContextFinder.java:356)
	        at javax.xml.bind.JAXBContext.newInstance(JAXBContext.java:431)
	        at javax.xml.bind.JAXBContext.newInstance(JAXBContext.java:394)
	        at org.apache.camel.spring.handler.CamelNamespaceHandler.createJaxbContext(CamelNamespaceHandler.java:187)
	        at org.apache.camel.spring.handler.CamelNamespaceHandler.getJaxbContext(CamelNamespaceHandler.java:174)
	        at org.apache.camel.spring.handler.CamelNamespaceHandler$CamelContextBeanDefinitionParser.doParse(CamelNamespaceHandler.java:256)
	        ... 26 more
	Caused by: com.sun.xml.internal.bind.v2.runtime.IllegalAnnotationsException: 1 counts of IllegalAnnotationExceptions
	类的两个属性具有相同名称 "outputs"
	        this problem is related to the following location:
	                at public java.util.List org.apache.camel.model.ResequenceDefinition.getOutputs()
	                at org.apache.camel.model.ResequenceDefinition
	        this problem is related to the following location:
	                at private java.util.List org.apache.camel.model.ResequenceDefinition.outputs
	                at org.apache.camel.model.ResequenceDefinition
	
	        at com.sun.xml.internal.bind.v2.runtime.IllegalAnnotationsException$Builder.check(IllegalAnnotationsException.java:91)
	        at com.sun.xml.internal.bind.v2.runtime.JAXBContextImpl.getTypeInfoSet(JAXBContextImpl.java:445)
	        at com.sun.xml.internal.bind.v2.runtime.JAXBContextImpl.<init>(JAXBContextImpl.java:277)
	        at com.sun.xml.internal.bind.v2.runtime.JAXBContextImpl.<init>(JAXBContextImpl.java:124)
	        at com.sun.xml.internal.bind.v2.runtime.JAXBContextImpl$JAXBContextBuilder.build(JAXBContextImpl.java:1123)
	        at com.sun.xml.internal.bind.v2.ContextFactory.createContext(ContextFactory.java:147)
	        at com.sun.xml.internal.bind.v2.ContextFactory.createContext(ContextFactory.java:271)
	        at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
	        at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
	        at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
	        at java.lang.reflect.Method.invoke(Method.java:498)
	        at javax.xml.bind.ContextFinder.newInstance(ContextFinder.java:171)
	        at javax.xml.bind.ContextFinder.newInstance(ContextFinder.java:131)
	        ... 32 more
	
	D:\devsoft\apache-james-3.0-beta4\bin>


```

```


	D:\devsoft\apache-james-3.0-beta4\bin>run.bat
	INFO  09:37:44,247 | org.apache.james.container.spring.context.JamesServerApplicationContext | Refreshing org.apache.james.container.spring.context.JamesServerApplicationContext@7ca48474: startup date [Thu Jan 03 09:37:44 CST 2019]; root of context hierarchy
	log4j:WARN No appenders could be found for logger (org.apache.commons.configuration.ConfigurationUtils).
	log4j:WARN Please initialize the log4j system properly.
	log4j:WARN See http://logging.apache.org/log4j/1.2/faq.html#noconfig for more info.
	INFO  09:37:45,153 | org.apache.james.container.spring.context.JamesServerApplicationContext | Bean 'logprovider' of type [class org.apache.james.container.spring.lifecycle.LogProviderImpl] is not eligible for getting processed by all BeanPostProcessors (for example: not eligible for auto-proxying)
	INFO  09:37:45,544 | james.mailrepositorystore | JamesMailStore init...
	INFO  09:37:45,550 | james.mailrepositorystore | Registering Repository instance of class org.apache.james.mailrepository.file.FileMailRepository to handle file protocol requests for repositories with key file
	INFO  09:37:45,550 | james.mailrepositorystore | Registering Repository instance of class org.apache.james.mailrepository.jdbc.JDBCMailRepository to handle db protocol requests for repositories with key db
	INFO  09:37:45,550 | james.mailrepositorystore | Registering Repository instance of class org.apache.james.mailrepository.jdbc.JDBCMailRepository to handle dbfile protocol requests for repositories with key dbfile
	INFO  09:37:45,551 | james.mailrepositorystore | Registering Repository instance of class org.apache.james.mailrepository.file.MBoxMailRepository to handle mbox protocol requests for repositories with key mbox
	INFO  09:37:45,936 | james.dnsservice | Autodiscovery is enabled - trying to discover your system's DNS Servers
	INFO  09:37:45,942 | james.dnsservice | Adding autodiscovered server 202.100.192.68
	INFO  09:37:45,942 | james.dnsservice | Adding autodiscovered server 202.100.199.8
	INFO  09:37:45,943 | james.dnsservice | DNS Server is: 202.100.192.68
	INFO  09:37:45,943 | james.dnsservice | DNS Server is: 202.100.199.8
	INFO  09:37:45,949 | james.dnsservice | Registered cache, resolver and search paths as DNSJava defaults
	12  James  WARN   [main] openjpa.Runtime - An error occurred while registering a ClassTransformer with PersistenceUnitInfo: name 'James', root URL [file:/D:/devsoft/apache-james-3.0-beta4/conf/]. The error has been consumed. To see it, set your openjpa.Runtime log level to TRACE. Load-time class transformation will not be available.
	INFO  09:37:55,187 | james.domainlist | Set autodetect to: true
	INFO  09:37:55,187 | james.domainlist | Set autodetectIP to: true
	28  James  INFO   [main] openjpa.Runtime - Starting OpenJPA 2.1.0
	52  James  INFO   [main] openjpa.jdbc.JDBC - Using dictionary class "org.apache.openjpa.jdbc.sql.MySQLDictionary".
	Cannot load JDBC driver class 'com.mysql.jdbc.Driver'
	java.lang.ClassNotFoundException: com.mysql.jdbc.Driver
	        at java.net.URLClassLoader.findClass(URLClassLoader.java:381)
	        at java.lang.ClassLoader.loadClass(ClassLoader.java:424)
	        at sun.misc.Launcher$AppClassLoader.loadClass(Launcher.java:338)
	        at java.lang.ClassLoader.loadClass(ClassLoader.java:357)
	        at org.apache.commons.dbcp.BasicDataSource.createConnectionFactory(BasicDataSource.java:1420)
	        at org.apache.commons.dbcp.BasicDataSource.createDataSource(BasicDataSource.java:1371)
	        at org.apache.commons.dbcp.BasicDataSource.getConnection(BasicDataSource.java:1044)


```

解决办法

D:\devsoft\apache-james-3.0-beta4\conf\lib：存放自己导入的jar

D:\devsoft\apache-james-3.0-beta4\lib：存放James默认jar

复制jaxb-impl-2.1.3.jar，mysql驱动jar包到conf/lib下

![](https://i.imgur.com/4wuAqzA.png)


设置服务器域名、建立账户

```

	D:\devsoft\apache-james-3.0-beta4\bin> james-cli.bat -h localhost -p 9999 adddomain atguigu.com
	adddomain command executed sucessfully in 106 ms.
	
	
	D:\devsoft\apache-james-3.0-beta4\bin> james-cli.bat -h localhost -p 9999 adduser test@atguigu.com test
	adduser command executed sucessfully in 94 ms.

```

配置Foxmail客户端连接James邮件服务器

![](https://i.imgur.com/f8p6D2w.png)
![](https://i.imgur.com/9J6L4rf.png)


测试：

```
	
	/**
     * 测试james发送邮件
     * @Description (TODO这里用一句话描述这个方法的作用)
     * @throws Exception
     */
    @Test
    public void test02() throws Exception{
        SimpleEmail email = new SimpleEmail();
        //设置主机名，远程服务器的主机名
        email.setHostName("127.0.0.1");
        //自定义的ip，一定要手动设置好端口号
        email.setSmtpPort(25);
        //设置登陆远程服务器的密码
        email.setAuthentication("test@atguigu.com", "000000");
        
        //编写一个邮件
        //设置发送给谁
        email.addTo("17512080612@163.com");
        //设置这个邮件来源于哪里
        email.setFrom("admin@atguigu.com");
        //设置邮件主题
        email.setSubject("哈哈，给你测试 ");
        //设置邮件内容
        email.setMsg("我能给您发邮件<a href='http://www.atguigu.com'>尚硅谷</a>");
        //邮件发送
        email.send();
        
        
    }

```





