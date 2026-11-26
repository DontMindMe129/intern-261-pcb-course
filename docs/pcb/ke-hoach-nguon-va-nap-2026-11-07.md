# Chuẩn bị PCB: nguồn và nạp/debug

Ghi chú học đi dây: [Chọn kích thước và hình dạng đường đồng](kien-thuc-chon-va-di-duong-dong.md).

Trạng thái ngày 07/11/2026: **đang chuẩn bị layout**, chưa có PCB đã đặt linh kiện/đi dây. Schematic có 192 linh kiện; bảng CSV cùng thư mục lấy trực tiếp từ netlist hiện tại. Giá và tồn kho dưới đây chỉ là thông tin tại lúc tra cứu, cần kiểm tra lại trước khi đặt hàng. “Có listing” không đồng nghĩa hàng nằm trong kho tại Việt Nam; nhiều nguồn giao từ nước ngoài.

**Tiêu chí mua hàng đã thống nhất:** ưu tiên cửa hàng/kho ở TP.HCM nếu mua được đúng mã; shop Shopee có thể ở bất kỳ tỉnh/thành nào. Chỉ đánh dấu “có thể đặt” khi trang bán thể hiện đúng MPN và package, cho đặt số lượng cần thiết, có tình trạng hàng/thời gian giao rõ ràng và người bán đủ thông tin để kiểm tra. Với IC quan trọng, ưu tiên nhà phân phối có nguồn gốc rõ. Listing của đại lý tại TP.HCM nhưng hàng giao sau 2–3 tuần vẫn là “đặt qua đại lý”, không ghi là hàng sẵn ở TP.HCM. Chưa xác nhận bằng giỏ hàng/thanh toán thì trạng thái vẫn là ứng viên.

## Phạm vi layout đợt đầu

- Sheet `Power_USB`: J3 (USB-C nguồn ngoài), J5 (USB-C nạp/debug), D2/D3 TVS VBUS, U3 ESD dữ liệu, U7 chọn nguồn, R24 shunt, U8 đo dòng, U11 buck 3,3 V và linh kiện phụ thuộc.
- Sheet `ESP32_P4`: U4 cùng phần nguồn lõi U5/L2, mạch RESET/BOOT SW1/SW2, Flash U6 và thạch anh Y1. Đây là các khối cần đặt tương quan với đầu USB nạp và chân P4. Phần còn lại của board vẫn cần chừa diện tích và kết nối.

## Mã hàng đã có nguồn tra cứu

| Ref | Mã ứng viên | Nguồn | Tình trạng kiểm tra | Footprint / việc phải xác nhận |
|---|---|---|---|---|
| J3, J5 | TYPE-C-31-M-12 | [JLC Việt Nam](https://jlc.com.vn/linh-kien/usb-connectors/type-c-31-m-12-korean-hroparts-elec/) | Có listing, báo thời gian giao 2–3 tuần | KiCad có `Connector_USB:USB_C_Receptacle_HRO_TYPE-C-31-M-12`; pin symbol và pad đều gồm A/B và SH. Kiểm tra bản vẽ cơ khí đầu nối mua thực tế. |
| U7 | TPS2121RUXR | [Mouser Việt Nam](https://www.mouser.vn/vi/ProductDetail/Texas-Instruments/TPS2121RUXR?qs=sGAEpiMZZMv0DJfhVcWlK5dti4XbSOwo1POLEzioWHXv%252BFBorRHKkg%3D%3D) | Có listing | `Package_DFN_QFN:Texas_VQFN-HR-12_2x2.5mm_P0.5mm`; đối chiếu pad 1–12 với RUX. |
| U11 | AP63300WU-7 | [Mouser Việt Nam](https://www.mouser.vn/ProductDetail/Diodes-Incorporated/AP63300WU-7?qs=bZr6mbWTK5nNNXnHDcrFZg%3D%3D) | Có listing | Schematic ghi TSOT-23-6; kiểm tra sơ đồ chân và pad số. |
| U8 | INA226AIDGSR | [element14 Việt Nam](https://vn.element14.com/texas-instruments/ina226aidgsr/current-sense-amplifier-3-5khz/dp/3005354) | Có listing | Hãng ghi **VSSOP-10 DGS**; schematic đang gán `TSSOP-10_3x3mm_P0.5mm`. So kích thước pad/lead theo bản vẽ DGS trước khi dùng. |
| U3 | TPD2E2U06DCKR | [Mouser Việt Nam](https://www.mouser.vn/vi/c/circuit-protection/esd-protection-diodes-tvs-diodes/?m=Texas+Instruments&series=TPD2E2U06) | Listing hiện báo hết hàng | DCK là SC-70-3; footprint đang gán có 3 pad, cần đối chiếu pinout. Tìm nguồn khác trước khi khóa BOM. |
| D2, D3 | SMF6V0A-M3-08 | [Mouser Việt Nam](https://www.mouser.vn/ProductDetail/Vishay-Semiconductors/SMF6V0A-M3-08?qs=liFJyUJTUDEpN6KM4i%2FVgg%3D%3D) | Có listing, mã Vishay DO-219AB | Phù hợp loại package mà `Diode_SMD:D_SMF` mô tả; kiểm tra chiều cathode. Ứng viên Littelfuse SMF6.0A trước đó dùng SOD-123F nên không chốt với footprint hiện tại. |
| U5 | TLV62569DBVR | [JLC Việt Nam, mục TI](https://jlc.com.vn/hang/texas-instruments/) | Có trong danh mục; chưa xác minh trang mã riêng | SOT-23-5 đang gán trong schematic. |

Chưa chốt mã cho U4, L2/L3, R24, tụ nguồn, nút nhấn, LED và phần lớn điện trở. Không thay `U4` bằng một mã ESP32-P4 chỉ vì cùng package: cần xác nhận biến thể silicon/flash/PSRAM với kiến trúc đã chọn. Các hàng này trong CSV được đánh dấu chưa chốt, không coi là BOM mua hàng hoàn chỉnh.

## Trình tự placement

1. Chốt cạnh board và khoảng cách hai cổng USB-C; đặt J3/J5 trên mép board theo bản vẽ cơ khí. Đặt D2/D3 gần đầu vào tương ứng. Đặt U3 sát J5 trên đường D+/D− và chừa đường GND ngắn cho ESD.
2. Đặt U7 giữa hai nguồn vào và `5V_MUX`, kèm tụ đầu vào/đầu ra sát chân. Đặt R24 trên đường sang `5V_SYS`; hai đường Kelvin tới U8 lấy trực tiếp từ pad shunt, tránh dùng cùng đường đồng mang dòng tải.
3. Đặt U11, L3, tụ đầu vào/đầu ra và cầu hồi tiếp theo sơ đồ layout trong datasheet AP63300. Giữ nút SW nhỏ, đường hồi tiếp tránh SW. Đặt tụ bootstrap sát chân tương ứng.
4. Đặt U4 trong vùng còn lại theo các đầu nối và bus tốc độ cao. U5/L2 và tụ nguồn lõi đặt gần các chân nguồn P4; đặt Flash U6 và Y1 gần U4. SW1/SW2 ở vị trí dễ bấm, đường RESET/BOOT không chạy sát nút SW của buck.
5. Đặt và đi các đường nguồn/dòng cao, GND, USB D+/D−, nguồn lõi, Flash/clock trước. Sau khi cả board hoàn chỉnh mới có thể kết luận DRC và các đường chưa nối.

## Điểm chặn trước khi đồng bộ sang PCB

- KiCad đang mở project (có file `.lck`), nên chưa sửa trực tiếp schematic/PCB trong phiên này để tránh ghi đè trạng thái đang mở.
- Chốt chính xác J3/J5, U7 và các footprint chưa gán; xác nhận kích thước board/lỗ gá và vị trí các đầu nối còn lại để không phải chuyển khối nguồn sau khi layout.
- ERC trên bản schematic hiện tại báo **53 mục**: 25 chân chưa nối, 12 đầu dây hở, 6 net nhiều tên, 5 nhãn cô lập, 3 chân nguồn chưa có nguồn lái, 1 điểm lệch lưới và 1 cảnh báo kiểu chân. Cần rà từng mục trước khi coi schematic sẵn sàng sản xuất. Báo cáo thô: `tmp/current-pcb-planning-erc.json`.
