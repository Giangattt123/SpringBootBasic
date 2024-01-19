## Component Scan trong Springboot

- Ở bài này, chúng ta sẽ tìm hiểu thêm về cách Spring Boot tìm kiếm `Bean` trong project của bạn như thế nào.
- Spring Boot khi chạy sẽ dò tìm toàn bộ các Class cùng cấp hoặc ở trong các package thấp hơn và tạo ra Bean từ các Class tìm thấy, bây giờ ta sẽ tùy biến nó nhé.
- Tạo ra 2 Bean:

  - `Girl` nằm cùng cấp với file `App`(file run build)
  - `OtherGirl`. Nằm ở package con **others**. **others** cùng cấp với `App`
  - Girl.java

  ```
  @Component
  public class Girl {
      @Override
      public String toString() {
          return "Girl.java";
      }
  }
  ```

  - OtherGirl.java

  ```
  @Component
  public class OtherGirl {
      @Override
      public String toString() {
          return "OtherGirl.java";
      }
  }
  ```

  - App.java

    ![image1](https://live.staticflickr.com/65535/53474176523_ddcc847e82_z.jpg)

  - Sau đó chạy chương trình và thu được kết quả:

  ```
  Bean: Girl.java
  Bean: OtherGirl.java
  ```

  => Kết quả in ra màn hình là cả 2 bean Girl và OtherGirl đều được tạo ra trong Context.
  Điều này chứng tỏ Spring Boot đã đi tìm các Bean bên cạnh class App và những package con bên cạnh App

- Tuy nhiên đôi khi ta muốn tùy chỉnh cấu hình cho Springboot chỉ tìm kiếm các Bean trong một package nhất định nào đó thì ta sẽ làm như sau:
  - 1. Sử dụng `@componentScan`
  - 2. Sử dụng `scanBasePackages` trong **@SpringBootApplication**

### @ComponentScan

- Sửa file App.java bằng cách thêm:`@ComponentScan("com.example.hellospringboot.componentScan.others")`
  > Lúc này Bean Girl không được Springboot đi tìm nên kết quả khi chạy như sau:

```
Bean Girl không tồn tại
Bean: OtherGirl.java
```

### scanBasePackages

- `@SpringBootApplication(scanBasePackages = "com.example.hellospringboot.componentScan.others")`

### Multiple package scan

- Có thể cấu hình cho Spring Boot tìm kiếm các Bean ở nhiều package khác nhau bằng cách:

```
@ComponentScan({"com.example.hellospringboot.componentScan.others","com.example.hellospringboot.componentScan.others2"})
```

- Hoặc như sau:

```
@SpringBootApplication(scanBasePackages = {"com.example.hellospringboot.componentScan.others", "com.example.hellospringboot.componentScan.others"})
```
