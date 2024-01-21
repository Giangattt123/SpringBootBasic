## Spring Boot với application.properties

- Trong thực tế không phải lúc nào chúng ta cũng nên để mọi thứ trong code của mình. Có những thông số tốt hơn hết nên được truyền từ bên ngoài vào ứng dụng, để giúp ứng dụng của bạn dễ dàng thay đổi giữa các môi trường khác nhau.
- Để phục vụ điều này, chúng ta sẽ tìm hiểu về khái niệm config ứng dụng Spring Boot với application.properties
- Trong Spring Boot, các thông tin cấu hình mặc định được lấy từ file **resources/applications.properties**
  ![image1](https://live.staticflickr.com/65535/53478413948_63f1982de8_n.jpg)
- Một case rất phổ biến đó là rất nhiều các ứng dụng khi dùng đến các môi trường tạo server thì dùng port mặc định là **8080** ví dụ như: Apache trong Xampp , Apache Tomcat(kể cả khi chạy trên Eclipse hay Intelliji), ví dụ như:

![image2](https://live.staticflickr.com/65535/53478595494_efea8ef228_c.jpg)

![image3](https://live.staticflickr.com/65535/53478594419_f711d30d62_z.jpg)

> Vì vậy nếu chạy tomcat server trên project springboot hiện tại chắc chắn sẽ bị **conflig port** => đổi qua một port khác

## application.properties

- Ví dụ, bạn muốn Spring Boot chạy trên port 8081 thay vì 8080:
- Ở file **applications.properties**

```
server.port = 8081
```

- Hoặc bạn muốn log của chương trình chi tiết hơn. Hãy chuyển nó sang dậng Debug thay vì Console bằng cách config như sau:

```
logging.level.root=DEBUG
```

> Đây là cách chúng ta có thể can thiệp vào các cấu hình của ứng dụng từ bên ngoài. Cho phép thay đổi linh hoạt tùy môi trường.

## @Value

- Trong trường hợp, bạn muốn tự config những giá trị của riêng mình, thì Spring Boot hỗ trợ bạn với annotation `@Value`
- Ví dụ, tôi muốn cấu hình cho thông tin database của tôi từ bên ngoài ứng dụng

```
giang.mysql.url=jdbc:mysql://host1:33060/giangptit
```

- `@Value` được sử dụng trên thuộc tính của class, Có nhiệm vụ lấy thông tin từ file properties và gán vào biến.

```
public class AppConfig {
    // Lấy giá trị config từ file application.properties
    @Value("${loda.mysql.url}")
    String mysqlUrl;
}
```

> Thông tin truyền vào annottaion **@Value** chính là tên của cấu hình đặt trong dấu ${name}

- Ví dụ file **applications.properties**

```
server.port = 8081
logging.level.root=INFO
giang.mysql.url=jdbc:mysql://host1:33060/giangptit
```

```
 @Bean("sqlValue")
    DatabaseConnector mysqlValue() {
        DatabaseConnector mySqlValue = new MySqlConnector();
        mySqlValue.setUrl(mysqlUrl);
        System.out.println("Config Mysql Url: " + mySqlValue.getUrl());
        return mySqlValue;
    }
```

> Output

```
Đã kết nối tới Mysql: jdbc:mysql://host1:33060/giangptit
```
