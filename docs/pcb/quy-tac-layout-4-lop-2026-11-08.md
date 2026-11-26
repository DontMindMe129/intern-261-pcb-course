# Đề xuất quy tắc layout PCB 4 lớp

Ngày: 08/11/2026. Phạm vi hiện tại: nguồn, đo dòng, USB nạp và BOOT/RESET của ESP32-P4 rev 1.3.

Đây là bộ quy tắc đề xuất để thống nhất trước khi đặt lại linh kiện. Chưa sửa Board Setup, schematic hoặc placement. Bản chính sau reset đã được kiểm tra: 78 footprint, 0 track/via, 0 zone.

## 1. Cơ sở tham khảo

- PCB ESP32-P4X Function EV Board V1.6 trong `references/pcb/ESP32-P4X-Function-EV-Board/`. Đã đối chiếu PDF và Gerber: L1_TOP, L2_GND, L3_POWER, L4_BOTTOM. Dùng để học cách phân vai các lớp, phân vùng chức năng và phân phối nguồn; không sao chép netlist, thông số IC nguồn hoặc phiên bản silicon.
- Hướng dẫn layout ESP32-P4 của Espressif xác nhận cấu trúc TOP / GND / POWER / BOTTOM và yêu cầu lớp tham chiếu liên tục cho USB.
- AP63300, TPS2121 và INA226: lấy layout của chính IC làm cơ sở cho vòng dòng điện, tản nhiệt, tụ bypass và Kelvin sense.
- Năng lực PCB cứng JLCPCB là mốc kiểm tra khả năng gia công hiện tại, chưa phải quyết định đặt hàng ở hãng này.

## 2. Phân vai lớp đồng

| Lớp KiCad | Vai trò đề xuất | Quy tắc |
|---|---|---|
| F.Cu | Linh kiện, kết nối cục bộ, USB và tín hiệu cần tham chiếu tốt | Ưu tiên hoàn thành các vòng buck trên mặt này; giữ đường USB trên một lớp nếu có thể. |
| In1.Cu | GND chính | Giữ liên tục; không chạy nguồn/tín hiệu để né dây. Chỉ chừa khoảng hở hoặc keepout có lý do cụ thể. |
| In2.Cu | Phân phối các rail nguồn bằng vùng đồng/đường rộng, GND ở vùng phù hợp | Tách riêng từng net nguồn. Không chia lớp thành các đảo tùy tiện. Dưới khối buck ưu tiên đồng GND và via tản nhiệt theo datasheet AP63300. |
| B.Cu | Tín hiệu phụ/chậm, GND bổ sung | Không mặc định đây là lớp cấp nguồn chính. Trước khi dùng cho tín hiệu nhanh phải xác định lớp tham chiếu và đường hồi. |

Mục tiêu cơ khí: FR-4, 4 lớp, dày danh nghĩa 1,6 mm. Nếu giữ mục tiêu buck 3 A liên tục, ưu tiên đồng ngoài 2 oz theo khuyến nghị AP63300. Độ dày đồng trong, khoảng cách điện môi và cấu trúc ép lớp phải chốt từ stackup thực tế của nhà gia công; không coi giá trị mặc định trong KiCad là thông số đã đặt hàng.

## 3. Quy tắc hình học ban đầu

Các số dưới đây là lựa chọn thiết kế của project, có đối chiếu khả năng gia công; không phải tất cả được chép từ board tham khảo.

| Mục | Đề xuất | Phạm vi |
|---|---|---|
| Track tín hiệu thông thường | 0,20 mm | I2C, CC, BOOT/RESET, LED, điều khiển nguồn và nhánh đo ngắn. |
| Minimum track width | 0,15 mm | Giới hạn kỹ thuật cho thoát chân nhỏ; ưu tiên 0,20 mm khi đủ chỗ. |
| Clearance mặc định | 0,20 mm | Khoảng hở mép đồng khác net. |
| Clearance cục bộ vùng chân nhỏ | 0,15 mm | Áp dụng có phạm vi rõ ràng cho footprint/fanout cần nó. Footprint U4 hiện có pitch 0,35 mm và bề rộng pad theo hướng pitch 0,20 mm, nên khoảng hở pad liền kề là 0,15 mm. Không sửa nhỏ pad chỉ để vượt DRC 0,20 mm. |
| Via thông dụng | Đường kính 0,60 mm / lỗ 0,30 mm | Through via cho tín hiệu và nối GND; không tự hiểu là đủ cho mọi dòng nguồn. |
| Via nhỏ hơn | Chỉ dùng khi fanout thực sự cần | Chọn theo nhà gia công; không dùng microvia/via mù/chôn trong phương án cơ bản. |
| Đồng cách mép phay | 0,50 mm | Ngoại lệ tại đầu nối chỉ theo bản vẽ cơ khí và yêu cầu nhà gia công. |
| Khoảng hở lỗ khoan với lỗ khoan | Tối thiểu 0,25 mm mép lỗ | Đối chiếu lại riêng via, PTH và NPTH với năng lực nhà gia công. |
| Chữ reference ưu tiên | Cao khoảng 1,0 mm, nét 0,15 mm | Không đè pad, giữ dễ đọc; vị trí chữ chỉnh sau placement. |
| Lưới đặt thân linh kiện | 0,50 mm, giảm 0,25 mm cho nhóm nhỏ | Linh kiện vòng nguồn/decoupling được ngoại lệ theo nhu cầu điện. Không ép tâm mọi pad lên lưới placement. |

Đặt giới hạn kỹ thuật 0,15 mm không làm mọi net tự động được đi cách nhau 0,15 mm: cần giữ netclass mặc định 0,20 mm và tạo ngoại lệ cục bộ phù hợp.

## 4. Bố trí trước, đi dây sau

1. Chốt hướng mép board, vị trí cổng USB và các ràng buộc cơ khí. Dự kiến vị trí U4 dù chưa đi các ngoại vi khác.
2. Chia các khối: đầu vào J3/J5 với TVS, CC và ESD; U7 với các tụ; R24 với INA226; buck U11 với tụ/cuộn cảm; nhánh nạp/BOOT/RESET.
3. Đường công suất đi theo chuỗi: J3/J5 -> U7 -> R24 -> U11 -> 3V3. Nhánh dữ liệu J5 -> U3 -> USB Serial/JTAG của P4 có hành lang riêng, tránh vùng SW.
4. Xoay tụ để pad nguồn và GND hướng thuận với chân IC và đường hồi. Đặt tụ bypass gần chân cấp nguồn/GND; không chỉ gần tâm thân IC.
5. Giữ C53-U11, U11-C6-L3 và tụ đầu ra thành cụm gọn theo layout AP63300. Giữ mạch FB xa SW; đường từ cầu chia đến FB ngắn. Hai đường INA226 lấy trực tiếp từ pad shunt, không lấy tại đoạn đồng mang dòng tải ở xa.
6. Căn hàng các linh kiện phụ và giữ khoảng cách đều khi không làm xấu kết nối điện. Không bắt buộc mọi linh kiện phải chung một hàng.
7. Duyệt placement toàn khối, kiểm tra hướng đi của nguồn, GND và tín hiệu trước khi bắt đầu routing.

## 5. Quy tắc đường nguồn và via

- Đường nguồn chính ưu tiên vùng đồng rộng, kết nối trực tiếp và ít chuyển lớp; các đoạn thoát pad ngắn có thể hẹp hơn vùng đồng chính.
- Không gán bề rộng theo điện áp đơn thuần. Phải xét dòng tải/dòng xung, chiều dài, độ dày đồng, nhiệt độ và sụt áp cho phép.
- Chưa chốt 0,5 mm hoặc 1 mm là đủ cho mục tiêu 3 A. Sau placement phải đánh giá toàn tuyến: J3/J5, U7, shunt, buck, GND và các đoạn chuyển lớp.
- Via nguồn dùng một hay nhiều lỗ theo dòng, độ dày mạ, chiều dài lỗ, tản nhiệt và mặt bằng. Không mặc định hai via là đủ; các via ở đoạn nối tiếp cũng phải được kiểm tra.
- Via GND đặt gần điểm cần đường hồi và nối với plane In1.Cu; các vòng công suất cần đồng GND và via phù hợp, không gom dòng tải qua nhánh GND nhỏ của mạch đo.
- Có thể nhập nhánh cùng net vào track hoặc vùng đồng có sẵn nếu đường hồi và dòng trên đoạn dùng chung vẫn hợp lý. Kelvin sense và các nhánh nhạy nhiễu phải được xem riêng.
- Không dùng via chỉ vì mỗi pad cần một via. Via tín hiệu dùng khi việc chuyển lớp có ích; via GND/tản nhiệt còn có vai trò khác.
- Via đặt ngoài pad SMD trong phương án gia công cơ bản; via-in-pad cần được xem xét riêng về công nghệ lấp/mạ và lắp ráp.
- `Remove redundant tracks` chỉ tắt khi cần giữ các kết nối song song có chủ đích. Không mặc định tạo cầu vượt nhiều via thay cho việc xem lại tuyến đi và placement.

## 6. USB và những thông số cần chốt trước routing

- Mục tiêu USB: differential impedance 90 ohm ±10%, hai đường đi cùng nhau trên lớp có mặt phẳng tham chiếu liên tục.
- Ưu tiên USB trên F.Cu tham chiếu In1.Cu. Hạn chế via và nhánh cụt; nếu chuyển lớp cần xem lại đường hồi GND.
- Không dùng mặc định width 0,20 mm/gap 0,25 mm trong project để khẳng định đạt 90 ohm. Width/gap phải tính theo stackup, đồng, solder mask và dữ liệu nhà gia công.
- Trước khi đi USB: chọn stackup thực tế; chốt bề rộng/khoảng cách cặp; kiểm tra vị trí cổng-ESD-điện trở-P4.
- Trước khi chốt phần nguồn: xác định tải liên tục/đỉnh, đồng ngoài/trong, mức sụt áp và tăng nhiệt cho phép; đánh giá cả đường đồng lẫn via.

## 7. Nguồn đối chiếu

- [Espressif: ESP32-P4 PCB Layout Design](https://docs.espressif.com/projects/esp-hardware-design-guidelines/en/latest/esp32p4/pcb-layout-design-esp32p4.html)
- [Diodes: AP63300/AP63301, mục PCB Layout trang 19](https://www.diodes.com/datasheet/download/AP63300_AP63301.pdf)
- [TI: TPS2121, mục 11-12 trang 33](https://www.ti.com/lit/ds/symlink/tps2121.pdf)
- [TI: INA226, mục 8.4 trang 31](https://www.ti.com/lit/ds/symlink/ina226.pdf)
- [JLCPCB: Rigid PCB Capabilities](https://jlcpcb.com/capabilities/pcb-capabilities/)

Giới hạn của lần rà soát: đã kiểm tra số lớp Gerber, xem PDF TOP/GND/POWER và đọc hướng dẫn hãng; chưa trích xuất bộ constraint gốc từ Allegro, chưa xác nhận thông số stackup trong đơn hàng, chưa tính/tối ưu hoàn chỉnh nhiệt và trở kháng của board này.
