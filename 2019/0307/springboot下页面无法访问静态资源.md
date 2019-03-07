### springboot 访问页面时，静态资源报错。因为我是application.properties给项目设置了项目名的  -- > server.servlet.context-path=/dingdang。然后访问静态资源死活没有项目名

- ![](1551924887(1).jpg)

### 解决步骤
1. 现在pom文件里面引入依赖
~~~
<!--访问静态资源 -->
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-thymeleaf</artifactId>
		</dependency>
~~~
2. 在配置文件里面 设置静态资源路径

~~~
 静态资源
Spring.resources.static-locations= classpath:/

 页面跳转
spring.thymeleaf.prefix=classpath:/templates/
spring.thymeleaf.suffix:.html
~~~

3. 在页面中引入标签

~~~
<html lang="en" xmlns="http://www.w3.org/1999/xhtml" xmlns:th="http://www.thymeleaf.org">



<head>
    <meta charset="UTF-8">
    <title>部门页面</title>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
    <!-- 引入静态资源不要忘记加type 和rel 。 还有要在href和src前面加上th: 。 接下来再路径里面加上@{} 去获取项目名。 PS: 不要忘记引入标签库。 最上面的html里面的内容 -->
    <!-- jquery -->
    <script type="text/javascript" th:src="@{/static/jquery/jquery-1.11.3.js}"></script>
    <!--element  -->
    <link rel="stylesheet" th:href="@{/static/element/css/index.css}">
    <script type="text/javascript" th:src="@{/static/element/js/index.js}"></script>
    <!-- vue -->
    <script type="text/javascript" th:src="@{/static/vue/js/vue.js}"></script>

</head>
~~~

#### 最后结果
- ![](1551925199(2).jpg)
