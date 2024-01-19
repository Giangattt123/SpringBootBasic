## Kiến trúc trong Spring Boot

- Cách tốt nhất để kiểm tra bất kỳ phần mềm nào là tách nó thành các lớp, sau đó hợp nhất các lớp đó lại với nhau.Trước khi đi sâu vào **Java Spring Boot**, chúng ta hãy xem một ví dụ nổi tiếng về mô hình OSI trong mạng máy tính. Mặc dù mạng nhìn chung có vẻ phức tạp nhưng chúng ta thường chia nó thành các lớp để tổ chức các giao thức. Mỗi lớp phụ thuộc vào các dịch vụ được cung cấp bởi lớp bên dưới nó. Trong Spring boot, nguyên tắc tương tự cũng được áp dụng.

- Kiến trúc MVC trong Spring Boot được xây dựng dựa trên tư tưởng "độc lập" kết hợp với các nguyên lý thiết kế hướng đối tượng (một đại diện tiêu biểu là Dependency Inversion). Độc lập ở đây ám chỉ việc các layer phục vụ các mục đích nhất định, khi muốn thực hiện một công việc ngoài phạm vi thì sẽ đưa công việc xuống các layer thấp hơn.

- Kiến trúc Controller-Service-Repository chia project thành 3 lớp:
  - Consumer Layer hay Controller: là tầng giao tiếp với bên ngoài và handler các request từ bên ngoài tới hệ thống.
  - Service Layer: Thực hiện các nghiệp vụ và xử lý logic
  - Repository Layer: Chịu trách nhiệm giao tiếp với các DB, thiết bị lưu trữ, xử lý query và trả về các kiểu dữ liệu mà tầng Service yêu cầu.

![image1](https://media.licdn.com/dms/image/D5612AQE6qenXPDpdDA/article-cover_image-shrink_600_2000/0/1675004833615?e=2147483647&v=beta&t=o_trd7kJKyIABknYYhTn4zyj-ENrRLM4V2Mylfh1nPs)

### 1. Controller Layer

- Phần đầu tiên của hệ thống tương tác với yêu cầu của **client** là `Controllers`. Nó xác định các **endpoints** của API, ta có thể tưởng tượng **endpoints** là các tuyến đường và phương thức yêu cầu hợp lệ (GET, POST, PUT). Mục tiêu chính của bộ điều khiển là cung cấp dịch vụ cho **client**, tức là cung cấp phản hồi, trạng thái và hơn thế nữa. Bộ điều khiển sử dụng các dịch vụ do `Service Layer` cung cấp để phục vụ **client**.
- Ví dụ về **endpoints**:

```
/login (POST)
/register (POST)
/products (GET)
```

### 2. Service layer

- Các lớp dịch vụ có nghĩa là để thực hiện `business logics`. Mục đích chính của lớp này là cung cấp dịch vụ cho `controller layer`. Tất cả các loại tính toán trên dữ liệu đều được thực hiện trong lớp này, do đó lớp dịch vụ yêu cầu dữ liệu. Do đó, nó dựa vào các dịch vụ được cung cấp bởi lớp DAO/Repository.

### 3. Repository/DAO layer

- DAO là viết tắt của `Data Acess Object`, Mục tiêu chính của lớp này là **access**(Truy vấn) hiệu quả từ cơ sở dữ liệu và cung cấp dịch vụ cho `Service Layer`.

## @Controller vs @Service vs @Repository

- Để phục vụ cho kiến trúc ở trên, Spring Boot tạo ra 3 Annotation là `@Controller` vs `@Service` vs `@Repository` để chúng ta có thể đánh dấu các tầng với nhau.
  - `@Service` Đánh dấu một Class là tầng Service, phục vụ các logic nghiệp vụ.
  - `@Repository` Đánh dấu một Class Là tầng Repository, phục vụ truy xuất dữ liệu.
- Tôi sẽ tạo ra một model `Girl`

```
package com.example.hellospringboot.mvc.model;
public class Girl {
    private String name;
    public Girl(String name) {
        this.name = name;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }
    @Override
    public String toString() {
        return "Girl(" + this.name + ")";
    }
}
```

- Tiếp theo tạo ra một `GirlRepository` để giao tiếp với DB

```
public interface GirlRepository {
    /**
     * Tìm kiếm một cô gái trong database theo tên
     * @param name
     * @return
     */
    Girl getGirlByName(String name);
}
```

- Một class đặt tên là `GirlRepositoryImpl` implement `GirlRepository`

```
@Repository
public class GirlRepositoryImpl implements GirlRepository {
    @Override
    public Girl getGirlByName(String name) {
        // Ở đây tôi ví dụ là database đã trả về
        // một cô gái với tên đúng như tham số
        // Còn thực tế phải query trong csdl
        return new Girl(name);
    }
}
```

- Tạo ra một class `GirlService` để giải quyết các logic nghiệp vụ. Lớp `GirlService` sẽ giao tiếp với DB thông qua GirlRepository.

```
import org.apache.commons.lang3.RandomStringUtils;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

@Service
public class GirlService {
    @Autowired
    private GirlRepository girlRepository;
    public Girl getRandomGirl(){
        // Random 1 cái tên độ dài 10
        String name = randomGirlName(10);
        // Gọi xuông tầng repository để query lấy một cô gái tên là "name" trong database
        return girlRepository.getGirlByName(name);
    }

    public String randomGirlName(int length) {
        // Random một string có độ dài quy định
        // Sử dụng thư viện Apache Common Lang
        return RandomStringUtils.randomAlphanumeric(length).toLowerCase();
    }
}
```

> Ở đây tôi không cấu hình một **DB** cụ thể nào cả nên sẽ sinh ra một **string_name** bằng cách dùng thư viện `Apache Common Lang`

#### Các bước setup nó như sau:

- Mở file pom.xml và thêm vào dependency như sau:

```
   <dependency>
       <groupId>org.apache.commons</groupId>
       <artifactId>commons-lang3</artifactId>
       <version>3.12.0</version> <!-- Use the latest version available -->
   </dependency>
```

- Tuy nhiên maven sẽ chưa tải xuống các dependency và build luôn cho chúng ta, nên khi run vẫn sẽ báo thư viện không tồn tại, vì vậy hãy làm mới maven project như sau:

  - Trên thanh công cụ Intelliji, chọn "View" -> "Tool Window"

  ![image2](https://live.staticflickr.com/65535/53474073598_2f340550b6_z.jpg)

  - Chọn "Maven Projects", click on "Refresh" là hình vòng tròn xoay để refresh project maven

  ![imag3](https://live.staticflickr.com/65535/53473935961_aac1d2b96b_z.jpg)

  - Sau đó run và chương trình chạy bình thường

  ![image4](https://live.staticflickr.com/65535/53474356445_a4751dc328_h.jpg)

  - Cấu trúc dự án theo chuẩn như sau

  ![image5](https://live.staticflickr.com/65535/53473038247_d19c288477_m.jpg)

- Chúng ta đã biết `@Component` đánh dấu cho Spring Boot biết Class đó là `Bean`. Và hiển nhiên `@Service` và `@Repository` cũng vậy. Vì thế ở ví dụ trên chúng ta có thể lấy **GirlService** từ **ApplicationContext**.

> Về bản chất thì bạn có thể sử dụng thay thế 3 Annotation @Component, @Service và @Repository cho nhau mà không ảnh hưởng gì tới code của bạn cả. Nó vẫn sẽ hoạt động.

- Tuy nhiên từ góc độ thiết kế thì chúng ta cần phân rõ 3 Annotation này cho các Class đảm nhiệm đúng nhiệm vụ của nó.
  - @Service gắn cho các Bean đảm nhiệm xử lý logic
  - @Repository gắn cho các Bean đảm nhiệm giao tiếp với DB
  - @Component gắn cho các Bean khác.
