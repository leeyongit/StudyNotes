# JPA 注解指南

## 实体类基本注解

```java
@Entity  // 声明这是一个实体类
@Table(name = "user_table")  // 指定表名
public class User {
    @Id  // 声明主键
    @GeneratedValue(strategy = GenerationType.IDENTITY)  // 主键生成策略
    private Long id;
    
    @Column(name = "user_name", length = 50)  // 指定列名和属性
    private String userName;
    
    @Transient  // 非持久化字段
    private String tempField;
    
    @Temporal(TemporalType.TIMESTAMP)  // 时间类型
    private Date createTime;
}
```

## 关系映射注解

```java
// 一对一关系
@OneToOne
@JoinColumn(name = "address_id")
private Address address;

// 一对多关系
@OneToMany(mappedBy = "user", cascade = CascadeType.ALL)
private List<Order> orders;

// 多对一关系
@ManyToOne
@JoinColumn(name = "department_id")
private Department department;

// 多对多关系
@ManyToMany
@JoinTable(
    name = "user_role",
    joinColumns = @JoinColumn(name = "user_id"),
    inverseJoinColumns = @JoinColumn(name = "role_id")
)
private Set<Role> roles;
```

## 常用属性配置

```java
@Column(
    name = "column_name",        // 列名
    unique = true,              // 唯一约束
    nullable = false,           // 非空约束
    length = 100,              // 长度限制
    precision = 10,            // 精度
    scale = 2                  // 小数位数
)
private String field;

// 枚举类型映射
@Enumerated(EnumType.STRING)
private UserStatus status;

// 大文本字段
@Lob
private String content;
```

## 继承策略注解

```java
@MappedSuperclass  // 父类注解
public abstract class BaseEntity {
    @Id
    @GeneratedValue
    private Long id;
    
    @Version  // 乐观锁版本控制
    private Integer version;
}

@Entity
@Inheritance(strategy = InheritanceType.SINGLE_TABLE)  // 继承策略
@DiscriminatorColumn(name = "type")  // 区分字段
public class Animal extends BaseEntity {
    // 实体字段
}
```

## 查询相关注解

```java
@NamedQuery(
    name = "User.findByStatus",
    query = "SELECT u FROM User u WHERE u.status = :status"
)
@Entity
public class User {
    // 实体定义
}

// 索引注解
@Table(
    indexes = {
        @Index(name = "idx_username", columnList = "userName"),
        @Index(name = "idx_email", columnList = "email", unique = true)
    }
)
```

## 验证注解

```java
public class User {
    @NotNull
    @Size(min = 3, max = 50)
    private String username;
    
    @Email
    private String email;
    
    @Past
    private Date birthDate;
    
    @Min(0)
    @Max(100)
    private Integer age;
}
```
