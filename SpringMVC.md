# 谷粒商城

## 网关

https://docs.spring.io/spring-cloud-gateway/docs/current/reference/html/

### 商品服务 三级分类

#### 配置网关路由与路径重写

配置网关路由：

1）在yml文件配置

应用模块：

```yaml
  cloud:
    nacos:
      discovery:
        server-addr:  127.0.0.1:8848
  application:
    name: gulimall-coupon
```

gateway模块：

```yaml
spring:
  cloud:
    gateway:
      routes:
        - id: test_route
          uri: https://www.baidu.com
          ##断言：如果url=百度，则跳转到uri
          predicates:
            - Query=url,baidu
        - id: test_route
          uri: https://www.qq.com
          predicates:
            - Query=url,qq
        - id: admin_route
          uri: lb://renren-fast
          predicates:
            - Path=/api/**
     ###路径重写：
          filters:
                            ###前面的路径变到后面的路径
            - RewritePath=/api/(?<segment>.*),/reneren-fast/$\{segment}        

```

跨域：

![image-20220525101427841](SpringMVC.assets/image-20220525101427841.png)

![image-20220525105930299](SpringMVC.assets/image-20220525105930299.png)

![image-20220525105945373](SpringMVC.assets/image-20220525105945373.png)

![image-20220525110340374](SpringMVC.assets/image-20220525110340374.png)（利用网关)



（多用）逻辑删除：不是真的删除，而是用一个标志位表示是否删除

## nacos

#### 上传阿里云：

![image-20220527105331738](SpringMVC.assets/image-20220527105331738.png)

一

导入依赖

```java
<dependency>
			<groupId>com.aliyun.oss</groupId>
			<artifactId>aliyun-sdk-oss</artifactId>
			<version>2.8.3</version>
			<scope>test</scope>
</dependency>
```



```java
@Test
	public void testUpload() throws FileNotFoundException {
		// Endpoint以杭州为例，其它Region请按实际情况填写。
		String endpoint = "oss-cn-chengdu.aliyuncs.com";
// 云账号AccessKey有所有API访问权限，建议遵循阿里云安全最佳实践，创建并使用RAM子账号进行API访问或日常运维，请登录 https://ram.console.aliyun.com 创建。
		String accessKeyId = "LTAI5tJrGmaPFD8xr83vjTa5";
		String accessKeySecret = "ZSwtpyUmGWEHMKBoiCs2mZjWA51N7K";

// 创建OSSClient实例。
		OSS ossClient = new OSSClientBuilder().build(endpoint, accessKeyId, accessKeySecret);

// 上传文件流。
		InputStream inputStream = new FileInputStream("E:\\tx\\_20220418205259.jpg");
		ossClient.putObject("gulimall-liutianxu", "_20220418205259.jpg", inputStream);

// 关闭OSSClient。
		ossClient.shutdown();
		System.out.println("上传成功");
	}

```

第二种方法：

导入starter依赖

```java
<dependency>
		<groupId>com.alibaba.cloud</groupId>
		<artifactId>spring-cloud-starter-alicloud-oss</artifactId>
		<version>2.2.0.RELEASE</version>
</dependency>
```

在application中配置账号密码

![image-20220526204840772](SpringMVC.assets/image-20220526204840772.png)

然后就不需要再文件里配置了

：

```java
@Test
public void testUpload() throws FileNotFoundException {
		// Endpoint以杭州为例，其它Region请按实际情况填写。
//		String endpoint = "oss-cn-chengdu.aliyuncs.com";
//// 云账号AccessKey有所有API访问权限，建议遵循阿里云安全最佳实践，创建并使用RAM子账号进行API访问或日常运维，请登录 https://ram.console.aliyun.com 创建。
//		String accessKeyId = "LTAI5tJrGmaPFD8xr83vjTa5";
//		String accessKeySecret = "ZSwtpyUmGWEHMKBoiCs2mZjWA51N7K";
//
//// 创建OSSClient实例。
//		OSS ossClient = new OSSClientBuilder().build(endpoint, accessKeyId, accessKeySecret);
//
//// 上传文件流。
		/**
		 * 上面不需要了
		 */
InputStream inputStream = new FileInputStream("E:\\tx\\_20220418205259.jpg");
		ossClient.putObject("gulimall-liutianxu", "_20220418205259.jpg", inputStream);

// 关闭OSSClient。
		ossClient.shutdown();
		System.out.println("上传成功");
	}

```

### 表单校验：

对实体类的属性的校验

![image-20220531180418126](SpringMVC.assets/image-20220531180418126.png)

该类可以校验很多：

如@Email：必须为邮箱

@NotNull:不能为空

可以自定义校验：用@Pattren，里面是正则表达式

![image-20220531181237329](SpringMVC.assets/image-20220531181237329.png)

![image-20220531181528433](SpringMVC.assets/image-20220531181528433.png)

可自定义messege提示：

![image-20220531180743856](SpringMVC.assets/image-20220531180743856.png)

返回为R结果类：利用@Valid注解



![image-20220531181129666](SpringMVC.assets/image-20220531181129666.png)

#### 统一处理异常：

![image-20220531181528433](SpringMVC.assets/image-20220531181528433.png)

可以不接收异常，当发现异常时抛出去：

![image-20220531182215083](SpringMVC.assets/image-20220531182215083.png)

全部注释掉，建立一个 统一异常类：

![image-20220531183820320](SpringMVC.assets/image-20220531183820320.png)

状态码的设置建议规范：

![image-20220531183909901](SpringMVC.assets/image-20220531183909901.png)

![image-20220531190414258](SpringMVC.assets/image-20220531190414258.png)

#### 分组校验：

![image-20220531193127012](SpringMVC.assets/image-20220531193127012.png)

![image-20220531195814487](SpringMVC.assets/image-20220531195814487.png) 

![image-20220531192720218](SpringMVC.assets/image-20220531192720218.png)

#### 自定义注解：

目的：

![image-20220531193648838](SpringMVC.assets/image-20220531193648838.png)

方法：

![image-20220531194959633](SpringMVC.assets/image-20220531194959633.png)

1）在所有valid注解的配置文件地址处自定义一个配置文件：

![image-20220531195535453](SpringMVC.assets/image-20220531195535453.png)

建类：

![image-20220531194747459](SpringMVC.assets/image-20220531194747459.png)

2）自定义注解器：自定义一个类实现接口Con...

![image-20220531194901400](SpringMVC.assets/image-20220531194901400.png)

3）关联：

![image-20220531195157529](SpringMVC.assets/image-20220531195157529.png)

## Nginx

![image-20220606204537835](SpringMVC.assets/image-20220606204537835.png)



![image-20220606204025912](SpringMVC.assets/image-20220606204025912.png)

在浏览器输入gulimall.com能转到192.168.56.1：10000

大致步骤：

1.浏览器输入gulimall.com：windows的host配置里指定了gulimall.com->（映射为）192.168.56.1(虚拟机地址)

2.虚拟机的nginx在监听80端口（浏览器默认端口），代理域名为gulimall.com，如下 

![image-20220606204756260](SpringMVC.assets/image-20220606204756260.png)



### nginx负载均衡

1.在nginx的http块中配置上游服务器，（nginx和服务之间通过网关代理。）

![image-20220606210556655](SpringMVC.assets/image-20220606210556655.png)

2.其次server块中负载均衡的配置：

![image-20220606210810906](SpringMVC.assets/image-20220606210810906.png)

![image-20220606212252722](SpringMVC.assets/image-20220606212252722.png)

3.网关配置：

![image-20220606212557093](SpringMVC.assets/image-20220606212557093.png)

注意：

![image-20220606213439637](SpringMVC.assets/image-20220606213439637.png)

![image-20220606213400015](SpringMVC.assets/image-20220606213400015.png)

![image-20220606214013174](SpringMVC.assets/image-20220606214013174.png)

### nginx动静分离

![image-20220606214831979](SpringMVC.assets/image-20220606214831979.png)

在nginx下建立一个static文件夹

![image-20220606215238775](SpringMVC.assets/image-20220606215238775.png)

将之前的静态资源替换成static文件夹下，同时删除之前的文件

![image-20220606215543839](SpringMVC.assets/image-20220606215543839.png)

![image-20220606215657881](SpringMVC.assets/image-20220606215657881.png)

![image-20220606215713859](SpringMVC.assets/image-20220606215713859.png)

![image-20220606220001955](SpringMVC.assets/image-20220606220001955.png)

从default配置文件中复制路径到nginx.conf文件里，配置路径

![image-20220606220143078](SpringMVC.assets/image-20220606220143078.png)

## 缓存

最简单map<key,value>

### 本地缓存

一致性问题：

![image-20220607112057809](SpringMVC.assets/image-20220607112057809.png)

解决：

![image-20220607112245244](SpringMVC.assets/image-20220607112245244.png)

### Redis

![image-20220607210450416](SpringMVC.assets/image-20220607210450416.png)

2）

![image-20220607210520791](SpringMVC.assets/image-20220607210520791.png)

主要用五大类型：

![image-20220607210225289](SpringMVC.assets/image-20220607210225289.png)

例子：

![image-20220607210352255](SpringMVC.assets/image-20220607210352255.png)

真正的业务逻辑：

![image-20220607211424890](SpringMVC.assets/image-20220607211424890.png)

会产生堆外内存溢出：OutOfDirectMemoryError

![image-20220607212229932](SpringMVC.assets/image-20220607212229932.png)

#### 高并发场景：

1.加本地锁：

每一个服务都有一把锁。

![image-20220608204018427](SpringMVC.assets/image-20220608204018427.png)

可以第一个拿到锁之后查数据库就将数据放入缓存。

2.分布式加锁

![image-20220608205639348](SpringMVC.assets/image-20220608205639348.png)

![image-20220608212108066](SpringMVC.assets/image-20220608212108066.png)



问题：死锁

解决：加过期时间

redis中有setnx:只有 没有时才set：

![image-20220608210212923](SpringMVC.assets/image-20220608210212923.png)

![image-20220608210244805](SpringMVC.assets/image-20220608210244805.png)

问题：加锁没有原子性，总会有加了之后就宕机使得无法加过期时间

解决：使得加锁和设置过期时间有原子性

redis中有setex：加锁时就设置过期时间，

既使用nx又使用ex

![image-20220608212659646](SpringMVC.assets/image-20220608212659646.png)



![image-20220608211926352](SpringMVC.assets/image-20220608211926352.png)

问题：删除锁的时候可能会删别人的锁：

业务代码太长，删除锁时锁已经过期被别人占用了

解决：uuid，判断是自己的拿的锁，再删

![image-20220608212942213](SpringMVC.assets/image-20220608212942213.png)

![image-20220608213006275](SpringMVC.assets/image-20220608213006275.png)

问题：执行完毕未过期并且此刻是自己的uuid，但是发送删除锁的请求的时候过期了，在此期间被其他人拿到，导致删除别人的锁

总之，删除锁没有原子性，对比uuid和删除锁是两步，不具有原子性

解决:解锁保证原子性

![image-20220608211845802](SpringMVC.assets/image-20220608211845802.png)

利用脚本语言

![image-20220608213848148](SpringMVC.assets/image-20220608213848148.png)

### redisson

![image-20220609205537700](SpringMVC.assets/image-20220609205537700.png)

Redisson的可重入锁RLock实现了java的lock接口

示例：

![image-20220609211606887](SpringMVC.assets/image-20220609211606887.png)

**Redisson不会死锁：**

1.它可以给锁自动续期（看门狗机制）

2.只要业务运行完成 ，默认30s会自动解锁

![image-20220609212300685](SpringMVC.assets/image-20220609212300685.png)

![image-20220609213358601](SpringMVC.assets/image-20220609213358601.png)

如果直接lock(),会有看门狗自动续期的机制

如果自定义lock($leastitme$)，没有自动续期

但建议使用第二种（时间给大一点）





### 读写锁

读数据必须等待写锁释放，否则一直阻塞

写锁

![image-20220612200533136](SpringMVC.assets/image-20220612200533136.png)

读锁：

![image-20220612200803131](SpringMVC.assets/image-20220612200803131.png)

![image-20220612201229111](SpringMVC.assets/image-20220612201229111.png)

### 闭锁



![image-20220612202101301](SpringMVC.assets/image-20220612202101301.png)

### 信号量：

![image-20220612203213706](SpringMVC.assets/image-20220612203213706.png)

**可以做限流**（只给10000个线程，多了就不给）

### 缓存一致性

![image-20220612204149124](SpringMVC.assets/image-20220612204149124.png)

更新数据库就删除对应缓存

![image-20220612204345744](SpringMVC.assets/image-20220612204345744.png)

![image-20220612204600667](SpringMVC.assets/image-20220612204600667.png)

**缓存数据**+*过期时间**已经能解决大部分需求



#### 中间件：Canal

![image-20220612205130750](SpringMVC.assets/image-20220612205130750.png)

![image-20220612205341473](SpringMVC.assets/image-20220612205341473.png)

经常读写会不好



#### Spring Cache

![image-20220614195036618](SpringMVC.assets/image-20220614195036618.png)

使用：

![image-20220614200322525](SpringMVC.assets/image-20220614200322525.png)

![image-20220614203119562](SpringMVC.assets/image-20220614203119562.png)

自定义配置：在config下自己写配置类

![image-20220614203836897](SpringMVC.assets/image-20220614203836897.png)

失效模式：

![image-20220614203656509](SpringMVC.assets/image-20220614203656509.png)

还有@CachePut：双写模式

SpringCache的不足：

![image-20220614205228637](SpringMVC.assets/image-20220614205228637.png)



![image-20220614205147116](SpringMVC.assets/image-20220614205147116.png)

### nginx检索服务

![image-20220614210845005](SpringMVC.assets/image-20220614210845005.png)

### 异步线程

![image-20220615205440750](SpringMVC.assets/image-20220615205440750.png)





![image-20220615211104197](SpringMVC.assets/image-20220615211104197.png)





![image-20220615212415858](SpringMVC.assets/image-20220615212415858.png)

七大参数：

![image-20220615212936773](SpringMVC.assets/image-20220615212936773.png)

最后的拒绝策略：默认是AbortPolicy，直接丢弃

![image-20220615214428088](SpringMVC.assets/image-20220615214428088.png)

应用：

![image-20220615214336806](SpringMVC.assets/image-20220615214336806.png)

![image-20220615213927802](SpringMVC.assets/image-20220615213927802.png)

几种常用的线程池：

![image-20220615214147886](SpringMVC.assets/image-20220615214147886.png)

#### CompletableFuture：异步编排

![image-20220616201702372](SpringMVC.assets/image-20220616201702372.png)

runAsync:

![image-20220616201114139](SpringMVC.assets/image-20220616201114139.png)

supplyAsync

![image-20220616202355992](SpringMVC.assets/image-20220616202355992.png)

supplyAsync好处：返回一个future，可以用很多方法

![image-20220616202517954](SpringMVC.assets/image-20220616202517954.png)

 完成回调和异常感知：

![image-20220616203158594](SpringMVC.assets/image-20220616203158594.png)

![image-20220616203119921](SpringMVC.assets/image-20220616203119921.png)

#### 最终处理：

![image-20220616203533141](SpringMVC.assets/image-20220616203533141.png)

![image-20220616204104043](SpringMVC.assets/image-20220616204104043.png)

#### 线程串行化：

![image-20220616204817962](SpringMVC.assets/image-20220616204817962.png)

加Async:再开一个新线程

不加：用同一个线程

![image-20220616205252070](SpringMVC.assets/image-20220616205252070.png)

#### 两任务组合：&&

![image-20220616205524379](SpringMVC.assets/image-20220616205524379.png)

例子：



![image-20220616205752989](SpringMVC.assets/image-20220616205752989.png)

thenCombineAsync

![image-20220616210417355](SpringMVC.assets/image-20220616210417355.png)

**叫run的方法都是直接执行，不感知之前的结果**

#### 两任务执行一个就执行：||

![image-20220616212211906](SpringMVC.assets/image-20220616212211906.png)

![image-20220616210809941](SpringMVC.assets/image-20220616210809941.png)

例子：



![image-20220616211149163](SpringMVC.assets/image-20220616211149163.png)



![image-20220616211705547](SpringMVC.assets/image-20220616211705547.png)



![image-20220616212135114](SpringMVC.assets/image-20220616212135114.png)

#### 多任务组合：

![image-20220616212640278](SpringMVC.assets/image-20220616212640278.png)

![image-20220616213916167](SpringMVC.assets/image-20220616213916167.png)

### 认证服务

#### 加密

![image-20220623205912238](SpringMVC.assets/image-20220623205912238.png)

![image-20220623210559918](SpringMVC.assets/image-20220623210559918.png)

自动盐值，判断：密码+数据库中的密码（一串）

![image-20220623210816281](SpringMVC.assets/image-20220623210816281.png)

#### 登录

![image-20220623214018585](SpringMVC.assets/image-20220623214018585.png)



#### session分布式

**一台服务器保存cookie,到其他的服务器下没有这个cookie了**

两方面：1.服务器水平扩展（负载均衡）2.子域的分布式问题（gulimall.product的session，在gulimall下就没有了）

![image-20220624192741375](SpringMVC.assets/image-20220624192741375.png)

解决：

![image-20220624192936602](SpringMVC.assets/image-20220624192936602.png)

![image-20220624193145261](SpringMVC.assets/image-20220624193145261.png)

用的最多：

![image-20220624193215021](SpringMVC.assets/image-20220624193215021.png)

![image-20220624193447055](SpringMVC.assets/image-20220624193447055.png)



2.子域问题：

**SpringSession**：

![image-20220624205135870](SpringMVC.assets/image-20220624205135870.png)



![image-20220624210025805](SpringMVC.assets/image-20220624210025805.png)

扩大cookie作用域

![image-20220624204741929](SpringMVC.assets/image-20220624204741929.png)

首先将发给浏览器的cookie的session放入后台redis

其次发session时其作用域扩大到全局，且session id是相同的

**springsession核心原理**：

![image-20220624211226142](SpringMVC.assets/image-20220624211226142.png)

多系统：多个不同域名的登录 

![image-20220624212628584](SpringMVC.assets/image-20220624212628584.png)





## RabbitMQ:

### 可靠投递

#### 发送端：

![image-20220714173548207](SpringMVC.assets/image-20220714173548207.png)

![image-20220714163150774](SpringMVC.assets/image-20220714163150774.png)

发送端确认：

![image-20220714163123502](SpringMVC.assets/image-20220714163123502.png)

![image-20220714173811299](SpringMVC.assets/image-20220714173811299.png)



队列确认：

![image-20220714173132331](SpringMVC.assets/image-20220714173132331.png)

#### 接收端：

![image-20220715153044183](SpringMVC.assets/image-20220715153044183.png)
![image-20220715153124636](SpringMVC.assets/image-20220715153124636.png)

用法：

![image-20220715153158509](SpringMVC.assets/image-20220715153158509.png)

![image-20220715153337006](SpringMVC.assets/image-20220715153337006.png)



## Feign远程调用丢失请求头：

同步:![image-20220715164028493](SpringMVC.assets/image-20220715164028493.png)

异步：

![image-20220715165113104](SpringMVC.assets/image-20220715165113104.png)

解决：

![image-20220715164943069](SpringMVC.assets/image-20220715164943069.png)

## 分布式服务：

同类中传播行为失效：

![image-20220716172443046](SpringMVC.assets/image-20220716172443046.png)

![image-20220716172507112](SpringMVC.assets/image-20220716172507112.png)







