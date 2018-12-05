## 同一个tomcat8下面部署多个springboot项目时候报错

### 报错信息如下图
![01](error/error.jpg)

### 需要在各个springboot项目中appcation.properties 加对项目的唯一标识

```
//个人认为最好用项目名来做唯一标识
 spring.jmx.default-domain=proj01
```
