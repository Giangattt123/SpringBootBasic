## Introduction

- Một vài kiến thức trước là kiến thức mà những người học Springboot cần nắm vững, bài này ta sẽ học về mảng chính mà ta áp dụng trong Springboot là "Lập trình web" hoặc "Restful Web Service"
- Để giúp chúng ta tạo và hiển thị dữ liệu động trong các trang web hoặc ứng dụng, chúng ta cần sử dụng một `Template Engine`. Điều này giúp tách biệt logic của ứng dụng và dữ liệu hiển thị, làm cho mã nguồn dễ đọc, bảo trì và mở rộng.
- Một template engine hoạt động bằng cách nhúng các biểu diễn dữ liệu động vào trong các template (mẫu) tĩnh. Các biểu diễn này thường được đặt trong các đoạn mã hoặc cú pháp cụ thể, và khi trang web hoặc ứng dụng được hiển thị, template engine sẽ thay thế những vị trí này bằng dữ liệu thực tế.
- Ở đây chúng ta sẽ sử dụng `Template Engine` là **Thymeleaf**

## Thymeleaf

- Thêm thử viện `spring-boot-starter-thymeleaf` bằng cách đưa nó vào thẻ **dependency** trong file `pom.xml` và tiến hành **refresh** project web maven(Chi tiết cách làm xem ở file [@Service@Repository.md](https://github.com/Giangattt123/SpringBootBasic/blob/master/%40Service%40Repository.md))

```
    <!-- thymeleaf -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-thymeleaf</artifactId>
    </dependency>
```

## @Controller

- Ở file [@Service@Repository.md](https://github.com/Giangattt123/SpringBootBasic/blob/master/%40Service%40Repository.md) khi nói về `@Servive` và `@Repository` chúng ta đã đề cập đến kiến trúc trong **SpringBoot**

![image1](https://live.staticflickr.com/65535/53478396541_8c9f7f75bd.jpg)

- `@Controller` là nơi tiếp nhận các thông tin request từ phía người dùng. Nó có nhiệm vụ đón nhận các yêu cầu (kèm theo thông tin request) và chuyển các yêu cầu này xuống cho tầng `@Serivce` xử lý logic.

## HTML

- Để tạo ra một trang Web, bạn sẽ cần tạo ra các trang html để trả về cho người dùng.
- Mặc định trong Spring Boot, các file html này sẽ được lưu trữ trong thư mục **resources/templates** như sau:

![image2](https://live.staticflickr.com/65535/53478549233_44cd91589c_n.jpg)

```
index.html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>

</body>
</html>
```

- Spring Boot + Thymeleaf sẽ tìm kiếm các file này theo tên, ví dụ "index" sẽ tương ứng với "index.html".

## Hello World

- Chúng ta sẽ tạo ra một Server Web đơn giản để bạn dễ dàng hiểu `@Controller` làm việc như nào.
- Ở đây tôi tạo ra một file `HelloController.java`

![image3](https://live.staticflickr.com/65535/53478426346_892a270f7c_b.jpg)

```
<!--file index.html-->
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <h1>HelloWorld!!!</h1>
</body>
</html>
```

```
// File App.java
package com.example.hellospringboot.helloWorld;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class App {
    public static void main(String[] args) {
        SpringApplication.run(App.class , args);
    }
}
```

> Run chương trình và nhận thấy rằng Server đã run ở port 8081

![image2](https://live.staticflickr.com/65535/53478570093_b9850e4b92_b.jpg)

- Bây giờ lên trình duyệt gõ `http://localhost:8081/` sẽ thấy được nội dung của file **index.html**

![image3](https://live.staticflickr.com/65535/53478835370_0649edba44_b.jpg)

- Nói thêm bản thân `@Controller` cũng là một `@Component` nên nó sẽ được Spring Boot quản lý.

```
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Component
public @interface Controller {
    @AliasFor(
        annotation = Component.class
    )
    String value() default "";
}
```

## Mở rộng hơn với ví dụ "Hello Your Name!"

- Ở đây trong thư mục **resources/templates** tôi tạo thêm hai file ;à **hello.html, about.html**

```
<!--file index.html-->
<!DOCTYPE html>
<html>
<head>
    <title>Hello World</title>
</head>
<body>
<h1>Phùng Đức Giang</h1>
<a href="/about">About</a>
<form method="get" action="/hello">
    <input type="input" name="name">
    <button type="submit">Submit</button>
</form>
</body>
</html>

<!-- hello.html -->
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
    <title>Hello World</title>
</head>
<body>
<h1 th:text="'Hello, ' + ${name}"></h1>
<a href="/">Trang chủ</a>
</body>
</html>

<!-- about.html -->
<!DOCTYPE html>
<html>
<head>
  <title>Hello World</title>
</head>
<body>
<h1>GiangPTIT</h1>
<a href="/">Website</a>
<a href="/">Github</a>
</body>
</html>
```

- Sửa lại file **HelloController.java** một chút như sau:

  ![image4](https://live.staticflickr.com/65535/53478609278_0a881f0d1c_w.jpg)

- Run project và vào lại url trên ta sẽ thấy một giao diện với nội dung ở file **index.html**

![image5](https://live.staticflickr.com/65535/53477560997_c67e440c28_c.jpg)

- Bây giờ, nhập tên của bạn vào ô và submit. Nó sẽ tạo một request **GET** tới địa chỉ **/hello** kèm theo `param` **?name=your_name**

![image6](https://live.staticflickr.com/65535/53478465471_a687702675_c.jpg)

### Giải thích ví dụ "Hello Your Name!"

- `Model` model: Tham số này là một đối tượng của Spring Boot Model được đính kèm trong mỗi **Response**, được sử dụng để chuyển dữ liệu giữa **Controller** và **View**. Bạn có thể sử dụng model.addAttribute("key", value) để đưa dữ liệu vào model.
- model.addAttribute("name", name): Dòng này đưa giá trị của tham số "name" vào model với key là "name". Có nghĩa là trong trang view (thường là một template engine như Thymeleaf hoặc FreeMarker), bạn có thể sử dụng key "name" để truy cập giá trị này.

  ![image7](https://live.staticflickr.com/65535/53478798524_9f7ed19457.jpg)

- Khi request lên, chúng ta nhận được giá trị của **name** và tiếp tục gán nó vào **Model**.
- Trong file **hello.html** tôi lấy giá trị của **name** trong `Model` ra bằng cách sử dụng cú pháp của Thymeleaf

```
<h1 th:text="'Hello, ' + ${name}"></h1>
```
