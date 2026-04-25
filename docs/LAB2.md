# LAB 02 - Digital Input/Output & Logic Conditioning Block
> Mức độ: Trung bình (3-4 ngày)
  
> [Link Submit](https://forms.office.com/Pages/ResponsePage.aspx?id=dMsRDwTit0uX3TG3jEPS1AShXtfWh3tElWV0mV3zb55UQzIzVFZENVhGWVZKUVhHMUhVS0QyVUhISi4u)

## 1. Mục tiêu

Thiết kế một mạch **Digital Input/Output Block** dùng KiCad 9.

Mạch có nhiệm vụ nhận tín hiệu từ nút nhấn, xử lý qua IC logic **74HC14 Schmitt Trigger**, sau đó đưa tín hiệu sạch ra connector để MCU đọc.

Sau bài lab này, bạn cần nắm được:

- Vẽ schematic nhiều khối chức năng
- Sử dụng IC logic SMD
- Cấp nguồn đúng cho IC logic
- Hiểu pull-up/pull-down cho input
- Hiểu debounce cơ bản bằng RC
- Tạo hoặc chỉnh sửa symbol/footprint cho linh kiện SMD
- Tạo BOM đầy đủ
- Layout PCB 2 lớp cho mạch digital IO
- Chạy ERC/DRC
- Xuất file kết quả cơ bản

---

## 2. Yêu cầu thiết kế

### 2.1. Chức năng mạch

Thiết kế mạch digital input/output theo luồng sau:

```text
Button Input
    |
    v
Pull-up / Pull-down + RC Debounce
    |
    v
74HC14 Schmitt Trigger
    |
    +---- Clean Digital Output to MCU
    +---- LED Status
    +---- Test Point
```

### 2.2. Nguồn hoạt động

| Hạng mục | Yêu cầu |
|---|---|
| Input voltage | 3.3V |
| Logic voltage | 3.3V |
| IC logic | 74HC14 |
| PCB layer | 2 lớp |

### 2.3. Connector yêu cầu

Board cần có tối thiểu **2 connector**:

| Connector | Tên | Chức năng |
|---|---|---|
| J1 | Power Input Connector | Cấp nguồn 3.3V cho board |
| J2 | MCU Interface Connector | Xuất tín hiệu digital sạch về MCU |

#### J1 - Power Input Connector

| Pin | Net |
|---:|---|
| 1 | 3V3 |
| 2 | GND |

#### J2 - MCU Interface Connector

| Pin | Net |
|---:|---|
| 1 | 3V3 |
| 2 | GND |
| 3 | BTN1_CLEAN |
| 4 | BTN2_CLEAN |
| 5 | BTN3_CLEAN |
| 6 | BTN4_CLEAN |

---

## 3. Linh kiện yêu cầu

| Ref | Linh kiện | Package đề xuất | Ghi chú |
|---|---|---|---|
| U1 | 74HC14 | SOIC-14 / TSSOP-14 | Schmitt Trigger inverter |
| SW1-SW4 | Button SMD | SMD tact switch | Input button |
| R1-R4 | Pull resistor | 0603 / 0805 | Pull-up hoặc pull-down |
| C1-C4 | Debounce capacitor | 0603 / 0805 | Optional RC debounce |
| R5-R6 | LED resistor | 0603 / 0805 | Hạn dòng LED |
| D1-D2 | Status LED | 0603 / 0805 | Báo trạng thái input/output |
| C5 | Decoupling capacitor | 0603 / 0805 | 100nF gần VCC IC |
| J1 | Power input connector | SMD connector | Cấp nguồn 3.3V |
| J2 | MCU interface connector | SMD connector | Xuất tín hiệu về MCU |
| TPx | Test point | SMD test pad | Debug tín hiệu |

---

## 4. Yêu cầu Library

Bạn cần tự tạo hoặc chỉnh sửa library cho ít nhất **1 linh kiện SMD** trong bài này.

Bắt buộc chọn một trong hai yêu cầu sau:

| Option | Yêu cầu |
|---|---|
| Option A | Tự tạo symbol và footprint cho 74HC14 |
| Option B | Tự tạo footprint cho button SMD hoặc connector SMD |

Thư viện phải đặt trong project:

```text
lib/
├── symbols/
│   └── custom_digital_symbols.kicad_sym
└── footprints/
    └── custom_digital_footprints.pretty/
```

### Yêu cầu nếu tự tạo 74HC14

Symbol cần có:

| Pin | Name |
|---:|---|
| 1 | 1A |
| 2 | 1Y |
| 3 | 2A |
| 4 | 2Y |
| 5 | 3A |
| 6 | 3Y |
| 7 | GND |
| 8 | 4Y |
| 9 | 4A |
| 10 | 5Y |
| 11 | 5A |
| 12 | 6Y |
| 13 | 6A |
| 14 | VCC |

Footprint cần có:

- Đúng package SOIC-14 hoặc TSSOP-14
- Đúng pad numbering
- Có pin 1 marker
- Có silkscreen
- Có courtyard
- Không để silkscreen đè pad

---

## 5. Yêu cầu Schematic

Schematic cần có đầy đủ:

1. Power input connector `J1`:
   - `3V3`
   - `GND`
2. MCU interface connector `J2`:
   - `3V3`
   - `GND`
   - `BTN1_CLEAN`
   - `BTN2_CLEAN`
   - `BTN3_CLEAN`
   - `BTN4_CLEAN`
3. IC 74HC14.
4. Cấp nguồn cho IC 74HC14:
   - Pin 14 nối `3V3`
   - Pin 7 nối `GND`
5. Tụ decoupling 100nF đặt gần chân nguồn IC.
6. 4 button input.
7. Mỗi input có:
   - Pull-up hoặc pull-down resistor
   - Optional capacitor để debounce
8. Ít nhất 2 LED status.
9. Test point:
   - `3V3`
   - `GND`
   - `BTN1_CLEAN`
   - `BTN2_CLEAN`
   - `BTN3_CLEAN`
   - `BTN4_CLEAN`

### Net name bắt buộc

| Net | Ý nghĩa |
|---|---|
| `3V3` | Nguồn logic |
| `GND` | Ground |
| `BTN1_RAW` | Tín hiệu button 1 trước 74HC14 |
| `BTN2_RAW` | Tín hiệu button 2 trước 74HC14 |
| `BTN3_RAW` | Tín hiệu button 3 trước 74HC14 |
| `BTN4_RAW` | Tín hiệu button 4 trước 74HC14 |
| `BTN1_CLEAN` | Tín hiệu button 1 sau 74HC14 |
| `BTN2_CLEAN` | Tín hiệu button 2 sau 74HC14 |
| `BTN3_CLEAN` | Tín hiệu button 3 sau 74HC14 |
| `BTN4_CLEAN` | Tín hiệu button 4 sau 74HC14 |

---

## 6. Yêu cầu Layout PCB

| Hạng mục | Yêu cầu |
|---|---|
| PCB layer | 2 lớp |
| Board size | Tối đa 45mm x 30mm |
| Track signal | 8-12 mil |
| Track 3V3 power | Tối thiểu 20 mil |
| Clearance | Tối thiểu 8 mil |
| GND Plane | Bắt buộc |
| Test point | Đặt ở top side |
| DRC | Pass |

### Quy tắc placement

- J1 Power Input đặt sát mép board
- J2 MCU Interface đặt sát mép board, dễ cắm dây
- Button đặt dễ thao tác
- LED status đặt dễ quan sát
- 74HC14 đặt ở giữa vùng input và output
- Tụ decoupling 100nF đặt gần chân VCC/GND của U1
- Đường `3V3` từ J1 đến U1 dùng track tối thiểu 20 mil
- Pin 14 của U1 phải nối `3V3`
- Pin 7 của U1 phải nối `GND`
- Test point dễ đo
- GND plane phủ toàn mạch
- Silkscreen không được đè pad
- Silkscreen phải ghi rõ `3V3`, `GND`, `BTN1`, `BTN2`, `BTN3`, `BTN4`

---

## 7. Cấu trúc thư mục nộp bài

Bạn cần nộp project theo cấu trúc sau:

```text
HW02_Digital_IO_74HC14_Block/
├── datasheet/
│   └── 74hc14_datasheet.pdf
├── docs/
│   └── README.md
├── lib/
│   ├── symbols/
│   │   └── custom_digital_symbols.kicad_sym
│   └── footprints/
│       └── custom_digital_footprints.pretty/
├── output/
│   ├── schematic.pdf
│   ├── bom.csv
│   ├── pcb_top.png
│   ├── pcb_bottom.png
│   ├── pcb_3d.png
│   ├── erc_report.txt
│   └── drc_report.txt
├── HW02_Digital_IO_74HC14_Block.kicad_pro
├── HW02_Digital_IO_74HC14_Block.kicad_sch
└── HW02_Digital_IO_74HC14_Block.kicad_pcb
```

---

## 8. README yêu cầu

File `README.md` cần có các mục sau:

```markdown
# HW02 Digital IO 74HC14 Block

## 1. Project Description

Mô tả ngắn chức năng board.

## 2. Input/Output Specification

- Logic voltage:
- Number of input channels:
- Number of output channels:
- IC used:
- Debounce method:
- Power input connector:
- MCU interface connector:

## 3. Connector Pinout

### J1 - Power Input Connector

| Pin | Net | Description |
|---:|---|---|
| 1 | 3V3 | Logic power input |
| 2 | GND | Ground |

### J2 - MCU Interface Connector

| Pin | Net | Description |
|---:|---|---|
| 1 | 3V3 | Logic power |
| 2 | GND | Ground |
| 3 | BTN1_CLEAN | Clean digital output 1 |
| 4 | BTN2_CLEAN | Clean digital output 2 |
| 5 | BTN3_CLEAN | Clean digital output 3 |
| 6 | BTN4_CLEAN | Clean digital output 4 |

## 4. Custom Library

Liệt kê symbol/footprint tự tạo hoặc chỉnh sửa:

- Component:
- Symbol:
- Footprint:

## 5. Symbol-Footprint Mapping

| Component | Symbol Pin | Pin Name | Footprint Pad | Note |
|---|---:|---|---:|---|
| 74HC14 | 14 | VCC | 14 | Logic power 3.3V |
| 74HC14 | 7 | GND | 7 | Ground |
| 74HC14 | 1 | 1A | 1 | Input channel 1 |
| 74HC14 | 2 | 1Y | 2 | Output channel 1 |

## 6. Design Notes

Giải thích ngắn:

- Cách cấp nguồn cho 74HC14
- Vì sao cần pull-up hoặc pull-down
- Vai trò của 74HC14 Schmitt Trigger
- Vai trò tụ debounce nếu có dùng
- Vị trí tụ decoupling
- Cách bố trí GND plane

## 7. ERC/DRC Result

Ghi kết quả ERC/DRC.

## 8. Known Issues

Ghi rõ nếu còn điểm chưa chắc chắn hoặc rủi ro thiết kế.
```

---

## 9. Kết quả cần đạt

Sau khi hoàn thành, bạn cần có:

- Project KiCad mở được đầy đủ
- Có connector cấp nguồn riêng `J1`
- Có connector xuất tín hiệu về MCU riêng `J2`
- IC 74HC14 được cấp nguồn đúng:
  - Pin 14 = `3V3`
  - Pin 7 = `GND`
- Có tụ decoupling 100nF gần IC
- Có ít nhất 1 custom symbol hoặc footprint trong thư mục `lib/`
- Schematic có 4 input button
- Tín hiệu input được xử lý qua 74HC14
- Có ít nhất 2 LED status
- Có test point cho các tín hiệu chính
- ERC không có lỗi nghiêm trọng
- PCB layout 2 lớp đúng constraint
- Có GND plane
- DRC pass
- Có BOM
- Có ảnh PCB top/bottom/3D
- README mô tả rõ thiết kế

---

## 10. Tiêu chí đánh giá

| Tiêu chí | Điểm |
|---|---:|
| Project structure đúng | 10 |
| Connector và cấp nguồn IC đúng | 15 |
| Custom library đúng | 15 |
| Schematic đúng nguyên lý | 20 |
| Pull-up/pull-down và debounce hợp lý | 10 |
| Layout PCB hợp lý | 15 |
| ERC/DRC pass | 10 |
| README rõ ràng | 5 |

Tổng điểm: **100**

---

## 11. Checklist trước khi nộp

```text
[ ] Project mở được trên KiCad 9.
[ ] Có datasheet 74HC14.
[ ] Có ít nhất 1 custom symbol hoặc footprint.
[ ] Pin symbol khớp pad footprint.
[ ] Có J1 Power Input Connector.
[ ] Có J2 MCU Interface Connector.
[ ] J1 có 3V3 và GND.
[ ] J2 có 3V3, GND và 4 tín hiệu BTN_CLEAN.
[ ] Pin 14 của 74HC14 nối 3V3.
[ ] Pin 7 của 74HC14 nối GND.
[ ] Có tụ decoupling 100nF gần IC.
[ ] Schematic có đủ 4 button input.
[ ] Mỗi input có pull-up hoặc pull-down.
[ ] Có tụ debounce nếu thiết kế yêu cầu.
[ ] Có 74HC14 xử lý tín hiệu input.
[ ] Có ít nhất 2 LED status.
[ ] Có test point cho tín hiệu chính.
[ ] Net name đúng yêu cầu.
[ ] ERC không có lỗi nghiêm trọng.
[ ] PCB 2 lớp.
[ ] Đường 3V3 tới IC đạt tối thiểu 20 mil.
[ ] Có GND plane.
[ ] Silkscreen không đè pad.
[ ] DRC pass.
[ ] Có schematic PDF.
[ ] Có BOM.
[ ] Có ảnh PCB top/bottom/3D.
[ ] README đầy đủ.
```