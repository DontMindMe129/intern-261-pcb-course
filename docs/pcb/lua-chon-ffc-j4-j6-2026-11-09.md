# Đầu FFC cho J4/J6 — đề xuất ngày 09/11/2026

Đề xuất dùng **Amphenol SFW15R-1STE1LF**, thay Molex 200528-0150. Đây là đề xuất đã chuẩn bị footprint, chưa gán vào schematic và chưa cập nhật BOM thành mã chính thức.

## Cơ sở chọn

- Giữ 15 chân, bước 1,00 mm, gắn ngang SMT, tiếp xúc phía dưới và cáp FFC/FPC dày 0,30 mm.
- Hãng công bố dòng định mức dòng SFW-R là 1,0 A; giữ mức định mức của đầu Molex đang chọn. Dòng thực tế còn phụ thuộc cáp, mạch nguồn và tải, không phải chứng nhận toàn bộ đường cấp nguồn chịu được 1 A.
- LCSC có mã **C3168538**; trang danh mục hãng lúc tra cứu báo có hàng và giá từ số lượng 1. Nguồn dự phòng DigiKey có bảng giá Cut Tape từ 1 cái. Chưa xác nhận bằng checkout hoặc báo giá đại lý tại Việt Nam.
- Không dùng JUSHUO AFA07-S15FCA-00 làm phương án chính vì datasheet dòng AFA07 công bố 0,5 A. TE 1-84952-5 có footprint thư viện và 1 A nhưng Mouser hiện ghi hết hàng, đơn nhập dự kiến 27/11/2026. Không nhầm TE 84952-5 (khác mã) với 1-84952-5.

Nguồn mua: [LCSC C3168538](https://www.lcsc.com/product-detail/C3168538.html), [danh mục Amphenol tại LCSC có bảng tồn/giá](https://www.lcsc.com/brand/755-815.html), [DigiKey](https://www.digikey.com/en/products/detail/amphenol-cs-fci/SFW15R-1STE1LF/1002220).

Nguồn kỹ thuật: [trang chính hãng](https://www.amphenol-cs.com/product/sfw15r1ste1lf.html), [datasheet SFW-R](https://cdn.amphenol-cs.com/media/wysiwyg/files/documentation/datasheet/flex/flexconnectors_100mm_sfw_r.pdf), [bản vẽ 10172241 rev A](https://cdn.amphenol-cs.com/media/wysiwyg/files/drawing/10172241.pdf). Bản vẽ nhà sản xuất tải qua LCSC được lưu tại `references/datasheets/Amphenol_SFW-R_10172241_revA.pdf`.

## Footprint đã chuẩn bị

`DevKit:Amphenol_SFW15R-1STE1LF_1x15-1MP_P1.00mm_Horizontal`

Nằm trong thư viện DevKit đã đăng ký của project. Đây là footprint khác với Molex; không giữ footprint cũ khi đổi mã.

Kiểm tra theo bản vẽ 10172241 rev A, sheet 2 và 5:

- 15 pad tín hiệu 0,60 × 2,00 mm, bước 1,00 mm, khoảng cách tâm pad 1–15 là 14,00 mm.
- Hai pad giữ cơ khí MP 0,70 × 4,20 mm, khoảng cách tâm hai pad 16,00 mm; tọa độ tâm so với hàng pad tín hiệu là x = ±8,00 mm, y = +2,10 mm.
- Pad 1 ở trái khi nhìn từ mặt linh kiện với hướng cáp ra phía +Y. Không đảo số chân symbol.
- Kích thước bao ngoài thân đóng: 20,80 × 5,50 mm; chiều cao 2,70 mm. Fab biểu diễn bao ngoài để bố trí, không mô hình hóa mọi chi tiết vỏ.
- Chừa 1,80 mm cho cơ cấu trượt mở khóa; courtyard bao gồm khoảng mở này và thêm biên 0,50 mm. Khi bố trí còn phải chừa chỗ thao tác tay và đường cáp.
- KiCad tải được footprint, có đủ pad 1–15 và hai pad MP; chưa có mẫu vật hoặc kiểm chứng lắp ráp thực tế. Chưa có mô hình 3D kèm theo.

## Kết nối schematic hiện tại được giữ

| Chân | J4 camera | J6 display |
|---|---|---|
| 1 | GND | GND |
| 2 | CSI_DATA0N | DSI_DATA1N |
| 3 | CSI_DATA0P | DSI_DATA1P |
| 4 | GND | GND |
| 5 | CSI_DATA1N | DSI_CLKN |
| 6 | CSI_DATA1P | DSI_CLKP |
| 7 | GND | GND |
| 8 | CSI_CLKN | DSI_DATA0N |
| 9 | CSI_CLKP | DSI_DATA0P |
| 10 | GND | GND |
| 11 | CAM_RESET | I2C_SCL |
| 12 | Không nối | I2C_SDA |
| 13 | I2C_SCL | GND |
| 14 | I2C_SDA | 3V3 |
| 15 | 3V3 | 3V3 |

Bảng lấy từ netlist `tmp/procurement-current.xml`. Việc khớp số chân đầu nối không chứng minh tương thích với mọi camera/màn hình 15 chân. Khi chọn module/cáp phải đối chiếu pinout hai đầu, chiều mặt đồng và thứ tự chân; không mặc định cắm trực tiếp mọi module Raspberry Pi.

## Metadata để người dùng gán cho cả J4/J6

- MPN: `SFW15R-1STE1LF`
- Manufacturer: `Amphenol Communications Solutions`
- Datasheet: `https://cdn.amphenol-cs.com/media/wysiwyg/files/drawing/10172241.pdf`
- Footprint: `DevKit:Amphenol_SFW15R-1STE1LF_1x15-1MP_P1.00mm_Horizontal`
- Giữ nguyên Value và toàn bộ dây nối.

Sau khi người dùng gán và lưu: xuất lại netlist để kiểm tra chỉ thay metadata, cập nhật BOM nguồn LCSC/DigiKey rồi mới Update PCB from Schematic.
