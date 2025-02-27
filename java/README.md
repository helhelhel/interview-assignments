# 短域名服务技术方案


## 设计思路

* 算法

长域名和短域名的映射，可以转换为长域名与唯一数值型id、该id与短域名的映射关系。我们可以选取[0-9A-Za-z]作为合法字符集，如果短域名长度均为8，理论上能够支持8的62次方种长域名，相信能够满足需求。

为了方便的进行域名与id的相互映射，我们可以利用进制转换，即为长域名生成唯一自增id，并将该id转换为62进制，62进制的每一位固定对应[0-9A-Za-z]的一个字符。

系统需要记录已建立的长域名与短域名映射关系、id与长域名的映射关系。


* 并发控制

使用ConcurrentHashMap等并发集合进行控制，并发请求第一次获取相同长域名对应的短域名时，只允许一个请求进行计算，其他请求睡眠1s，唤起时判断是否有映射关系，有则返回，没有作异常返回（也可以选取重试等其他方案，可以展开讨论）。

* 异常返回

通过返回null，表明发生了异常：如参数错误等


## Jacoco单元测试覆盖率截图
![test-coverage](./doc/test-coverage.jpg)



## 架构设计图
![structure](./doc/structure.jpg)


## 系统性能测试方案以及测试结果
* 性能测试方案

性能测试采用[Gatling](https://gatling.io/)工具，编写scala(详情见ToSUrlSimulation.scala和ToLUrlSimulation.scala)。

压测设置为500qps、持续20s。由于条件限制，应用和Gatling均在本地电脑进行。 配置为MacBook Pro (13-inch, M1, 2020)，16GB。

* 测试结果

rt/scdt/toSUrl 压测结果：
![to-short-url-statistics](./doc/to-short-url-statistics.jpg)
![to-short-url-time](./doc/to-short-url-time.jpg)


rt/scdt/toLUrl 压测结果：
![to-long-url-statistics](./doc/to-long-url-statistics.jpg)
![to-long-url-time](./doc/to-long-url-time.jpg)



## 接口地址
Swagger：[http://localhost:8115/rt/swagger-ui.html](http://localhost:8115/rt/swagger-ui.html)


长域名转短域名 http://localhost:8115/rt/scdt/toSUrl

短域名转长域名 http://localhost:8115/rt/scdt/toLUrl

