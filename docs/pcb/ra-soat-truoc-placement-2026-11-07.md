# Rà soát trước placement nguồn và nạp/debug — 07/11/2026

Bản này đọc từ `tmp/current-pcb-planning.xml` và `tmp/current-pcb-planning-erc.json`; không thay đổi schematic hoặc PCB đang mở trong KiCad. BOM CSV cũng đang được một ứng dụng khác mở nên các ứng viên mới dưới đây **chưa ghi vào CSV**.

## Kết nối đã đối chiếu trên netlist

- J5 D+/D− đi qua R11/R10 (0 Ω) tới chân USB Serial/JTAG U4.53/U4.52; U3 nối ở phía đầu USB.
- J3 VBUS tới U7.7; J5 VBUS tới U7.2. U7 đầu ra `5V_MUX` đi qua R24 10 mΩ tới `5V_SYS` và VIN của U11.
- Chân U4.49 `USB-DM` bị ERC báo chưa nối là chân USB PHY khác với U4.52 `USB_JTAG_DM`; cần xác nhận ý định để hở khi rà ERC, không tự nối sang J5.
- ERC riêng `Power_USB`: 2 mục (U7 ST nối cờ nguồn GND; U11 VIN báo thiếu nguồn lái vì đường cấp qua shunt). ERC `ESP32_P4`: 11 mục, gồm các nhãn nhiều tên và `VDD_USBPHY` sau R72 0 Ω. Cần review nhưng các mục này không tự chứng minh mạch nạp bị ngắt.

## Ứng viên mới cần thêm vào BOM sau khi CSV được đóng

| Ref | Mã ứng viên | Nguồn | Lý do / điều kiện chốt |
|---|---|---|---|
| R24 | RA2512F3R010G | [JLC Việt Nam](https://jlc.com.vn/hang/paker/), [thông số LCSC](https://www.lcsc.com/es/product-detail/C49652793.html) | 10 mΩ, 2512, 3 W, 1%; kiểm tra bản vẽ pad và lấy hai đường đo Kelvin trực tiếp từ hai đầu điện trở. |
| L3 | SRP4021HMT-4R7M | [Mouser Việt Nam](https://www.mouser.vn/en/ProductDetail/Bourns/SRP4021HMT-4R7M?qs=bN4HulGPsrHCgJpuHnPJ5g%3D%3D), [datasheet Bourns](https://www.bourns.com/docs/product-datasheets/SRP4021HMT.pdf) | 4,7 µH, Irms 4,5 A, Isat 4,2 A; kiểm tra dòng gợn/đỉnh và footprint theo bản vẽ hãng trước khi chốt. |

Không chọn cuộn CKCSA4020-4.7uH/M dù có listing tại JLC: datasheet ghi dòng nhiệt 2,85 A điển hình, dưới mức 3 A mục tiêu của buck. [Datasheet CENKER](https://www.cenkersz.com/file/upload/products/CKCSAPDF.pdf). Datasheet AP63300 khuyến nghị cuộn cảm có dòng DC ít nhất 35% trên dòng tải tối đa; nếu thiết kế tới 3 A, mốc sơ bộ là 4,05 A. [Datasheet Diodes Incorporated](https://www.diodes.com/datasheet/download/AP63300_AP63301.pdf).

## Cần chốt trước khi đặt footprint lên board

1. Mã thực mua và footprint của J3/J5, U7, U8, U11, R24, L3; đặc biệt so bản vẽ VSSOP-10 DGS của U8 với footprint TSSOP-10 đang gán.
2. Kích thước board sơ bộ, cạnh đặt J3/J5, vị trí lỗ gá và khoảng trống cho RJ45, camera/màn hình và các header.
3. Các tụ 22 µF/10 µF/1 µF ở đầu vào và đầu ra: điện áp định mức, điện dung hiệu dụng theo DC bias, package. Chỉ sau đó mới khóa vòng nguồn U11–L3–tụ.

Sau các điểm này có thể bắt đầu **placement** khối nguồn và nạp, chưa cần chốt MPN cho toàn bộ 192 linh kiện.
