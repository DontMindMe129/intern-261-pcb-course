# ESP32-P4 DevKit - Design Tracker

**Cập nhật: 26/11/2026**

File làm việc đi kèm [Hardware Design Reference](ESP32-P4_Hardware_Design.md). Các yêu cầu chưa chốt, câu hỏi, phép tính và việc biên soạn được theo dõi tại đây; phần giải thích kỹ thuật đã biên soạn được đưa vào tài liệu chính.

## 1. Phạm vi hiện tại

- [Bản KiCad chính](hardware/esp32_p4_devkit/esp32_p4_devkit.kicad_pro) có schematic và layout PCB 4 lớp, 100 × 70 mm.
- Thiết kế đã có nguồn và nạp, Wi-Fi/BLE, Ethernet, microSD, camera, màn hình, audio, cảm biến ánh sáng và GPIO.
- BOM và sơ đồ khối được đối chiếu với bản chính. Nguồn mua và hồ sơ gia công còn các mục mở bên dưới.
- Chưa ghi nhận kết quả chế tạo, lắp ráp hoặc thử nghiệm trên board thực.

## 2. Quy ước theo dõi

| Trạng thái | Căn cứ |
|---|---|
| Dự kiến | Định hướng hoặc phương án chưa chốt |
| Đang thiết kế | Đang phân tích/lựa chọn |
| Đã vẽ | Có trong schematic |
| Đã kiểm tra schematic | Có phạm vi, bản lưu và kết quả review tương ứng |
| Đã kiểm chứng phần cứng | Có điều kiện thử và số đo thực |
| Chưa thực hiện | Chưa triển khai |

Không dùng “đã vẽ” như kết luận rằng thông số đã được bảo đảm hoặc người thiết kế đã hiểu mọi nguyên lý.

## 3. Yêu cầu thiết kế

| ID | Yêu cầu | Hiện trạng | Phần cần làm rõ |
|---|---|---|---|
| REQ-01 | ESP32-P4 revision 1.3 | Đã chọn trong KiCad | Xác nhận silicon thực tế khi mua U4 |
| REQ-02 | Nguồn ngoài DC 5 V ổn áp | Đã vẽ | Dung sai, dòng và đầu nối |
| REQ-03 | Nguồn USB cho cấu hình tải phù hợp | Đã vẽ | Điều kiện cấp nguồn và ngân sách dòng USB |
| REQ-04 | Ưu tiên nguồn ngoài | Đã vẽ | Các trạng thái chuyển nguồn và mức sụt áp cho phép |
| REQ-05 | Bảo vệ nhánh nguồn ngoài | Đã vẽ | Phạm vi sự cố, phân tích biên và tiêu chí thử |
| REQ-06 | Nạp/debug USB Serial/JTAG | Đã vẽ | Nhận thiết bị/nạp/debug trên board thật |
| REQ-07 | RESET và download bằng tay | Đã vẽ | Timing, strapping và kiểm chứng thao tác |
| REQ-08 | Không dây/camera/màn hình/header | Đã vẽ | Module tương thích và tải nguồn |
| REQ-09 | Ethernet, audio, microSD và cảm biến ánh sáng | Đã vẽ | Firmware và thử từng giao tiếp |

## 4. Các vấn đề mở

| ID | Nội dung | Bằng chứng để đóng mục |
|---|---|---|
| OPEN-01 | Ngân sách nguồn toàn kit | Tải, rail, chế độ, dòng trung bình/đỉnh, hiệu suất và điều kiện nguồn vào |
| OPEN-02 | Căn cứ chọn linh kiện/giá trị | Phân tích nguyên lý, datasheet đúng biến thể và phép tính có giả định |
| OPEN-03 | Nguồn mua BOM | Xác nhận MK1, revision U4 và nguồn đủ linh kiện cho lô lắp |
| OPEN-04 | Ngưỡng PR1, giới hạn dòng và chuyển nguồn của TPS2121 | Biên sai số, dòng rò, sụt áp, trạng thái lỗi/phục hồi và giới hạn hệ thống |
| OPEN-05 | Thạch anh và tụ tải | Mã thạch anh, CL, sai số/ESR/drive level, giả định ký sinh và kế hoạch đo |
| OPEN-06 | Review ERC/DRC bản chính và hồ sơ gia công | Bản thiết kế xác định và cách xử lý từng loại cảnh báo |
| OPEN-07 | Phần mở rộng | Phạm vi chức năng, module, giao diện, điện áp và ngân sách nguồn |

## 5. Ngân sách nguồn

| Tải | Rail | Dòng liên tục | Dòng đỉnh/thời gian | Căn cứ |
|---|---|---|---|---|
| P4 và bộ nhớ | Chưa tổng hợp | Chưa xác định | Chưa xác định | Datasheet và chế độ hoạt động |
| Không dây | 3V3 | Chưa xác định | Chưa xác định | U1 ESP32-C6-MINI-1-N4 |
| Camera | 3V3 qua J4 | Chưa xác định | Chưa xác định | Module cần chọn |
| Màn hình/đèn nền | 3V3 qua J6 | Chưa xác định | Chưa xác định | Module cần chọn |
| Tải mở rộng | 3V3/5V_SYS qua J7 | Chưa xác định | Chưa xác định | Phạm vi cần xác định |

## 6. Sổ tính toán

Mỗi phép tính cần có: đầu vào và nguồn số liệu; công thức; kết quả danh định; biên sai số; điều kiện áp dụng; tác động tới yêu cầu hệ thống.

| Phép tính | Chỗ đặt trong tài liệu chính | Nội dung cần bổ sung |
|---|---|---|
| PR1 và chuyển nguồn | Thông số nguồn và cơ sở thiết kế | Sai số IC/điện trở, dòng rò và sụt áp |
| Giới hạn dòng | U7 TPS2121 | Danh định, giới hạn sai số và tải liên tục cho phép |
| Tổn hao/nhiệt | Các khối công suất | Điều kiện điện, nhiệt và cấu trúc đồng dự kiến |
| Tụ nguồn | Cơ sở thiết kế | C hiệu dụng, đáp ứng tải, ESR/ESL, yêu cầu ổn định |
| RESET | Khởi động | RC và timing yêu cầu |
| Tụ tải thạch anh | Clock | CL và giả định ký sinh |

## 7. Bản đồ nội dung biên soạn

Với mỗi khối, hoàn thiện: mục đích/yêu cầu, hình mạch, nguyên lý, lựa chọn/tính toán và điểm cần kiểm chứng. Trong tài liệu chính, viết thành mô tả kỹ thuật; câu hỏi học tập giữ ở file ghi chú.

| Khối | Phần cần học/rà tiếp |
|---|---|
| Tụ nguồn | Trở kháng theo tần số, ESR/ESL, phối hợp nhiều tụ và kiểm tra giá trị |
| J3/J5/J11 | Điều kiện cấp nguồn cùng vai trò từng cổng USB-C |
| D2/D3/D5 | TVS VBUS và đối chiếu mã mua SMAJ6.0A với footprint SMA |
| U7 | ILM, UVLO/OVLO, hysteresis, dV/dT, lỗi/phục hồi |
| U2/J9 | RMII, MDI, clock và các chân cấu hình khi reset |
| U11 | Buck 3V3, bootstrap, LC và hồi tiếp |
| U5/P4 | Điều khiển nguồn lõi và các miền nguồn/LDO |
| RESET/BOOT | RC, timing và strapping |
| Thạch anh | Mạch dao động và tụ tải |
| Flash | QSPI, CS#, nguồn và mã đặt mua |
| USB | CC, VBUS, D+/D−, ESD và điều kiện nguồn USB |

## 8. Bằng chứng review

Các lần kiểm tra ngày 27/10/2026 là các review kết nối theo bản lưu trong phiên làm việc. Không coi chúng là báo cáo cho mọi bản thiết kế về sau.

Ở mỗi mốc chính thức, lưu: ngày, bản thiết kế/commit hoặc snapshot, phiên bản KiCad, lệnh/cấu hình kiểm tra, báo cáo, kết luận và vấn đề còn lại.

Các cảnh báo đã biết cần rà:

- Chân còn chờ triển khai, không đánh dấu No Connect chỉ để làm sạch ERC.
- Nhiều nhãn khác tên trên cùng net.
- VDD_ANA/VDD_BAT được thư viện đang dùng khai báo Bidirectional.
- Thuộc tính linh kiện, thư viện và footprint cần hoàn thiện.

## 9. Việc còn lại

### PCB

Review bản layout chính và phân loại kết quả DRC trước khi xuất Gerber và file khoan mới.

### Chế tạo và lắp ráp

Revision board, Gerber/drill, yêu cầu đặt PCB, BOM, bản vẽ lắp ráp, pick-and-place nếu cần và kiểm tra tính nhất quán file xuất.

### Bring-up và thử nghiệm

Kiểm tra sau lắp ráp; nguồn/rail/tải/nhiệt; các trạng thái chọn nguồn; điều kiện bảo vệ; RESET/BOOT; USB/Flash; các phần mở rộng.

Mỗi phép thử cần thiết bị, sơ đồ đo, điều kiện vào/tải, trình tự, giới hạn chấp nhận, số đo/log và kết luận. Không đưa giá trị kỳ vọng vào cột kết quả đo.

### Firmware thử

Phiên bản SDK/công cụ, cấu hình chip/bộ nhớ/LDO, mã thử, log và điều kiện tái lập.

## 10. Tài liệu làm việc

- [Ghi chú học schematic/PCB](ESP32-P4_Ghi_chu_schematic_PCB.md).
- [Ghi chú block diagram](ESP32-P4_Ghi_chu_thiet_ke.md).
- [Project KiCad](hardware/esp32_p4_devkit/esp32_p4_devkit.kicad_pro).
- [Block diagram](block_diagram/esp32-p4_tong-hop.drawio), trang Bản hiện hành.
- [BOM](BOM.xlsx), gồm danh sách lắp và DNP.

## 11. Cách cập nhật tài liệu

1. Học và rà một khối theo schematic hiện có.
2. Ghi câu hỏi/giả định tại ghi chú và tracker.
3. Biên soạn phần giải thích kỹ thuật cùng bảng/hình/tài liệu đối chiếu vào bản chính.
4. Cập nhật trạng thái theo bằng chứng; giữ riêng kiểm tra schematic và kiểm chứng phần cứng.
5. Cập nhật lịch sử tài liệu khi có thay đổi có ý nghĩa. Revision tài liệu không tự đổi revision PCB hoặc silicon.
