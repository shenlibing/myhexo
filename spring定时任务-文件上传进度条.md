---
title: spring定时任务_文件上传进度条
date: 2019-01-30 15:49:12
categories: 日常记录
tags: 
	- spring
	- bootstrap
---

**spring定时任务**

导依赖

```xml
	<!-- https://mvnrepository.com/artifact/org.quartz-scheduler/quartz -->
	<dependency>
	    <groupId>org.quartz-scheduler</groupId>
	    <artifactId>quartz</artifactId>
	    <version>2.2.3</version>
	</dependency>

```

配置定时任务类

```java
	package com.atguigu.scw.portal.service;
	
	import org.springframework.stereotype.Service;
	
	@Service
	public class ExampleJob {
	
	    public void hello() {
	        System.out.println("定时任务触发===========>");
	    }
	
	}

```

配置定时任务

```xml
	<?xml version="1.0" encoding="UTF-8"?>
	<beans xmlns="http://www.springframework.org/schema/beans"
		xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
		xmlns:context="http://www.springframework.org/schema/context"
		xmlns:mvc="http://www.springframework.org/schema/mvc"
		xsi:schemaLocation="http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc-4.3.xsd
			http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
			http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.3.xsd">
	
	
	
		<!-- <bean id="exampleJob"
			class="com.atguigu.scw.portal.service.ExampleJob"></bean> -->
	
		<!--配置定时任务触发类和方法  -->
		<bean id="jobDetail"
			class="org.springframework.scheduling.quartz.MethodInvokingJobDetailFactoryBean">
			<property name="targetObject" ref="exampleJob" />
			<property name="targetMethod" value="hello" />
		</bean>
	
	
		<!--配置触发器，指定何时触发 -->
		<bean id="cronTrigger"
			class="org.springframework.scheduling.quartz.CronTriggerFactoryBean">
			<property name="jobDetail" ref="jobDetail" />
			<!-- run every morning at 6 AM -->
			<property name="cronExpression" value="*/5 * * * * ?" />
		</bean>
	
	
		<!--配置定时任务调用哪一个触发器  -->
		<bean
			class="org.springframework.scheduling.quartz.SchedulerFactoryBean">
			<property name="triggers">
				<list>
					<ref bean="cronTrigger" />
				</list>
			</property>
		</bean>
	</beans>


```

cron表达式使用

```
每隔5秒执行一次：*/5 * * * * ?
每隔1分钟执行一次：0 */1 * * * ?
每天23点执行一次：0 0 23 * * ?
每天凌晨1点执行一次：0 0 1 * * ?
每月1号凌晨1点执行一次：0 0 1 1 * ?
每月最后一天23点执行一次：0 0 23 L * ?
每周星期天凌晨1点实行一次：0 0 1 ? * L
在26分、29分、33分执行一次：0 26,29,33 * * * ?
每天的0点、13点、18点、21点都执行一次：0 0 0,13,18,21 * * ?

```

参考

[https://docs.spring.io/spring/docs/4.0.0.RELEASE/spring-framework-reference/htmlsingle/#scheduling-quartz](https://docs.spring.io/spring/docs/4.0.0.RELEASE/spring-framework-reference/htmlsingle/#scheduling-quartz)


**文件上传进度条**

ajax异步提交表单`$("#subBtn").on('click', function() { return false}`是js对象的机制

`<button type="submit" id='subBtn' class="btn btn-default">Submit</button>`点击按钮直接提交是浏览器的机制

文件上传

表单直接提交`action='${ctp}/member/upload' method='post' enctype="multipart/form-data"`

```html
	<form action='${ctp}/member/upload' method='post' enctype="multipart/form-data">
		<div class="form-group">
			<input type="file" class="form-control" id="file" name='file'>
		</div>
		<div class="progress">
			<div class="progress-bar" role="progressbar" aria-valuenow="60"
				aria-valuemin="0" aria-valuemax="100" style="width: 60%;">
				60%</div>
		</div>
		<button type="submit" id='subBtn' class="btn btn-default">Submit</button>
	</form>
```

ajax提交`type : "post",contentType : false,processData : false`  

```html
	<form id='submitForm'>
		<div class="form-group">
			<input type="file" class="form-control" id="file" name='file'>
		</div>
		<div class="progress" style='display: none;'>
			<div class="progress-bar" role="progressbar" aria-valuenow="0"
				aria-valuemin="0" aria-valuemax="100" style="width: 0%;">0%</div>
		</div>
		<button type="submit" id='subBtn' class="btn btn-default">Submit</button>
	</form>
	
	<script type="text/javascript">
		$("#subBtn").on('click', function() {
			var fd = new FormData($("#submitForm")[0]);
			$.ajax({
				url : "${ctp}/member/upload",
				data : fd,
				type : "post",
				contentType : false,
				processData : false,
					//获取ajaxSettings中的xhr对象，为它的upload属性绑定progress事件的处理函数  
					xhr : function() {
						myXhr = $.ajaxSettings.xhr()
						//检查upload属性是否存在  
						if (myXhr.upload) {
							//绑定progress事件的回调函数  
							myXhr.upload.addEventListener('progress',
									progressHandlingFunction, false)
						}
						//xhr对象返回给jQuery使用  
						return myXhr;
					},
				success : function(result) {
					console.log("result==========>", result)
				},
				error : function(e) {
					console.log('e=========>', e)
				}
			})
			return false
		})
		var progressHandlingFunction = function(e) {
			var curr = e.loaded
			var total = e.total
			process = curr / total * 100
			console.log('上传进度============>', process)
			$('.progress').attr('style', 'display:show;')
			$('.progress-bar').html(process + '%')
			$('.progress-bar').attr('style', 'width:' + process + '%;')

		}
	</script>

```

**图片预览**

```js
	$("#ad_file_input").on('change', function(event) {
		$(this).empty()
		$(this).parent('.form-group').next('.form-group').find('.imgdiv').empty()
		
		fileList = event.currentTarget.files
		log('fileList==============>', fileList)
		
		var URL = window.URL || window.webkitURL
		var imgURL
		//遍历上传的文件进行显示 
		$.each(fileList, function (index, item) {
			//创建一个临时的url地址
	       imgURL = URL.createObjectURL(item)
	       log('this=========>',this)
	       log('item==========>',item)
	       $('#ad_file_input').parent(".form-group").next(".form-group").find(".imgdiv").append("<img src='"+imgURL+"' style='width:200px;height:220px;'/>").append('<p>'+item.name+'<p>')
		})
	})

```





