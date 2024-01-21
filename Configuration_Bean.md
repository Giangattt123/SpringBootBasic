## @Configuration và @Bean

- `@Configuration` là một Annotation đánh dấu trên một Class cho phép Spring Boot biết được đây là nơi định nghĩa ra các Bean.
- `@Bean` là một Annotation được đánh dấu trên các method cho phép Spring Boot biết được đây là Bean và sẽ thực hiện đưa Bean này vào Context.
  `@Bean` sẽ nằm trong các class có đánh dấu `@Configuration`.

- Tôi có 1 class là **SimpleBean.java**:

```
/**
 * Một class cơ bản, không sử dụng `@Component`
 */
public class SimpleBean {
    private String username;

    public SimpleBean(String username) {
        setUsername(username);
    }

    @Override
    public String toString() {
        return "This is a simple bean, name: " + username;
    }

    public String getUsername() {
        return username;
    }

    public void setUsername(String username) {
        this.username = username;
    }
}
```

- Tiếp theo là **AppConfig.java**

```
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class AppConfig {
    @Bean
    SimpleBean simpleBeanConfigure(){
        // Khởi tạo một instance của SimpleBean và trả ra ngoài
        return new SimpleBean("loda");
    }
}
```

- **App.java**

```
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.ApplicationContext;

@SpringBootApplication
public class App {
    public static void main(String[] args) {
        ApplicationContext context = SpringApplication.run(App.class, args);
        // Lấy ra bean SimpleBean trong Context
        SimpleBean simpleBean = context.getBean(SimpleBean.class);
        // In ra màn hình
        System.out.println("Simple Bean Example: " + simpleBean.toString());
    }
}
```

> Output: **Simple Bean Example: This is a simple bean, name: loda**
> => Bạn sẽ thấy là `SimpleBean` là một object được quản lý trong Context của Spring Boot, mặc dù trong bài này, chúng ta không hề sử dụng tới các khái niệm `@Component`.

## In Background

- Đằng sau chương trình, Spring Boot lần đầu khởi chạy, ngoài việc đi tìm các `@Component` thì nó còn làm một nhiệm vụ nữa là tìm các class `@Configuration`. + Đi tìm class có đánh dấu @Configuration + Tạo ra đối tượng từ class có đánh dấu @Configuration + Tìm các method có đánh dấu @Bean trong đối tượng vừa tạo + Thực hiện gọi các method có đánh dấu @Bean để lấy ra các Bean và đưa vào `Context.
  > Ngoài ra, về bản chất, @Configuration cũng là @Component. Nó chỉ khác ở ý nghĩa sử dụng. (Giống với việc class được đánh dấu @Service chỉ nên phục vụ logic vậy).
      ```
      @Target({ElementType.TYPE})
      @Retention(RetentionPolicy.RUNTIME)
      @Documented
      @Component // Nó được đánh dấu là Component
      public @interface Configuration {
          @AliasFor(
              annotation = Component.class
          )
          String value() default "";
      }
      ```

### Ý NGHĨA

- Nhiều bạn sẽ tự hỏi rằng `@Configuration` và `@Bean` sẽ có ý nghĩa gì khi chúng ta đã có `@Component`? Sao không đánh dấu SimpleBean là @Component cho nhanh? Các bạn thắc mắc rất đúng, và việc sử dụng @Component cũng hoàn toàn ổn.
- Thông thường thì các class được đánh dấu @Component đều có thể tạo tự động và inject tự động được.
- Tuy nhiên trong thực tế, nếu một Bean có quá nhiều logic để khởi tạo và cấu hình, thì chúng ta sẽ sử dụng `@Configuration` và `@Bean` để tự tay tạo ra Bean. Việc tự tay tạo ra Bean như này có thể hiểu phần nào là chúng ta đang config cho chương trình.
- Ví dụ có một class là **DatabaseConnector.java** làm nhiệm vụ kết nối đến database

```
public abstract class DatabaseConnector {
    private String url;
    /**
     * Hàm này có nhiệm vụ Connect tới một Database bất kỳ
     */
    public abstract void connect();

    public String getUrl() {
        return url;
    }

    public void setUrl(String url) {
        this.url = url;
    }
}
```

- Kế thừa class này có 3 class đại diện: `MySqlConnector`, `PostgreSqlConnector`, `MongoDbConnector`

```
public class MongoDbConnector extends DatabaseConnector {
    @Override
    public void connect() {
        System.out.println("Đã kết nối tới Mongodb: " + getUrl());
    }
}

public class MySqlConnector extends DatabaseConnector {
    @Override
    public void connect() {
        System.out.println("Đã kết nối tới Mysql: " + getUrl());
    }
}

public class PostgreSqlConnector  extends DatabaseConnector{
    @Override
    public void connect() {
        System.out.println("Đã kết nối tới Postgresql: " + getUrl());
    }
}
```

- Tiếp theo tôi sẽ sử dụng annotation `@Configuration` cùng với `@Bean` để tạo ra các bean trong file `AppConfig.java`

![image1](https://live.staticflickr.com/65535/53478560365_54bfaea760_w.jpg)

- Chạy thử với file `App.java`

![image2](https://live.staticflickr.com/65535/53478465394_dd1d63fb52.jpg)

> Output

```
Đã kết nối tới Mysql: jdbc:mysql://host1:33060/loda
Đã kết nối tới Mongodb: mongodb://mongodb0.example.com:27017/loda
Đã kết nối tới Postgresql: postgresql://localhost/loda
```

=> Chúng ta tạo ra `DatabaseConnector` phục vụ cho nhiều ngữ cảnh.

## @Bean có tham số

- Nếu method được đánh dấu bởi @Bean có tham số truyền vào, thì Spring Boot sẽ tự inject các Bean đã có trong Context vào làm tham số.
- Ví dụ:

```
@Configuration
public class AppConfig {
    @Bean
    SimpleBean simpleBeanConfigure(){
        // Khởi tạo một instance của SimpleBean và trả ra ngoài
        return new SimpleBean("loda");
    }

    @Bean("mysqlConnector1")
    DatabaseConnector mysqlConnector1(SimpleBean simpleBean) {
        DatabaseConnector mySqlConnector1 = new MySqlConnector();
        mySqlConnector1.setUrl("jdbc:mysql://host1:33060/" + simpleBean.getUsername());
        return mySqlConnector1;
    }
}
```

```
    DatabaseConnector mysql1 = (DatabaseConnector) context.getBean("mysqlConnector1");
    mysql1.connect();
```

> Output: **Đã kết nối tới Mysql: jdbc:mysql://host1:33060/loda**

- Trong thực tế, việc sử dụng `@Configuration` là hết sức cần thiết, và nó đóng vai trò là nơi cấu hình cho toàn bộ ứng dụng của bạn. Một Ứng dụng sẽ có nhiều class chứa `@Configuration` và mỗi class sẽ đảm nhận cấu hình một bộ phận gì đó trong ứng dụng.

- Ví dụ đây là một đoạn code cấu hình cho **Spring Security**

```
@Configuration
@EnableWebSecurity
public class WebSecurityConfig extends WebSecurityConfigurerAdapter {
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http
            .authorizeRequests()
                .antMatchers("/", "/home").permitAll()
                .anyRequest().authenticated()
                .and()
            .formLogin()
                .loginPage("/login")
                .permitAll()
                .and()
            .logout()
                .permitAll();
    }

    @Bean
    @Override
    public UserDetailsService userDetailsService() {
        UserDetails user =
             User.withDefaultPasswordEncoder()
                .username("user")
                .password("password")
                .roles("USER")
                .build();

        return new InMemoryUserDetailsManager(user);
    }
}
```
