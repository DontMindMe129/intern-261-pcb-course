# ESP32-P4 — Ghi chú học lại schematic và chuẩn bị cho PCB

Ngày bắt đầu: 27/10/2026.

Cập nhật phần học nguyên lý: 28/10/2026 — GND/vòng dòng điện, Q1, eFuse U7, chọn nguồn U2, buck U3, USB và bản đồ nguồn P4/U5/U6.

## 1. Mục đích và phạm vi hiện tại

Tài liệu này dành cho giai đoạn schematic, bổ sung cho file `ESP32-P4_Ghi_chu_thiet_ke.md` vốn tập trung vào block diagram và chức năng tổng quát.

**Tình trạng học tập:** đã vẽ nhiều mạch theo hướng dẫn và đã kiểm tra kết nối, nhưng chưa thể coi là đã hiểu nguyên lý điện của từng khối. Từ đây sẽ học lại từng phần trên chính schematic đang có.

**Phạm vi đang dừng:** schematic của khối nguồn, khởi động và nạp/debug cho ESP32-P4 revision 3.x. Chưa triển khai layout PCB. Không tự thêm linh kiện hoặc sửa mạch chỉ để tiếp tục tiến độ; trước tiên cần hiểu mạch hiện có.

Ba mức xác nhận cần phân biệt:

1. **Đã vẽ:** các symbol, giá trị và dây đã có trên schematic.
2. **Đã kiểm tra schematic:** netlist/ERC và đối chiếu tài liệu cho thấy các kết nối được kiểm tra phù hợp với phương án đã chọn.
3. **Đã kiểm chứng phần cứng:** đã đo trên board thật. Hiện chưa đạt mức này.

## 2. Schematic đã làm những gì?

Project nằm tại `hardware/esp32_p4_devkit/`, gồm bốn sheet:

| Sheet | Nội dung |
|---|---|
| Root — `esp32_p4_devkit.kicad_sch` | Kết nối giữa các sheet |
| `Power.kicad_sch` | Nhận nguồn, bảo vệ nguồn ngoài, chọn nguồn và tạo nguồn 3,3 V |
| `USB_GPIO.kicad_sch` | USB-C và phần hỗ trợ đầu nối USB đã vẽ |
| `ESP32_P4.kicad_sch` | P4, nguồn lõi và các miền nguồn, RESET/BOOT, thạch anh, Flash, USB tới chip |

### 2.1. Các khối đã có

| Khối | Linh kiện/chức năng hiện có | Mức hiểu nguyên lý |
|---|---|---|
| Đầu vào nguồn ngoài | J1 hai chân: +5 V và GND | Đã trao đổi đầu nối, mốc điện áp, đường hồi dòng và vòng kín; xem mục 6 |
| Chống ngược cực | Q1 AO3401A, R21 và Zener D1 | Đã trao đổi D/G/S, diode thân, VGS, đấu ngược và kẹp Gate; xem mục 8 |
| eFuse nguồn ngoài | U7 TPS259630DDA; R22–R26; C46/C47 | Đã trao đổi chức năng, loại chân, UVLO/OVLO và cách chọn tỷ lệ chia áp; xem mục 9. Giới hạn dòng/dVdt còn cần học sâu |
| Chọn nguồn | U2 TPS2116; nguồn ngoài ưu tiên, USB dự phòng | Đã trao đổi MODE/PR1, ngưỡng cầu chia và tình huống chọn nguồn; xem mục 10 |
| Nguồn chính 3,3 V | U3 AP63203WU, L1, tụ và bootstrap | Đã trao đổi hai pha buck, vị trí FB và ảnh hưởng giá trị L; xem mục 11. Bootstrap/vòng điều khiển còn cần học sâu |
| Nguồn lõi P4 | U5 TLV62569DBV, L2, hồi tiếp và điều khiển từ P4 | Đã phân biệt đường công suất với EN_DCDC/FB_DCDC; xem mục 13. Cơ chế phối hợp còn cần học sâu |
| Các miền nguồn P4 | Nguồn LP, analog, GPIO, Flash, PSRAM, USB PHY và MIPI PHY | Đã tổng hợp đầu vào 3V3, đầu ra LDO và các nhánh cấp trở lại P4; xem mục 13 |
| Tụ nguồn | Tụ đầu vào, đầu ra và tụ gần các chân nguồn | Cần học vai trò theo tần số và vị trí đặt |
| RESET | R7 10 kΩ, C42 1 µF, SW1 | Cần học quá trình nạp/xả tụ và mức CHIP_PU |
| BOOT | R8/R9 10 kΩ và SW2 tại GPIO35/36 | Cần học mức kéo, chân ở trạng thái trở kháng cao và thời điểm đọc strapping |
| Clock | Y1 40 MHz, R12/R13 0 Ω, C43/C44 16 pF ban đầu | Cần học mạch dao động và điện dung tải |
| Flash | U6 W25Q128JVS, C45, R14–R19 và R20 kéo CS# | Đã nhắc lại vai trò lưu trữ và nguồn VDDO_FLASH; xem mục 13. Kết nối/timing QSPI cần học tiếp |
| USB nạp/debug | J2, CC1/CC2, ESD U1, R10/R11 và GPIO24/25 | Đã trao đổi nhóm chân, điện trở CC và ESD; xem mục 12. Chưa học sâu giao thức/tín hiệu USB |
| Khai báo ERC | PWR_FLAG, loại chân, No Connect | Cần phân biệt ký hiệu kiểm tra với linh kiện vật lý |

Tên linh kiện trong bảng là cấu hình schematic hiện có, không đồng nghĩa đã chốt toàn bộ mã đặt mua và khả năng cấp tải của kit.

### 2.2. Đường nguồn tổng quát

```text
Nguồn ngoài 5 V
      |
      J1
      |
  5V_EXT_RAW
      |
  Q1 chống ngược cực
      |
  5V_EXT_REV
      |
  U7 eFuse
      |
    5V_EXT ----------------> VIN1 của U2 TPS2116
                                    |
USB-C VBUS ----------------> VIN2   | chọn nguồn
                                    |
                                  5V_SYS
                                    |
                                U3: buck
                                    |
                                   3V3
                                    |
                    +---------------+-----------------+
                    |                                 |
            Các đầu vào nguồn                  U5: buck nguồn lõi
             3,3 V phù hợp                            |
                    |                               VDD_HP
              LDO nội bộ P4                            |
                    |                      VDD_HP_0/1/2/3 của P4
          Flash / PSRAM / MIPI
```

Hình này giản lược các nhánh nguồn. GND là đường tham chiếu và đường hồi dòng chung, được lược bỏ trong hình cho dễ đọc.

Các tên `5V_EXT_REV`, `5V_EXT`, `5V_SYS` mô tả vai trò và mức điện áp dự kiến. **Đặt tên net không tạo ra hoặc đảm bảo điện áp đó:** đường thực tế có thể có sụt áp qua linh kiện và dây dẫn.

### 2.3. Các giá trị bảo vệ đang dùng

| Tham số | Cấu hình hiện tại | Cách hiểu |
|---|---|---|
| Giới hạn dòng U7 | R22 = 453 Ω | Khoảng 2 A danh định trên nhánh 5 V ngoài |
| Ngưỡng bật U7 | R23 = 249 kΩ, R24 = 100 kΩ | Khoảng 4,19 V danh định tại `5V_EXT_REV` |
| Ngưỡng ngắt thấp áp U7 | Cùng cầu chia R23/R24 | Khoảng 3,84 V danh định |
| Ngưỡng ngắt quá áp U7 | R25 = 34,8 kΩ, R26 = 10 kΩ; yêu cầu 0,1% | Khoảng 5,38 V danh định |
| Mức gỡ điều kiện quá áp | Cùng cầu chia R25/R26 | Khoảng 4,93 V danh định; không đồng nghĩa mọi trạng thái lỗi đều tự phục hồi |
| Chọn nguồn ưu tiên U2 | R3 = 27 kΩ, R4 = 10 kΩ | Ngưỡng PR1 quy về `5V_EXT` khoảng 3,7 V danh định |

Đây là các giá trị thiết kế ban đầu có sai số. Chưa kiểm chứng ngưỡng thực, đáp ứng sự cố, nhiệt hoặc tính liên tục khi chuyển nguồn trên board thật. Giới hạn 2 A không phải cam kết rằng toàn kit luôn dùng được 2 A từ mọi nguồn.

## 3. Điều gì còn chưa chốt?

- Ngân sách dòng của toàn kit và khả năng cấp tải khi chỉ dùng USB; nhánh USB hiện chưa có eFuse tương tự nhánh J1.
- Mã đặt mua đầy đủ của các linh kiện, sai số, điện áp chịu đựng, công suất, dòng chịu nhiệt và dòng bão hòa cuộn cảm.
- Mã P4 cụ thể và một số thuộc tính linh kiện/footprint. Symbol đang dùng là ESP32-P4X; thông tin BOM cần hoàn thiện riêng.
- Giá trị tụ tải thạch anh: 16 pF được tính ban đầu với CL = 10 pF và giả định điện dung ký sinh tổng 2 pF; chưa có số đo PCB.
- Dọn các cảnh báo ERC phù hợp với phạm vi đã hoàn thành. Không đánh dấu No Connect cho các chân còn dự kiến sử dụng chỉ để làm sạch báo cáo.
- Symbol Espressif hiện khai báo VDD_ANA và VDD_BAT là Bidirectional, gây cảnh báo khi nối với net có Power output; cần xử lý khai báo symbol khi hoàn thiện thư viện project.
- Wi-Fi/Bluetooth, đầu nối camera, đầu nối màn hình và các header mở rộng chưa được triển khai đầy đủ trong schematic.

## 4. Schematic, PCB và mạch thật khác nhau thế nào?

| Giai đoạn | Kết quả tạo ra | Chưa chứng minh được gì? |
|---|---|---|
| Schematic | Linh kiện dự kiến, giá trị, tên net, chân nào nối với chân nào | Chưa đảm bảo đường điện vật lý đủ tốt hoặc mạch chắc chắn chạy |
| PCB layout | Footprint, vị trí linh kiện, đường đồng, via, vùng đồng và cấu trúc lớp | Chưa phải board đã được sản xuất/lắp ráp hoặc thử nghiệm |
| Chế tạo PCB | Board trần có lớp đồng, pad và lỗ | Chưa có đầy đủ linh kiện hoạt động |
| Lắp ráp | Linh kiện được hàn lên PCB | Chưa xác nhận không có lỗi hàn hay lỗi thiết kế |
| Bring-up và kiểm thử | Đo nguồn, kiểm tra khởi động, USB, nạp Flash và chạy chương trình | Cần thử theo phạm vi hoạt động yêu cầu, không chỉ một lần bật thành công |

Ví dụ: trên schematic, một tụ 100 nF đặt xa hình chip vẫn có thể nối đúng net. Trên PCB, đường nối xa có điện cảm và điện trở; tụ có thể lọc kém tại chân nguồn. Vì vậy bố trí linh kiện và đường hồi dòng là một phần của thiết kế điện.

ERC kiểm tra các quy tắc điện được khai báo trong schematic. DRC kiểm tra các quy tắc hình học/kết nối trên PCB. Hai công cụ đều không thay thế phân tích mạch, đối chiếu datasheet và đo kiểm.

## 5. Cách học lại từng khối

Với mỗi khối, lần lượt trả lời:

1. Khối giải quyết vấn đề gì trong kit này?
2. Mỗi chân và mỗi net mang ý nghĩa gì?
3. Điện áp được đo giữa hai điểm nào? Dòng điện đi theo đường kín nào?
4. Khi vừa cấp nguồn, khi hoạt động bình thường và khi có lỗi, chuyện gì xảy ra?
5. Từng linh kiện làm gì? Nếu bỏ hoặc đổi nó thì điều gì thay đổi?
6. Giá trị linh kiện được chọn từ công thức hoặc điều kiện datasheet nào?
7. Điều gì phải chú ý ở PCB và điều gì phải đo trên board thật?

Chỉ ghi “đã hiểu” sau khi có thể tự giải thích mạch, không chỉ vì đã đặt symbol đúng.

Người học chọn bắt đầu với tụ nguồn và cơ chế điện phía sau trước khi quay lại từng khối. Thứ tự dưới đây là lộ trình tham khảo, có thể điều chỉnh theo chỗ cần hiểu:

1. J1, nguồn áp, GND, mạch kín; pin, dây và net trên schematic.
2. MOSFET Q1: D/G/S, diode thân, VGS và chống ngược cực.
3. R21 và Zener D1: cách kéo mức Gate và giới hạn điện áp.
4. U7: đường công suất, giới hạn dòng, UVLO/OVLO, hysteresis và tụ dV/dT.
5. U2: ưu tiên nguồn, ngắt/nối đường công suất, chống dòng ngược và sụt áp khi chuyển nguồn.
6. Buck U3: đóng cắt, cuộn cảm, tụ đầu ra, bootstrap và hồi tiếp.
7. Buck U5 và cách P4 phối hợp điều khiển nguồn lõi.
8. Các miền nguồn, LDO và tụ decoupling của P4.
9. RESET và strapping/BOOT.
10. Thạch anh và tụ tải.
11. Flash, điện trở 0 Ω, kéo CS# và đường QSPI.
12. USB-C, CC, D+/D− và ESD.
13. ERC, netlist, PWR_FLAG và giới hạn của việc kiểm tra schematic.

## 6. Bài đầu tiên — J1, nguồn 5 V và GND

### 6.1. J1 biểu diễn điều gì?

J1 là đầu nối hai cực của nguồn ngoài. Nó không phải bộ chuyển đổi điện áp, và hai chân của nó không phải một chân nhận 5 V rồi chuyển 5 V sang chân kia.

```text
Adapter                         Đầu nối trên board        Mạch PCB
Cực dương +5 V ---- dây -------- J1.1 -------------------- nhánh cấp nguồn
Cực âm / return --- dây -------- J1.2 -------------------- GND
```

Mỗi chân trên symbol tương ứng với một tiếp điểm điện của đầu nối thật. Phần tiếp xúc với dây/đầu cắm ngoài và phần chân hàn vào PCB là cùng một đường dẫn điện. Schematic thường không vẽ riêng adapter và đầu cắm ghép vào J1.

### 6.2. “5 V” được đo ở đâu?

Điện áp là chênh lệch điện thế giữa hai điểm. Với đầu vào này:

`V(J1.1) − V(J1.2) = khoảng 5 V` khi adapter được đấu đúng cực và làm việc đúng định mức.

Ta chọn J1.2/GND làm mốc 0 V, nên gọi J1.1 là +5 V. Khi đo bằng đồng hồ, que COM đặt tại J1.2/GND và que đỏ đặt tại J1.1.

GND không mặc nhiên là đất bảo vệ của ổ điện hoặc một điểm hấp thụ hết dòng điện. Trong mạch này, nó nối với cực âm của adapter và tham gia tạo đường hồi dòng.

### 6.3. Dòng điện cần đường đi và đường về

Ví dụ minh họa riêng, thay toàn bộ board bằng một điện trở tải 1 kΩ:

```text
Adapter (+) → J1.1 → điện trở tải 1 kΩ → J1.2/GND → Adapter (−)
```

Với nguồn 5 V và bỏ qua điện trở dây/đầu nối:

`I = V/R = 5 V / 1000 Ω = 5 mA`.

Mũi tên biểu diễn dòng điện quy ước. Đây là ví dụ học nguyên lý, không phải mô hình điện trở cố định của ESP32-P4.

Trong điều kiện DC thông thường, nếu mất đường đi hoặc đường về thì không còn dòng tải duy trì qua vòng đó. Điện áp tại một số điểm vẫn có thể tồn tại dù tải chưa có dòng.

### 6.4. Adapter ghi “5 V, 2 A” có nghĩa gì?

Adapter đặt điện áp đầu ra khoảng 5 V trong phạm vi hoạt động của nó; tải quyết định dòng tiêu thụ theo đặc tính và trạng thái của tải. “2 A” là khả năng cấp dòng định mức của nguồn, không có nghĩa adapter luôn ép 2 A vào board.

Nếu tải yêu cầu vượt khả năng cấp dòng, điện áp và cách bảo vệ phản ứng phụ thuộc loại adapter. Không suy ra tất cả adapter đều xử lý quá tải giống nhau.

### 6.5. Những khái niệm cần nắm trước khi sang Q1

- J1.1 và J1.2 là hai cực khác nhau của đầu vào nguồn.
- Điện áp đo giữa hai điểm; GND là mốc được chọn trong mạch.
- Dòng tải có đường đi và đường về.
- Một net có nhiều điểm nối; tên net không tạo ra năng lượng hoặc quy định chiều dòng.
- Loại chân Passive/Power input trong KiCad là thông tin cho công cụ kiểm tra, không phải van điện vật lý.

Trạng thái: đã trao đổi các khái niệm cơ bản; tiếp tục đối chiếu với từng đường dòng trong mạch. Không dùng trạng thái này thay cho kết quả kiểm chứng phần cứng.

### 6.6. Vòng kín và vai trò của GND

Ví dụ nguồn DC và một điện trở tải:

```text
                Dòng điện quy ước →
       (+) ─────────[ Tải ]────────┐
        │                         │
     Nguồn DC                     │
        │                         │
       (−) ───────────────────────┘
                     ← Dòng trở về
```

Vòng kín là đường dẫn liên tục qua nguồn và tải. Nguồn duy trì chênh lệch điện áp nhờ năng lượng bên trong nó. Tải chuyển năng lượng điện sang nhiệt, ánh sáng hoặc dạng khác; không tiêu hủy điện tích. Ở trạng thái ổn định, điện tích không liên tục tích tụ tại tải.

Nếu ngắt một đoạn trên đường đi hoặc đường về, vòng qua tải bị hở và không còn dòng tải DC duy trì theo đường đó. Nguồn vẫn có thể có điện áp giữa hai cực. Dòng quá độ qua điện dung là vấn đề khác với dòng DC liên tục.

Trong ví dụ, chọn cực âm và dây hồi dòng làm mốc 0 V, gọi là GND. Các ký hiệu GND rời nhau trên schematic biểu diễn cùng mạng điện; trên PCB phải thực hiện bằng đường/vùng đồng. GND không phải nơi hút dòng và không tự động là đất bảo vệ (Earth).

GND chung chưa đủ tạo thành vòng kín: vẫn cần một đường dẫn khác nối tải về cực còn lại của nguồn. Vì vậy ngắt tại Q1 có thể chặn vòng dòng qua nhánh nguồn ngoài dù GND vẫn nối chung.

### 6.7. Đổi mốc đo không làm thay đổi trạng thái linh kiện

Khi đấu ngược adapter, có thể chọn một trong hai mốc sau:

| Điểm | Lấy GND board làm 0 V | Lấy chân 1 J1 làm 0 V |
|---|---:|---:|
| Chân 1 J1 / Drain Q1 | −5 V | 0 V |
| Chân 2 J1 / GND board | 0 V | +5 V |
| Gate qua R21 về GND board | ≈ 0 V | ≈ +5 V |
| Source lúc đầu, với C46 chưa nạp | ≈ 0 V | ≈ +5 V |

Giả định của hàng Source là điện áp ban đầu trên C46 bằng 0: `V_S − V_GND = 0`. Nếu GND board được biểu diễn là +5 V thì Source cũng khoảng +5 V theo mốc đó. **Hai điểm cùng +5 V vẫn có chênh lệch bằng 0 V.**

“Chưa có điện áp” phải nói rõ giữa hai điểm nào. Không suy ra một nút luôn bằng 0 V theo mọi mốc, cũng không tự áp dụng giả định tụ chưa nạp khi đầu ra còn tích điện hoặc được cấp từ một nguồn khác.

Theo cả hai cách chọn mốc, `V_GS ≈ 0 V` và điện áp Anode–Cathode của diode thân khoảng −5 V: kết luận Q1 tắt, diode thân bị phân cực ngược không thay đổi.

## 7. Tụ nguồn — nguyên lý cấp dòng xung và khoảng cách trên PCB

### 7.1. Tụ nguồn nối như thế nào?

Tụ decoupling/bypass mắc song song giữa VCC và GND của IC:

```text
Nguồn 3V3 ----+-------- VCC của IC
             |
             C
             |
GND ---------+-------- GND của IC
```

Dòng cấp cho IC không đi xuyên qua tụ trước khi tới IC. Tụ là một nhánh riêng nối giữa hai đường nguồn, hỗ trợ giữ điện áp tại IC khi tải biến đổi nhanh.

GND là đường hồi dòng trong vòng mạch, không phải nơi dòng điện biến mất.

### 7.2. Vì sao linh kiện thụ động có thể cấp dòng?

Tụ đã được nạp có điện tích trên các bản và năng lượng tích trữ trong điện trường. Với tụ lý tưởng, độ lớn điện tích trên mỗi bản thỏa mãn:

`Q = C × V`.

Khi có đường dẫn kín qua tải, điện trường tác dụng lực lên các hạt mang điện trong đường dẫn, làm chúng dịch chuyển. Đó là dòng điện. Khi tụ xả, độ lớn điện tích trên các bản giảm, đồng thời điện áp tụ giảm.

Ví dụ dễ hình dung: nạp tụ tới 3,3 V, tháo nguồn rồi nối tụ với một công tắc và điện trở thành vòng kín. Công tắc mở thì chưa có dòng xả qua điện trở. Công tắc đóng thì có đường dẫn; tụ xả qua tải theo các quan hệ điện của mạch. Tụ không cần nhận biết trạng thái công tắc.

Trong IC, transistor chuyển trạng thái làm thay đổi các đường dẫn hoặc bắt đầu nạp các điện dung bên trong. Câu “IC cần thêm dòng” là cách mô tả sự thay đổi tải này. **Tụ không biết nhu cầu của IC; dòng xả là hệ quả của điện trường, đường dẫn và trạng thái điện của mạch.**

Với điện áp được lấy từ bản phía VCC so với bản phía GND, quan hệ dòng nạp là `i_C = C × dV_C/dt`. Khi tụ cấp dòng ra tải, điện áp tụ giảm. Nếu điện áp trên tụ lý tưởng hoàn toàn không thay đổi theo thời gian thì dòng qua tụ bằng 0.

Ở mô hình tụ lý tưởng, không có dòng dẫn DC xuyên qua chất điện môi; vẫn có dòng tại các chân trong quá trình nạp/xả.

### 7.3. Khi IC chuyển sang lấy dòng lớn hơn

Ví dụ minh họa: IC đang lấy 20 mA rồi chuyển sang khoảng 100 mA. Các con số chỉ để học nguyên lý, không phải số đo P4.

| Giai đoạn | Không lắp tụ decoupling cục bộ | Có tụ decoupling gần IC |
|---|---|---|
| Trước biến đổi tải | Nguồn cấp dòng tải qua đường cấp | Nguồn cấp tải; tụ đã được nạp tới điện áp gần VCC |
| Tải bắt đầu lấy thêm dòng | Điện trở, điện cảm đường cấp và đáp ứng hữu hạn của bộ nguồn gây sụt VCC tại IC | Tụ tại chỗ xả điện tích, tham gia cấp phần dòng nguồn chưa bù kịp |
| Trong quá trình đáp ứng | VCC có thể sụt đáng kể nếu đường nguồn không đáp ứng đủ | Điện áp tụ cũng là VCC tại chỗ giảm; chọn tụ và đường nối phù hợp giúp giảm mức sụt |
| Sau quá độ | Dòng và áp tiến về trạng thái xác lập tương ứng tải mới | Nguồn tiếp tục cấp tải và có thể cấp thêm dòng để bổ sung điện tích cho tụ |

Tại nút VCC, theo định luật dòng Kirchhoff:

`I_IC = I_nguồn + I_tụ_cấp`.

Chẳng hạn, nếu IC lấy 100 mA trong khi dòng từ đường nguồn mới là 20 mA thì tụ đang bù 80 mA. Các biến đổi dòng và áp diễn ra liên tục, liên hệ với nhau; tụ không đợi một ngưỡng rồi mới “bật”.

Tụ và chân VCC của IC nối cùng net nên không được tưởng tượng là hai nguồn điện áp độc lập, một bên giữ nguyên 3,3 V trong khi bên kia tự tụt sâu. Năng lượng từ tụ cấp qua vòng: bản phía VCC → IC → GND → bản phía GND. Đường nối thực vẫn có điện trở và điện cảm.

Tụ không khắc phục được sụt áp DC liên tục hoặc một nguồn thiếu công suất lâu dài. Khi điện áp tụ đã ổn định, tụ lý tưởng không tiếp tục cấp dòng DC cho tải.

### 7.4. Ước tính điện dung để hạn chế sụt áp

Trong mô hình lý tưởng, nếu phần dòng tụ phải bù gần như không đổi trong khoảng xét:

`C >= ΔI × Δt / ΔV`.

- `ΔI`: phần dòng thiếu tụ phải cấp, không mặc định là toàn bộ dòng IC.
- `Δt`: khoảng thời gian cần bù, liên quan đáp ứng hệ thống nguồn; không tự lấy bằng một chu kỳ CPU.
- `ΔV`: độ sụt điện áp cho phép, không phải điện áp nguồn danh định.

Ví dụ: cần bù 100 mA trong 1 µs, cho phép VCC giảm từ 3,3 V xuống 3,25 V:

`C >= 0,1 A × 1 µs / 0,05 V = 2 µF`.

Đây là ước tính lý tưởng. Tụ thực còn có ESR, ESL, sai số và thay đổi điện dung theo điện áp/nhiệt độ. Phải xét điện dung hiệu dụng và các thành phần sụt áp khác.

Với IC phức tạp, bắt đầu bằng yêu cầu datasheet và mạch tham khảo, sau đó phân tích/đo để điều chỉnh. Tụ đầu ra bộ ổn áp còn liên quan tới vòng điều khiển, không chỉ nhu cầu dự trữ điện tích.

### 7.5. “Nguồn ở xa” là xa về vật lý

“Xa” ở đây là khoảng cách vật lý trên PCB, chính xác hơn là **chiều dài, hình dạng và cấu trúc của cả đường cấp lẫn đường hồi dòng**.

```text
Bộ ổn áp ----- đường đồng có điện trở, điện cảm ----- IC
                                                     |
                                                Tụ đặt gần IC
```

- Điện trở gây sụt áp khi có dòng: `V_R = I × R`.
- Điện cảm gây điện áp khi dòng biến đổi: `V_L = L × di/dt`; nó cản dòng thay đổi nhanh.
- Đường dài, vòng dòng lớn và cấu trúc đi dây không phù hợp có thể làm trở kháng cấp nguồn tăng. Không chỉ xét khoảng cách thẳng trên mặt board.
- Tụ gần chân VCC/GND tạo vòng cấp dòng tức thời ngắn và có điện cảm thấp hơn.
- Tụ nằm sát hình IC trên schematic **chưa có nghĩa** tụ thật sẽ nằm sát IC trên PCB. Trên schematic, khoảng cách chủ yếu phục vụ trình bày; sang layout mới xác định vị trí và đường đồng thực.
- Không có một khoảng cách tối đa duy nhất áp dụng cho mọi tụ/IC. Cần đối chiếu hướng dẫn và đánh giá vòng dòng thực tế.

Tụ lớn hơn không tự động thay thế được tụ gần từng chân nguồn: điện dung, ESR/ESL, kích thước package, vị trí và đường nối cùng quyết định hiệu quả.

### 7.6. Liên hệ với schematic đang có

- Các tụ 100 nF gần chân nguồn P4 hoặc Flash: tụ decoupling cục bộ.
- Các tụ vài µF đến hàng chục µF: hỗ trợ dự trữ điện tích và đáp ứng nguồn; vai trò cụ thể tùy vị trí.
- Tụ đầu ra buck: phối hợp với cuộn cảm và bộ điều khiển để tạo nguồn ổn định.
- Không phải mọi tụ nối GND đều là tụ nguồn: tụ RESET, tụ tại dV/dT và tụ tải thạch anh cần được học riêng.

Trạng thái: đã trao đổi cơ chế tụ cấp dòng theo hiện tượng vật lý và ý nghĩa khoảng cách trên PCB; tiếp tục học cách chọn thông số thực tế. Chưa coi các giá trị hiện tại là đã được đo tối ưu.

## 8. Khối 01 — EXTERNAL INPUT / REVERSE POLARITY

### 8.1. Mục đích và các linh kiện

Khối này nhận nguồn ngoài qua J1 và chặn dòng cấp ngược cực theo nhánh nguồn ngoài đang xét:

```text
J1.1 → 5V_EXT_RAW → Drain Q1 → Source Q1 → 5V_EXT_REV → eFuse U7
J1.2 → GND board
```

| Reference | Linh kiện | Vai trò |
|---|---|---|
| J1 | Đầu nối hai chân | Nhận hai cực nguồn ngoài |
| Q1 | AO3401A, P-channel MOSFET | Phần tử đóng/ngắt đường công suất |
| R21 | 10 kΩ | Nối Gate về GND; tham gia đường dòng khi D1 kẹp |
| D1 | BZT52C8V2, Zener danh định 8,2 V | Giới hạn điện áp Source–Gate |

Q1 là transistor MOSFET rời; U7 mới là IC tích hợp điều khiển bảo vệ. PWR_FLAG chỉ là ký hiệu khai báo nguồn cho ERC, không phải linh kiện vật lý trên board.

### 8.2. Điện áp điều khiển Q1

- Drain (D), chân 3: phía đầu vào `5V_EXT_RAW`.
- Source (S), chân 2: phía đầu ra `5V_EXT_REV`.
- Gate (G), chân 1: nối R21 về GND và Anode D1.

`V_GS = V_G − V_S`.

Với P-channel MOSFET, Gate thấp hơn Source đủ mức sẽ mở kênh dẫn. Gate và Source gần bằng điện thế thì kênh tắt. Chỉ biết Gate là 0 V chưa đủ kết luận; phải biết Source đang ở đâu.

Khi mở, kênh có điện trở RDS(on), không phải dây lý tưởng. Mức VGS(th) chỉ mô tả bắt đầu dẫn theo điều kiện thử, không thay thế mức VGS dùng để bảo đảm RDS(on) cho dòng tải.

### 8.3. Cấp đúng cực — diode thân giúp khởi động

Xét đầu ra ban đầu chưa được cấp nguồn, lấy GND board = 0 V:

1. Drain nhận +5 V; Gate được R21 nối về gần 0 V.
2. Diode thân có sẵn trong Q1, hướng Anode ở Drain sang Cathode ở Source, dẫn lúc đầu và làm Source tăng.
3. Source tăng trong khi Gate gần 0 V làm VGS âm; Q1 mở kênh dẫn.
4. Khi ổn định, nguồn đi qua kênh điện trở nhỏ; Source tiến gần 5 V, tùy dòng tải và sụt áp.

Khi đó `V_GS ≈ 0 − 5 = −5 V`. Diode thân không phải diode Zener D1 bên ngoài. Không coi đầu ra luôn bị trừ một mức sụt diode cố định sau khi kênh MOSFET đã mở.

### 8.4. Đấu ngược — chặn vòng dòng điện

Xét nguồn ngoài cách ly, đầu ra ban đầu chưa tích điện và chưa có đường khác đi vòng qua Q1:

- Cực dương adapter nối J1.2/GND board; cực âm nối J1.1.
- Theo mốc GND board, Drain ≈ −5 V, Gate và Source ban đầu ≈ 0 V.
- VGS ≈ 0 V nên kênh tắt; diode thân bị phân cực ngược.

Vòng dòng dự định qua tải bị chặn tại Q1:

```text
Adapter (+) → J1.2 / GND → mạch phía sau → Q1 tắt ─✕─ J1.1 → Adapter (−)
```

Ngắt một nhánh là đủ ngăn dòng DC liên tục qua vòng đó. Việc GND nối chung không tự tạo một vòng kín thứ hai.

Phải xét lại nếu có liên kết bên ngoài: chẳng hạn cực âm adapter đã nối điện với GND máy tính, trong khi USB cũng nối GND máy tính vào board. Đấu ngược có thể tạo đường `adapter (+) → GND board → GND USB → adapter (−)` không đi qua Q1. Không suy rộng mạch một MOSFET thành cách ly điện hoàn toàn hoặc chặn mọi đường dòng ngược trong mọi trạng thái.

### 8.5. Zener D1 bảo vệ gì?

Zener có Anode (A) và Cathode (K): dẫn thuận như diode thông thường; ở chiều ngược, gần như không dẫn khi chưa tới vùng Zener và dẫn đáng kể khi tới vùng đó. Dòng/công suất vẫn phải được giới hạn phù hợp.

Trong mạch này, Cathode D1 nối Source, Anode nối Gate. Nó hạn chế `V_S − V_G`, tức độ lớn VGS âm. Nó không ổn áp đường nguồn đầu ra thành 8,2 V.

Với nguồn 5 V bình thường, `V_S − V_G ≈ 5 V`, D1 chưa dẫn đánh thủng Zener. Gate gần 0 V vì dòng DC vào Gate rất nhỏ sau khi ổn định, nên R21 gần như không có sụt áp.

Ví dụ sự cố cụ thể: cắm nhầm adapter 15 V đúng cực. Không có D1, Source có thể tiến gần 15 V trong khi Gate gần 0 V, làm VGS gần −15 V, vượt giới hạn tuyệt đối ±12 V của AO3401A.

Có D1, khi Source–Gate tăng tới vùng Zener, xuất hiện đường dòng:

```text
Source → D1 dẫn ngược → Gate → R21 → GND
```

Dòng qua R21 nâng Gate lên. Dùng mức kẹp lý tưởng 8,2 V để minh họa: nếu Source = 15 V thì Gate ≈ 6,8 V, VGS ≈ −8,2 V và dòng qua R21 ≈ 0,68 mA. **Gate không còn giữ ở 0 V sau khi Zener dẫn.**

Đây là ví dụ nguyên lý, không phải mức kẹp đã đo: điện áp Zener phụ thuộc dòng, sai số và nhiệt độ; cần kiểm tra đặc tính tại dòng làm việc thực. D1 bảo vệ Gate của Q1; ngắt nguồn quá áp cho tải thuộc U7.

Trạng thái: đã trao đổi nguyên lý. Chưa kiểm chứng đấu ngược, xung điện áp, dòng tải hoặc nhiệt trên board.

## 9. Khối 02 — eFuse / INPUT PROTECTION, IC U7

### 9.1. IC và đường công suất

U7 hiện dùng **TPS259630DDA**. Đây là eFuse có MOSFET công suất và các mạch giám sát/điều khiển bên trong. Nó hoạt động bằng phần cứng, không cần P4 chạy firmware trước.

```text
5V_EXT_REV → IN U7 → MOSFET công suất bên trong → OUT U7 → 5V_EXT
```

Ngoài đóng/ngắt, IC có thể điều chỉnh mức dẫn để hạn dòng hoặc điều khiển quá trình bật. Khi dẫn bình thường, VOUT gần VIN trừ sụt áp; không phải bộ ổn áp tạo 5 V từ mọi điện áp vào.

### 9.2. Các chân cần hiểu

| Chân U7 | Loại/chức năng | Cấu hình hiện tại |
|---|---|---|
| IN, chân 4 | Đầu vào công suất | `5V_EXT_REV` |
| OUT, chân 5 | Đầu ra công suất | `5V_EXT` |
| GND, chân 1 và pad nhiệt | Mốc điện và đường liên quan tản nhiệt | GND |
| EN/UVLO, chân 3 | Đầu vào điện áp cho phép bật/giám sát thấp áp | Điểm giữa R23/R24 |
| OVLO, chân 8 | Đầu vào điện áp giám sát quá áp của biến thể 630 | Điểm giữa R25/R26 |
| ILM, chân 7 | Cấu hình giới hạn dòng và cung cấp tín hiệu theo dòng tải | R22 = 453 Ω xuống GND |
| dV/dT, chân 2 | Cấu hình tốc độ tăng điện áp khi bật | C47 = 10 nF xuống GND |
| FLT, chân 6 | Đầu ra báo lỗi active-low, open-drain | Hiện không sử dụng |

EN/UVLO và OVLO nhận điện áp để so sánh với ngưỡng nội bộ. ILM và dV/dT được cấu hình bằng linh kiện thụ động, không phải các đầu vào để tùy ý ép điện áp. FLT là đầu ra, không phải chân ra lệnh bật/tắt.

### 9.3. Điện áp tại chân khác với ngưỡng quy về nguồn vào

Các mức sau là **điển hình tại chân IC so với GND**, không phải các ngưỡng cố định chính xác tuyệt đối:

| Chân | Điện áp chân tăng | Điện áp chân giảm |
|---|---|---|
| EN/UVLO | Vượt khoảng 1,2 V: cho phép bật | Dưới khoảng 1,1 V: ngắt thấp áp |
| OVLO | Vượt khoảng 1,2 V: ngắt quá áp | Dưới khoảng 1,1 V: gỡ điều kiện quá áp |

IC chỉ dẫn khi các điều kiện cần thiết khác cũng cho phép; không suy ra chỉ một chân đạt ngưỡng là chắc chắn bật.

Khoảng giữa ngưỡng tăng và giảm tạo hysteresis: trạng thái phụ thuộc lịch sử, giúp tránh bật/tắt liên tục gần ngưỡng. Không được dùng một phép so sánh duy nhất tại 1,2 V để mô tả đầy đủ cả hai chiều biến đổi.

### 9.4. Thứ tự thiết kế cầu chia — cái gì phụ thuộc vào cái gì?

1. **Hãng quyết định** ngưỡng nội bộ tại chân IC, ví dụ EN bật khoảng 1,2 V và ngắt khoảng 1,1 V.
2. **Yêu cầu hệ thống quyết định** muốn nguồn ngoài tăng tới bao nhiêu thì cho phép bật, hoặc cao tới đâu thì phải ngắt.
3. **Người thiết kế tính tỷ lệ điện trở** để ánh xạ điện áp nguồn mong muốn về ngưỡng IC.
4. Chọn giá trị chuẩn rồi tính lại ngưỡng danh định và biên sai số.

```text
VIN cần giám sát
      │
     R_trên
      │
      ├────► chân giám sát của IC
      │
     R_dưới
      │
     GND
```

Bỏ qua dòng rò chân khi tính danh định:

`V_chân = VIN × R_dưới / (R_trên + R_dưới)`.

`VIN_tại_ngưỡng = V_ngưỡng_IC × (1 + R_trên / R_dưới)`.

IC không biết người thiết kế muốn bật ở 4 V hay 4,2 V. Cầu chia làm điện áp chân đạt ngưỡng nội bộ đúng tại mức VIN được chọn. Đây là ánh xạ điện áp bằng mạch thụ động, không phải firmware thay đổi ngưỡng bên trong IC.

### 9.5. R23/R24 — ví dụ thiết kế UVLO

Ví dụ đặt mục tiêu minh họa: cho phép bật khi VIN tăng tới 4,2 V. Khi đó cần EN đạt khoảng 1,2 V:

`R23/R24 = 4,2/1,2 − 1 = 2,5`.

Nếu chọn R24 = 100 kΩ thì R23 tính ra 250 kΩ. Chọn giá trị chuẩn gần đó là 249 kΩ, được cấu hình hiện tại:

`V_EN = VIN × 100/(249 + 100)`.

| Điều kiện | Phép tính danh định | Kết quả tại `5V_EXT_REV` |
|---|---|---:|
| VIN tăng, EN đạt 1,2 V | `1,2 × 349/100` | 4,188 V ≈ 4,19 V |
| VIN giảm, EN xuống 1,1 V | `1,1 × 349/100` | 3,839 V ≈ 3,84 V |

**Hai ngưỡng không chọn độc lập** bằng cầu chia đơn giản này. Chọn tỷ lệ điện trở làm thay đổi cả hai; tỷ số danh định của ngưỡng ngắt/bật bằng tỷ số ngưỡng nội bộ `1,1/1,2`.

Ví dụ mục tiêu 4,2 V giải thích cách tính; chưa phải chứng minh yêu cầu hệ thống đã được chốt ở mức đó. 4,19 V/3,84 V là kết quả từ cấu hình đang vẽ. Cần đối chiếu dung sai adapter, sụt áp và yêu cầu điện áp các khối phía sau.

Tỷ lệ điện trở quyết định ngưỡng danh định. Độ lớn tuyệt đối còn liên quan tới dòng tiêu thụ cầu chia, dòng rò chân IC và độ nhạy nhiễu; chọn R24 = 100 kΩ không phải quy tắc bắt buộc cho mọi thiết kế.

### 9.6. R25/R26 — OVLO và việc trở về 5 V

Với R25 = 34,8 kΩ phía trên, R26 = 10 kΩ phía dưới:

`V_OV = VIN × 10/(34,8 + 10)`.

| Điều kiện | Phép tính danh định | Kết quả tại `5V_EXT_REV` |
|---|---|---:|
| VIN tăng, OVLO đạt 1,2 V | `1,2 × 44,8/10` | 5,376 V ≈ 5,38 V |
| VIN giảm, OVLO xuống 1,1 V | `1,1 × 44,8/10` | 4,928 V ≈ 4,93 V |

Nếu đã ngắt do quá áp, giảm về đúng 5 V chưa vượt qua ngưỡng gỡ quá áp danh định 4,93 V. Không nhầm khoảng cho phép khởi động bình thường với điều kiện phục hồi sau sự cố.

Ngưỡng thực phải tính cả sai số IC, điện trở và dòng rò. Yêu cầu điện trở 0,1% không loại bỏ sai số ngưỡng của chính IC.

### 9.7. ILM, dV/dT và tụ nguồn

- **R22 = 453 Ω tại ILM:** cấu hình giới hạn dòng khoảng 2 A danh định. IC còn tạo tín hiệu tại ILM theo dòng tải. Nguyên lý cảm nhận/hạn dòng và cách tính R22 cần học tiếp; không coi R22 là điện trở công suất mắc nối tiếp đường tải.
- **C47 = 10 nF tại dV/dT:** làm quá trình tăng VOUT có kiểm soát; tụ lớn hơn làm quá trình bật chậm hơn. Điều này hạn chế dòng nạp vào các tụ phía sau theo quan hệ `I_tụ = C_tải × dVOUT/dt`. Chưa tính thời gian bật thực của board.
- **C46 = 1 µF tại IN:** tụ nguồn cục bộ, khác chức năng với tụ thời gian C47. C46 cũng nối giữa Source Q1/đầu vào U7 và GND trong phân tích trạng thái ban đầu ở mục 6.7.

### 9.8. Trạng thái lỗi và phạm vi đã học

TPS259630 là biến thể OVLO điều chỉnh và **latch-off sau bảo vệ quá nhiệt**; không đồng nhất hành vi phục hồi của mọi lỗi. Hysteresis UVLO/OVLO là cơ chế khác với latch-off nhiệt. Không mặc định quá dòng vừa vượt mức là IC lập tức ngắt hoàn toàn; nó có chế độ hạn dòng và có thể tắt do nhiệt nếu tình trạng kéo dài.

Đã trao đổi: vai trò eFuse, phân loại chân, cầu chia, ngưỡng tại chân so với ngưỡng nguồn vào và hysteresis. Còn cần học sâu: cơ chế ILM, dV/dT, quá tải/ngắn mạch, nhiệt và trình tự phục hồi. Các phần tiếp theo được ghi ở mục 10–13; không coi mọi IC đã được học chi tiết chỉ vì có trong bảng linh kiện.

Mọi giá trị ở mục này là cấu hình hoặc tính toán danh định, chưa phải số đo hay bảo đảm khả năng chịu tải toàn kit.

## 10. Khối 03 — Chọn nguồn, IC U2 TPS2116

### 10.1. Vai trò và đường nguồn

U2 chọn một trong hai nguồn để cấp đường `5V_SYS`. Nguồn ngoài được lấy sau Q1 và U7; USB đi theo nhánh riêng:

```text
J1 → Q1 → U7 → 5V_EXT → VIN1 ┐
                              ├── U2 → 5V_SYS → buck U3
USB-C → VBUS_USB ───────→ VIN2 ┘
```

Đây là chọn nguồn bằng các công tắc công suất và mạch điều khiển tích hợp. Không cộng hai mức 5 V thành 10 V, cũng không cộng dòng định mức hai nguồn thành một khả năng cấp dòng lớn hơn.

U7 bảo vệ nhánh nguồn ngoài; U2 quyết định nhánh nào cấp hệ thống. Không coi U2 thay thế mọi chức năng của eFuse hoặc bộ ổn áp.

### 10.2. Các chân U2 và cấu hình hiện tại

| Chân | Kết nối | Ý nghĩa |
|---|---|---|
| VIN1 | `5V_EXT` | Đầu vào nguồn ngoài sau bảo vệ |
| VIN2 | `VBUS_USB` | Đầu vào nguồn USB |
| VOUT | `5V_SYS` | Đầu ra được chọn |
| MODE | Nối VIN1 | Cấu hình ưu tiên nguồn VIN1 theo hướng dẫn hãng |
| PR1 | Điểm giữa R3/R4 | Giám sát nguồn ưu tiên để lựa chọn |
| GND | GND chung | Mốc điện và đường hồi dòng |
| ST | Hiện không sử dụng | Đầu ra trạng thái open-drain; không phải đầu vào điều khiển |

Với cấu hình ưu tiên, ngưỡng so sánh PR1 điển hình khoảng 1 V. PR1 trên ngưỡng chọn VIN1; dưới ngưỡng chuyển sang VIN2 khi đầu vào đó đủ điều kiện. Giá trị ngưỡng có sai số; không dùng 1 V như một hằng số chính xác tuyệt đối.

### 10.3. R3/R4 đặt ngưỡng lựa chọn

R3 = 27 kΩ phía trên, R4 = 10 kΩ phía dưới:

`V_PR1 = V_5V_EXT × 10/(27 + 10)`.

Tại nguồn ngoài 5 V, `V_PR1 ≈ 1,35 V`, cao hơn ngưỡng điển hình 1 V. Ngưỡng nguồn ngoài suy ra danh định là:

`V_chuyển ≈ 1 V × (27 + 10)/10 = 3,7 V`.

Đây là ngưỡng giám sát tại **5V_EXT sau eFuse**, khác vị trí lấy mẫu **5V_EXT_REV trước eFuse** của U7. Khi phân tích chuyển nguồn phải xét cả hai mạch, tụ giữ điện và các biên sai số; không chỉ so sánh hai con số danh định.

### 10.4. Tình huống hoạt động và các tụ

| Điều kiện bình thường | Nguồn dự kiến cấp đầu ra |
|---|---|
| Chỉ có nguồn ngoài phù hợp | Nguồn ngoài |
| Chỉ có USB phù hợp | USB |
| Cả hai có, nguồn ngoài phù hợp | Ưu tiên nguồn ngoài |
| Nguồn ngoài mất/bị U7 ngắt, USB vẫn phù hợp | Chuyển sang USB |

U2 có cơ chế chặn dòng ngược từ đầu ra về đầu vào. Đây không phải cách ly điện hoàn toàn: GND của các nhánh vẫn nối chung.

- C1 = 1 µF: tụ cục bộ tại VIN1.
- C2 = 1 µF: tụ cục bộ tại VIN2.
- C3 = 10 µF: tụ đầu ra, hỗ trợ điện áp khi tải hoặc trạng thái nguồn thay đổi.

Chuyển nguồn có khoảng chuyển mạch và có thể gây sụt áp; chưa xác nhận board chuyển nguồn mà không reset. Khả năng USB cấp tải phải được đánh giá riêng.

## 11. Khối 04 — Buck 5 V xuống 3,3 V, IC U3 AP63203

### 11.1. Điện áp cố định của đúng mã IC

AP63203WU là buck có mức đầu ra cố định 3,3 V. Không cấu hình sang mức khác bằng firmware hoặc thay cầu chia ngoài như bản điều chỉnh được.

| IC cùng họ | Mức đầu ra |
|---|---|
| AP63203 — đang chọn | Cố định 3,3 V |
| AP63205 | Cố định 5 V |
| AP63200/AP63201 | Điều chỉnh bằng cầu chia hồi tiếp ngoài |

Dải đầu vào của riêng AP63203 là 3,8–32 V; kit hiện dùng `5V_SYS`. Điều đó không cho phép cấp cao hơn 5 V vào toàn kit, vì những linh kiện phía trước có giới hạn khác.

“Đầu ra cố định” nghĩa là mức mục tiêu do hãng đặt; IC vẫn phải đo đầu ra và điều chỉnh quá trình chuyển đổi để giữ mức đó.

### 11.2. Các chân và linh kiện trong khối

| Chân U3 | Kết nối hiện tại | Chức năng |
|---|---|---|
| VIN, chân 3 | `5V_SYS` | Nhận năng lượng đầu vào |
| EN, chân 2 | `5V_SYS` | Cho phép tự khởi động khi nguồn đủ điều kiện |
| SW, chân 5 | Đầu vào L1 | Nút đóng cắt công suất, không phải nguồn DC 3,3 V |
| FB, chân 1 | `3V3` sau L1 | Lấy mẫu điện áp đầu ra |
| BST, chân 6 | C4 nối sang SW | Hỗ trợ nguồn driver MOSFET phía trên |
| GND, chân 4 | GND | Mốc điện/đường hồi dòng |

| Linh kiện | Giá trị | Vai trò |
|---|---|---|
| L1 | 3,9 µH | Phần tử tích trữ năng lượng từ trường và điều tiết biến đổi dòng |
| C5, C6 | 22 µF mỗi tụ | Tụ đầu ra, giảm gợn áp và hỗ trợ quá độ tải |
| C7 | 10 µF | Tụ cục bộ đầu vào, hỗ trợ dòng đóng cắt |
| C4 | 100 nF | Tụ bootstrap, nối giữa BST và SW, không nối xuống GND |

Các giá trị trên khớp cấu hình khuyến nghị AP63203 trong datasheet. Mã cuộn cảm/tụ và đặc tính thực vẫn phải được lựa chọn; chưa coi là bộ nguồn đã được kiểm chứng với toàn tải kit.

### 11.3. Hai pha chuyển đổi năng lượng

Sơ đồ giản lược, chưa thể hiện đầy đủ vòng dòng công suất:

```text
VIN → các MOSFET bên trong U3 → SW → L1 → nút 3V3 → tải
                                           │
                                         C5, C6
                                           │
                                          GND
                                           
Nút 3V3 ───────────────────────────────────→ FB
```

Xét mô hình lý tưởng khi đã ổn định, VIN = 5 V, VOUT = 3,3 V, dòng cuộn cảm còn liên tục và bỏ qua tổn hao:

| Pha | SW so với GND | Điện áp trên L1: trái trừ phải | Diễn biến dòng L1 |
|---|---:|---:|---|
| MOSFET phía trên dẫn | ≈ 5 V | +1,7 V | Tăng |
| MOSFET phía dưới dẫn | ≈ 0 V | −3,3 V | Giảm, nhưng vẫn có thể tiếp tục đi tới tải |

Quan hệ cuộn cảm: `V_L = L × di_L/dt`. Điện áp hai đầu cuộn cảm quyết định tốc độ biến đổi dòng; dòng không nhảy tức thời trong mô hình lý tưởng có điện áp hữu hạn.

Ở pha MOSFET phía dưới dẫn, vòng dòng được duy trì qua GND → MOSFET phía dưới → SW → L1 → tải/nhánh đầu ra → GND. Cuộn cảm giải phóng năng lượng từ trường. SW gần 0 V không có nghĩa dòng qua L1 lập tức bằng 0.

Mạch thực có thời gian chuyển tiếp giữa các trạng thái. Không coi hai MOSFET được phép dẫn đồng thời để nối tắt VIN xuống GND.

IC điều chỉnh đóng cắt qua vòng điều khiển. Với mô hình buck lý tưởng dòng liên tục:

`VOUT ≈ D × VIN`, trong đó `D = thời gian dẫn MOSFET trên / chu kỳ`.

Ví dụ 5 V xuống 3,3 V cho `D ≈ 66%`. Đây không phải cách P4 phát PWM cho U3; bộ điều khiển bên trong U3 tự vận hành. Ở tải nhẹ, AP63203 còn có chế độ PFM nên không áp nguyên xi mô hình hai pha liên tục cho mọi điều kiện.

### 11.4. Vì sao FB nối sau cuộn cảm?

FB nối trực tiếp vào nút **đầu ra 3V3 sau L1**, cũng là nút tải nhận điện áp. Không phải IC đo SW rồi đưa “phép đo” xuyên qua cuộn cảm.

- SW mang xung điện áp thay đổi nhanh.
- L1 làm dòng biến đổi có giới hạn; C5/C6 nạp/xả để điện áp đầu ra ít gợn hơn.
- Khi dòng L1 lớn hơn dòng tải, phần dư nạp tụ; khi nhỏ hơn, tụ bù phần thiếu.
- FB theo dõi điện áp đầu ra, IC điều chỉnh việc truyền năng lượng để giữ mức mục tiêu.

L1 và tụ tham gia mạch công suất; FB là nhánh lấy tín hiệu. Cố định mục tiêu 3,3 V vẫn cần hồi tiếp.

### 11.5. Cuộn cảm có bắt buộc không? Thay L thì thay gì?

Với kiến trúc buck AP63203 này, **cuộn cảm ngoài là thành phần bắt buộc**. Không thể bỏ hoặc nối tắt L1 mà vẫn xem phần còn lại là bộ buck hoạt động đúng.

Từ `di/dt = V_L/L`, với cùng điện áp tức thời trên cuộn cảm:

| Thay đổi L | Hệ quả trong cùng điều kiện so sánh |
|---|---|
| L tăng | Dòng tăng/giảm chậm hơn; độ gợn dòng mỗi chu kỳ thường nhỏ hơn |
| L giảm | Dòng tăng/giảm nhanh hơn; độ gợn và dòng đỉnh thường lớn hơn |

Tăng L gấp đôi làm tốc độ biến đổi dòng giảm một nửa nếu giữ điện áp trên cuộn cảm như nhau. Đây là so sánh nguyên lý, không phải đề xuất tự đổi L1.

L lớn có thể làm tăng dòng đáp ứng tải chậm hơn; L nhỏ có thể làm dòng đỉnh chạm giới hạn sớm hơn. Khi chọn mã cuộn cảm phải xét thêm dòng bão hòa, dòng chịu nhiệt, DCR và tương thích với vòng điều khiển/tụ đầu ra. Không có kết luận L càng lớn càng tốt.

**L không đặt mức đầu ra mục tiêu.** Nếu thay đổi trong phạm vi thiết kế phù hợp, IC vẫn điều khiển về 3,3 V; nó thay đổi đặc tính dòng, gợn và đáp ứng của bộ nguồn. Ở trạng thái ổn định lý tưởng, điện áp trung bình trên cuộn cảm bằng 0; không coi L1 là linh kiện luôn làm rơi cố định 1,7 V.

Trạng thái: đã trao đổi nguyên lý buck, vị trí FB và ảnh hưởng L. Chưa học sâu bootstrap, bù vòng điều khiển hoặc kiểm chứng đáp ứng/nhiệt.

## 12. Sheet USB_GPIO — Đầu nối, CC và ESD

### 12.1. Phân biệt đầu nối với giao thức

USB-C/Type-C là kiểu đầu nối vật lý. Thiết kế hiện tại dùng các đường USB 2.0 để giao tiếp với bộ USB Serial/JTAG tích hợp trong P4.

```text
J2 USB-C
 ├── VBUS/GND → sheet Power → U2 → U3 → nguồn phù hợp cho board
 ├── D+/D− ──┬──→ R10/R11 ở sheet P4 → GPIO25/GPIO24
 │          └──→ U1 bảo vệ ESD mắc nhánh → GND
 └── CC1/CC2  → mỗi chân qua một điện trở 5,1 kΩ riêng xuống GND
```

Mũi tên dòng dữ liệu chỉ thể hiện kết nối tổng quát; cặp D+/D− truyền hai chiều. Không phải D+ là TX, D− là RX như hai đường UART riêng.

### 12.2. Các nhóm chân J2

| Nhóm chân | Kết nối/vai trò hiện tại |
|---|---|
| VBUS | `VBUS_USB`, nguồn danh định 5 V tới VIN2 của U2 |
| GND | GND chung, đường tham chiếu và hồi dòng |
| D+ A6/B6 | Nối chung thành `USB_DP_CONN` |
| D− A7/B7 | Nối chung thành `USB_DM_CONN` |
| CC1/CC2 | Mỗi chân có điện trở kéo xuống riêng |
| SBU1/SBU2 | Không sử dụng |
| Shield | Vỏ kim loại, phương án hiện tại nối GND |

Hai cặp tiếp điểm D+/D− hỗ trợ cắm đảo chiều; chỉ nối cùng loại với nhau, không nối D+ với D−. VBUS không được đưa thẳng vào GPIO như một mức logic 5 V; nó đi vào nhánh nguồn.

### 12.3. R1/R2 — nhận diện phía nhận nguồn

R1 = 5,1 kΩ từ CC1 xuống GND; R2 = 5,1 kΩ từ CC2 xuống GND. Đây là điện trở Rd của phía nhận nguồn (Sink). Phía cấp nguồn Type-C kiểm tra đặc tính CC để nhận diện kết nối.

Không nối chung CC1/CC2 dùng một điện trở. Hai điện trở không phải phần tử giới hạn dòng công suất cho board, không tự bảo đảm mọi khả năng cấp dòng và không tạo chức năng đàm phán USB Power Delivery.

### 12.4. U1 TPD2E2U06DCK — IC bảo vệ

U1 có hai kênh ESD cho hai đường dữ liệu, GND nối mạch bảo vệ về mạng GND. Nó **mắc nhánh**, không nhận rồi tái tạo/giải mã luồng dữ liệu USB.

- Bình thường: dòng rò và tải ký sinh nhỏ, nhằm hạn chế ảnh hưởng tới đường tín hiệu.
- Khi có xung ESD: đường bảo vệ dẫn dòng xung để hạn chế điện áp tại các đường dữ liệu. Dòng vẫn phải có đường hồi; GND không phải nơi tiêu hủy dòng.

Hiệu quả trên board phụ thuộc cả đường kết nối và layout. U1 trên D+/D− không đồng nghĩa VBUS/CC cũng đã có cùng mức bảo vệ, và không thay thế eFuse đường nguồn.

### 12.5. Bộ xử lý giao thức nằm ở P4

Theo kết nối hiện tại, D− tới GPIO24, D+ tới GPIO25 qua R10/R11. Bộ USB Serial/JTAG trong P4 xử lý giao tiếp tương ứng; không cần IC USB–UART rời trên đường này.

Nạp, console và debug còn phụ thuộc trạng thái boot, phần mềm/công cụ và cấu hình phù hợp. Có cổng và dây đúng không phải bằng chứng board thật đã nhận USB hoặc nạp thành công.

Trạng thái: đã phân biệt nguồn, dữ liệu, CC và bảo vệ; chưa học sâu mã hóa/gói tin, điện tín hiệu hoặc thiết kế PCB USB.

## 13. Sheet ESP32_P4 — U5/U6 và bản đồ nguồn của P4

### 13.1. Phân biệt ba IC trên sheet

| IC | Vai trò |
|---|---|
| U4 — ESP32-P4X | Bộ xử lý chính, chứa các miền nguồn, LDO và khối điều khiển nguồn |
| U5 — TLV62569DBV | Buck ngoài, nhận 3V3 và cấp nguồn lõi VDD_HP cho P4 |
| U6 — W25Q128JVS | Flash ngoài, lưu firmware/dữ liệu không mất khi tắt nguồn; cấu hình dự kiến 128 Mbit = 16 MB |

U5 là IC chuyển đổi nguồn; U6 là IC bộ nhớ. PSRAM trong package P4 khác với Flash U6 rời. Mã đặt mua cụ thể vẫn phải được chốt theo BOM.

### 13.2. 3V3 cấp trực tiếp vào những đầu vào nào?

Đây là ánh xạ **đang nối trong schematic**, không phải quy tắc rằng mọi chân nguồn P4 đều phải nhận 3V3:

| Đầu vào P4 | Chân số | Vai trò |
|---|---|---|
| VDD_LP | 9 | Nhóm nguồn/I/O LP |
| VDD_IO_0/4/5/6 | 21, 62, 85, 96 | Các nhóm I/O HP |
| VDD_ANA | 101 | Nguồn analog |
| VDD_BAT | 102 | Nguồn analog/backup; đang dùng 3V3, không dùng pin riêng |
| VDD_LDO | 75 | Cấp năng lượng cho các LDO bên trong P4 |
| VDD_DCDCC | 77 | Cấp cho khối điều khiển DC–DC bên trong P4 |
| VDD_USBPHY | 51 | Nguồn PHY USB High-Speed |

3V3 còn đi vào VIN U5 ở bên ngoài P4. Nguồn của nhóm I/O khác với nguồn lõi số; các tên cùng bắt đầu bằng VDD không có nghĩa có thể nối hết vào một mức điện áp.

VDD_USBPHY liên quan PHY USB High-Speed, không nên suy ra mọi khối USB của chip cùng dùng đúng một đường nguồn chỉ vì đều mang tên USB.

### 13.3. Nguồn nào do P4 tạo ra, rồi nối lại vào đâu?

Các LDO được cấp qua VDD_LDO tạo những đường sau:

| Đầu ra P4 | Mức/điều kiện | Kết nối đang dùng |
|---|---|---|
| VDDO_FLASH, chân 71 | Mặc định 3,3 V theo cấu hình chip thông thường | VCC chân 8 của U6 và VDD_FLASH_IO chân 30 của P4 |
| VDDO_PSRAM, chân 72 | Khoảng 1,9 V theo hướng dẫn; cần phần mềm cấu hình, mặc định đầu ra tắt | VDD_PSRAM_0/1, chân 59/67 |
| VDDO_3, chân 73 | Dự kiến cấu hình 2,5 V; mặc định tắt | VDD_MIPI_DPHY, chân 41 |
| VDDO_4, chân 74 | Có thể cấu hình | Hiện chưa sử dụng |

Symbol đang ghi `VDD_FLASH_IO`; tài liệu hãng cũng dùng cách viết `VDD_FLASHIO`. Bảng mô tả cùng đầu vào nguồn giao tiếp Flash, không phải một đường nguồn mới.

Đầu ra LDO đi ra package rồi nối vào đầu vào của miền cần nguồn, kèm các tụ bên ngoài. Nguồn không tự được tạo chỉ do nối hai chân với nhau; năng lượng xuất phát từ đầu vào LDO và hoạt động phụ thuộc cấu hình chip.

Không coi VDDO_PSRAM/VDDO_3 có ngay mức mong muốn chỉ vì 3V3 đã xuất hiện. Không dùng mức đặt của LDO làm bảo đảm khả năng cấp mọi tải mở rộng.

### 13.4. U5 TLV62569 — nguồn lõi từ bên ngoài

Đường công suất:

```text
3V3 → VIN U5 → MOSFET đóng cắt → SW → L2 → VDD_HP → VDD_HP_0/1/2/3 của P4
```

| Chân U5 (DBV) | Kết nối hiện tại |
|---|---|
| VIN, chân 4 | 3V3 |
| EN, chân 1 | EN_DCDC từ P4, chân 79 |
| SW, chân 3 | L2 = 2,2 µH tới VDD_HP |
| FB, chân 5 | Nút hồi tiếp và FB_DCDC từ P4, chân 78 |
| GND, chân 2 | GND |

C35 = 4,7 µF là tụ đầu vào, C36 = 22 µF là tụ đầu ra. R5/R6 = 499 kΩ mỗi điện trở và C37 = 22 pF thuộc mạng hồi tiếp theo mạch tham khảo revision 3.x. Các tụ C38–C41 hỗ trợ tại từng chân nguồn HP.

TLV62569 có mức đầu ra điều chỉnh được. Trong hệ này, P4 phối hợp điều khiển qua EN_DCDC/FB_DCDC; không coi U5 là một bộ nguồn 3,3 V cố định độc lập với P4. Các chân VDD_HP dùng mức thấp hơn 3V3, theo yêu cầu chip; dải khuyến nghị trong datasheet là 0,99–1,3 V với mức điển hình 1,1 V và có điều chỉnh theo tình trạng hoạt động.

**EN_DCDC và FB_DCDC là tín hiệu điều khiển/phối hợp, không phải đường cấp năng lượng lõi.** Năng lượng công suất đi từ 3V3 qua U5 và L2 rồi trở lại các chân VDD_HP. Cơ chế điều chỉnh chi tiết và trình tự bật/tắt còn cần học tiếp.

### 13.5. U6 Flash — nguồn và dữ liệu là hai việc khác nhau

U6 dùng nguồn VDDO_FLASH, có C45 = 100 nF cục bộ. Cùng đường nguồn đó cấp VDD_FLASH_IO phía P4 để giao tiếp phù hợp với cấu hình Flash đang chọn.

Lệnh và dữ liệu đi trên FLASH_CS/CK/D/Q/WP/HOLD qua R14–R19. Những đường này không thay thế chân VCC/GND. IC Flash không tạo nguồn; nó tiêu thụ năng lượng để lưu trữ/trao đổi dữ liệu. Sơ đồ boot từ ROM, bootloader và ứng dụng đã được học ở ghi chú thiết kế trước; chưa xem việc có kết nối QSPI là bằng chứng firmware đã chạy.

### 13.6. Sơ đồ nguồn tổng hợp và ba loại đường cần phân biệt

```text
3V3
 ├──→ Các đầu vào 3,3 V phù hợp của P4
 │
 ├──→ VDD_LDO trong P4
 │      ├── LDO Flash → VDDO_FLASH ──┬──→ Flash U6
 │      │                           └──→ VDD_FLASH_IO của P4
 │      ├── LDO PSRAM → VDDO_PSRAM ────→ VDD_PSRAM_0/1 của P4
 │      └── LDO VO3   → VDDO_3 ────────→ VDD_MIPI_DPHY của P4
 │
 └──→ VIN U5 → SW → L2 → VDD_HP ───────→ VDD_HP_0/1/2/3 của P4
          ↑
          └── EN_DCDC / FB_DCDC từ P4: điều khiển và phối hợp hồi tiếp
```

Ba loại cần tách: **đầu vào nguồn**, **đầu ra bộ ổn áp**, **tín hiệu điều khiển/đo**. Việc nhận một đường nguồn từ P4 rồi nối trở lại P4 là kết nối giữa bộ ổn áp và miền sử dụng nguồn, không phải tạo một vòng tự sinh năng lượng.

Trạng thái: đã tổng hợp đường nguồn và vai trò U5/U6. Cấu hình firmware, ngân sách dòng từng miền, trình tự nguồn và phép đo thực chưa hoàn thiện.

## 14. Mẫu ghi chú sau mỗi buổi

```text
Khối đang học:
Vấn đề cần giải quyết:
Nguyên lý điện đã hiểu:
Điện áp / đường dòng điện:
Vai trò từng linh kiện:
Thông số còn là giả định:
Điều kiện PCB cần lưu ý về sau:
Điều cần đo trên phần cứng:
Chỗ còn chưa hiểu:
```

## 15. Cách trình bày tiến độ trong báo cáo

Có thể dùng đoạn sau, rồi bổ sung phần nguyên lý sau khi học lại:

> Đã xây dựng sơ đồ nguyên lý cơ bản cho các khối nguồn, bảo vệ nguồn ngoài, khởi động, clock, bộ nhớ Flash và nạp/debug USB của dev-kit ESP32-P4. Đã kiểm tra các kết nối được triển khai bằng netlist và ERC. Các thông số linh kiện và ngân sách nguồn tiếp tục được rà soát. Chưa triển khai layout PCB và chưa kiểm chứng hoạt động trên phần cứng.

Không báo cáo rằng đã hoàn thành toàn bộ dev-kit, đã nạp thành công hoặc đã chứng minh các ngưỡng bảo vệ chỉ từ việc schematic nối đúng.

## 16. Tài liệu đối chiếu

- [ESP32-P4 Datasheet](https://documentation.espressif.com/esp32-p4_datasheet_en.html): chân, miền nguồn, strapping và chức năng của chip.
- [ESP32-P4 Hardware Design Guidelines](https://docs.espressif.com/projects/esp-hardware-design-guidelines/en/latest/esp32p4/schematic-checklist-esp32p4.html): mạch tham khảo và yêu cầu thiết kế; dùng phần dành cho revision 3.x.
- [AO3401A — Alpha & Omega Semiconductor](https://www.aosmd.com/pdfs/datasheet/AO3401A.pdf): chân MOSFET, VGS, RDS(on), điện và nhiệt.
- [BZT52C8V2 — Diodes Incorporated](https://www.diodes.com/part/view/BZT52C8V2): điện áp Zener danh định và điều kiện đặc tả; kiểm tra đúng hãng/mã khi chốt BOM.
- [TPS2596 — Texas Instruments](https://www.ti.com/lit/ds/symlink/tps2596.pdf): eFuse, biến thể TPS259630, các ngưỡng và cấu hình.
- [TPS2116 — Texas Instruments](https://www.ti.com/lit/ds/symlink/tps2116.pdf): bộ chọn nguồn và PR1/MODE.
- [AP63203 — Diodes Incorporated](https://www.diodes.com/datasheet/download/AP63200-AP63201-AP63203-AP63205.pdf): buck 3,3 V và linh kiện ngoại vi.
- [TLV62569 — Texas Instruments](https://www.ti.com/lit/ds/symlink/tlv62569.pdf): buck nguồn lõi; kết hợp với mạch tham khảo P4 revision 3.x.
- [Winbond W25Q-JV](https://www.winbond.com/hq/product/code-storage-flash/qspi-nor/w25q-jv/?__locale=en): họ Flash ngoài; đối chiếu đúng dung lượng, điện áp, package và mã đặt mua.
- [TPD2E2U06 — Texas Instruments](https://www.ti.com/lit/ds/symlink/tpd2e2u06.pdf): bảo vệ ESD hai kênh cho dữ liệu USB.
- [Richtek — Introduction to USB Type-C Power Delivery Solutions](https://www.richtek.com/Design%20Support/Technical%20Document/AN056): CC, điện trở Rd và nhận diện nguồn Type-C.
- [Synopsys — Implementing USB Type-C in High-Speed USB Products](https://www.synopsys.com/articles/implementing-usb-type-c.html): đầu nối Type-C dùng dữ liệu USB 2.0 và các tiếp điểm đảo chiều.
- [ESP32-P4 USB Serial/JTAG](https://docs.espressif.com/projects/esp-idf/en/stable/esp32p4/api-guides/usb-serial-jtag-console.html): nạp, console và debug qua USB.
- [TI — The decoupling capacitor…is it really necessary?](https://e2e.ti.com/blogs_/archives/b/precisionhub/posts/the-decoupling-capacitor-is-it-really-necessary): dòng quá độ, điện cảm đường cấp và vị trí tụ.
- [Analog Devices AN-1099](https://www.analog.com/en/resources/app-notes/an-1099.html): chọn tụ cho bộ ổn áp, ESR/ESL và điện dung hiệu dụng.
- [KYOCERA AVX — Effects of ESR and ESL in Digital Decoupling Applications](https://www.kyocera-avx.com/docs/techinfo/DecouplingLowInductance/esr_esl.pdf): giới hạn của mô hình tụ lý tưởng và ảnh hưởng đường kết nối.

Ghi chú này ghi lại thiết kế đang học. Khi chốt linh kiện phải kiểm tra đúng tài liệu, biến thể và điều kiện áp dụng, thay vì dùng các con số trong ghi chú như thông số đã được đo.
