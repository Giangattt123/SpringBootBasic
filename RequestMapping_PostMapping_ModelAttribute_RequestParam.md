## Introduce

- Ở bài này chúng ta sẽ tìm hiểu cách handle request POST của Spring Boot.

### @PostMapping

- `@PostMapping` có nhiệm vụ đánh dấu hàm xử lý POST request trong `Controller`.
- Khái niệm về **GET** và **POST** hi vọng các bạn đã biết rồi và chúng ta sẽ không đề cập thêm trong bài này. Còn nếu chưa biết bạn có thể tham khảo tại wiki: [Hypertext Transfer Protocol](https://vi.wikipedia.org/wiki/Hypertext_Transfer_Protocol#HTTP_Request_methods)
- Ví dụ ta có 2 hàm xử lí, một cho **POST** và 1 cho **GET**

```
@Controller
public class WebController {
    @GetMapping("/addTodo")
    public String addTodo(Model model) {
        return "addTodo";
    }

    @PostMapping("/addTodo")
    public String addTodo(Model model) {
        return "success";
    }
}
```

> Từ đây bạn có thể suy ra cách handle cho các method khác như **PUT, DELETE**: `@PutMapping , @DeleteMapping`

### @RequestMapping

- Trong trường hợp bạn muốn tất cả các method đều dùng chung một cách xử lý thì có thể sử dụng Annotation `@RequestMapping`.
- `@RequestMapping` là một annotation có ý nghĩa và mục đích sử dụng rộng hơn các loại **@GetMapping, @PostMapping,...**

```
@Controller
@RequestMapping("api/v1")
public class WebController {
    // Đường dẫn lúc này là: /api/v1/addTodo và method GET
    @RequestMapping(value = "/addTodo", method = RequestMethod.GET)
    public String addTodo(Model model) {
        return "addTodo";
    }

    // Đường dẫn lúc này là: /api/v1/addTodo và method POST
    @RequestMapping(value = "/addTodo", method = RequestMethod.POST)
    public String addTodo(@ModelAttribute Todo todo) {
        return "success";
    }
}
```

> Nếu không chỉ định method cho `@RequestMapping` thì nó sẽ nhận toàn bộ các method

## Website To-do

- Chúng ta sẽ làm một WebApp quản lý công việc và từ đó tìm hiểu cách gửi dữ liệu lên Server thông qua 2 method GET và POST

### Create Model

- Chúng ta sẽ tạo ra một object gọi là `Todo` để thể hiện thông tin.

```
package com.example.hellospringboot.WebToDo;

public class Todo {
    private String title;
    private String detail;

    public String getTitle() {
        return title;
    }

    public void setTitle(String title) {
        this.title = title;
    }

    public String getDetail() {
        return detail;
    }

    public void setDetail(String detail) {
        this.detail = detail;
    }
}
```

### GET /listTodo - Lấy danh sách các việc cần làm

- Chúng ta sẽ dùng đường dẫn `/listTodo` để lấy ra danh sách việc cần làm.
- Thiết kế method cho đường dẫn `/listTodo` là **GET**
- `WebController.java`

![image1](https://live.staticflickr.com/65535/53482448607_e0c9789b00.jpg)

> Khi tôi request lên server như này http://localhost:8081/listTodo?limit=2.

    - Thì cái đoạn ?limit=2 là Request Param.
    - Spring Boot sẽ tự xử lý và gán số 2 vào biến Integer limit hộ chúng ta.
    - Nếu chúng ta không gửi gì lên, thì limit sẽ là null

### GET /addTodo - Trang thêm công việc

```
@GetMapping("/addTodo")
public String addTodo(Model model) {
    // Thêm mới một đối tượng Todo vào model
    model.addAttribute("todo", new Todo());
    // Trả về template addTodo.html
    return "addTodo";
}
```

- File **addTodo.html**

![image2](https://live.staticflickr.com/65535/53483650694_5494185bbd_z.jpg)

- Ở đây:
  - `<form></form>` là thẻ tag mà bạn cần biết khi làm Web. Nó tạo Request Form gửi tới server với thông tin và các thẻ `<input>` trong nó.
  - Ở đây, tôi gán vào `Model` một đối tượng `Todo`.
  - Trong form, chúng ta lấy ra đối tượng Todo và chỉ định bởi `th:object="${todo}"`
  - Gán thông tin người dùng nhập vào Todo bằng cú pháp `th:field=*{tên_thuộc_tính}`.
  - Bấm Button thì form sẽ gửi request POST có chứa Todo lên địa chỉ **/addTodo**.

### POST /addTodo - Thêm công việc vào list

```
/*
@ModelAttribute marks the Todo object sent by the Form Request
*/
@PostMapping("/addTodo")
public String addTodo(@ModelAttribute Todo todo) {
    // Add object todo in list
    todoList.add(todo);
    // return page success "success.html"
    return "success";
}

<!-- success.html -->
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8" />
    <title>Page Success Add Todo</title>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />

    <link th:href="@{/css/bootstrap.css}" rel="stylesheet" />
    <link th:href="@{/css/main.css}" rel="stylesheet" />
</head>
<body>
    <h1>To-do</h1>
    <h1>Thêm thành công!</h1>
    <a th:href="@{/listTodo}" class="btn btn-primary">Xem danh sách công việc</a>
</body>
</html>
```

- Ở đây tôi sẽ tạo 1 template nữa là **home.html** do tôi đã có file index.html rồi nên mới cần tạo mới một template mới, nếu bạn đã có file **index.html** và chưa dùng vào việc gì thì không cần thêm đoạn code bên dưới(mặc định trình duyệt sẽ trỏ đến `/` hay `/index.html`), còn không hãy thêm nó vào nhé:

```
    @GetMapping("/home")
    public String home() {
        // return template "home.html"
        return "home";
    }
```

### Chạy chương trình

- Toàn bộ code hoàn chỉnh trong ``@Controller`:

```
package com.example.hellospringboot.WebToDo;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.ModelAttribute;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestParam;
import java.util.ArrayList;
import java.util.List;
import java.util.concurrent.CopyOnWriteArrayList;

@Controller
public class WebController {
    @GetMapping("/home")
    public String home() {
        // return template "home.html"
        return "home";
    }

    // use List instead of Database => demo mock data
    List<Todo> todoList = new CopyOnWriteArrayList<>();
    /*
    * @RequestParam is used to mark a variable as request param in a request sent to the server.
      It will assign the data of the corresponding param-name to the variable
    * */
    @GetMapping("/listTodo")
    public String index(Model model , @RequestParam(value = "limit" , required = false) Integer limit) {
        // Response object todoList
        // If the user sends param limit, the sublist of todoList is returned
        model.addAttribute("todoList", limit != null ? todoList.subList(0 , limit) : todoList);
        // return template "listTodo.html"
        return "listTodo";
    }

    @GetMapping("/addTodo")
    public String addTodo(Model model) {
        // Thêm mới một đối tượng Todo vào model
        model.addAttribute("todo", new Todo());
        // Trả về template addTodo.html
        return "addTodo";
    }

    /*
    @ModelAttribute marks the Todo object sent by the Form Request
    */
    @PostMapping("/addTodo")
    public String addTodo(@ModelAttribute Todo todo) {
        // Add object todo in list
        todoList.add(todo);
        // return page success "success.html"
        return "success";
    }
}
```

- Tạo file **App.java** để run chương trình

```
package com.example.hellospringboot.WebToDo;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class App {
    public static void main(String[] args) {
        SpringApplication.run(App.class , args);
    }
}
```

- Run chương trình và tìm đến **http://localhost:8081/home** sẽ render giao diện như sau:

![image3](https://live.staticflickr.com/65535/53483352236_43908bf6c2_c.jpg)

    + Ấn vào button **Thêm công việc** để add thêm một công việc

![image4](https://live.staticflickr.com/65535/53483758015_4dca6e5fd0_z.jpg)

    + Sau khi thêm thành công nó hiển thị như sau:

![image5](https://live.staticflickr.com/65535/53483758010_90a8a3d83d_z.jpg)

    + Sau khi thêm 1 công việc ta có thể xem danh sách các công việc hiện có:

![image6](https://live.staticflickr.com/65535/53483650644_6a0bf5663d_b.jpg)

    + Chẳng hạn tôi chỉ muốn nó hiển thị 2 công việc thay vì 3 công việc hiện tại hãy sử dụng thêm Request Param "limit"

![image7](https://live.staticflickr.com/65535/53483367086_b9b423ab99_b.jpg)

## To be Continue

- Webapp **Todo** của ta vẫn chưa hoàn thiện do còn thiếu `Delete` và `Update`, mình sẽ cập nhật ở bài viết sau do giờ nó cũng đã quá dài rồi (😁:)
