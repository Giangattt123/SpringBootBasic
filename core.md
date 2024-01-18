## Khái niệm tight-coupling (liên kết ràng buộc) và cách loosely coupled

### 1. Giới thiệu

- `tight-coupling` hay "liên kết ràng buộc" là một khái niệm trong Java ám chỉ việc mối quan hệ giữa các Class quá chặt chẽ. Khi yêu cầu thay đổi logic hay một class bị lỗi sẽ dẫn tới ảnh hưởng tới toàn bộ các Class khác.
- `loosely-coupled` là cách ám chỉ việc làm giảm bớt sự phụ thuộc giữa các Class với nhau.
- Lấy ví dụ như sau:
  - Cách code level 1

```
public class BubbleSortAlgorithm{

    public void sort(int[] array) {
        // TODO: Add your logic here
        System.out.println("Đã sắp xếp bằng thuật toán sx nổi bọt");
    }
}

public class VeryComplexService {
    private BubbleSortAlgorithm bubbleSortAlgorithm = new BubbleSortAlgorithm();
    public VeryComplexService(){
    }

    public void complexBusiness(int array[]){
        bubbleSortAlgorithm.sort(array);
        // TODO: more logic here
    }
}
```

=> Với cách làm ở trên, `VeryComplexService` đã hoàn thiện được nhiệm vụ, tuy nhiên, khi có yêu cầu thay đổi thuật toán sắp xếp sang QuickSort thì nghe vẻ chúng ta sẽ phải sửa lại hoàn toàn cả 2 Class trên.
Ngoài ra `BubbleSortAlgorithm` sẽ chỉ tồn tại nếu `VeryComplexService` tồn tại, vì `VeryComplexService` tạo đối tượng `BubbleSortAlgorithm` bên trong nó (hay nói cách khác là sự sống chết của `BubbleSortAlgorithm` sẽ do `VeryComplexService` quyết định), theo như cách implement này, nó là liên kết rất chặt với nhau.

    + Cách code level 2

```
public interface SortAlgorithm {
    /**
     * Sắp xếp mảng đầu vào
     * @param array
     */
    public void sort(int array[]);
}

public class BubbleSortAlgorithm implements SortAlgorithm{

    @Override
    public void sort(int[] array) {
        // TODO: Add your logic here
        System.out.println("Đã sắp xếp bằng thuật toán sx nổi bọt");
    }
}

public class VeryComplexService {
    private SortAlgorithm sortAlgorithm;
    public VeryComplexService(){
        sortAlgorithm = new BubbleSortAlgorithm();
    }

    public void complexBusiness(int array[]){
        sortAlgorithm.sort(array);
        // TODO: more logic here
    }
}
```

=> Với cách làm này, `VeryComplexService` sẽ chỉ quan hệ với một **interface** `SortAlgorithm`. Với cách này thì mỗi quan hệ giảm bớt sự liên kết, nhưng nó không thay đổi được việc thuật toán vẫn đang là `BubbleSortAlgorithm`.

    + Cách code level 3

```
public interface SortAlgorithm {
    /**
     * Sắp xếp mảng đầu vào
     * @param array
     */
    public void sort(int array[]);
}

public class BubbleSortAlgorithm implements SortAlgorithm{
    @Override
    public void sort(int[] array) {
        // TODO: Add your logic here
        System.out.println("Đã sắp xếp bằng thuật toán sx nổi bọt");
    }
}

public class QuicksortAlgorithm implements SortAlgorithm {
    @Override
    public void sort(int[] array) {
        // TODO: Add your logic here
        System.out.println("Đã sắp xếp bằng thuật sx nhanh");
    }
}

public class VeryComplexService {
    private SortAlgorithm sortAlgorithm;
    public VeryComplexService(SortAlgorithm sortAlgorithm){
        this.sortAlgorithm = sortAlgorithm;
    }

    public void complexBusiness(int array[]){
        sortAlgorithm.sort(array);
        // TODO: more logic here
    }
}

public static void main(String[] args) {
    SortAlgorithm bubbleSortAlgorithm = new BubbleSortAlgorithm();
    SortAlgorithm quickSortAlgorithm = new QuicksortAlgorithm();
    VeryComplexService business1 = new VeryComplexService(bubbleSortAlgorithm);
    VeryComplexService business2 = new VeryComplexService(quickSortAlgorithm);
}
```

=> Cách thứ ba này cũng là cách làm phổ biển nhất. Mối liên hệ giữa 2 Class đã "lỏng lẻo" hơn trước rất nhiều. `VeryComplexService` sẽ không quan tâm tới việc thuật toán sắp xếp là gì nữa, mà chỉ cần tập trung vào nghiệp vụ. Còn SortAlgorithm sẽ được đưa vào từ bên ngoài tùy theo nhu cầu sử dụng.

### 2. Dependency Injection (DI) và IoC

- **Dependency Injection (DI)**: DI là một khía cạnh của IoC. Thay vì đối tượng tự tạo ra các phụ thuộc của mình, các phụ thuộc được "đẩy" vào đối tượng từ bên ngoài (thông qua constructor, setter, hoặc method injection). Container chịu trách nhiệm giải quyết và cung cấp các dependency này, giúp giảm sự phụ thuộc giữa các thành phần của ứng dụng.
- **Dependency Injection** hay (**DI**) là một design pattern, nôm na nó là một phương pháp lập trình, là một thiết kế để bạn có được hiệu quả cao hơn khi code. Trước khi phương pháp này ra đời, bạn vẫn code bình thường, nhưng bây giờ có rồi, đi theo nó sẽ giúp ích nhiều hơn cho việc lập trình của bạn.
- Ở đây ta có một ví dụ

```
public class Girl{
    private Bikini outfit; // mỗi cô gái sẽ có một bộ bikini khi ra ngoài
    public Girl(){
      outfit = new Bikini(); // Khi bạn tạo ra 1 cô gái, bạn cho cô ta mặc Bikini chẳng hạn
    }
}
```

> Trước hết, qua đoạn code này, bạn sẽ thấy là khi bạn tạo ra một Girl, bạn sẽ tạo ra thêm 1 bộ Bikini đi kèm với cô gái đó. Lúc này, Bikini tồn tại mang ý nghĩa là **dependency** (phụ thuộc) của Girl.
> Khi khởi tạo thuộc tính như này, bạn vô tình tạo ra một điểm thắt nút trong chương trình của mình, giả sử, Girl muốn mặc một bộ Váy + Áo thun hở rốn hay không mặc gì thì sao? Bạn sẽ phải thay class `Bikini` thành `SkirtWithTshirt`(Váy với áo T-shirt) hay `Naked`(Trần như nhộng) ư?
> Hay nguy hiểm hơn, bộ đồ Bikini bị hỏng? (code lớp Bikini không hoạt động?) nó sẽ ảnh hưởng trực tiếp tới Girl.

- Vấn đề là ở đó, nguyên tắc là:
  Các **Class** không nên phụ thuộc vào các kế thừa cấp thấp, mà nên phụ thuộc vào Abstraction (lớp trừu tượng).

```
// Một interface cho việc ăn mặc
public interface Outfit {
  public void wear();
}

// Một object cấp thấp, implement của Outfits
public class Bikini implements Outfit {
  public void wear() {
    System.out.println("Đã mặc Bikini");
  }
}

// Bây giờ Girl chỉ phụ thuộc vào Outfit. nếu muốn thay đổi đồ của cô gái, chúng ta chỉ cần cho Outfit một thể hiện mới.
public class Girl{
    private Outfit outfit;
    public Girl(){
      outfit = new Bikini();
    }
}
```

> Tới đây, chúng ta mới chỉ Abtract hóa thuộc tính của Girl mà thôi, còn thực tế, Girl vẫn đang bị gắn với một bộ Bikini duy nhất. Vậy muốn thay đồ cho cô gái, bạn phải làm như nào.

Phải sửa code thêm chút nữa:

```
public class Girl{
    private Outfit outfit;
    public Girl(Outfit anything){
      this.outfit = anything // Tạo ra một cô gái, với một món đồ tùy biến
      // Không bị phụ thuộc quá nhiều vào thời điểm khởi tạo, hay code.
    }
}

public class Main {
  public static void main(String[] args) {
    Outfit bikini = new Bikini(); // Tạo ra đối tượng Bikini ở ngoài đối tượng
    Girl ngocTrinh = new Girl(bikini); // Mặc nó vào cho cô gái khi tạo ra cô ấy.
  }
}
```

> Với đoạn code ở trên, chúng ta đã gần như tách được Bikini ra hoàn toàn khỏi Girl. điều này làm giảm sự phụ thuộc giữa Girl và Bikini. Mà tăng tính tùy biến, linh hoạt cho code.
> Outfit của cô gái bây giờ là "bikini"

![image1](https://live.staticflickr.com/65535/53467940686_c0ece662d8_z.jpg)

Bây giờ Girl sẽ hoạt động với Outfit mà thôi. Và Outfit ở đâu ra? Chúng ta tạo ra và đưa nó vào (Inject) cô gái Girl => Khái niệm `Dependency Injection` từ đây mà ra~

- **Dependency Injection** là việc các Object nên phụ thuộc vào các `Abstract Class` và thể hiện chi tiết của nó sẽ được `Inject` vào đối tượng lúc runtime.
  - Bây giờ muốn Girl mặc gì khác, bạn chỉ cần tạo một Class kế thừa Outfit và Inject (dịch là Tiêm vào cũng được) nó vào Girl là xong!
  - Giờ tôi muốn cô ấy mặc 1 chiếc áo dài thì sao nhỉ?? => tạo 1 class dressLong implement Outfit()

![image2](https://live.staticflickr.com/65535/53467952941_b2588111ff.jpg)

- Các cách để **Inject dependency** vào một đối tượng có thể kể đến như sau:
  - `Constructor Injection`: Cái này chính là ví dụ của mình, tiêm dependency ngay vào Contructor cho tiện.
  - `Setter Injection`: Xài girl.setOutfit(new Naked()) 😈
  - `Interface Injection`: Mỗi Class muốn inject cái gì, thì phải implement một Interface có chứa một hàm inject(xx) (Gần như thay thế cho setter ý bạn). Rồi bạn muốn inject gì đó thì gọi cái hàm inject(xx) ra. Cách này hơi dài và khó cho người mới.

### 3. Inversion of Control(IoC)

- Dependency Injection giúp chúng ta dễ dàng mở rộng code và giảm sự phụ thuộc giữa các dependency với nhau. Tuy nhiên, lúc này, khi code bạn sẽ phải kiêm thêm nhiệm vụ **Inject dependency** (tiêm sự phụ thuộc). Thử tưởng tượng một Class có hàng chục dependency thì bạn sẽ phải tự tay inject từng ý cái. Việc này lại dẫn tới khó khăn trong việc code, quản lý code và dependency

```
public static void main(String[] args) {
    Outfit bikini = new Bikini();
    Accessories gucci = new GucciAccessories();
    HairStyle hair = new KoreanHairStyle();
    Girl ngocTrinh = new Girl(bikini, gucci, hair);
}
```

- Bây giờ giả sử, chúng ta định nghĩa trước toàn bộ các dependency có trong Project, mô tả nó và tống nó vào 1 cái `kho` và giao cho một thằng tên là `framework` quản lý. Bất kỳ các Class nào khi khởi tạo, nó cần dependency gì, thì cái `framework`này sẽ tự tìm trong kho rồi inject vào đối tượng thay chúng ta như vậy sẽ tiện hơn phải không?
  ![image3](https://images.spr.so/cdn-cgi/imagedelivery/j42No7y-dcokJuNgXeA0ig/6b4c57b8-6861-4abd-b586-f55c2d7e994c/Untitled/w=1920,quality=80)
  > Đó cũng chính là nguyên lý chính của Inversion of Control (IOC) - Đảo chiều sự điều khiển
- **Inversion of Control (IoC)**: Trong mô hình IoC, không phải là ứng dụng điều khiển quyền lực, mà là container điều khiển. Thay vì đối tượng tự tạo ra các đối tượng phụ thuộc của mình, đối tượng chỉ đơn giản là sử dụng các đối tượng được cung cấp bởi container. Điều này giúp giảm sự ràng buộc giữa các đối tượng và làm cho ứng dụng linh hoạt hơn.
- Khi đó, code chúng ta sẽ chỉ cần như này, để lấy ra 1 đối tượng:

```
@Override
public void run(String... args) throws Exception {
    Girl girl = context.getBean(Girl.class);
}
```

- Đối với Java thì có một số Framework hỗ trợ chúng ta Inversion of Control (IOC), trong đó nổi bật có:
  - Spring framework
  - Google Guice
- Spring framework là một framework từ những ngày đầu, ra đời để hiện thực ý tưởng Inversion of Control (IOC), tuy nhiên, theo thời gian, Spring lớn mạnh và trở thành một hệ sinh thái rộng lớn phục vụ rất nhiều chức năng trên nền tảng IoC này. Google Guice ra đời sau và tập trung vào nhiệm vụ DI thôi.
