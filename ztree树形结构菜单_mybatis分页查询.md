
---
title: ztree树形结构菜单_mybatis分页查询
date: 2019-01-22 12:58:55
tags:
---

1、二级树形菜单

效果图

![](https://i.imgur.com/xC5UACi.png)

表结构
![](https://i.imgur.com/yNutkpm.png)


业务层：

二级菜单实现思路：

1）查出父菜单（pid=0），通过遍历父菜单获得父菜单(id)，根据父菜单id值作为条件查出子菜单（子菜单pid=父菜单id），接着整理父子菜单关系；这种不推荐，假如所有菜单为18个，查出父菜单3个封装到集合时需要遍历18次，根据父菜单id作为查询子菜单的条件需要遍历3*18次。

2）一次性查出所有菜单18个，使用程序进行组合，这种推荐，以空间换时间。

封装菜单实体

![](https://i.imgur.com/bNHeGw9.png)


业务层：

```java {.line-numbers}

public List<TPermission> getAllMenus() {

    // 保存父菜单
    List<TPermission> menus = new ArrayList<TPermission>();
    Map<Integer, TPermission> map = new HashMap<Integer, TPermission>();

    // 查询所有菜单
    // 参数为null，表示不带条件查询菜单
    List<TPermission> list = mapper.selectByExample(null);
    System.out.println("所有菜单=========>");
    System.out.println(list);

    // 1、将所有菜单都放在map中
    // 都是引用，如果从map中拿到这个数据改变以后，map中页面变化
    for (TPermission tPermission : list) {
        map.put(tPermission.getId(), tPermission);
    }

    // 2、先封装父菜单，再将子菜单放入到父菜单中
    for (TPermission tPermission : list) {
        if (tPermission.getPid() == 0) {
            menus.add(tPermission);
        } else {
            // tPermission（子菜单），拿到父菜单
            Integer pid = tPermission.getPid();
            // 拿到父菜单；以pid的值作为map中的菜单id，就是父菜单
            TPermission p_menu = map.get(pid);
            // 拿到当前父菜单的子菜单；子菜单会有一些额外的问题
            // 这个list第一次获取是没有的，如果添加上一次以后。这个list是有的
            List<TPermission> childs = p_menu.getChilds();
            if (childs != null) {
                // 当前有子菜单
                childs.add(tPermission);
            } else {
                // 当前没有子菜单
                childs = new ArrayList<>();
                // 添加当前子菜单
                childs.add(tPermission);
                // 将当前整理好的childs设置进去
                p_menu.setChilds(childs);
            }
        }
    }

    System.out.println("父菜单===========>");
    System.out.println(menus);

    return menus;
}


```

控制层

1）将整理好的父子菜单数据放入到session域中，当前用户的这次会话一直使用，只需要去数据库查询一次

```java

@RequestMapping(value = "/main.html")
public String toMainPage(HttpSession session) {

    // 校验
    // 判断session中是否有这个用户，如果没有去登陆页面
    Object object = session.getAttribute(Constants.LOGIN_USER);
    if (object == null) {
        // 用户没登陆
        return "redirect:/login.jsp";
    } else {
        // 用户登陆才来到主页，session中没有菜单，或者菜单被我们从session中清除了
        if (session.getAttribute(Constants.USER_MENUS) == null) {
            // 1、查出所有菜单，在页面进行显示
            List<TPermission> menus = ps.getAllMenus();
            // 2、将查到的菜单放在session域中
            // 菜单这些数据没必要每次来到主页，都调用service方法进行查询；放在session用户，
            // 当前用户的这次会话一直使用，只需要去数据库查一次
            session.setAttribute(Constants.USER_MENUS, menus);
        }

        return "manager/main";
    }

}


```

前台：

1）引入：<%@ taglib prefix="fn" uri="http://java.sun.com/jsp/jstl/functions"%>
计算后台传过来的list集合的长度：${fn:length(p_menu.childs)}


```jsp

<%@ page language="java" contentType="text/html; charset=UTF-8"
	pageEncoding="UTF-8"%>
<%@taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c"%>
<%@ taglib prefix="fn" uri="http://java.sun.com/jsp/jstl/functions"%>
<div class="col-sm-3 col-md-2 sidebar">
	<div class="tree">
		<ul style="padding-left: 0px;" class="list-group">

			<li class="list-group-item tree-closed"><a href="main.html"><i
					class="glyphicon glyphicon-dashboard"></i> 控制面板</a></li>
			<!-- 遍历父菜单 -->
			<c:forEach items="${sessionScope.userMenus}" var="p_menu">
				<li class="list-group-item tree-closed"><span><i
						class="${p_menu.icon}"></i>${p_menu.name} <span class="badge"
						style="float: right">${fn:length(p_menu.childs)}</span></span>
					<ul style="margin-top: 10px; display: none;">
						<!-- 遍历子菜单 -->
						<c:forEach items="${p_menu.childs}" var="c_menu">
							<li style="height: 30px;"><a href="${ctp}/${c_menu.url}" data-action="${c_menu.id}"><i
									class="${c_menu.icon}"></i>${c_menu.name}</a></li>
						</c:forEach>
					</ul></li>
			</c:forEach>
		</ul>
	</div>
</div>


```


2、mybatis分页查询

根据用户名或者账号进行查询

效果图：

![](https://i.imgur.com/uATdmaF.png)

引入依赖：

```xml

<!-- 分页 插件 -->
<dependency>
	<groupId>com.github.pagehelper</groupId>
	<artifactId>pagehelper</artifactId>
</dependency>

```

业务层：

1）带条件查询

```java

@Override
public List<TUser> getAllUsersByCondition(String str) {
    System.out.println("UserServiceImpl.getAllUsersByCondition 带条件查询员工============>");
    
    // 查询条件拼接
    // 第一次创建的条件，默认使用and连接的
    TUserExample tUserExample = new TUserExample();
    Criteria criteria = tUserExample.createCriteria();
    Criteria criteria2 = tUserExample.createCriteria();
    if (!str.trim().equals("")) {
        criteria.andLoginacctLike("%" + str + "%");
        criteria.andUsernameLike("%" + str + "%");
    }
    tUserExample.or(criteria2);
    List<TUser> list_users_example = userMapper.selectByExample(tUserExample);
    return list_users_example;
}


```

控制层：

1）mybatis分页插件会对结果集进行包装，必须在查询前设置： PageHelper.startPage(pn, ps);

2）查询后输入框数据回显：model.addAttribute("sp", search);（转发）

```java

@RequestMapping("/list")
public String users(@RequestParam(value = "pn", defaultValue = "1") Integer pn,
        @RequestParam(value = "ps", defaultValue = "5") Integer ps,
        @RequestParam(value = "sp", defaultValue = "") String search, Model model) {
    System.out.println("UserController.users 用户列表显示页===========>");

    System.out.println("前台请求参数[第xxx页，每页显示xxx条数]==========>");
    System.out.println(pn);
    System.out.println(ps);
    // 分页显示数据,这里设置必须放到查询数据之前，否则前台会有问题
    PageHelper.startPage(pn, ps);

    List<TUser> list_users = userService.getAllUsersByCondition(search);
    System.out.println("查询到的所有用户列表========>");
    System.out.println(list_users);

    PageInfo<TUser> users = new PageInfo<>(list_users, 5);
    System.out.println("分页后的数据处理");
    System.out.println(users);
    // 将查询用户列表数据放在请求域中，表单查询参数回显到页面
    model.addAttribute("users", users);
    model.addAttribute("sp", search);

    return "manager/permission/user";
}


```

前台：

1）为所有分页连接绑定单击事件，让其动态的带上分页的查询参数

```js

var href = $(this).attr("href") + "&sp="+ $("input[name='sp']").val()<br/>
$(this).attr("href", href)

```


2)全选和全不选函数

```js

//全选/全不选函数
function checkall_reverse(check_all_btn, check_btn) {
	check_all_btn.click(function() {
		//如果是原生的属性，使用prop获取比较好
		check_btn.prop("checked", $(this).prop("checked"))
	})
	check_btn.click(function() {
		//当check_btn点满以后check_all_btn勾上，否则不选中
		//获取被选中的checkbtn个数
		var flag = check_btn.filter(":checked").length == check_btn.length
		check_all_btn.prop("checked", flag);
	})
}


```


```html
	

<%@ page language="java" contentType="text/html; charset=UTF-8"
	pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html lang="UTF-8">
<head>
<meta charset="UTF-8">
<meta http-equiv="X-UA-Compatible" content="IE=edge">
<meta name="viewport" content="width=device-width, initial-scale=1">
<meta name="description" content="">
<meta name="author" content="">


<%@include file="/WEB-INF/includes/css-file.jsp"%>
<link rel="stylesheet" href="${ctp}/css/main.css">
<style>
.tree li {
	list-style-type: none;
	cursor: pointer;
}

table tbody tr:nth-child(odd) {
	background: #F4F4F4;
}

table tbody td:nth-child(even) {
	color: #C00;
}
</style>
</head>

<body>

	<%
	    //设置导航条上的显示
				pageContext.setAttribute("navinfo", "用户维护");
				//设置点击高亮效果
				pageContext.setAttribute("curUrl", "permission/user/list");
	%>
	<!-- 引入navbar-->
	<%@include file="/WEB-INF/includes/nav-bar.jsp"%></nav>

	<div class="container-fluid">
		<div class="row">
			<!-- 引入树形菜单 -->
			<%@include file="/WEB-INF/includes/user_menu.jsp"%>
			<div class="col-sm-9 col-sm-offset-3 col-md-10 col-md-offset-2 main">
				<div class="panel panel-default">
					<div class="panel-heading">
						<h3 class="panel-title">
							<i class="glyphicon glyphicon-th"></i> 数据列表
						</h3>
					</div>
					<div class="panel-body">
						<form class="form-inline" role="form" style="float: left;"
							action="${ctp }/permission/user/list" method="post">
							<div class="form-group has-feedback">
								<div class="input-group">
									<div class="input-group-addon">查询条件</div>
									<input class="form-control has-success" type="text" name="sp"
										placeholder="用户名/账号查询" value="${sp}">
								</div>
							</div>
							<button type="submit" class="btn btn-warning">
								<i class="glyphicon glyphicon-search"></i> 查询
							</button>
						</form>
						<button type="button" class="btn btn-danger"
							style="float: right; margin-left: 10px;">
							<i class=" glyphicon glyphicon-remove"></i> 删除
						</button>
						<button type="button" class="btn btn-primary"
							style="float: right;" onclick="window.location.href='add.html'">
							<i class="glyphicon glyphicon-plus"></i> 新增
						</button>
						<br>
						<hr style="clear: both;">
						<div class="table-responsive">
							<table class="table  table-bordered">
								<thead>
									<tr>
										<th width="30">#</th>
										<th width="30"><input type="checkbox" id="checkall_btn"></th>
										<th>账号</th>
										<th>名称</th>
										<th>邮箱地址</th>
										<th width="100">操作</th>
									</tr>
								</thead>
								<tbody>
									<c:forEach items="${users.list}" var="user">
										<tr>
											<td>${user.id}</td>
											<td><input type="checkbox" class="single_check"></td>
											<td>${user.loginacct }</td>
											<td>${user.username }</td>
											<td>${user.email }</td>
											<td>
												<button type="button" class="btn btn-success btn-xs">
													<i class=" glyphicon glyphicon-check"></i>
												</button>
												<button type="button" class="btn btn-primary btn-xs">
													<i class=" glyphicon glyphicon-pencil"></i>
												</button>
												<button type="button" class="btn btn-danger btn-xs">
													<i class=" glyphicon glyphicon-remove"></i>
												</button>
											</td>
										</tr>
									</c:forEach>
								</tbody>
								<tfoot>
									<tr>
										<td colspan="6" align="center">

											<ul class="pagination">
												<!-- 即使点击分页连接也应该带上查询条件的值 -->
												<!-- 给分页超链接绑定单击事件； -->
												<li><a href="${ctp}/permission/user/list?pn=1">首页</a></li>
												<!-- 是否还有前一页 -->
												<c:if test="${users.hasPreviousPage}">
													<li><a
														href="${ctp}/permission/user/list?pn=${users.prePage}">上一页</a></li>
												</c:if>


												<!-- 遍历页数 -->
												<c:forEach items="${users.navigatepageNums}" var="pn">
													<!-- 当前页 -->
													<c:if test="${pn==users.pageNum }">
														<li class="active"><a
															href="${ctp}/permission/user/list?pn=${pn}">${pn}<span
																class="sr-only">(current)</span></a></li>
													</c:if>
													<c:if test="${pn != users.pageNum }">
														<li><a href="${ctp}/permission/user/list?pn=${pn}">${pn }</a></li>
													</c:if>
												</c:forEach>
												<!--是否还有下一页  -->
												<c:if test="${users.hasNextPage}">
													<li><a
														href="${ctp}/permission/user/list?pn=${users.nextPage}">下一页</a></li>
												</c:if>
												<li><a
													href="${ctp}/permission/user/list?pn=${users.pages}">末页</a></li>
											</ul>
										</td>
									</tr>

								</tfoot>
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
			});
		});
		$("tbody .btn-success").click(function() {
			window.location.href = "assignRole.html";
		});
		$("tbody .btn-primary").click(function() {
			window.location.href = "edit.html";
		});

		
		//当前页面所在的哪个超链接是color:red
		//他的父list-group-item.  tree-closed是没有的
		//找到当前页面的a连接
		//使用css为某个元素加样式  list-group-item
		//为所有分页连接绑定单击事件，让其动态的带上分页的查询参数
		$(".pagination").find("a").click(
				function() {
					//1、获取到查询表单的查询参数
					//不禁用默认行为，而是为超链接多拼装上查询条件
					//为超链接动态拼装查询条件
					var href = $(this).attr("href") + "&sp="
							+ $("input[name='sp']").val()
					$(this).attr("href", href)
				})

		//这是调用了抽取过来的方法；
		checkall_reverse($("#checkall_btn"), $(".single_check"))
	</script>
</body>
</html>


```