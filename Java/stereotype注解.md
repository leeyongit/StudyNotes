注解
------

# org.springframework.stereotype注解
1、@controller 控制器（注入服务）
2、@service 服务（注入dao）
3、@repository dao（实现dao访问）
4、@component （把普通pojo实例化到spring容器中，相当于配置文件中的<bean id="" class=""/>）
  @Component,@Service,@Controller,@Repository注解的类，并把这些类纳入进spring容器中管理。