# NGÂN HÀNG CÂU HỎI – LÝ THUYẾT (MỨC HIỂU) — ĐÁP ÁN
> Kiến trúc phần mềm · SOLID · Design Patterns · DDD · Kiến trúc UI

---

## A. KIẾN TRÚC PHẦN MỀM (Layered / Onion / Hexagonal)

**1. Trong kiến trúc Layered, tầng nào chứa logic nghiệp vụ chính?**
**Tầng Business Logic** (còn gọi là Domain Layer hoặc Service Layer) chứa logic nghiệp vụ chính. Tầng này nằm giữa tầng Presentation (UI) và tầng Data Access, chịu trách nhiệm xử lý các quy tắc, tính toán và quyết định đặc thù của miền ứng dụng.

**2. Trong kiến trúc Onion, chiều phụ thuộc là gì?**
Các phụ thuộc luôn hướng **vào trong** — các tầng bên ngoài phụ thuộc vào các tầng bên trong, không bao giờ ngược lại. Model Domain nằm ở trung tâm, bao quanh bởi Domain Services, rồi Application Services, và cuối cùng là Infrastructure/UI ở vòng ngoài cùng.

**3. Kiến trúc nào nhấn mạnh rằng domain độc lập với infrastructure?**
**Kiến trúc Onion** (và Hexagonal Architecture) nhấn mạnh mạnh mẽ sự độc lập của domain. Domain model ở trung tâm không có kiến thức gì về database, framework, hay dịch vụ bên ngoài; các mối quan tâm về infrastructure nằm ở các tầng ngoài cùng.

**4. Trong kiến trúc Hexagonal, vai trò của "port" là gì?**
**Port** là một giao diện (interface) hay ranh giới trừu tượng định nghĩa cách lõi ứng dụng giao tiếp với thế giới bên ngoài. Port thể hiện *điều gì* ứng dụng cần hoặc cung cấp, mà không quan tâm đến *cách* thực hiện. Có hai loại: **driving port** (được gọi từ bên ngoài, ví dụ: giao diện use-case) và **driven port** (được lõi gọi ra, ví dụ: giao diện repository).

**5. Mục đích của "adapter" trong kiến trúc Hexagonal là gì?**
**Adapter** là một cài đặt cụ thể kết nối một port với một công nghệ bên ngoài thực tế. Ví dụ, một REST controller là adapter cho driving port, còn một cài đặt JPA repository là adapter cho driven port. Adapter dịch chuyển giữa định dạng bên ngoài và model nội bộ của ứng dụng.

**6. Kiến trúc nào giúp dễ thay đổi UI hơn? Tại sao?**
Kiến trúc **Hexagonal** và **Onion** giúp dễ thay đổi UI nhất, vì UI được đặt ở tầng ngoài cùng và phụ thuộc vào lõi thông qua port/interface. Domain và logic ứng dụng không biết gì về UI, vì vậy việc thay một công nghệ UI bằng một công nghệ khác (ví dụ: REST → GraphQL, hoặc Web → CLI) chỉ cần viết một adapter mới.

**7. Trong kiến trúc Layered, UI có nên gọi trực tiếp database không? Tại sao?**
**Không.** UI không bao giờ nên gọi trực tiếp database. Mỗi tầng chỉ nên giao tiếp với tầng ngay phía dưới nó. Bỏ qua các tầng tạo ra sự kết nối chặt chẽ, làm cho hệ thống khó bảo trì, kiểm thử và thay đổi hơn, đồng thời cho phép các lỗi ở tầng UI làm hỏng tính toàn vẹn dữ liệu.

**8. Điểm khác biệt chính giữa kiến trúc Onion và Layered là gì?**
Trong kiến trúc **Layered**, luồng phụ thuộc đi từ trên xuống dưới và tầng domain thường phụ thuộc vào tầng data phía dưới. Trong kiến trúc **Onion**, tất cả phụ thuộc đều hướng vào domain, nên domain hoàn toàn độc lập — nó không phụ thuộc vào bất cứ thứ gì. Onion thực thi sự cô lập chặt chẽ hơn đối với logic nghiệp vụ cốt lõi.

**9. Tên gọi khác của kiến trúc Hexagonal là gì?**
Còn được gọi là kiến trúc **Ports and Adapters** (Cổng và Bộ chuyển đổi), thuật ngữ được đặt ra bởi Alistair Cockburn.

**10. Trong một kiến trúc tốt, business logic có nên phụ thuộc vào framework không? Tại sao?**
**Không.** Business logic nên độc lập với framework. Framework là chi tiết cài đặt có thể thay đổi, nâng cấp hoặc thay thế. Nếu các quy tắc nghiệp vụ phụ thuộc vào một framework cụ thể, toàn bộ domain model trở nên khó kiểm thử (yêu cầu framework phải đang chạy) và khó di chuyển. Phụ thuộc vào trừu tượng (interfaces) thay thế giúp domain ổn định và dễ kiểm thử.

---

## B. NGUYÊN LÝ SOLID

**11. Nguyên lý Single Responsibility (SRP) yêu cầu gì từ một class?**
Một class chỉ nên có **một, và chỉ một, lý do để thay đổi** — nghĩa là nó chỉ chịu trách nhiệm về một chức năng hoặc mối quan tâm duy nhất. Nếu một class xử lý cả logic nghiệp vụ lẫn việc gửi email, thì một thay đổi trong nhà cung cấp email sẽ buộc phải thay đổi code logic nghiệp vụ, vi phạm SRP.

**12. Nguyên lý Open/Closed (OCP) mang lại lợi ích gì?**
OCP cho phép bạn **thêm tính năng mới mà không cần sửa đổi code đã có và đã kiểm thử**. Các class mở để mở rộng (có thể thêm hành vi mới qua kế thừa hoặc composition) nhưng đóng để sửa đổi (không chạm vào code hiện có). Điều này giảm rủi ro hồi quy và làm cho codebase ổn định hơn.

**13. Theo Dependency Inversion Principle (DIP), chúng ta nên phụ thuộc vào điều gì?**
Chúng ta nên phụ thuộc vào **abstractions (interfaces hoặc abstract classes)**, không phải vào các cài đặt cụ thể. Các module cấp cao không nên import trực tiếp các module cấp thấp; cả hai nên phụ thuộc vào một abstraction chung. Điều này giúp dễ dàng thay đổi cài đặt và cải thiện khả năng kiểm thử thông qua dependency injection.

**14. Liskov Substitution Principle (LSP) liên quan đến mối quan hệ nào trong OOP?**
LSP liên quan đến **kế thừa (quan hệ is-a)**. Nguyên lý phát biểu rằng một subclass phải có thể thay thế cho class cha của nó mà không làm thay đổi tính đúng đắn của chương trình. Nếu việc thay thế một subclass phá vỡ hành vi được mong đợi từ class cha, thì hệ thống kế thừa được thiết kế sai.

**15. Nguyên lý Interface Segregation (ISP) nhằm tránh vấn đề gì?**
ISP nhằm tránh việc ép một class phải **cài đặt các phương thức mà nó không cần**. Khi một interface quá lớn ("fat interface"), các class chỉ cần một tập con các phương thức vẫn bị buộc phải cài đặt (dù chỉ là stub rỗng) tất cả các phương thức còn lại. ISP yêu cầu interface nên nhỏ gọn và tập trung để các class cài đặt không bị ràng buộc bởi các hợp đồng không liên quan.

**16. Một class có nhiều câu lệnh if-else dựa trên kiểu có thể vi phạm nguyên lý nào?**
Nhiều khả năng vi phạm **Nguyên lý Open/Closed (OCP)**. Mỗi kiểu mới đòi hỏi phải sửa đổi chuỗi if-else hiện có, nghĩa là class không đóng để sửa đổi. Giải pháp là sử dụng đa hình (ví dụ: Strategy hoặc Factory pattern) để mỗi kiểu được xử lý bởi class riêng của nó mà không cần chạm vào code hiện có.

**17. "Lập trình hướng interface, không hướng implementation" liên quan đến nguyên lý nào?**
Điều này trực tiếp liên quan đến **Dependency Inversion Principle (DIP)** và cũng phù hợp với **Open/Closed Principle**. Phụ thuộc vào interface thay vì class cụ thể giúp tách rời các module cấp cao khỏi các chi tiết cấp thấp.

**18. Nếu một class phải được sửa đổi mỗi lần thêm tính năng mới, nguyên lý nào bị vi phạm?**
**Nguyên lý Open/Closed (OCP)** bị vi phạm. Một class được thiết kế tốt nên có thể mở rộng (qua kế thừa, composition, hoặc cấu hình) mà không cần thay đổi source code của chính nó khi có tính năng mới.

**19. Việc sử dụng interface cải thiện điều gì trong hệ thống?**
Interface cải thiện **tính tách rời (decoupling), khả năng kiểm thử, tính linh hoạt và khả năng thay thế**. Chúng cho phép các thành phần giao tiếp thông qua hợp đồng thay vì cài đặt cụ thể, giúp dễ dàng thay thế database thực bằng stub trong bộ nhớ khi kiểm thử, hoặc thay thế một thư viện bên thứ ba bằng thư viện khác.

**20. Nguyên lý SOLID nào giúp cải thiện khả năng kiểm thử?**
**Dependency Inversion Principle (DIP)** cải thiện khả năng kiểm thử trực tiếp nhất. Bằng cách phụ thuộc vào interface, bạn có thể inject các cài đặt mock hoặc stub trong quá trình unit testing, cô lập class đang được kiểm thử khỏi các phụ thuộc bên ngoài như database, hệ thống tệp, hoặc API.

---

## C. DESIGN PATTERNS

**21. Strategy pattern giải quyết vấn đề gì?**
Strategy giải quyết vấn đề **chọn một thuật toán hoặc hành vi tại thời điểm chạy (runtime)**. Thay vì hard-code logic điều kiện (if-else/switch) để chọn giữa các hành vi, mỗi hành vi được đóng gói trong class riêng của nó cài đặt một interface chung. Client giữ tham chiếu đến interface và có thể hoán đổi strategy mà không cần thay đổi code của chính nó.

**22. Khi nào nên sử dụng Factory pattern?**
Sử dụng Factory khi **loại đối tượng cần tạo có thể thay đổi**, hoặc khi logic tạo đối tượng phức tạp và không nên để lộ cho client. Factory tập trung hóa logic tạo đối tượng, giúp dễ dàng thay đổi loại cụ thể được tạo mà không ảnh hưởng đến code gọi.

**23. Repository pattern giúp tách điều gì khỏi domain?**
Repository tách **logic truy cập dữ liệu / logic lưu trữ** khỏi domain model. Domain chỉ làm việc với các đối tượng trong bộ nhớ thông qua interface giống như collection, không biết gì về SQL, ORM, hay bất kỳ cơ chế lưu trữ nào.

**24. Singleton pattern đảm bảo điều gì?**
Singleton đảm bảo rằng một class chỉ có **một instance duy nhất** trong suốt vòng đời ứng dụng, và cung cấp điểm truy cập toàn cục vào instance đó.

**25. Khi nào Observer pattern phù hợp?**
Observer phù hợp khi **sự thay đổi trong một đối tượng (subject) cần tự động thông báo và cập nhật nhiều đối tượng phụ thuộc (observers)**, mà không cần subject biết ai đang lắng nghe. Các trường hợp sử dụng phổ biến: hệ thống sự kiện, data binding UI, pub/sub messaging.

**26. Pattern nào phù hợp để thay đổi hành vi tại runtime?**
**Strategy pattern** phù hợp nhất để thay đổi hành vi tại runtime. Bạn có thể thay thế đối tượng strategy mà một context object đang giữ bất cứ lúc nào, thực tế là chuyển đổi thuật toán một cách linh hoạt.

**27. Pattern nào giúp giảm sự kết nối giữa các class?**
Nhiều pattern giảm coupling, nhưng **Observer**, **Mediator**, và **Dependency Injection (dựa trên DIP)** nổi bật nhất. Mediator cụ thể giảm phụ thuộc trực tiếp giữa các đối tượng bằng cách định tuyến tất cả giao tiếp qua một mediator trung tâm.

**28. Khi có nhiều cách xử lý tương tự nhau, pattern nào phù hợp?**
**Strategy pattern** (hoặc **Template Method** nếu khung xương là cố định) phù hợp. Strategy cho phép mỗi thuật toán xử lý tồn tại trong class riêng của nó, tất cả chia sẻ một interface chung.

**29. Mục đích của Adapter pattern là gì?**
Adapter pattern **chuyển đổi interface của một class hiện có sang interface khác** mà client mong đợi. Nó hoạt động như một cầu nối (wrapper) để các class có interface không tương thích có thể làm việc cùng nhau.

**30. Pattern nào giúp ẩn logic tạo đối tượng?**
**Factory pattern** (Factory Method hoặc Abstract Factory) ẩn logic tạo đối tượng khỏi người gọi. **Builder** cũng được sử dụng khi việc tạo đối tượng liên quan đến nhiều bước hoặc cấu hình phức tạp.

---

## D. DOMAIN-DRIVEN DESIGN (DDD – Cơ bản)

**31. Sự khác biệt giữa Entity và Value Object là gì?**
**Entity** có một định danh duy nhất (ví dụ: Customer ID) tồn tại theo thời gian; hai entity có cùng thuộc tính nhưng khác ID được coi là các đối tượng khác nhau. **Value Object** không có định danh — nó được xác định hoàn toàn bởi các thuộc tính của nó; hai value object có cùng giá trị thuộc tính được coi là bằng nhau và có thể hoán đổi (ví dụ: Money(100, USD) bằng với một Money(100, USD) khác).

**32. Value Object thường có thể thay đổi (mutable) hay bất biến (immutable)?**
Value Object thường **bất biến (immutable)**. Vì chúng được xác định bởi giá trị của mình, cho phép thay đổi sẽ phá vỡ ngữ nghĩa bằng nhau và gây ra lỗi tinh vi. Khi bạn cần một value object "đã sửa đổi", bạn tạo một instance mới.

**33. Mục đích của Aggregate là gì?**
Aggregate **nhóm một cụm các đối tượng domain liên quan** (entities và value objects) và xử lý chúng như một đơn vị duy nhất cho các thay đổi dữ liệu. Nó có một **Aggregate Root** — đối tượng duy nhất mà code bên ngoài có thể truy cập hoặc sửa đổi aggregate thông qua đó. Điều này thực thi các bất biến và giữ cho domain nhất quán.

**34. Quy tắc nghiệp vụ nên được đặt ở đâu trong DDD?**
Quy tắc nghiệp vụ nên được đặt trong **Domain Layer** — bên trong Entities, Value Objects, Aggregates, và Domain Services. Giữ các quy tắc ở đó đảm bảo domain model tự nhất quán và có thể thực thi các bất biến bất kể use case ứng dụng nào kích hoạt một thao tác.

**35. Domain model có nên phụ thuộc vào database không? Tại sao?**
**Không.** Domain model không nên có kiến thức gì về cách nó được lưu trữ. Phụ thuộc vào framework database buộc domain phải được kiểm thử với DB thực, kết nối model với một công nghệ lưu trữ cụ thể, và làm mờ ranh giới giữa logic nghiệp vụ và các mối quan tâm về infrastructure. Repositories tóm tắt việc lưu trữ đằng sau một interface giống như collection.

**36. Repository trong DDD đại diện cho điều gì?**
Repository đại diện cho một **tập hợp (collection) các đối tượng domain** (cụ thể là Aggregate Roots). Nó cung cấp các phương thức như `find`, `save`, và `remove` có cảm giác như các thao tác collection trong bộ nhớ, ẩn tất cả chi tiết lưu trữ khỏi domain.

**37. Entity được nhận dạng như thế nào?**
Entity được nhận dạng bởi một **định danh duy nhất (ID)** không thay đổi trong suốt vòng đời của nó, bất kể các thuộc tính khác của nó thay đổi như thế nào. Ví dụ bao gồm UUID, khóa database tự tăng, hoặc một định danh nghiệp vụ có ý nghĩa như số đơn hàng.

**38. Khi nào nên dùng Value Object thay vì Entity?**
Sử dụng Value Object khi khái niệm **không có vòng đời hay định danh có ý nghĩa riêng**, và sự bằng nhau được xác định hoàn toàn bởi các thuộc tính của nó. Ví dụ: địa chỉ bưu chính, số tiền, khoảng thời gian, tọa độ, màu sắc. Nếu bạn không cần theo dõi "instance cụ thể nào", thì đó nên là Value Object.

**39. Domain Service được sử dụng khi nào?**
Domain Service được sử dụng khi **một thao tác liên quan đến nhiều aggregate hoặc entity và không tự nhiên thuộc về bất kỳ entity nào trong số chúng**. Ví dụ, một `TransferService` chuyển tiền giữa hai `Account` aggregate là một domain service vì logic không thuộc về riêng account nào.

**40. Business logic nên được đặt ở Application layer hay Domain layer? Tại sao?**
Logic nghiệp vụ cốt lõi thuộc về **Domain layer**. Application layer điều phối các use case — nó điều phối các lời gọi đến domain objects, repositories, và external services — nhưng nó không nên chứa các quy tắc domain. Đặt quy tắc trong domain đảm bảo chúng có thể tái sử dụng, kiểm thử được, và được thực thi nhất quán bất kể điểm vào nào (API, CLI, UI, v.v.).

---

## E. KIẾN TRÚC UI (MVC / MVVM)

**41. Trong MVC, vai trò của Controller là gì?**
**Controller** xử lý đầu vào từ người dùng (HTTP requests, nhấp nút, v.v.), diễn giải nó, gọi các thao tác Model phù hợp, và xác định View nào cần render. Nó hoạt động như trung gian giữa View và Model.

**42. Trong MVC, Model đại diện cho điều gì?**
**Model** đại diện cho dữ liệu và logic nghiệp vụ của ứng dụng. Nó chịu trách nhiệm về truy xuất dữ liệu, xử lý, và quản lý trạng thái. Model độc lập với View và Controller.

**43. Sự khác biệt chính giữa MVVM và MVC là gì?**
Trong **MVC**, Controller làm trung gian giữa View và Model, và View thường truy vấn Model hoặc được cập nhật theo kiểu mệnh lệnh. Trong **MVVM**, **ViewModel** cung cấp dữ liệu và lệnh dưới dạng các thuộc tính có thể quan sát, và **View** bind vào chúng theo kiểu khai báo thông qua data binding — không có tham chiếu trực tiếp từ View đến ViewModel sau khi binding được thiết lập. MVVM phù hợp hơn cho UI phong phú với trạng thái phức tạp.

**44. ViewModel trong MVVM có trách nhiệm gì?**
**ViewModel** chuẩn bị và cung cấp dữ liệu từ Model ở dạng thuận tiện cho View, và cung cấp các lệnh cho các hành động của người dùng. Nó giữ trạng thái presentation (ví dụ: "nút có được bật không?") và phản ứng với các thay đổi Model bằng cách cập nhật các thuộc tính có thể quan sát. Nó **không có tham chiếu trực tiếp đến View**.

**45. UI có nên chứa business logic không? Tại sao?**
**Không.** Business logic trong UI tạo ra nhiều vấn đề: khó kiểm thử (yêu cầu render UI), trùng lặp các quy tắc có thể cần được thực thi ở nơi khác, và kết nối chặt chẽ presentation với các mối quan tâm domain. Business logic thuộc về tầng domain/service; UI chỉ nên xử lý presentation và tương tác người dùng.

**46. Pattern nào thường được sử dụng trong ứng dụng WPF?**
**MVVM (Model–View–ViewModel)** là pattern thống trị cho ứng dụng WPF. Engine data binding của WPF làm cho việc View bind trực tiếp vào các thuộc tính và lệnh của ViewModel trở nên tự nhiên, cho phép phân tách rõ ràng mà không cần code-behind logic.

**47. Pattern nào thường được sử dụng trong ứng dụng web?**
**MVC (Model–View–Controller)** là pattern phổ biến nhất cho ứng dụng web (ví dụ: ASP.NET MVC, Spring MVC, Ruby on Rails, Laravel). Controller xử lý HTTP request, Model quản lý dữ liệu, và View render HTML.

**48. Mục đích của data binding trong MVVM là gì?**
Data binding **tự động đồng bộ hóa** View và ViewModel. Khi thuộc tính của ViewModel thay đổi, UI cập nhật tự động, và khi người dùng tương tác với UI, trạng thái của ViewModel cập nhật tự động. Điều này loại bỏ hầu hết code gán `get/set` thủ công trong tầng View.

**49. View có nên truy cập trực tiếp database không? Tại sao?**
**Không.** View chỉ nên tương tác với Controller/ViewModel. Cho phép View truy cập database trực tiếp vi phạm separation of concerns, làm cho việc kiểm thử không thể thực hiện được nếu không có database thực, và kết nối tầng presentation với công nghệ lưu trữ cụ thể. Mọi dữ liệu mà View cần nên được chuẩn bị và cung cấp bởi ViewModel hoặc Controller.

**50. Mục tiêu chính của kiến trúc UI là gì?**
Mục tiêu chính là đạt được **separation of concerns (phân tách mối quan tâm)** — giữ việc render UI, logic ứng dụng, và logic dữ liệu/nghiệp vụ trong các tầng riêng biệt rõ ràng. Điều này cải thiện khả năng kiểm thử (mỗi tầng có thể được kiểm thử độc lập), khả năng bảo trì (thay đổi trong một tầng không lan sang các tầng khác), và khả năng mở rộng (các phần khác nhau có thể phát triển độc lập).

---

*Kết thúc Ngân Hàng Đáp Án (Tiếng Việt)*
