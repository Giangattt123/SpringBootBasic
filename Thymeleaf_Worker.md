## Thymeleaf

- Thymeleaf là một **Java Template Engine**. Có nhiệm vụ xử lý và generate ra các file HTML, XML, v.v..
- Các file HMTL do Thymeleaf tạo ra là nhờ kết hợp dữ liệu và template + quy tắc để sinh ra một file HTML chứa đầy đủ thông tin.
- Việc của bạn là cung cấp dữ liệu và quy định template như nào, còn việc dùng các thông tin đó để render ra HTML sẽ do Thymeleaf giải quyết.

## Syntax

- Cú pháp của `Thymeleaf` sẽ là một attributes (Thuộc tính) của thẻ HTML và bắt đầu bằng chữ **th:**
- Với cách tiếp cận này, bạn sẽ chỉ cần sử dụng các thẻ HTML cơ bản đã biết mà không cần bổ sung thêm syntax hay thẻ mới như JSP truyền thống.
- Ví dụ: để truyền dữ liệu từ biến `name` trong Java vào một thẻ `h1` của HTML ta làm như sau:

```
<h1 th:text="${name}"></h1>
```

> Chúng ta viết thẻ h1 như bình thường, nhưng không chứa bất cứ text nào trong thẻ. Mà sử dụng cú pháp `th:text="${name}"` để Thymeleaf lấy thông tin từ biến name và đưa vào thẻ H1.

```
Kết quả khi render ra giao diện
// Ví dụ name = "GiangPTIT"
<h1>GiangPTIT</h1>
```

## Model & View Trong Spring Boot

- `Model` là đối tượng lưu giữ thông tin và được sử dụng bởi `Template Engine` để generate ra webpage. Có thể hiểu nó là `Context` của `Thymeleaf`
- `Model` lưu giữ thông tin dưới dạng key-value.
- Trong template **Thymeleaf**, để lấy các thông tin trong `Model`, ta sẽ sử dụng cú pháp `Thymeleaf Standard Expression`.
  - `${...}`: Giá trị của một biến.
  - `{...}`: Giá trị của một biến được chỉ định
  - Ngoài ra, để lấy thông tin đặc biệt hơn:
    - `#{...}`: Lấy message
    - `@{...}`: Lấy đường dẫn URL dựa theo context của server

### ${...} - Variables Expressions

- Trên **Controller** bạn đưa vào một số giá trị:

```
model.addAttribute("name", "Giang");
```

- Để lấy giá trị của biến **name**, tôi sử dụng **${...}**

```
<p>My name is: <span th:text="${name}"></span>.</p>
```

### \*{...} - Variables Expressions on selections

- Dấu \* còn gọi là `asterisk syntax`. Chức năng của nó giống với `${...}` là lấy giá trị của một biến.
- Điểm khác biệt là nó sẽ lấy ra giá trị của một biến cho trước bởi `th:object`

```
    <div th:object="${session.user}"><!-- th:object tồn tại trong phạm vi của thẻ div này -->
    <!-- Lấy ra firstName của đối tượng session.user -->
    <p>Name: <spanth:text="*{firstName}"></span>.</p>
    <!-- Lấy ra lastName của đối tượng session.user -->
    <p>Surname: <spanth:text="*{lastName}"></span>.</p></div>
```

- Còn `${...}` sẽ lấy ra giá trị cục bộ trong `Context` hay `Model`

```
<div><p>Name: <spanth:text="${session.user.firstName}"></span>.</p><p>
Surname: <spanth:text="${session.user.lastName}"></span>.</p></div>
```

### #{...} - Message Expression

- Trong Thymeleaf, `#{...}` được sử dụng để biểu diễn cho **Message Expression**, cung cấp khả năng dịch ngôn ngữ và quản lý văn bản tĩnh trong ứng dụng.
- **Message Expression** giúp tách rời văn bản tĩnh khỏi mã nguồn Java hoặc các template Thymeleaf, điều này làm cho việc quản lý và dịch ngôn ngữ trở nên dễ dàng hơn.
- Ví dụ, trong file config **.properties** của tôi có một message chào người dùng bằng nhiều ngôn ngữ.

```
home.welcome=¡Bienvenido a nuestra tienda de comestibles!
<p th:utext="#{home.welcome}">Xin chào các bạn!</p>
```

> Đoạn text tiếng việt bên trong thẻ p sẽ bị thay thế bởi thymeleaf khi render #{home.welcome}

### @{...} - URL Expression

- `@{...}` xử lý và trả ra giá trị URL theo context của máy chủ cho chúng ta.

```
<!-- tương đương với 'http://localhost:8080/order/details?orderId=3' -->
<a href="details.html" th:href="@{http://localhost:8080/order/details(orderId=${o.id})}">view</a><!-- tương đương  '/order/details?orderId=3' -->
<a href="details.html" th:href="@{/order/details(orderId=${o.id})}">view</a><!-- tương dương '/gtvg/order/3/details' -->
< ahref="details.html" th:href="@{/order/{orderId}/details(orderId=${o.id})}">view</a>
```

## Demo

### Cấu hình thymeleaf

- Chúng ta sẽ cấu hình ứng dụng bằng file `application.properties`

```
server.port=8081
giang.mysql.url=jdbc:mysql://host1:33060/giangptit
# Bỏ tính năng cache của thymeleaf để lập trình cho nhanh
spring.thymeleaf.cache=false
# Các message tĩnh sẽ được lưu tại thư mục i18n
spring.messages.basename=i18n/messages
# Bỏ properties này đi khi deploy
# Nó có tác dụng cố định ngôn ngữ hiện tại chỉ là Tiếng Việt
spring.mvc.locale-resolver=fixed
# Mặc định ngôn ngữ là tiếng việt
spring.mvc.locale=vi_VN
# Đổi thành tiếng anh bằng cách bỏ comment ở dứoi
# spring.mvc.locale=en_US
```

### Static & Templates

- Làm Web thì không thể thiếu css và javascript. Các file này sẽ được lưu tại thư mục **resources/static**.
- File .html là dạng template sử dụng để render ra webpage và trả về cho người dùng. Nó sẽ được lưu tại thư mục **resources/templates**
- Thymleaf sẽ tự biết tìm đường tới những tài nguyên này.
- File **index.html**

![image1](https://live.staticflickr.com/65535/53481283923_3debf3bbfd_c.jpg)

- File **profile.html**

![image2](https://live.staticflickr.com/65535/53481445129_6ae301133e_z.jpg)

- File **App.java**

![image3](https://live.staticflickr.com/65535/53480235082_4e29394d46_z.jpg)

> Run và gõ lên trình duyệt URL sau: **http://localhost:8081/**

![image4](https://live.staticflickr.com/65535/53481549050_779d1f0826_b.jpg)

### Thêm @Controller cho path /profile

- Bây giờ chúng ta sẽ bổ sung thêm `@Controller` để handle các request tới địa chỉ `/profile`.
- Giả sử tôi muốn lưu thông tin về tôi, vì vậy tôi sẽ tạo ra một class là `Info` và để nó trong package `demoThymeleaf`

```
package com.example.hellospringboot.demoThymeleaf;

public class Info {
    private String key;
    private String value;
    public Info(String key , String value) {
        this.key = key;
        this.value = value;
    }
    public String getKey() {
        return key;
    }

    public void setKey(String key) {
        this.key = key;
    }

    public String getValue() {
        return value;
    }

    public void setValue(String value) {
        this.value = value;
    }
}
```

- Tiếp theo tôi tạo một `@Controller` có tên là **InfoController** và setup một **mock data**

![image5](https://live.staticflickr.com/65535/53481230741_888a27506c.jpg)

> Một lưu ý là hãy chắc chắn rằng **template** trong **controller** và **template** được sử dụng trong mã **HTML** phải khớp nhau, nếu không `Thymeleaf` sẽ không thể tìm thấy hoặc truy cập được

- Do có quá nhiều các package cùng với thiết lập khá nhiều `Bean` ở trong `Context` khiến khi run project sẽ bị `conflig` các `Bean`, dựa vào **Stack Trace** và thông báo lỗi về việc đăng kí các `Bean` như `ConflictingBeanDefinitionException`, để tránh điều này, tôi sẽ không cho Springboot **scan auto** nữa mà sẽ chỉ định nơi mà nó sẽ **scan** như sau

```
@ComponentScan("com.example.hellospringboot.demoThymeleaf")
```

- Bây giờ khi run lại project đi đến `/profile` sẽ thấy data được render ra giao diện

![image6](https://live.staticflickr.com/65535/53480328482_436770873c_c.jpg)
