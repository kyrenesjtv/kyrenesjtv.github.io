## 新建一个新的springboot2.X版本的项目

### 按照以下图片向导进行初始化

#### new project之后的图片
![01](springboot2_newProject/springboot2_springboot2_newProject_1.jpg)

#### 接下来选择类型，TYPE我是选择POM.xml，PACKAGING我是选择WAR。当然这个是因人而异的。
![02](springboot2_newProject/springboot2_springboot2_newProject_2.jpg)

#### 接下来选择spring boot versions 我是选择2.0.3 （因为我不清楚里面的snapshot是什么意思，英文意思是快照，但是我不清楚具体用途），里面的所有选项都不打钩，当然你想要集成什么可以选择，我个人建议是后续自己引入相对应的POM依赖即可
![03](springboot2_newProject/springboot2_springboot2_newProject_3.jpg)

#### 最后是项目构建构建成功的目录结构图
![04](springboot2_newProject/springboot2_springboot2_newProject_4.jpg)

#### 接下来是启动项目，但是启动的时候springboot会自动关闭进程。这个时候只要把POM文件里面的内置TOMTCAT中的<scope>注释掉即可
![05](springboot2_newProject/springboot2_springboot2_newProject_5.jpg)

#### 接下来访问项目，新建一个controller进行访问
![06](springboot2_newProject/springboot2_springboot2_newProject_6.jpg)
![07](springboot2_newProject/springboot2_springboot2_newProject_7.jpg)




