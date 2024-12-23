# Spring Stereotype Annotations

## @Component
```java
@Component
public class BasicComponent {
    // 基础组件，其他构造型注解的父类
}
```

## @Controller
```java
@Controller
@RequestMapping("/api")
public class UserController {
    @GetMapping("/users")
    public List<User> getUsers() {
        return userService.findAll();
    }
}
```

## @RestController
```java
@RestController  // @Controller + @ResponseBody
@RequestMapping("/api/v1")
public class UserRestController {
    @GetMapping("/users")
    public ResponseEntity<List<User>> getUsers() {
        return ResponseEntity.ok(userService.findAll());
    }
}
```

## @Service
```java
@Service
public class UserService {
    @Autowired
    private UserRepository userRepository;
    
    public User findById(Long id) {
        return userRepository.findById(id);
    }
}
```

## @Repository
```java
@Repository
public class UserRepository {
    @PersistenceContext
    private EntityManager em;
    
    // 自动异常转换为 Spring 数据访问异常
    public User findById(Long id) {
        return em.find(User.class, id);
    }
}
```

## @Configuration
```java
@Configuration
public class AppConfig {
    @Bean
    public UserService userService() {
        return new UserService();
    }
    
    @Bean
    public DataSource dataSource() {
        return DataSourceBuilder
            .create()
            .url("jdbc:mysql://localhost/db")
            .build();
    }
}
```

## 特性总结

1. **组件扫描**
   - 所有stereotype注解都会被`@ComponentScan`扫描
   - 可通过basePackages指定扫描路径

2. **依赖注入**
   - 支持`@Autowired`自动装配
   - 支持`@Qualifier`限定符

3. **AOP支持**
   - 自动代理
   - 事务管理
   - 异常处理

4. **语义化**
   - 每个注解代表特定的层
   - 便于代码组织和维护

