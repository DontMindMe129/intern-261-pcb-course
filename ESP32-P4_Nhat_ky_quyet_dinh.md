# ESP32-P4 Dev-kit — Theo dõi công việc và quyết định

**Cập nhật: 29/10/2026**

Ghi theo thứ tự trao đổi và thực hiện trong đợt rà soát thiết kế gần đây. Chỉ ghi những việc đã diễn ra; không dựng sẵn danh mục cho các giai đoạn chưa làm. Các mục trao đổi không có ngày riêng vì chưa có căn cứ gắn thời điểm chính xác.

**Cách đọc:** “Đã chốt” là đã thống nhất phạm vi hoặc hướng thiết kế, không có nghĩa đã vẽ schematic hay đã thử phần cứng. “Ứng viên” chưa phải linh kiện được chọn cuối cùng. Những phương án được điều chỉnh về sau vẫn giữ ở vị trí cũ để thấy diễn biến.

## Điểm xuất phát của đợt trao đổi này

- Đã có project KiCad và schematic nguồn, USB nạp/debug, P4 cùng các mạch lõi: nguồn HP, Flash, RESET/BOOT, thạch anh và tụ nguồn.
- Thiết kế hướng tới P4 revision 3.x; mã chip đặt mua cụ thể chưa chốt.
- Đã có block diagram, ghi chú học thiết kế và tài liệu Hardware Design.
- Chưa triển khai layout PCB hoặc kiểm chứng phần cứng. Mạch đã vẽ vẫn cần review; không coi là thiết kế đã hoàn tất.

## 01. Chốt lại phạm vi mở rộng

- **Đã chốt:** Wi-Fi/Bluetooth, camera, màn hình, microSD và đo điện năng tiêu thụ.
- Loa, micro và Ethernet đã được thảo luận nhưng không nằm trong danh sách mở rộng được chốt.
- Khi đó chưa chọn module, pinout và ngân sách nguồn.

## 02. Đề xuất phân bổ tài nguyên và hướng nhóm 2

- Đã phân tích hướng dành giao tiếp riêng cho các chức năng trên kit, đưa GPIO còn tự do ra header đa dụng.
- Đã đề xuất CSI cho camera, DSI cho màn hình, SDIO cho module không dây, SDMMC cho thẻ và I²C cho khối đo.
- Hướng này đã được đưa vào bản block diagram cập nhật theo yêu cầu tiếp tục của người dùng.
- **Chưa chốt:** GPIO cụ thể, số slot SDMMC cụ thể, số chân header và các vị trí cần tháo nối.

## 03. Cập nhật và thu gọn bộ block diagram

- **Đã thực hiện:** giữ một file drawio chính gồm tổng quan và ba nhóm; tổng quan đặt P4 ở giữa.
- Nhóm 2 thể hiện giao tiếp riêng + GPIO đa dụng; nhóm 3 có đủ năm phần mở rộng.
- Đã xóa các bản diagram phụ, giữ PNG/SVG xuất từ bản chính và hình học tập về boot.
- Đã sửa các liên kết liên quan trong ghi chú.
- File chính: [esp32-p4_tong-hop.drawio](block_diagram/esp32-p4_tong-hop.drawio).

## 04. Rà hình thức schematic hiện có

- **Đã thực hiện:** xem bản xuất của Root, Power, USB_GPIO và ESP32_P4.
- Đã đánh giá cách chia vùng, khoảng trắng, độ dễ đọc, vị trí nhãn và khung tên.
- Đã tham khảo thêm schematic Espressif, Olimex, Waveshare và cấu trúc tài liệu Nucleo.
- Mục này chỉ đánh giá hình thức, không phải review hoàn chỉnh nguyên lý điện.

## 05. Thử tổ chức hai trang đầu schematic

- **Đã thực hiện rồi được điều chỉnh sau đó:** thêm Design Block trang 1, bố trí Root thành Sheet Overview trang 2.
- Đã xuất hình xem trước và kiểm tra kết nối trước/sau.
- Đây là bản thử trung gian, không phải bố cục hiện hành.

## 06. Gom ba tài liệu tham khảo vừa tải

- **Đã thực hiện:** tạo thư mục [references/schematics](references/schematics), sao chép/giải nén đúng ba PDF schematic:
  - P4X-C5 Function EV Board v2.0.
  - P4X Function EV Board v1.8.
  - P4X-EYE v2.4.
- Đã kiểm tra bản sao khớp nguồn; giữ nguyên file gốc trong Downloads.

## 07. Gộp Root thành Design Block

- Sau khi đối chiếu cách trình bày của ba reference, đã thử bỏ trang Overview riêng.
- **Bố cục hiện hành:** Root là Design Block, bên dưới có ba hierarchical sheet thật để mở Power, USB_GPIO và ESP32_P4.
- Root dùng A3; tổng bộ schematic trở lại bốn trang.
- Đã kiểm tra: 89 mục linh kiện và 119 net giữ nguyên; ba file mạch chi tiết giữ nguyên ở lần chỉnh đó. ERC vẫn 75 mục cũ, không phải ERC sạch.

## 08. Chỉnh chữ và bản xem trước trắng–đen

- **Đã thực hiện:** chữ Arial, tăng nhẹ cỡ chữ, in đậm tiêu đề trong Root.
- Bản xem trước được xuất nền trắng, nét đen.
- Đã giải thích màu trong trình soạn thảo phụ thuộc theme KiCad; mục đổi màu chú thích là `Schematic text & graphics`.
- Chưa có thao tác tự cài hay đổi theme toàn cục của KiCad từ phía trợ lý.

## 09. Đối chiếu nội dung đã vẽ với reference

- **Đã phân tích:** cả ba reference đều có P4 và tụ nguồn, buck HP, thạch anh, Flash, RESET/BOOT và USB nạp tương tự về chức năng.
- Đã chỉ vị trí nguồn chung và USB ở trang cuối của các tài liệu, nguồn HP nằm cạnh P4.
- Đã phân biệt “cùng chức năng” với “cùng mạch và giá trị linh kiện”.

## 10. So sánh nhận/chọn nguồn của kit với hãng

- **Đã phân tích:** hãng ghép các nhánh nguồn qua Ideal Diode; kit hiện dùng chuỗi chống ngược cực, eFuse và TPS2116 chọn nguồn ưu tiên.
- Đã giải thích Ideal Diode, chọn nguồn ưu tiên và chống ngược cực là những nhiệm vụ khác nhau.
- **Chưa chốt:** bỏ, giữ hay thay các khối Q1 và eFuse. Chưa sửa chúng trong schematic.

## 11. Xác định ranh giới nguồn ở tầng hệ thống

- **Đã thống nhất hướng:** bên ngoài cung cấp nguồn DC phù hợp; kit tạo các rail nội bộ và hỗ trợ các ngoại vi tiêu chuẩn.
- Đã phân biệt nguồn nuôi board với tầng công suất điều khiển tải ngoài.
- Đã kiểm tra cách hãng cấp nguồn: màn hình lớn có thể nhận nguồn riêng mà vẫn thuộc bộ kit được hỗ trợ.

## 12. Chốt mục tiêu một nguồn cấp cho cấu hình tiêu chuẩn

- **Đã chốt:** đầu vào mục tiêu là DC 5 V ổn áp; nguồn này nuôi board chính và các phần mở rộng tiêu chuẩn, kể cả camera/màn hình cắm rời.
- Vẫn giữ hướng nhận nguồn từ USB hoặc nguồn ngoài, ưu tiên nguồn ngoài.
- **Chưa chốt:** dung sai đầu vào, dòng adapter tối thiểu, cấu hình được phép chạy chỉ bằng USB máy tính.
- Đã giải thích thêm vai trò adapter, tổn hao khi có nhiều tầng bảo vệ và vì sao chưa thể bỏ bảo vệ chỉ vì dùng adapter.

## 13. Chọn kiểu nhận nguồn ngoài

- **Tạm chốt:** adapter USB-C, cổng USB-C nguồn riêng trên kit, bên cạnh cổng USB-C nạp/debug.
- Đầu vào mục tiêu vẫn 5 V; hiện chưa yêu cầu USB PD để lấy điện áp cao hơn.
- **Chưa thực hiện:** thay J1 hai chân trong schematic bằng cổng USB-C nguồn.
- **Chưa chốt:** mức dòng USB-C cần hỗ trợ, mạch nhận biết khả năng cấp dòng, adapter/cáp cụ thể và bảo vệ tương ứng.

## 14. Xác định yêu cầu và khảo sát màn hình

- **Đã chọn nhu cầu:** nhỏ gọn, xem ảnh camera và kết quả AI.
- Đã đề xuất khoảng 2,8–3,5 inch, chữ nhật, ưu tiên MIPI-DSI; cảm ứng chưa phải yêu cầu bắt buộc.
- **Ứng viên:** Waveshare 2.8inch DSI LCD, 480 × 640. Đã tìm thấy pinout cấp 3V3 và tài liệu BSP liệt kê hỗ trợ P4 revision 3.x.
- **Chưa chốt module:** chưa có thông số dòng hoạt động tối đa/dòng khởi động đủ rõ; chưa khóa đầu nối, cáp, mạch nguồn hay cấu hình phần mềm.
- Chưa dùng ứng viên này để kết luận buck 3V3 đủ tải.

## 15. Khảo sát camera và giải thích cách chia module/adapter

- **Đã khảo sát:** camera 2 MP và camera adapter theo bộ tham khảo Espressif; SC2336 là hướng cảm biến đã được xem xét, chưa chốt bộ module mua thực tế.
- Đã phân biệt ống kính, cảm biến, module camera và adapter trung gian.
- Đã giải thích adapter tham khảo nhận 3V3, tạo nguồn con, clock và ghép đầu nối/tín hiệu cho camera.
- **Tạm chốt:** camera cắm rời qua camera adapter rời; PCB chính đưa ra nguồn và giao tiếp cần thiết.
- **Chưa chốt:** module + adapter + cáp cụ thể, pinout, dòng tiêu thụ và mã đầu nối.

## 16. Chọn hướng kết nối không dây

- Đã giải thích khác nhau giữa module và chip C6 riêng; đối chiếu cả ba reference đều dùng module không dây.
- **Tạm chốt:** ESP32-C6-MINI-1 hàn trên PCB chính, anten PCB tích hợp, nguồn 3V3, nối SDIO với P4 và có đường nạp C6 riêng.
- Phạm vi là Wi-Fi 2,4 GHz + Bluetooth LE, không phải Bluetooth Classic.
- **Chưa chốt:** mã biến thể đặt mua, GPIO, chi tiết nạp/reset, ngân sách dòng và bố trí anten.

## 17. Chọn hướng microSD

- Đã phân biệt khe thẻ tích hợp với module đọc thẻ rời.
- **Đã chốt:** khe microSD và mạch hỗ trợ nằm trên PCB chính; thẻ tháo lắp được, kit cấp 3V3.
- Giao tiếp SDMMC 4-bit, tách với đường SDIO của C6; ưu tiên khe có Card Detect.
- **Chưa chốt:** mã khe, GPIO/slot, loại thẻ hỗ trợ, dòng dự phòng và linh kiện hỗ trợ cụ thể.

## 18. Chọn hướng đo điện năng

- Đã học nguyên lý shunt, đo vi sai, đánh đổi sụt áp/nhiệt/độ chính xác và phân chia tính toán giữa IC với P4.
- **Đã chốt:** INA226 + shunt tích hợp trên PCB chính, đo trên đường 5 V sau khối chọn nguồn và trước các nhánh tải.
- INA226 đo điện áp, tính dòng/công suất; P4 cấu hình hiệu chuẩn, đọc qua I²C, tích lũy điện năng và xử lý kết quả.
- Phạm vi đo là phần tải phía sau shunt, không bao gồm adapter hay khối nhận/chọn nguồn phía trước.
- **Chưa chốt:** shunt, dải đo, tốc độ lấy mẫu, hiệu chuẩn và cách cấp nguồn IC đo chi tiết. Chưa có mục tiêu định lượng về đo dòng ngủ hoặc xung ngắn.

## 19. Phác lại cây nguồn sau khi bàn các phần mở rộng

- **Đã thực hiện ở mức thảo luận:** ghép đường chọn nguồn → shunt/INA226 → các rail → P4 và ngoại vi.
- Đã xác định phần lớn nhánh dự kiến dùng 3V3; camera adapter tiếp tục tạo nguồn con.
- Đã phân biệt một đầu vào 5 V chung với việc có thể cần nhiều nhánh ổn áp bên trong.
- **Chưa lập xong ngân sách nguồn:** chưa chốt tổng dòng/công suất, chưa kết luận adapter, buck 3V3, bộ chọn nguồn hoặc eFuse hiện tại đủ cho cấu hình mở rộng.

## Lưu ý về file thiết kế hiện hành

- Những quyết định mới về USB-C nguồn, camera adapter, C6-MINI-1, microSD và INA226 chủ yếu mới được chốt trong trao đổi; **chưa được triển khai thành schematic mở rộng**.
- Block diagram/Root đã được cập nhật ở các mốc trước nhưng **chưa đồng bộ đầy đủ các quyết định chi tiết phát sinh sau đó**.
- File này là danh sách theo dõi mới theo trình tự thực tế; [Design Tracker cũ](ESP32-P4_Design_Tracker.md) chưa được đồng bộ trong lượt tạo danh sách này.
