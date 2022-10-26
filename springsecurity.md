### 该看36

### 简介

Spring 是非常流行和成功的 Java 应用开发框架，Spring Security 正是 Spring 家族中的
成员。Spring Security 基于 Spring 框架，提供了一套 Web 应用安全性的完整解决方案。
正如你可能知道的关于安全方面的两个主要区域是“认证”和“授权”（或者访问控制），一般来说，
Web 应用的安全性包括**用户认证Authentication**和**用户授权Authorization**两个部分，这两点也是 Spring Security 重要核心功能。

（1）用户认证指的是：验证某个用户是否为系统中的合法主体，也就是说用户能否访问该系统。用户认证一般要求用户提供用户名和密码。系统通过校验用户名和密码来完成认证过程。**通俗点说就是系统认为用户是否能登录。**

（2）用户授权指的是验证某个用户是否有权限执行某个操作。在一个系统中，不同用户
所具有的权限是不同的。比如对一个文件来说，有的用户只能进行读取，而有的用户可以
进行修改。一般来说，系统会为不同的用户分配不同的角色，而每个角色则对应一系列的
权限。**通俗点讲就是系统判断用户是否有权限去做某些事情。**

## SpringSecurity 入门

### 项目版本

```xml
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.2.1.RELEASE</version>
    </parent>
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-security</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
    </dependencies>
```

### SpringSecurity Web 权限方案

#### 通过配置文件

```yaml
spring:
  security:
    user:
      name: admin
      password: admin
```

#### 通过配置类

```java
@Configuration
public class DemoConfig extends WebSecurityConfigurerAdapter {
    // 注入 PasswordEncoder 类到 spring 容器中
    @Bean
    public PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }

    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        BCryptPasswordEncoder encoder = new BCryptPasswordEncoder();
        String password = encoder.encode("admin");
        auth.inMemoryAuthentication().withUser("admin").password(password).roles("");
    }
}
```

#### 自定义配置类

##### config

```java
@Configuration
public class DemoConfigAnother extends WebSecurityConfigurerAdapter {
    @Autowired
    private UserDetailsService userDetailsService;
    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        auth.userDetailsService(userDetailsService).passwordEncoder(passwordEncoder());
    }
    @Bean
    public PasswordEncoder passwordEncoder(){
        return new BCryptPasswordEncoder();
    }
}
```

##### service

```java
@Service("userDetailService")
public class UserDetailServiceImp implements UserDetailsService {
    @Override
    public UserDetails loadUserByUsername(String s) throws UsernameNotFoundException {
        List<GrantedAuthority> grantedAuthorities = AuthorityUtils.commaSeparatedStringToAuthorityList("");
        return new User("admin",new BCryptPasswordEncoder().encode("admin"),grantedAuthorities);
    }
}
```

##### 使用数据库

```sql
-- create DATABASE springcecurity;
use springcecurity;

create table users(
 id bigint primary key auto_increment,
    username varchar(20) unique not null,
    password varchar(100)
);
-- 密码 atguigu
insert into users values(1,'张
san','$2a$10$2R/M6iU3mCZt3ByG7kwYTeeW0w7/UqdeXrb27zkBIizBvAven0/na');
-- 密码 atguigu
insert into users values(2,'李
si','$2a$10$2R/M6iU3mCZt3ByG7kwYTeeW0w7/UqdeXrb27zkBIizBvAven0/na');
create table role(
id bigint primary key auto_increment,
name varchar(20)
);
insert into role values(1,'管理员');
insert into role values(2,'普通用户');
create table role_user(
uid bigint,
rid bigint
);
insert into role_user values(1,1);
insert into role_user values(2,2);
create table menu(
id bigint primary key auto_increment,
name varchar(20),
url varchar(100),
parentid bigint,
permission varchar(20)
);
insert into menu values(1,'系统管理','',0,'menu:system');
insert into menu values(2,'用户管理','',0,'menu:user');
create table role_menu(
mid bigint,
rid bigint
);
insert into role_menu values(1,1);
insert into role_menu values(2,1);
insert into role_menu values(2,2);
```

###### service

```java
@Service("userDetailService")
public class UserDetailServiceImp implements UserDetailsService {

    @Autowired
    private UsersService usersService;
    @Override
    public UserDetails loadUserByUsername(@NotNull String s) throws UsernameNotFoundException {
        QueryWrapper<Users> queryWrapper = new QueryWrapper<>();
        queryWrapper.eq("username",s);
        Users one = usersService.getOne(queryWrapper);
        System.out.println(one);
        if (one==null){
           throw new UsernameNotFoundException("用户名不存在！");
        }
        List<GrantedAuthority> grantedAuthorities = AuthorityUtils.commaSeparatedStringToAuthorityList("role");
        BCryptPasswordEncoder bCryptPasswordEncoder = new BCryptPasswordEncoder();
        return new User(one.getUsername(),bCryptPasswordEncoder.encode(one.getPassword()),grantedAuthorities);
    }
}
```

### 自定义登录界面

```java
 @Override
    protected void configure(HttpSecurity http) throws Exception {
       http.formLogin()
              .loginPage("login.html")  //自定义登录界面
               .loginProcessingUrl("/user/login") //登录页面设置
               .defaultSuccessUrl("/index/hello")  //成功之后路径调转
               .permitAll().and().authorizeRequests()
                .antMatchers("/","index","index/login")
                 .permitAll().anyRequest().authenticated()
               .and().csrf().disable();
    }
```

### 登录退出

```java
 @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.logout().logoutUrl("/logout").logoutSuccessUrl("/");
    }
```

### 基于角色和权限控制

```java
 @Override
    protected void configure(HttpSecurity http) throws Exception {
       http.formLogin()
//               .loginPage("login.html")  //自定义登录界面
//               .loginProcessingUrl("/user/login") //登录页面设置
               .defaultSuccessUrl("/index/hello")  //成功之后路径调转
               .permitAll().and().authorizeRequests()
//               .antMatchers("/","index","index/login").permitAll().anyRequest().authenticated()
               .antMatchers("/","/index","/index/login").permitAll() //设置路径可以直接访问
//               .antMatchers("/index/hello").hasAuthority("admin") //单个权限访问
               .antMatchers("/index/hello").hasAnyAuthority("admin,one")  //多个权限访问
               .antMatchers("/index/test").hasRole("good")  //底层封装为ROLE_good
//               .antMatchers("/index/test").hasRole("good,nice")
               .and().csrf().disable();
    }
```

###### service

一般从数据库查出权限

```java
 //权限
 List<GrantedAuthority> grantedAuthorities = AuthorityUtils.commaSeparatedStringToAuthorityList("admin,ROLE_good");
 BCryptPasswordEncoder bCryptPasswordEncoder = new BCryptPasswordEncoder();
 return new User(one.getUsername(),bCryptPasswordEncoder.encode(one.getPassword()),grantedAuthorities);
```

### 自定义没有权限访问界面

```java
 @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.exceptionHandling().accessDeniedPage("/cant");
    }
```

### 注解方式

主启动内添加启动注解

```java
//进入方法之前校验，开校验，之后校验
//@EnableGlobalMethodSecurity(prePostEnabled = true,securedEnabled = true)
```

```java
    //角色
    @Secured({"ROLE_good", "ROLE_管理员"})
    //前
//    @PreAuthorize("hasAnyAuthority('nice')")
//    //方法执行后
//    @PostAuthorize("hasAnyAuthority('nice')")
    //对返回数据进行过滤
    @PostFilter("filterObject.username == 'admin1'")
    //对输入数据进行过滤
//    @PreFilter(value = "filterObject.id%2==0")
    @GetMapping("haha")
    @ResponseBody
    public ArrayList<Users> haha() {
        ArrayList<Users> list = new ArrayList<>();
        list.add(new Users(1, "admin1", "6666"));
        list.add(new Users(22, "admin2", "888"));
        return list;
    }";
    }
```

### 实现自动登录（参考，实际意义不大）

```java
    @Autowired
    private DataSource dataSource;
    @Bean
    public PersistentTokenRepository persistentTokenRepository(){
        JdbcTokenRepositoryImpl jdbcTokenRepository = new
                JdbcTokenRepositoryImpl();
        // 赋值数据源
        jdbcTokenRepository.setDataSource(dataSource);
        // 自动创建表,第一次执行会创建，以后要执行就要删除掉！
        jdbcTokenRepository.setCreateTableOnStartup(true);
        return jdbcTokenRepository; }
     @Override
     protected void configure(HttpSecurity http) throws Exception {
        //开启功能
        http.rememberMe().tokenRepository(persistentTokenRepository())
         .userDetailsService(userDetailsService);  
    }
 //<input type="checkbox"name="remember-me"title="记住密码"/><br/>
 //此处：name 属性值必须位 remember-me.不能改为其他值
```

### CSRF

**跨站请求伪造（英语：Cross-site request forgery）**，也被称为 one-clickattack 或者 session riding，通常缩写为 CSRF 或者 XSRF， 是一种挟制用户在当前已登录的 Web 应用程序上执行非本意的操作的攻击方法。跟跨网站脚本（XSS）相比，XSS利用的是用户对指定网站的信任，CSRF 利用的是网站对用户网页浏览器的信任。

#### 实现过程

1. 生成 csrfToken 保存到 HttpSession 或者 Cookie 中
2. 请求到来时，从请求中提取 csrfToken，和保存的 csrfToken 做比较，进而判断当前请求是否合法。主要通过 CsrfFilter 过滤器来完成。

## 项目

[yueli14/grain-college (github.com)](https://github.com/yueli14/grain-college)
