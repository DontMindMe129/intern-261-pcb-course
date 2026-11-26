# Kiến thức PCB: chọn kích thước và hình dạng đường đồng

Ghi chú học và thực hành cho project `task_pcb`, ngày 08/11/2026. Ví dụ tập trung vào mạch nguồn và mạch USB nạp của bo ESP32-P4 rev 1.3.

## 1. Track và via là gì?

- **Track** là đường đồng nối các pad trên một lớp PCB. **Track width** là bề rộng đường đồng.
- **Via** là lỗ có thành mạ đồng để nối điện giữa các lớp. **Diameter** là đường kính đệm đồng bên ngoài; **Hole/Drill** là đường kính lỗ khoan.
- Với via diameter 0,60 mm và hole 0,30 mm, vành đồng danh nghĩa mỗi bên là `(0,60 - 0,30)/2 = 0,15 mm`, trước khi xét dung sai gia công.

Các **Pre-defined Sizes** vừa khai báo trong KiCad là danh sách để chọn nhanh khi đi dây:

| Mục | Kích thước đã khai báo |
|---|---|
| Track widths | 0,20; 0,30; 0,50; 1,00 mm |
| Via | Diameter 0,60 mm; Hole 0,30 mm |

Danh sách này chưa gán kích thước phù hợp cho từng net. Khả năng tải dòng của đường đồng và via vẫn phải được đánh giá theo thiết kế và thông số sản xuất.

## 2. Chọn bề rộng đường đồng dựa vào đâu?

Trước hết xác định đường đang xét mang nguồn, dòng tải, xung chuyển mạch, tín hiệu đo hay tín hiệu tốc độ cao.

| Yếu tố | Ý nghĩa khi chọn đường |
|---|---|
| Dòng liên tục và dòng xung | Xét phát nóng, sụt áp và ứng xử khi có xung. Dòng RMS liên quan đến tổn hao nhiệt; dòng đỉnh và tốc độ biến thiên còn ảnh hưởng đến nhiễu và điện áp ký sinh. |
| Chiều dài | Đường dài hơn có điện trở và điện cảm ký sinh lớn hơn. |
| Bề rộng và độ dày đồng | Tăng tiết diện đồng giúp giảm điện trở. Cùng một bề rộng nhưng độ dày đồng khác nhau sẽ có đặc tính khác nhau. |
| Sụt áp và tăng nhiệt cho phép | Phải xác định mức chấp nhận được của mạch, thay vì chỉ hỏi đường có “chịu được dòng” hay không. |
| Lớp đi dây và môi trường tản nhiệt | Đường lớp ngoài, lớp trong, vùng đổ đồng và đồng lân cận có điều kiện tản nhiệt khác nhau. |
| Pad, đoạn thắt hẹp và via | Cần xem toàn bộ đường dòng điện; một đoạn rộng không bù được mọi đoạn thắt hoặc via thiếu khả năng tải dòng. |
| Năng lực sản xuất | Bề rộng, khoảng hở, lỗ khoan và vành đồng phải phù hợp với nhà sản xuất PCB. |

Để hình dung điện trở DC của một đoạn đồng có tiết diện đều:

```text
R ≈ ρ × chiều dài / (bề rộng × độ dày đồng)
Sụt áp:       Vdrop = I × R
Tổn hao nhiệt: P = I² × R
```

Đây là mô hình đơn giản để hiểu xu hướng. Nó chưa thay thế phép đánh giá tăng nhiệt thực tế. Với đường mang dòng xung, phép tính tổn hao phải xét dạng dòng điện và dòng RMS phù hợp.

## 3. Chọn hình dạng đường đi dựa vào đâu?

### Độ dài và vòng dòng điện

Ưu tiên đường trực tiếp, ít vòng vèo và ít chuyển lớp không cần thiết. Đồng thời phải xác định **dòng điện quay về bằng đường nào**. Đường đi và đường hồi tạo thành một vòng; vòng lớn có thể gây nhiễu hoặc nhạy với nhiễu.

Với dòng thay đổi nhanh, điện cảm ký sinh có thể tạo điện áp:

```text
V_ký_sinh = L_ký_sinh × di/dt
```

Ở công thức này, `L_ký_sinh` là điện cảm, không phải chiều dài đường đồng. Vì vậy tần số chuyển mạch danh nghĩa chưa mô tả đầy đủ vấn đề: cạnh xung nhanh cũng rất quan trọng.

### Góc gấp và via

Đoạn thẳng và góc 45° thường giúp đi dây gọn, dễ quan sát và tránh góc nhọn. Tuy nhiên, góc 45° tự nó không bảo đảm chất lượng điện. Một đường vẫn có thể kém nếu quá dài, vòng hồi lớn hoặc đi sát vùng gây nhiễu.

Via giúp chuyển lớp nhưng cũng thêm điện trở, điện cảm và thay đổi đường hồi. Với tín hiệu tốc độ cao, cần xem cả lớp tham chiếu và sự liên tục của đường hồi khi chuyển lớp.

### Vùng gây nhiễu và vùng nhạy

- Giữ các đường đo điện áp nhỏ và hồi tiếp tránh vùng SW, cuộn cảm và đường dòng xung.
- Giữ mặt phẳng tham chiếu liên tục dưới các đường tín hiệu cần nó; việc chia khối chức năng không tự động có nghĩa là phải cắt mặt phẳng GND.
- Vùng SW cần đủ tiết diện cho dòng nhưng vẫn gọn. Mở rộng đồng SW quá mức làm tăng diện tích nút có điện áp biến thiên nhanh và khả năng ghép nhiễu.

## 4. Áp dụng vào mạch đang làm

### Các chân GND có thể nối tùy ý không?

Các pad mang net `GND` thuộc cùng mạng kết nối trong schematic. Trên PCB, chúng vẫn phải được nối bằng đồng thực tế: track, vùng đổ đồng hoặc mặt phẳng GND và via. Nhãn GND tự nó không tạo ra kết nối vật lý.

Đường đồng GND có điện trở và điện cảm. Khi dòng đi qua, các điểm GND có thể xuất hiện chênh lệch điện áp do sụt áp điện trở và tác động của dòng biến thiên nhanh. Nếu dòng công suất dùng chung một đoạn đồng hẹp với mạch đo hoặc hồi tiếp, điện áp ký sinh trên đoạn đó có thể trở thành sai số hoặc nhiễu của mạch nhạy.

Ví dụ ở U11: đường giữa **C53 pad 2 và U11 pad 4** cần ngắn để giữ vòng dòng đầu vào nhỏ. Không nên dẫn vòng dòng xung này qua đường GND nhỏ của R2 rồi mới quay về tụ. R2 vẫn cùng net GND nhưng cần một đường hồi ít bị dòng công suất làm nhiễu.

Với bo nhiều lớp, ta dự kiến dùng mặt phẳng GND rộng, liên tục và via gần các chân cần nối GND, kết hợp với đường hồi cục bộ của các vòng dòng quan trọng. Tên lớp `In1.Cu` chưa tự làm nó thành GND: vùng đồng trên lớp phải được gán net GND và nối đúng. Không tự động tách GND thành nhiều đảo hoặc gom tất cả về một pad duy nhất; cách nối cần phù hợp với đường hồi thực tế và hướng dẫn của IC.

DRC có thể xác nhận cùng net và các khoảng hở đã khai báo, nhưng không tự chứng minh đường hồi GND ít nhiễu hoặc đủ trở kháng thấp.

### Các nhóm kết nối cụ thể

| Đường/nhóm | Ưu tiên thiết kế |
|---|---|
| Nguồn 5V/3V3 | Đánh giá dòng, sụt áp, nhiệt, đoạn thắt và via. Có thể dùng vùng đổ đồng để tạo đường cấp nguồn có trở kháng thấp. |
| U11 SW → L3 | Kết nối ngắn, đủ tiết diện cho dòng cuộn cảm; giữ vùng SW gọn và tránh linh kiện hồi tiếp. |
| C53 với VIN/GND U11 | Xem cả vòng dòng xung đầu vào. Tụ và các kết nối VIN/GND cần gần IC. |
| BST → C6 → SW | Đây là vòng bootstrap của mạch điều khiển MOSFET phía cao. Cả hai đường nối C6 đều cần ngắn và gọn. |
| R25/R2/C5 với FB | Lấy mẫu từ đầu ra 3V3, giữ đường hồi tiếp tránh vùng SW và tránh chia sẻ đoạn đồng mang dòng tải. |
| R24 với INA226 | Hai đường đo IN+/IN− lấy trực tiếp từ hai đầu shunt, tách khỏi đoạn đường mang dòng tải: nguyên tắc Kelvin. |
| USB D+/D− | Chọn bề rộng, khoảng cách hai đường và lớp tham chiếu dựa trên trở kháng yêu cầu và stackup đã chốt. |

**Về đường BST đang bắt đầu vẽ:** mức 0,20 mm là lựa chọn khởi đầu cho đoạn nối bootstrap ngắn, thuận tiện thoát pad. Đây là vòng điều khiển riêng, không mang dòng tải chính của đầu ra. Kích thước này chưa được xác nhận là tối ưu bằng tính toán hoặc đo kiểm. Cần đánh giá cùng đường C6 → SW và hướng dẫn layout của AP63300.

## 5. Trước mỗi đường mới, tự hỏi gì?

1. Đường này mang loại dòng/tín hiệu gì?
2. Dòng đi qua những pad nào và quay về qua đâu?
3. Nó cần ưu tiên tải dòng, giảm vòng dòng, tránh nhiễu hay kiểm soát trở kháng?
4. Đường đi có khiến mình cần chỉnh lại vị trí linh kiện không?
5. Bề rộng, khoảng hở và via đã phù hợp với yêu cầu điện và khả năng gia công chưa?

**DRC kiểm tra các quy tắc đã khai báo.** Hết lỗi DRC chưa chứng minh nguồn ổn định, đủ khả năng tải dòng, đạt nhiệt độ cho phép hoặc đạt EMI. Bố trí và routing hiện tại vẫn là thiết kế đang phát triển.

## Nguồn tham khảo

- [Diodes Incorporated — AP63300/AP63301 datasheet](https://www.diodes.com/datasheet/download/AP63300_AP63301.pdf): phần Bootstrap Capacitor và PCB Layout, Figure 39.
- [Texas Instruments — INA226 datasheet](https://www.ti.com/lit/ds/symlink/ina226.pdf): phần Layout Guidelines, kết nối Kelvin và tụ bypass.
- [Analog Devices — AN-139: Power Supply Layout and EMI](https://www.analog.com/en/resources/app-notes/an-139.html): vòng dòng, đường hồi và điện cảm ký sinh.
- [Analog Devices — Staying Well Grounded](https://www.analog.com/en/resources/analog-dialogue/articles/staying-well-grounded.html): trở kháng GND, dòng hồi và nhiễu do dùng chung đường GND.
- [Analog Devices — AN-1142: Techniques for High Speed ADC PCB Layout](https://www.analog.com/en/resources/app-notes/an-1142.html): ảnh hưởng của việc chia cắt mặt phẳng GND đến đường hồi.
- [Analog Devices — Basic Guidelines for Mixed-Signal PCB Layout](https://www.analog.com/en/resources/analog-dialogue/articles/what-are-the-basic-guidelines-for-layout-design-of-mixed-signal-pcbs.html): tổ chức khối, phân phối nguồn và đường hồi.
- [KiCad 10 — PCB Editor manual](https://docs.kicad.org/10.0/en/pcbnew/pcbnew.pdf): Pre-defined Sizes, net classes và routing.
