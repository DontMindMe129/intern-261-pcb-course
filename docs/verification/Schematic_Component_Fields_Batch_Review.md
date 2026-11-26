# Bảng hoàn thiện thông tin linh kiện schematic

Ngày rà: 2026-11-08. Nguồn: netlist mới xuất từ project `hardware/esp32_p4_devkit/esp32_p4_devkit.kicad_sch`.

**Đây là bảng đề xuất để duyệt và điền theo nhóm; chưa áp dụng thay đổi vào schematic.**

Không đổi Value, số chân, dây, label, reference hay trạng thái DNP trong đợt điền thông tin. Footprint đề xuất cần đối chiếu land pattern trước khi khóa để sản xuất.

- Tổng số linh kiện trong netlist: **242**.
- Linh kiện lắp còn thiếu MPN hoặc footprint: **147**.
- Có thể đề xuất dùng lại mã đã có: **117 linh kiện / 9 nhóm**.
- Cần xem riêng: **30 linh kiện**.
- DNP giữ riêng: **5 linh kiện**.

## 1. Nhóm có thể điền cùng nhau

Các mã dưới đây đã xuất hiện ở linh kiện cùng giá trị trong project. Việc dùng lại không có nghĩa tất cả yêu cầu điện/nguồn mua đã được xác nhận. Mã nào còn thiếu định mức, đường đặc tính hoặc báo giá vẫn để ở trạng thái chờ.

| Nhóm | Linh kiện cần điền | Manufacturer / MPN đề xuất | Footprint đề xuất | Ghi chú |
|---|---|---|---|---|
| C — 100nF | C1, C52, C57, C59, C61, C62, C64, C65, C66, C69, C71, C73, C75, C83, C84, C85 | Samsung Electro-Mechanics / `CL10B104KB8NFNC` | `Capacitor_SMD:C_0603_1608Metric` | 100nF; 50V; X7R; ±10% |
| C — 10nF | C32 | Samsung Electro-Mechanics / `CL10B103KB8NFNC` | `Capacitor_SMD:C_0603_1608Metric` | 10nF; thông số chi tiết cần đối chiếu lại trước khi khóa BOM |
| C — 10uF | C7, C11, C17, C19, C51, C56, C58, C60, C63, C82 | Murata / `GRM31CR71E106MA12L` | `Capacitor_SMD:C_1206_3216Metric` | 10µF; 25V; X7R; ±20% |
| C — 1uF | C20, C22, C23, C25, C28, C31, C34, C70, C72, C74, C76, C77, C78, C79, C80, C81, C86 | Murata / `GRM21BR71C105KA01L` | `Capacitor_SMD:C_0805_2012Metric` | 1µF; 16V; X7R; ±10% |
| R — 0 | R12, R13, R14, R15, R16, R17, R18, R19, R44, R45, R46, R47, R48, R49, R50, R51, R52, R53, R54, R55, R56, R57, R58, R59, R60, R61, R64, R65, R66, R67, R68, R69, R76, R80, R81, R86, R89, R90, R91, R92, R93, R96, R97, R98, R99, R100, R107, R108 | Panasonic / `ERJ-3GEY0R00V` | `Resistor_SMD:R_0603_1608Metric` | Jumper 0Ω; xem riêng kích thước pad cho MIPI/clock khi layout |
| R — 100k | R63 | Vishay / `CRCW0603100KFKEA` | `Resistor_SMD:R_0603_1608Metric` | 100kΩ; ±1% |
| R — 10k | R1, R20, R21, R26, R34, R35, R36, R38, R39, R40, R41, R42, R43, R94, R95, R106 | Panasonic / `ERJ-3EKF1002V` | `Resistor_SMD:R_0603_1608Metric` | 10kΩ; ±1% |
| R — 1k | R74, R87, R88 | Panasonic / `ERJ-3EKF1001V` | `Resistor_SMD:R_0603_1608Metric` | 1kΩ; ±1% |
| R — 5.1k | R78, R79, R83, R84, R85 | Vishay / `CRCW06035K10FKEA` | `Resistor_SMD:R_0603_1608Metric` | 5.1kΩ; ±1% |

## 2. Các mục cần xem riêng

| Sheet | Linh kiện | Value hiện tại | Thông tin còn thiếu | Việc cần chốt |
|---|---|---|---|---|
| Connectivity | C67 | 20pF | MPN, Footprint | Tụ tải 20pF: gom với C68; chốt crystal Y2/CL trước khi khóa. |
| Connectivity | C68 | 20pF | MPN, Footprint | Tụ tải 20pF: gom với C67; chốt crystal Y2/CL trước khi khóa. |
| Connectivity | J1 | C6_UART_PROG | MPN, Footprint | Header UART C6 1x03: chọn bước chân/cơ khí; pinout TX, RX, GND giữ nguyên. |
| Connectivity | J9 | HR913550A | MPN, Footprint | HR913550A: có tên mã nhưng chưa có MPN field/footprint. Kiểm đúng chân LED, vỏ, lỗ định vị và land pattern HanRun. |
| Connectivity | R77 | 6.19k | MPN, Footprint | 6.19kΩ cho PHY ISET: 1%; chọn mã có tài liệu và nguồn mua. |
| Connectivity | R82 | 1.5k | MPN, Footprint | 1.5kΩ pull-up MDIO: chọn mã cụ thể 0603, 1%. |
| Connectivity | U1 | ESP32-C6-MINI-1 | MPN | Chốt hậu tố dung lượng flash của ESP32-C6-MINI-1; footprint module đã có. |
| Connectivity | U2 | IP101GRI | MPN, Footprint | IP101GRI: kiểm tra đúng bản công nghiệp và package QFN-32 có exposed pad, đối chiếu pad 33. |
| Connectivity | Y2 | 25 MHz | MPN, Footprint | 25MHz: chưa chốt MPN, CL, ESR, kích thước; chưa gán footprint chỉ dựa vào tần số. |
| ESP32_P4 | C43 | 16pF | MPN, Footprint | Tụ tải 16pF: gom với C44; chọn C0G/NP0. Giữ giá trị hiện tại; kiểm CL và điện dung ký sinh riêng. |
| ESP32_P4 | C44 | 16pF | MPN, Footprint | Tụ tải 16pF: gom với C43; chọn C0G/NP0. |
| ESP32_P4 | Q1 | 2N7002 | MPN | 2N7002 có nhiều hãng. Chốt đúng MPN/hãng và datasheet pin G/S/D; footprint SOT-23 đã có. |
| ESP32_P4 | R62 | 4.02k | MPN, Footprint | 4.02kΩ cho DSI_REXT: ưu tiên 1%; có thể cùng mã R70 sau khi đối chiếu datasheet. |
| ESP32_P4 | R70 | 4.02k | MPN, Footprint | 4.02kΩ cho CSI_REXT: ưu tiên 1%; không đổi giá trị chỉ để đồng bộ BOM. |
| ESP32_P4 | U4 | ESP32-P4 (rev 1.3) | MPN | Tên hiện tại chỉ ghi rev 1.3, chưa phải MPN đặt hàng. Cần chốt dung lượng PSRAM và xác nhận đúng silicon rev với nguồn cung; không tự đổi revision. |
| Multimedia | J2 | Micro_SD_Card_Det2 | MPN, Footprint | Socket microSD: phải chọn mã cụ thể có detect và đối chiếu pad DET_A/B, SH với symbol; không gán footprint bất kỳ. |
| Multimedia | J4 | CAMERA_MIPI_CSI_15P | MPN, Footprint | Camera 15P: chọn đầu nối thực tế, pitch, mặt tiếp xúc và chiều cáp phù hợp adapter. Không mặc định đầu 15P là cùng chuẩn. |
| Multimedia | J6 | DISPLAY_MIPI_DSI_15P | MPN, Footprint | Display 15P: chọn đầu nối thực tế, pitch, mặt tiếp xúc và chiều cáp phù hợp adapter. |
| Multimedia | J10 | SPEAKER_MONO | MPN, Footprint | Đầu loa 2P: chốt loại đầu nối/pitch; cả hai đường là BTL, không đổi một đầu thành GND. |
| Multimedia | MK1 | MIC_ELECTRET | MPN, Footprint | Chưa chốt capsule electret. Cần bias/current, đường kính, cao, pitch và cực tính trước khi chọn footprint. |
| Multimedia | R101 | 2.2k | MPN, Footprint | 2.2kΩ nhánh micro: gom với R103; chọn mã 0603, 1%. |
| Multimedia | R102 | 200 | MPN, Footprint | 200Ω nhánh bias micro: chọn mã 0603, 1%. |
| Multimedia | R103 | 2.2k | MPN, Footprint | 2.2kΩ nhánh micro: gom với R101; chọn mã 0603, 1%. |
| Multimedia | R104 | 150k | MPN, Footprint | 150kΩ đặt gain ampli: gom với R105, chọn 1%. |
| Multimedia | R105 | 150k | MPN, Footprint | 150kΩ đặt gain ampli: gom với R104, chọn 1%. |
| Multimedia | U10 | ES8311 | MPN, Footprint | ES8311: đã chọn IC nhưng chưa gán đầy đủ MPN/footprint QFN-20 + exposed pad 21; cần package drawing chính xác. |
| Multimedia | U12 | NS4150B | MPN, Footprint | NS4150B: chốt đúng MSOP-8 với shop; tránh nhầm sang SOP-8 theo tên listing. |
| User_IO | D4 | USER_LED | MPN, Footprint | Có thể dùng LTST-C190KGKT/LED0603 như D1 nếu chấp nhận cùng màu; chưa mặc định thay màu LED USER. |
| User_IO | J7 | Conn_02x12_Odd_Even | MPN, Footprint | Header 2x12: chốt pitch, male/female, thẳng/góc; giữ bảng pinout 24 chân đã duyệt. |
| User_IO | U9 | VEML7700-TT | MPN, Footprint | VEML7700-TT: đã chọn cảm biến; cần footprint đúng package quang học 4 chân, không dùng footprint module. |

## 3. DNP — không tự bật lắp

| Linh kiện | Value | Cách xử lý |
|---|---|---|
| C37 | 22pF | Giữ DNP theo cấu hình P4 rev 1.3; chưa cần MPN để mua. |
| C54 | C | Vị trí dự phòng tuning; Value chưa chốt, không tự đặt mã tụ. |
| C55 | C | Vị trí dự phòng tuning; Value chưa chốt, không tự đặt mã tụ. |
| R5 | 499k | Giữ DNP theo cấu hình P4 rev 1.3; chưa cần MPN để mua. |
| R6 | 499k | Giữ DNP theo cấu hình P4 rev 1.3; chưa cần MPN để mua. |

## 4. Trạng thái nguồn mua

| Mã / nhóm | Bằng chứng hiện có | Trạng thái |
|---|---|---|
| RA2512F3R010G | [JLC Việt Nam — danh mục PAKER](https://jlc.com.vn/hang/paker/) | Có niêm yết đúng mã; chờ xác nhận báo giá, MOQ và thời gian giao. Manufacturer trong schematic đã sửa PAKER. |
| TLV62569DBVR | [Thế Giới IC](https://www.thegioiic.com/tlv62569dbvr-ic-dieu-chinh-giam-ap-2a-sot-23-5) | Có niêm yết đúng mã; chưa xác nhận đơn hàng. |
| W25Q128JVSIQ | [JLC Việt Nam](https://jlc.com.vn/linh-kien/nor-flash/w25q128jvsiq-winbond-elec/) | Có niêm yết đúng mã; chờ xác nhận khi đặt. |
| SRP5030T-2R2M (L2) | Datasheet hãng đã đối chiếu; KiCad có footprint đúng dòng | Lựa chọn kỹ thuật tạm; chưa xác nhận shop Việt Nam nhận nhập đúng mã. |
| Các mã còn lại, kể cả linh kiện đã có MPN trong file | Chưa rà đồng loạt nguồn mua trong lượt này | Không ghi là đã mua được chỉ vì có trang quốc tế hoặc có mã trong schematic. |

## 5. Các nguồn kỹ thuật đã dùng trong các lượt rà

- [P4 chip rev 1.3](https://documentation.espressif.com/esp32-p4-chip-revision-v1.3_datasheet_en.html)
- [AP63300](https://www.diodes.com/datasheet/download/AP63300_AP63301.pdf)
- [INA226](https://www.ti.com/lit/ds/symlink/ina226.pdf)
- [TLV62569](https://www.ti.com/lit/ds/symlink/tlv62569.pdf)
- [SRP5030T](https://www.bourns.com/docs/Product-Datasheets/SRP5030T.pdf)
- [C35 Samsung 4.7µF](https://www1.futureelectronics.com/doc/Samsung%20Electro-Mechanics/CL21A475KPFNNNE.pdf)

## 6. Cách áp dụng theo lô

1. Duyệt các nhóm ở mục 1; xử lý riêng các quyết định ở mục 2.
2. Trong KiCad, mở Tools → Edit Symbol Fields, hiện MPN/Manufacturer/Footprint/Datasheet và điền theo nhóm. Không sửa Value hoặc DNP trong lô này.
3. Nếu giao agent áp dụng, chỉ sửa đúng các trường đã duyệt và lưu bản sao trước khi sửa. Bảng này chưa phải lệnh áp dụng toàn bộ.
4. Xuất netlist sau sửa, so sánh reference + pin → net và trạng thái DNP với bản trước. Kiểm tra footprint/pad riêng; netlist không chứng minh land pattern đúng.

## 7. Thông tin đã có trong file — giữ lại để đối chiếu

Bảng dưới chỉ ghi hiện trạng, không phải xác nhận mọi mã đều đã kiểm xong hoặc có hàng.

| Linh kiện | Value | Manufacturer / MPN hiện có | Footprint hiện có |
|---|---|---|---|
| C2, C6, C8, C9, C10, C12, C13, C14, C15, C16, C18, C21, C24, C27, C29, C33, C38, C39, C40, C41, C45, C46 | 100nF | (chưa khai báo hãng) / `CL10B104KB8NFNC` | `Capacitor_SMD:C_0603_1608Metric` |
| C3, C4 | 22uF | (chưa khai báo hãng) / `CL31A226KOCLNNC` | `Capacitor_SMD:C_1206_3216Metric` |
| C5 | 56pF | (chưa khai báo hãng) / `C0603C560J5GACTU` | `Capacitor_SMD:C_0603_1608Metric` |
| C26 | 10nF | (chưa khai báo hãng) / `CL10B103KB8NFNC` | `Capacitor_SMD:C_0603_1608Metric` |
| C30 | 4.7uF | (chưa khai báo hãng) / `GRM21BR71C475KE51L` | `Capacitor_SMD:C_0805_2012Metric` |
| C35 | 4.7uF | Samsung Electro-Mechanics / `CL21A475KPFNNNE` | `Capacitor_SMD:C_0805_2012Metric` |
| C36 | 22uF | Samsung Electro-Mechanics / `CL31A226KOCLNNC` | `Capacitor_SMD:C_1206_3216Metric` |
| C42, C47, C48, C49, C87 | 1uF | (chưa khai báo hãng) / `GRM21BR71C105KA01L` | `Capacitor_SMD:C_0805_2012Metric` |
| C50, C53 | 10uF | (chưa khai báo hãng) / `GRM31CR71E106MA12L` | `Capacitor_SMD:C_1206_3216Metric` |
| D1 | PWR_LED | (chưa khai báo hãng) / `LTST-C190KGKT` | `LED_SMD:LED_0603_1608Metric` |
| D2, D3, D5 | SMF6V0A | (chưa khai báo hãng) / `SMF6V0A-M3-08` | `Diode_SMD:D_SMF` |
| J3 | USB_C_POWER_IN | (chưa khai báo hãng) / `TYPE-C-31-M-12` | `Connector_USB:USB_C_Receptacle_HRO_TYPE-C-31-M-12` |
| J5 | USB_C_PROG_DEBUG | (chưa khai báo hãng) / `TYPE-C-31-M-12` | `Connector_USB:USB_C_Receptacle_HRO_TYPE-C-31-M-12` |
| J8 | PWR_LED_EN | (chưa khai báo hãng) / `TSW-102-07-G-S` | `Connector_PinHeader_2.54mm:PinHeader_1x02_P2.54mm_Vertical` |
| J11 | USB_C_HS_DATA | (chưa khai báo hãng) / `TYPE-C-31-M-12` | `Connector_USB:USB_C_Receptacle_HRO_TYPE-C-31-M-12` |
| L2 | 2.2uH | Bourns / `SRP5030T-2R2M` | `Inductor_SMD:L_Bourns_SRP5030T` |
| L3 | 4.7uH | (chưa khai báo hãng) / `SRP6060FA-4R7M` | `Inductor_SMD:L_Bourns_SRP6060FA` |
| R2 | 30.1k | (chưa khai báo hãng) / `ERA-3AEB3012V` | `Resistor_SMD:R_0603_1608Metric` |
| R3, R4, R7, R8, R9 | 10k | (chưa khai báo hãng) / `ERJ-3EKF1002V` | `Resistor_SMD:R_0603_1608Metric` |
| R10, R11, R72, R111, R112 | 0 | (chưa khai báo hãng) / `ERJ-3GEY0R00V` | `Resistor_SMD:R_0603_1608Metric` |
| R22, R23 | 4.7k | (chưa khai báo hãng) / `ERJ-3EKF4701V` | `Resistor_SMD:R_0603_1608Metric` |
| R24 | 0.01 | PAKER / `RA2512F3R010G` | `Resistor_SMD:R_2512_6332Metric` |
| R25 | 93.1k | (chưa khai báo hãng) / `ERA-3AEB9312V` | `Resistor_SMD:R_0603_1608Metric` |
| R27, R28, R29, R30, R109, R110 | 5.1k | (chưa khai báo hãng) / `CRCW06035K10FKEA` | `Resistor_SMD:R_0603_1608Metric` |
| R31, R32 | 100k | (chưa khai báo hãng) / `CRCW0603100KFKEA` | `Resistor_SMD:R_0603_1608Metric` |
| R33 | 29.8k | (chưa khai báo hãng) / `RT0603BRD0729K8L` | `Resistor_SMD:R_0603_1608Metric` |
| R37, R71, R75 | 1k | (chưa khai báo hãng) / `ERJ-3EKF1001V` | `Resistor_SMD:R_0603_1608Metric` |
| R73 | 1M | (chưa khai báo hãng) / `ERJ-3EKF1004V` | `Resistor_SMD:R_0603_1608Metric` |
| R113 | 10k | (chưa khai báo hãng) / `CRCW060310K0FKEA` | `Resistor_SMD:R_0603_1608Metric` |
| R114 | 15k | (chưa khai báo hãng) / `CRCW060315K0FKEA` | `Resistor_SMD:R_0603_1608Metric` |
| SW1 | RESET | (chưa khai báo hãng) / `TL3305AF160QG` | `Button_Switch_SMD:SW_SPST_TL3305A` |
| SW2 | BOOT | (chưa khai báo hãng) / `TL3305AF160QG` | `Button_Switch_SMD:SW_SPST_TL3305A` |
| SW3 | C6_RESET | (chưa khai báo hãng) / `TL3305AF160QG` | `Button_Switch_SMD:SW_SPST_TL3305A` |
| SW4 | C6_BOOT | (chưa khai báo hãng) / `TL3305AF160QG` | `Button_Switch_SMD:SW_SPST_TL3305A` |
| SW5 | USER1 | (chưa khai báo hãng) / `TL3305AF160QG` | `Button_Switch_SMD:SW_SPST_TL3305A` |
| SW6 | USER2 | (chưa khai báo hãng) / `TL3305AF160QG` | `Button_Switch_SMD:SW_SPST_TL3305A` |
| U3, U13 | TPD2E2U06DCK | (chưa khai báo hãng) / `TPD2E2U06QDCKRQ1` | `Package_TO_SOT_SMD:SOT-323_SC-70` |
| U5 | TLV62569DBV | Texas Instruments / `TLV62569DBVR` | `Package_TO_SOT_SMD:SOT-23-5` |
| U6 | W25Q128JVSIQ | Winbond / `W25Q128JVSIQ` | `Package_SO:SOIC-8_5.3x5.3mm_P1.27mm` |
| U7 | TPS2121 | (chưa khai báo hãng) / `TPS2121RUXR` | `Package_DFN_QFN:Texas_VQFN-HR-12_2x2.5mm_P0.5mm` |
| U8 | INA226 | Texas Instruments / `INA226AIDGSR` | `Package_SO:TSSOP-10_3x3mm_P0.5mm` |
| U11 | AP63300WU | (chưa khai báo hãng) / `AP63300WU-7` | `Package_TO_SOT_SMD:TSOT-23-6` |
| Y1 | 40MHz | Abracon / `ABM8-40.000MHZ-10-1-U-T` | `Crystal:Crystal_SMD_3225-4Pin_3.2x2.5mm` |
