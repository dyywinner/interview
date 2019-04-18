# 根据简历准备内容
## 实习
- 设备 N2K/N5K
- cisco 3500,4500,6500,7600

## 开发项目
### http客户端
[http源码](https://blog.csdn.net/u012234115/article/details/79596826) 
- 实现Get、Post、UpFile（文件上传）等常用操作，需要完善的部分是Cookie没有自动提取和传输
- 将mongoose中提供的http相关api封装成了httpserver类和httpclient类
-  服务器支持host静态页面资源，也支持rest api调用
- 需要手动设置loop polling的时间间隔
- 可以自定义静态页面根路径，注册和解注册自定义api函数回调
- 某些变量必须声明定义成全局或者静态变量
- client每次请求都是一个独立的请求
- 请求函数中加入回调用于处理网络返回
