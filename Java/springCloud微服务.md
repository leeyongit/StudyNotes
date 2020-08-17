# springCloud 微服务

## SpringBoot 核心功能
- 独立运行  java -jar xxx.jar
- 内嵌web服务器
- 简化配置
- 准生产的应用监控
SpringBoot意在简化，是一种开发、配置风格

## SpringCloud的使命
**简化java的分布式系统**
SpringCloud 意在简化分布式，是功能的集合，风格统一

## SpringCloud VS 微服务
- Java的微服务
- 侧重功能，侧重开发

## SpringCloud 核心组件
- Netflix Eureka 服务发现组件
- Netflix Ribbon 客户端负载均衡组件
- Netflix Hystrix 断路器
- Netflix Zuul    服务网关
- Netflix Cloud Config 分布式配置

> Eureka实现了微服务的注册与发现，
> Ribbon实现了客户端侧的负载均衡，
> Feign实现了声明式的API调用，
> Hystrix实现容错。

feign和ribbon是Spring Cloud的Netflix中提供的两个实现软负载均衡的组件，Ribbon和Feign都是用于调用其他服务的，方式不同。Feign则是在Ribbon的基础上进行了一次改进，采用接口的方式。