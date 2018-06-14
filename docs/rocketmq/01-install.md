
## RocketMQ-01-安装和Spring Boot 基本使用

###Mac 单机安装RocketMQ

[官方网站](http://rocketmq.apache.org/docs/quick-start/) 

下载源码编译

mvn install 之后启动之前要修改部分配置 MAC要修改下默认路径之后才可以启动

Mac 查看Java_HOME 
```
/usr/libexec/java_home -V 
```
修改配置:

注意配置路径: **distribution/target/apache-rocketmq**

1.vim bin/runserver.sh
```
[ ! -e "$JAVA_HOME/bin/java" ] && JAVA_HOME=/Library/Java/JavaVirtualMachines/jdk1.8.0_122.jdk/Contents/Home #改成你自己的JAVA_HOME
#[ ! -e "$JAVA_HOME/bin/java" ] && JAVA_HOME=/usr/java  # 这行注释掉
[ ! -e "$JAVA_HOME/bin/java" ] && error_exit "Please set the JAVA_HOME variable in your environment, We need java(x64)!"


#===========================================================================================
# JVM Configuration
#===========================================================================================
JAVA_OPT="${JAVA_OPT} -server -Xms1g -Xmx1g -Xmn512m -XX:MetaspaceSize=128m -XX:MaxMetaspaceSize=320m"
# JVM 参数可以修改小点

```
2.vim bin/runbroker.sh
```
[ ! -e "$JAVA_HOME/bin/java" ] && JAVA_HOME=/Library/Java/JavaVirtualMachines/jdk1.8.0_122.jdk/Contents/Home #改成你自己的JAVA_HOME
#[ ! -e "$JAVA_HOME/bin/java" ] && JAVA_HOME=/usr/java # 这行注释掉
[ ! -e "$JAVA_HOME/bin/java" ] && error_exit "Please set the JAVA_HOME variable in your environment, We need java(x64)!"

#===========================================================================================
# JVM Configuration
#===========================================================================================
JAVA_OPT="${JAVA_OPT} -server -Xms1g -Xmx1g -Xmn512m"  # JVM参数修改小了

```

3.vim bin/tools.sh

```
[ ! -e "$JAVA_HOME/bin/java" ] && JAVA_HOME=/Library/Java/JavaVirtualMachines/jdk1.8.0_122.jdk/Contents/Home #改成你自己的JAVA_HOME
#[ ! -e "$JAVA_HOME/bin/java" ] && JAVA_HOME=/usr/java    # 这行注释掉
[ ! -e "$JAVA_HOME/bin/java" ] && error_exit "Please set the JAVA_HOME variable in your environment, We need java(x64)!"

```

尝试启动以及按照官方文档发送和接收消息

Start Name Server

```
sh bin/mqnamesrv

```
Start Broker

```
sh bin/mqbroker -n localhost:9876
```


Shutdown Servers
```
> sh bin/mqshutdown broker
The mqbroker(36695) is running...
Send shutdown request to mqbroker(36695) OK

> sh bin/mqshutdown namesrv
The mqnamesrv(36664) is running...
Send shutdown request to mqnamesrv(36664) OK

```
正常后可以后台启动

## 创建Spring Boot 工程
例子参考:[Spring Boot 中使用 RocketMQ](https://mp.weixin.qq.com/s/wS5R5lKwWqhXNebQ4xy4-g)

遇到的问题:

①. No route info of this topic

原因可能有:
1. nameserver 或者 broker没有正常启动
2. broker 中没有相应的topic，并且不允许自动创建
3. producer 没有正确连接 broker


第二个可以启动broker时 设置自动创建:
```
nohup sh bin/mqbroker -n localhost:9876 autoCreateTopicEnable=true
```


②. console 项目报错 connect to <:10909> failed 

这个是broker获取机器IP时获取的可能不对，可以查看控制台主题TopicTest的路由brokerAddrs地址是否正确

可以启动broker时 绑定IP
```
nohup sh bin/mqbroker -n localhost:9876 autoCreateTopicEnable=true brokerIP1=127.0.0.1 &
```


参考:

1.[RocketMQ nameserver start error — Could not find or load main class](https://stackoverflow.com/questions/43709352/rocketmq-nameserver-start-error-could-not-find-or-load-main-class)

