## Tìm hiểu 2 annotation @Component và @Autowired

### Quản lí dự án với Maven

- Maven là công cụ quản lý và thiết lập tự động 1 dự án phần mềm. Chủ yếu dùng cho các lập trình viên java, nhưng nó cũng có thể được dùng để xây dựng và quản lý các dự án dùng C#, Ruby, Scala hay ngôn ngữ khác. Apache maven là một chương trình quản lý dự án cho phép các developers có thể quản lý về version, các dependencies ( các thư viện sử dụng trong dự án ) , quản lý build, tự động download javadoc & source, …
- Vì sao phải sử dụng maven? Bạn hãy thử tượng nếu dự án của chúng ta sử dụng rất nhiều thư viên thứ 3 : struts, hibernate, spring, … Việc import thư viện và các dependency ( dịch hiểu là “sự phụ thuộc” ) là rất vất vả, chưa kể đến việc version của các thư viện có thể conflig với nhau, việc import đầy đủ các thư viện là cả một vấn đề. VD như bạn muốn sử dụng struts, thì điều bắt buộc là chúng ta phải import cả thư viện servlet.
- Nơi để các chương trình `maven` download các dependency ở: [Maven Repository](https://maven.apache.org/guides/introduction/introduction-to-repositories.html), hoặc ở [https://mvnrepository.com/](https://mvnrepository.com/)

### Setup

- Bạn có thể cài đặt các gói thư viện của Spring Boot trong Maven bằng cách thêm gói `sping-boot-starter-parent` là parent của cả project:

```
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>3.2.1</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
```

- Và để thêm các thư viện cho việc lập trình web hoặc server side, chúng ta thêm:

```
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
    </dependencies>
```

- Maven là một công cụ quản lý dự án và xây dựng dựa trên cấu trúc mô-đun, và mọi cấu hình đều được maven quản lí ở trong file `pom.xml`.
  Tệp pom.xml chứa thông tin về dự án, các phụ thuộc, cài đặt xây dựng, và nhiều cấu hình khác.

```
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>3.2.1</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
    <groupId>com.example</groupId>
    <artifactId>hello-spring-boot</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>hello-spring-boot</name>
    <description>hello-spring-boot</description>
    <properties>
        <java.version>17</java.version>
    </properties>
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-jdbc</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-jpa</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <dependency>
            <groupId>com.h2database</groupId>
            <artifactId>h2</artifactId>
            <scope>runtime</scope>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

</project>

```

> Trong ví dụ trên:

**+ groupId:** Định danh duy nhất của nhóm dự án.

**+ artifactId:** Định danh duy nhất của dự án trong nhóm.

**+ version:** Phiên bản của dự án.

**+ dependencies:** Danh sách các phụ thuộc của dự án.

**+ build:** Cấu hình xây dựng, bao gồm các plugin như Maven Compiler Plugin để cài đặt cách biên dịch mã nguồn.

### Cách chạy ứng dụng Spring Boot

- Nếu trong Java truyền thống, khi chạy cả một project, chúng ta sẽ phải định nghĩa một hàm main() và để nó khởi chạy đầu tiên. Điều này không có gì thay đổi! Có khác là chúng ta sẽ phải chỉ cho Spring Boot biết nơi nó khởi chạy lần đầu, để nó cài đặt mọi thứ.
- Cách thực hiện là thêm **annotation** `@SpringBootApplication` trên class chính và gọi `SpringApplication.run(App.class, args);` để chạy project.
- Ví dụ như sau:

![image1](https://live.staticflickr.com/65535/53472491145_a46666cc5b_c.jpg)

- Spring Framework sử dụng mô hình `inversion of control (IoC)` và `dependency injection (DI)` để quản lý và kết nối các thành phần trong ứng dụng. Một trong những nhiệm vụ chính của `Spring` là tạo ra một cái Container chứa các Dependency cho chúng ta. `SpringApplication.run(App.class, args)` chính là câu lệnh để tạo ra container. Sau đó nó tìm toàn bộ các dependency trong project của bạn và đưa vào đó. Spring đặt tên cho container là `ApplicationContext` và đặt tên cho các dependency là `Bean`
- Vậy làm sao để Spring biết đâu là dependency? Chúng ta tới với khái niệm `@Component`

## Annotation @omponent

- `@Component` là một Annotation (chú thích) đánh dấu trên các Class để giúp Spring biết nó là một `Bean`
- Ví dụ chúng ta có một `interface` **Outfit**

```
public interface Outfit {
    public void wear();
}
```

- Class 'Bikini' sẽ được implement interface như sau:

```
/*
 Đánh dấu class bằng @Component
 Class này sẽ được Spring Boot hiểu là một Bean (hoặc dependency)
 Và sẽ được Spring Boot quản lý
*/
@Component
public class Bikini implements Outfit {
    @Override
    public void wear() {
        System.out.println("Mặc bikini");
    }
}
```

- Do container được khởi tạo bằng câu lệnh `SpringApplication.run(App.class, args)` nên nó sẽ trả về một đối tượng `ApplicationContext `. Đây là một đối tượng quan trọng trong Spring Framework, và nó chứa thông tin về cấu hình và quản lý các bean (đối tượng) của ứng dụng.
- Spring Boot khi chạy sẽ dò tìm toàn bộ các Class cùng cấp hoặc ở trong các package thấp hơn so với class App mà bạn cung cấp cho Spring (Chúng ta có thể cấu hình việc tìm kiếm này, sẽ đề cập sau). Trong quá trình dò tìm này, khi gặp một class được đánh dấu `@Component` thì nó sẽ tạo ra một instance và đưa vào `ApplicationContext` để quản lý.
- Ở đây nếu tôi không có từ khóa @Component thì container sẽ không hiểu class đó là một been

![image2](https://live.staticflickr.com/65535/53472267913_113c40de27_z.jpg)

> Kết quả sẽ hiển thị như sau:

![image3](https://live.staticflickr.com/65535/53472277838_7b7855cb58_h.jpg)

=> Thông báo lỗi `NoSuchBeanDefinitionException` xảy ra khi Spring Boot không tìm thấy bean (đối tượng) nào thuộc kiểu Outfit trong context.

- Vì vậy ta sẽ đăng kí một `bean` với context bằng cách thêm @Component như trên sẽ thu được kết quả:

```
Instance: com.example.hellospringboot.Bikini@60a6840c
```

=> Nhưng tại sao Object trả về có kiểu là **Bikini** trong khi mình yêu cầu có kiểu `Outfit`?? Lí giải cho điều này là do Outfit là một interface không thể tạo trực tiếp Object từ nó, nên container đã tìm trong context và thấy **Bean Bikini** đã `implement Outfit` nên nó đã tự động tạo ra một Object `Bikini` để trả về.

### Annotation @Autowired

- Bây giờ mình tạo ra một Class Girl và có một thuộc tính là `Outfit`. Mình cũng đánh dấu Girl là một `@Component`. Tức Spring Boot cần tạo ra một `instance ` của Girl để quản lý.

```
package com.example.hellospringboot;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

@Component
public class Girl {
    @Autowired
    Outfit outfit;
    public Girl(Outfit outfit) {
        this.outfit = outfit;
    }
}
```

- Mình đánh dấu thuộc tính Outfit của Girl bởi Annotation `@Autowired`. Điều này nói với Spring Boot hãy tự inject (tiêm) một instance của Outfit vào thuộc tính này khi khởi tạo Girl.

![image4](https://live.staticflickr.com/65535/53471269622_1fd843d4fd_b.jpg)

> Lúc này kết quả hiển thị như sau:

```
Girl Instance: com.example.hellospringboot.Girl@5072e638
Girl Outfit: com.example.hellospringboot.Bikini@13d984ee
Cô gái đã mặc Bikini
```

> Spring Boot đã tự tạo ra một Girl và trong quá trình tạo ra đó, nó truyền Outfit vào làm thuộc tính.

- Một điều đặc biệt là nếu tôi viết lại việc lấy ra object của bean Outfit:

![image5](https://live.staticflickr.com/65535/53472605030_6bcef79902_z.jpg)

> Lúc này kết quả hiển thị như sau:

```
Girl Instance: com.example.hellospringboot.Girl@5402612e
Girl Outfit: com.example.hellospringboot.Bikini@2e3900dc
Cô gái đã mặc Bikini
Outfit instance: com.example.hellospringboot.Bikini@2e3900dc
```

=> Điều đặc biệt là các `Bean`ean được quản lý bên trong `ApplicationContext` đều là **singleton**. Tức là Outfit ở 2 đối tượng trên chỉ là một. Tất cả những Bean được quản lý trong `ApplicationContext` đều chỉ được tạo ra một lần duy nhất và khi có Class yêu cầu `@Autowired` thì nó sẽ lấy đối tượng có sẵn trong `ApplicationContext` để inject vào.

- Trong trường hợp bạn muốn mỗi lần sử dụng là một instance hoàn toàn mới. Thì hãy đánh dấu @Component đó bằng **@Scope("prototype")**

```
package com.example.hellospringboot;

import org.springframework.context.annotation.Scope;
import org.springframework.stereotype.Component;

@Component
@Scope("prototype")
public class Bikini implements Outfit{
    @Override
    public void wear() {
        System.out.println("Cô gái đã mặc Bikini");
    }
}
```

> Lúc này kết quả đã hoàn toàn khác:

![image6](https://live.staticflickr.com/65535/53471289407_18b26411de_z.jpg)
