
---
title: 二维矩阵显示账户类型和资质_ajax异步传参_插入mysql中文乱码
date: 2019-01-11 12:58:55
categories: 日常记录
tags:
	-	ajax
---

1、二维矩阵显示账号类型和资质

效果：

![](https://i.imgur.com/H6MtJqE.png)


表结构：

账号类型和资质中间表

![](https://i.imgur.com/JrwRXlC.png)

资质表

![](https://i.imgur.com/dqgX6f3.png)


思路：

业务层查出账户和资质中间表、资质表、账户表数据，前端展示图横轴根据账户数据填充，纵轴根据资质数据填充，选择框的确定--根据**账户资质中间表数据**和选择框上**自定义属性绑定的账户名、资质id**进行相等比较确定。

前端：

1）单选框勾选关系初始化

![](https://i.imgur.com/TgzMKiS.png)

2）异步传参(封装成实体)

实体对象必须有get和set方法

![](https://i.imgur.com/Frl8lPj.png)

![](https://i.imgur.com/5r8EtpQ.png)

```html

	<body>
	<%
	    //设置导航条上的显示
				pageContext.setAttribute("navinfo", "分类管理");
				//设置点击高亮效果
				pageContext.setAttribute("curUrl", "servicectrl/type/ctrl");
	%>
	<!--引入导航条  -->
	<%@include file="/WEB-INF/includes/nav-bar.jsp"%>


	<div class="container-fluid">
		<div class="row">
			<!--引入树形菜单  -->
			<%@include file="/WEB-INF/includes/user_menu.jsp"%>

			<div class="col-sm-9 col-sm-offset-3 col-md-10 col-md-offset-2 main">
				<div class="panel panel-default">
					<div class="panel-heading">
						<h3 class="panel-title">
							<i class="glyphicon glyphicon-th"></i> 数据矩阵
						</h3>
					</div>
					<div class="panel-body">
						<div class="table-responsive">
							<table class="table  table-bordered">
								<thead>
									<tr>
										<th>名称</th>
										<c:forEach items="${types}" var="type">
											<th>${type}</th>
										</c:forEach>
									</tr>
								</thead>
								<tbody>
									<c:forEach items="${certs}" var="cert">
										<tr>
											<td>${cert.name}</td>
											<c:forEach items="${types}" var="type">
												<td><input class='checkSimple' type="checkbox"
													type_name='${type}' cid='${cert.id}'></td>
											</c:forEach>
									</c:forEach>
									</tr>
								</tbody>
							</table>
						</div>
					</div>
				</div>
			</div>
		</div>
	</div>
	<%@include file="/WEB-INF/includes/js-file.jsp"%>
	<!-- 引入树形菜单结构点击时动态显示页面高亮效果的js -->
	<%@include file="/WEB-INF/includes/common-js.jsp"%>

	<script type="text/javascript">
		$(function() {
			$(".list-group-item").click(function() {
				if ($(this).find("ul")) {
					$(this).toggleClass("tree-closed");
					if ($(this).hasClass("tree-closed")) {
						$("ul", this).hide("fast");
					} else {
						$("ul", this).show("fast");
					}
				}
			})
		})

		$(function() {
			//页面加载完成获取账户类型和资质的关系
			var list_type_cert = '${list_type_cert_json}'
			log('list_type_cert', list_type_cert)
			//string
			log('list_type_cert type=======>', typeof (list_type_cert))
			//JSON.parse(list_type_cert) 字符串转成json对象
			$.each(JSON.parse(list_type_cert),function(index, content) {
				log(index, content, content.accttype,content.certid)
				$('.checkSimple').each(function() {
					//attr获取自定义属性值
					if ($(this).attr('type_name') == (content.accttype)&& $(this).attr('cid') == (content.certid)) {
						//设置原生属性值
						$(this).prop('checked',true)
					}
			})
		})

			//执行事件
			__main()
		})

		//更新
		var update_type_cert_event = function() {
			var tAccountTypeCert = {}
			log('update_type_cert_event=============>')
			$('body').on('click', 'input', function() {
				log('update==========>', this)
				var flag = $(this).prop('checked')
				log('flag==========>', flag)
				accttype = $(this).attr('type_name')
				certid = $(this).attr('cid')
				log(accttype, certid)

				tAccountTypeCert.accttype = accttype
				tAccountTypeCert.certid = certid
				tAccountTypeCert.flag = flag
				var url = '${ctp}/servicectrl/type/update_type_cert'
				var data = tAccountTypeCert
				$.getJSON(url, data, function(result) {
					log(result)
				})
			})
		}
		var __main = function() {
			update_type_cert_event()
		}
	</script>
</body>



```

控制层：

1)接收异步传参实体返回json

![](https://i.imgur.com/7Pcl0Tu.png)

```java

	package com.atguigu.scw.manager.controller.manager;

	import java.util.Arrays;
	import java.util.List;

	import org.springframework.beans.factory.annotation.Autowired;
	import org.springframework.stereotype.Controller;
	import org.springframework.ui.Model;
	import org.springframework.web.bind.annotation.RequestMapping;
	import org.springframework.web.bind.annotation.RequestParam;
	import org.springframework.web.bind.annotation.ResponseBody;

	import com.atguigu.scw.manager.bean.TAccountTypeCert;
	import com.atguigu.scw.manager.bean.TCert;
	import com.atguigu.scw.manager.service.CertService;
	import com.atguigu.scw.manager.service.CertTypeService;
	import com.fasterxml.jackson.databind.ObjectMapper;

	@Controller
	@RequestMapping("/servicectrl/type")
	public class TypeController {

	    @Autowired
	    CertService certService;

	    @Autowired
	    CertTypeService cTypeService;

	    private static ObjectMapper MAPPER = new ObjectMapper();

	    @RequestMapping("/ctrl")
	    public String list(Model model) throws Exception {
	        System.out.println("分类管理界面============>");

	        // 1、先去数据库查出表格横向的显示数据
	        List<String> types = Arrays.asList("商业公司", "个体工商户", "个人经营", "政府及非营利组织");

	        // 2、在查出纵向要显示的标题
	        List<TCert> certs = certService.getAllCert();
	        System.out.println("资质===========>");
	        System.out.println(certs);

	        // 3、查询经营类型与资质关系中间表
	        // List<TAccountTypeCert> cTypeService.getAllCertType();
	        List<TAccountTypeCert> list_type_cert = cTypeService.getAllCertType();
	        System.out.println("经营类型===============>");
	        System.out.println(list_type_cert);

	        // 4、将数据放到模型域当中
	        model.addAttribute("types", types);
	        model.addAttribute("certs", certs);
	        // list转json字符串
	        String list_type_cert_json = MAPPER.writeValueAsString(list_type_cert);
	        System.out.println("经营类型--资质----");
	        System.out.println(list_type_cert_json);
	        model.addAttribute("list_type_cert_json", list_type_cert_json);

	        return "manager/servicemanager/type";
	    }

	    @RequestMapping("/update_type_cert")
	    @ResponseBody
	    public String update_type_cert(TAccountTypeCert tAccountTypeCert) {
	        System.out.println("更新经营类型---资质==========>");
	        System.out.println(tAccountTypeCert);
	        boolean flag = tAccountTypeCert.isFlag();
	        System.out.println("flag============>");
	        System.out.println(flag);
	        if (flag) {
	            System.out.println("增加一条记录========>");
	            int count = cTypeService.updateTypeCert(tAccountTypeCert);
	        } else {
	            System.out.println("移除一条记录========>");
	            cTypeService.removeTypeCert(tAccountTypeCert);
	        }

	        return "success";
	    }

	}


```

业务层：

1）单表的写读操作

```java


	package com.atguigu.scw.manager.service.impl;

	import java.util.List;

	import org.springframework.beans.factory.annotation.Autowired;
	import org.springframework.stereotype.Service;

	import com.atguigu.scw.manager.bean.TAccountTypeCert;
	import com.atguigu.scw.manager.bean.TAccountTypeCertExample;
	import com.atguigu.scw.manager.bean.TAccountTypeCertExample.Criteria;
	import com.atguigu.scw.manager.dao.TAccountTypeCertMapper;
	import com.atguigu.scw.manager.service.CertTypeService;

	@Service
	public class CertTypeServiceImpl implements CertTypeService {

	    @Autowired
	    TAccountTypeCertMapper mapper;

	    //查询资质和账户中间表
	    @Override
	    public List<TAccountTypeCert> getAllCertType() {
	        List<TAccountTypeCert> list_type_cert = mapper.selectByExample(null);
	        return list_type_cert;
	    }

	    // 增加经营类型--资质
	    @Override
	    public int updateTypeCert(TAccountTypeCert tAccountTypeCert) {
	        int count = mapper.insertSelective(tAccountTypeCert);
	        return count;
	    }

	    // 删除经营类型--资质
	    @Override
	    public int removeTypeCert(TAccountTypeCert tAccountTypeCert) {
	        TAccountTypeCertExample example = new TAccountTypeCertExample();
	        Criteria criteria = example.createCriteria();
	        criteria.andAccttypeEqualTo(tAccountTypeCert.getAccttype());
	        criteria.andCertidEqualTo(tAccountTypeCert.getCertid());
	        int count = mapper.deleteByExample(example);
	        return count;
	    }

	}


```


2、mysql插入中文乱码

1）数据库编码和web.xml配置均为utf-8，Controller读取到的是正确的中文，但是保存到数据库后变成"??"

解决

```xml

	<property name="url" value="jdbc:mysql://localhost:3306/scw_0325?useUnicode=true&amp;characterEncoding=UTF-8" ></property>

```

![](https://i.imgur.com/SYguSUW.png)
