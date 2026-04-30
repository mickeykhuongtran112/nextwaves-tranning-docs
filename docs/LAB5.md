# LAB 05 - Embedded IO Expansion Board Rev-A

## 1. Mục tiêu

Thiết kế một board **Embedded IO Expansion Board Rev-A** dùng KiCad 9.

Board này tổng hợp các khối đã học từ các lab trước:

- Power Block 3.3V
- Digital Input/Output
- I2C Communication
- MOSFET Load Driver
- Custom Library
- PCB Layout 2 lớp
- BOM
- Gerber Release

Sau bài lab này, bạn cần nắm được:

- Thiết kế một board embedded IO hoàn chỉnh
- Quản lý schematic theo nhiều khối chức năng
- Tái sử dụng và quản lý custom library
- Thiết kế nguồn đầu vào 9-12V xuống 3.3V
- Thiết kế digital input/output
- Thiết kế I2C connector
- Thiết kế MOSFET low-side driver
- Tạo net class cho signal, power và load current
- Layout PCB 2 lớp theo constraint
- Chạy ERC/DRC
- Xuất BOM, Gerber, Drill
- Đóng gói release package để gửi review/sản xuất

---

## 2. Yêu cầu thiết kế

### 2.1. Chức năng tổng thể

Thiết kế một board mở rộng IO cho MCU ngoài.

Board nhận nguồn từ **DC Jack 9-12V**, hạ xuống **3.3V**, sau đó cung cấp các khối IO cơ bản cho MCU:

```text
DC Jack 9-12V
    |
    v
Power Protection + LDO 3.3V
    |
    +---- Digital Input Buttons
    +---- LED Outputs
    +---- I2C Connector
    +---- UART Connector
    +---- MOSFET Load Driver
    +---- Test Points
```

### 2.2. Thông số hoạt động

| Hạng mục | Yêu cầu |
|---|---|
| Input voltage | 9-12V DC |
| Logic voltage | 3.3V |
| Load voltage | 5-12V |
| Load current giả định | <= 500mA |
| PCB layer | 2 lớp |
| Board size | Tối đa 70mm x 50mm |

---

## 3. Connector yêu cầu

Board cần có tối thiểu **6 connector**:

| Connector | Tên | Chức năng |
|---|---|---|
| J1 | DC Power Input | Cấp nguồn 9-12V |
| J2 | MCU Main Interface | Kết nối tín hiệu chính về MCU |
| J3 | I2C Connector | Kết nối thiết bị I2C |
| J4 | UART Connector | Kết nối UART |
| J5 | Load Power Input | Cấp nguồn tải |
| J6 | Load Output | Kết nối tải MOSFET |

### J1 - DC Power Input

| Pin | Net |
|---:|---|
| 1 | VIN_RAW |
| 2 | GND |

### J2 - MCU Main Interface

| Pin | Net |
|---:|---|
| 1 | 3V3 |
| 2 | GND |
| 3 | BTN1 |
| 4 | BTN2 |
| 5 | LED1_CTRL |
| 6 | LED2_CTRL |
| 7 | SDA |
| 8 | SCL |
| 9 | UART_TX |
| 10 | UART_RX |
| 11 | MOS_CTRL |
| 12 | GND |

### J3 - I2C Connector

| Pin | Net |
|---:|---|
| 1 | 3V3 |
| 2 | GND |
| 3 | SDA |
| 4 | SCL |

### J4 - UART Connector

| Pin | Net |
|---:|---|
| 1 | 3V3 |
| 2 | GND |
| 3 | UART_TX |
| 4 | UART_RX |

### J5 - Load Power Input

| Pin | Net |
|---:|---|
| 1 | VLOAD |
| 2 | GND |

### J6 - Load Output

| Pin | Net |
|---:|---|
| 1 | VLOAD |
| 2 | LOAD_SW |

Lưu ý:

- `GND` logic và `GND` tải phải nối chung trên board.
- `3V3` được tạo từ LDO trên board.
- `VLOAD` là nguồn riêng cấp cho tải, không đi qua LDO 3.3V.

---

## 4. Linh kiện yêu cầu

| Ref | Linh kiện | Package đề xuất | Ghi chú |
|---|---|---|---|
| J1 | DC Jack | Theo datasheet | Input 9-12V |
| F1 | PPTC Fuse | 1206 / 1812 | Bảo vệ quá dòng |
| D1 | Schottky Diode | SOD-123 / SMA / SMB | Chống ngược cực |
| U1 | AMS1117-3.3 hoặc LDO tương đương | SOT-223 / SOT-23-5 | Tạo 3.3V |
| C1 | Input capacitor | 0805 / 1206 | 10uF |
| C2 | Input bypass capacitor | 0603 / 0805 | 100nF |
| C3 | Output capacitor | 0805 / 1206 | 10uF |
| C4 | Output bypass capacitor | 0603 / 0805 | 100nF |
| SW1, SW2 | Button SMD | SMD tact switch | Digital input |
| R1, R2 | Pull resistor | 0603 / 0805 | Pull-up/pull-down |
| D2, D3 | User LED | 0603 / 0805 | LED output |
| R3, R4 | LED resistor | 0603 / 0805 | Hạn dòng LED |
| R5, R6 | I2C pull-up | 0603 / 0805 | SDA/SCL pull-up |
| JP1, JP2 | Solder jumper | SMD jumper | Bật/tắt pull-up I2C |
| Q1 | Logic-level N-MOSFET | SOT-23 | Low-side driver |
| R7 | Gate resistor | 0603 / 0805 | 47R - 220R |
| R8 | Gate pull-down | 0603 / 0805 | 47k - 100k |
| D4 | Flyback diode | SOD-123 / SMA | Bảo vệ tải cảm |
| D5 | Power LED 3.3V | 0603 / 0805 | Báo nguồn logic |
| R9 | Power LED resistor | 0603 / 0805 | 1k - 2.2k |
| TPx | Test point | SMD test pad | Debug nguồn và tín hiệu |

---

## 5. Yêu cầu Library

Bạn cần có tối thiểu **2 custom symbol/footprint** trong project.

Bắt buộc có:

| Linh kiện | Yêu cầu |
|---|---|
| DC Jack | Custom symbol và footprint |
| LDO | Custom symbol và footprint |

Chọn thêm ít nhất 1 linh kiện để tự tạo hoặc chỉnh sửa footprint:

| Option | Yêu cầu |
|---|---|
| Option A | MOSFET SOT-23 |
| Option B | I2C connector |
| Option C | Load connector |
| Option D | Solder jumper |

Thư viện phải đặt trong project:

```text
lib/
├── symbols/
│   └── custom_embedded_io_symbols.kicad_sym
└── footprints/
    └── custom_embedded_io_footprints.pretty/
```

### Yêu cầu chung cho footprint custom

- Đúng kích thước theo datasheet
- Đúng pad numbering
- Có silkscreen outline
- Có courtyard
- Có pin 1 marker nếu linh kiện cần phân cực/hướng
- Không để silkscreen đè pad

---

## 6. Yêu cầu Schematic

Schematic cần chia thành các khối rõ ràng:

```text
1. Power Input & Protection
2. 3.3V LDO Power
3. Digital Input Buttons
4. LED Outputs
5. I2C Interface
6. UART Interface
7. MOSFET Load Driver
8. Test Points
```

### 6.1. Power Block

Bắt buộc có:

1. DC Jack input 9-12V.
2. PPTC fuse.
3. Schottky diode chống ngược cực.
4. LDO 3.3V.
5. Tụ input:
   - 10uF
   - 100nF
6. Tụ output:
   - 10uF
   - 100nF
7. LED báo nguồn 3.3V.
8. Test point:
   - `VIN_RAW`
   - `VIN_PROTECTED`
   - `3V3`
   - `GND`

### 6.2. Digital Input Block

Bắt buộc có:

1. 2 button SMD.
2. Mỗi button có pull-up hoặc pull-down.
3. Tín hiệu output:
   - `BTN1`
   - `BTN2`
4. Test point:
   - `BTN1`
   - `BTN2`

### 6.3. LED Output Block

Bắt buộc có:

1. 2 LED output điều khiển từ MCU.
2. Mỗi LED có điện trở hạn dòng.
3. Tín hiệu điều khiển:
   - `LED1_CTRL`
   - `LED2_CTRL`

### 6.4. I2C Block

Bắt buộc có:

1. I2C connector `J3`.
2. Pull-up resistor cho `SDA` và `SCL`.
3. Solder jumper bật/tắt pull-up.
4. Test point:
   - `SDA`
   - `SCL`

### 6.5. UART Block

Bắt buộc có:

1. UART connector `J4`.
2. Tín hiệu:
   - `UART_TX`
   - `UART_RX`
3. Test point:
   - `UART_TX`
   - `UART_RX`

### 6.6. MOSFET Load Driver Block

Bắt buộc có:

1. N-MOSFET logic-level.
2. Gate resistor.
3. Gate pull-down resistor.
4. Flyback diode.
5. Load power input `J5`.
6. Load output `J6`.
7. Tín hiệu điều khiển:
   - `MOS_CTRL`
8. Switching node:
   - `LOAD_SW`
9. Test point:
   - `VLOAD`
   - `MOS_CTRL`
   - `LOAD_SW`

### 6.7. Net name bắt buộc

| Net | Ý nghĩa |
|---|---|
| `VIN_RAW` | Nguồn sau DC Jack |
| `VIN_FUSED` | Nguồn sau PPTC |
| `VIN_PROTECTED` | Nguồn sau diode Schottky |
| `3V3` | Nguồn logic |
| `GND` | Ground chung |
| `BTN1` | Button input 1 |
| `BTN2` | Button input 2 |
| `LED1_CTRL` | Điều khiển LED 1 |
| `LED2_CTRL` | Điều khiển LED 2 |
| `SDA` | I2C data |
| `SCL` | I2C clock |
| `UART_TX` | UART transmit |
| `UART_RX` | UART receive |
| `VLOAD` | Nguồn tải |
| `MOS_CTRL` | Điều khiển MOSFET |
| `LOAD_SW` | Switching node của tải |

---

## 7. Yêu cầu Layout PCB

| Hạng mục | Yêu cầu |
|---|---|
| PCB layer | 2 lớp |
| Board size | Tối đa 70mm x 50mm |
| Track signal | 8-12 mil |
| Track 3V3 power | Tối thiểu 20 mil |
| Track VIN/VLOAD | Tối thiểu 30-40 mil |
| Clearance | Tối thiểu 8 mil |
| GND Plane | Bắt buộc |
| Test point | Đặt ở top side |
| DRC | Pass |

### Net class yêu cầu

Tạo tối thiểu 4 net class:

| Net Class | Net áp dụng | Width đề xuất | Clearance |
|---|---|---:|---:|
| Signal | `BTNx`, `LEDx_CTRL`, `SDA`, `SCL`, `UART_TX`, `UART_RX`, `MOS_CTRL` | 8-12 mil | 8 mil |
| Logic_Power | `3V3` | >= 20 mil | 8 mil |
| Input_Power | `VIN_RAW`, `VIN_FUSED`, `VIN_PROTECTED` | >= 30 mil | 8-10 mil |
| Load_Current | `VLOAD`, `LOAD_SW` | >= 30-40 mil | 8-10 mil |

### Quy tắc placement

- J1 DC Power Input đặt sát mép board
- Power protection đặt gần J1
- LDO và tụ input/output đặt gần nhau
- Tab LDO cần vùng copper hỗ trợ tản nhiệt
- J2 MCU Main Interface đặt sát mép board, dễ kết nối
- J3 I2C Connector đặt sát mép board
- J4 UART Connector đặt sát mép board
- J5/J6 load connector đặt gần MOSFET
- MOSFET đặt gần load connector
- Gate resistor đặt gần gate MOSFET
- Flyback diode đặt gần load output hoặc MOSFET
- Button đặt dễ thao tác
- LED đặt dễ quan sát
- Test point đặt ở top side, dễ đo
- Đường dòng tải phải ngắn và rộng
- GND plane phủ toàn mạch
- Silkscreen không được đè pad
- Silkscreen phải ghi rõ connector, chiều nguồn, tín hiệu chính

---

## 8. Gerber Release Requirement

Sau khi hoàn thành layout, bạn cần xuất file sản xuất.

Bắt buộc có:

```text
Gerber files
Drill files
BOM
Schematic PDF
PCB top image
PCB bottom image
PCB 3D image
ERC report
DRC report
```

Bạn cần mở lại Gerber bằng **Gerber Viewer** để kiểm tra:

```text
[ ] Board outline đúng
[ ] Top copper đúng
[ ] Bottom copper đúng
[ ] Solder mask đúng
[ ] Silkscreen không bị lỗi quan trọng
[ ] Drill đúng vị trí
[ ] Không thiếu layer quan trọng
```

---

## 9. Cấu trúc thư mục nộp bài

Bạn cần nộp project theo cấu trúc sau:

```text
HW05_Embedded_IO_Expansion_RevA/
├── datasheet/
│   ├── dc_jack_datasheet.pdf
│   ├── ldo_datasheet.pdf
│   ├── mosfet_datasheet.pdf
│   ├── diode_datasheet.pdf
│   └── connector_datasheet.pdf
├── docs/
│   ├── README.md
│   └── release_note.md
├── lib/
│   ├── symbols/
│   │   └── custom_embedded_io_symbols.kicad_sym
│   └── footprints/
│       └── custom_embedded_io_footprints.pretty/
├── output/
│   ├── pdf/
│   │   └── schematic.pdf
│   ├── bom/
│   │   └── bom.csv
│   ├── gerber/
│   ├── drill/
│   ├── images/
│   │   ├── pcb_top.png
│   │   ├── pcb_bottom.png
│   │   └── pcb_3d.png
│   ├── erc_report.txt
│   └── drc_report.txt
├── HW05_Embedded_IO_Expansion_RevA.kicad_pro
├── HW05_Embedded_IO_Expansion_RevA.kicad_sch
└── HW05_Embedded_IO_Expansion_RevA.kicad_pcb
```

---

## 10. README yêu cầu

File `README.md` cần có các mục sau:

```markdown
# HW05 Embedded IO Expansion Board Rev-A

## 1. Project Description

Mô tả ngắn chức năng board.

## 2. Board Specification

- Input voltage:
- Logic voltage:
- Load voltage:
- Load current:
- PCB layer:
- Board size:
- LDO used:
- MOSFET used:

## 3. Block Diagram

Mô tả hoặc vẽ sơ đồ khối:

- Power Block
- Digital Input
- LED Output
- I2C
- UART
- MOSFET Driver

## 4. Connector Pinout

### J1 - DC Power Input

| Pin | Net | Description |
|---:|---|---|
| 1 | VIN_RAW | DC input 9-12V |
| 2 | GND | Ground |

### J2 - MCU Main Interface

| Pin | Net | Description |
|---:|---|---|
| 1 | 3V3 | Logic power |
| 2 | GND | Ground |
| 3 | BTN1 | Button input 1 |
| 4 | BTN2 | Button input 2 |
| 5 | LED1_CTRL | LED control 1 |
| 6 | LED2_CTRL | LED control 2 |
| 7 | SDA | I2C data |
| 8 | SCL | I2C clock |
| 9 | UART_TX | UART transmit |
| 10 | UART_RX | UART receive |
| 11 | MOS_CTRL | MOSFET control |
| 12 | GND | Ground |

### J3 - I2C Connector

| Pin | Net | Description |
|---:|---|---|
| 1 | 3V3 | I2C power |
| 2 | GND | Ground |
| 3 | SDA | I2C data |
| 4 | SCL | I2C clock |

### J4 - UART Connector

| Pin | Net | Description |
|---:|---|---|
| 1 | 3V3 | Logic power |
| 2 | GND | Ground |
| 3 | UART_TX | UART transmit |
| 4 | UART_RX | UART receive |

### J5 - Load Power Input

| Pin | Net | Description |
|---:|---|---|
| 1 | VLOAD | Load supply |
| 2 | GND | Ground |

### J6 - Load Output

| Pin | Net | Description |
|---:|---|---|
| 1 | VLOAD | Load positive |
| 2 | LOAD_SW | Low-side switched node |

## 5. Custom Library

Liệt kê symbol/footprint tự tạo hoặc chỉnh sửa:

- DC Jack:
- LDO:
- Additional custom component:

## 6. Symbol-Footprint Mapping

| Component | Symbol Pin | Pin Name | Footprint Pad | Note |
|---|---:|---|---:|---|
| LDO | 1 | GND/ADJ | 1 | Ground |
| LDO | 2 | VOUT | 2 | 3.3V output |
| LDO | 3 | VIN | 3 | Protected input |
| LDO | TAB | VOUT | TAB | Thermal tab |
| MOSFET | 1 | GATE | 1 | Control input |
| MOSFET | 2 | SOURCE | 2 | Ground |
| MOSFET | 3 | DRAIN | 3 | Load switching node |

## 7. Design Notes

Giải thích ngắn:

- Power protection: PPTC + Schottky diode
- Vai trò tụ input/output của LDO
- Digital input pull-up/pull-down
- I2C pull-up và jumper bật/tắt
- MOSFET low-side driver
- Flyback diode
- Net class và track width
- GND plane
- Các điểm cần chú ý khi layout

## 8. ERC/DRC Result

Ghi kết quả ERC/DRC.

## 9. Gerber Check Result

Ghi kết quả kiểm tra Gerber Viewer.

## 10. Known Issues

Ghi rõ nếu còn điểm chưa chắc chắn hoặc rủi ro thiết kế.
```

---

## 11. Release Note yêu cầu

File `release_note.md` cần có:

```markdown
# Release Note - HW05 Embedded IO Expansion Board Rev-A

## Version

Rev-A

## Release Files

- Schematic PDF:
- BOM:
- Gerber folder:
- Drill folder:
- PCB images:
- ERC report:
- DRC report:

## Manufacturing Notes

- PCB layer:
- PCB thickness:
- Copper thickness:
- Surface finish:
- Minimum trace/clearance:
- Board size:

## Known Issues

Liệt kê lỗi/rủi ro còn tồn tại nếu có.

## Review Checklist

- [ ] ERC pass
- [ ] DRC pass
- [ ] Gerber opened and checked
- [ ] Board outline correct
- [ ] Drill file correct
- [ ] BOM exported
- [ ] Connector pinout checked
- [ ] Silkscreen checked
```

---

## 12. Kết quả cần đạt

Sau khi hoàn thành, bạn cần có:

- Project KiCad mở được đầy đủ
- Schematic chia block rõ ràng
- Có Power Block 9-12V xuống 3.3V
- Có protection: PPTC + Schottky diode
- Có digital input 2 button
- Có LED output 2 channel
- Có I2C connector và pull-up
- Có UART connector
- Có MOSFET low-side driver
- Có flyback diode
- Có tối thiểu 6 connector đúng pinout
- Có test point cho nguồn và tín hiệu chính
- Có tối thiểu 2 custom symbol/footprint trong thư mục `lib/`
- Có net class cho signal, power, load current
- ERC không có lỗi nghiêm trọng
- PCB layout 2 lớp đúng constraint
- Track dòng nguồn/tải đúng yêu cầu
- Có GND plane
- DRC pass
- Có BOM
- Có Gerber và Drill
- Có kiểm tra Gerber Viewer
- Có ảnh PCB top/bottom/3D
- Có README và release note

---

## 13. Tiêu chí đánh giá

| Tiêu chí | Điểm |
|---|---:|
| Project structure đúng | 10 |
| Schematic chia block rõ, đúng nguyên lý | 20 |
| Custom library đúng | 15 |
| Connector và pinout rõ ràng | 10 |
| Net class và track width đúng | 10 |
| Layout PCB hợp lý | 15 |
| ERC/DRC pass | 10 |
| Gerber/Drill release đầy đủ | 5 |
| README và release note rõ ràng | 5 |

Tổng điểm: **100**

---

## 14. Checklist trước khi nộp

```text
[ ] Project mở được trên KiCad 9.
[ ] Có đầy đủ datasheet sử dụng.
[ ] Có tối thiểu 2 custom symbol/footprint.
[ ] Pin symbol khớp pad footprint.
[ ] Có J1 DC Power Input.
[ ] Có J2 MCU Main Interface.
[ ] Có J3 I2C Connector.
[ ] Có J4 UART Connector.
[ ] Có J5 Load Power Input.
[ ] Có J6 Load Output.
[ ] Power Block có PPTC, Schottky diode, LDO, tụ input/output.
[ ] LDO tạo ra net 3V3 đúng.
[ ] Có LED báo nguồn 3.3V.
[ ] Có 2 button input.
[ ] Có pull-up/pull-down cho button.
[ ] Có 2 LED output.
[ ] Có I2C pull-up cho SDA/SCL.
[ ] Có jumper bật/tắt pull-up I2C.
[ ] Có UART_TX và UART_RX.
[ ] Có MOSFET low-side driver.
[ ] MOSFET có gate resistor.
[ ] MOSFET có gate pull-down.
[ ] Có flyback diode đúng chiều.
[ ] Có test point cho nguồn và tín hiệu chính.
[ ] Net name đúng yêu cầu.
[ ] Có net class Signal, Logic_Power, Input_Power, Load_Current.
[ ] Track VIN/VLOAD đạt tối thiểu 30-40 mil.
[ ] Track 3V3 đạt tối thiểu 20 mil.
[ ] ERC không có lỗi nghiêm trọng.
[ ] PCB 2 lớp.
[ ] Có GND plane.
[ ] Silkscreen không đè pad.
[ ] Connector có label rõ.
[ ] DRC pass.
[ ] Có schematic PDF.
[ ] Có BOM.
[ ] Có Gerber.
[ ] Có Drill.
[ ] Đã kiểm tra Gerber bằng Gerber Viewer.
[ ] Có ảnh PCB top/bottom/3D.
[ ] README đầy đủ.
[ ] release_note.md đầy đủ.
```