# LAB 04 - MOSFET Load Driver Block

## 1. Mục tiêu

Thiết kế một mạch **MOSFET Load Driver Block** dùng KiCad 9.

Mạch có nhiệm vụ nhận tín hiệu điều khiển từ MCU và điều khiển tải DC bằng **N-MOSFET low-side driver**.

Sau bài lab này, bạn cần nắm được:

- Thiết kế mạch MOSFET low-side driver
- Hiểu vai trò gate resistor và gate pull-down
- Hiểu diode flyback khi điều khiển tải cảm
- Biết tách rõ đường signal, logic power và load current
- Tạo net class cho từng nhóm tín hiệu
- Layout PCB 2 lớp cho mạch có dòng tải
- Đặt test point phục vụ đo/debug
- Chạy ERC/DRC
- Xuất file kết quả cơ bản

---

## 2. Yêu cầu thiết kế

### 2.1. Chức năng mạch

Thiết kế mạch điều khiển tải theo luồng sau:

```text
MCU Control Signal
    |
    v
Gate Resistor
    |
    v
N-MOSFET Low-side Switch
    |
    v
Load Output Connector

Load Power Input
    |
    +---- Load+
    |
    +---- Flyback Diode Protection
```

### 2.2. Thông số hoạt động

| Hạng mục | Yêu cầu |
|---|---|
| Logic voltage | 3.3V |
| Load voltage | 5V - 12V |
| Load current giả định | <= 500mA mỗi channel |
| Number of channels | 2 |
| Switch type | N-MOSFET low-side |
| PCB layer | 2 lớp |

---

## 3. Connector yêu cầu

Board cần có tối thiểu **4 connector**:

| Connector | Tên | Chức năng |
|---|---|---|
| J1 | MCU Control Connector | Nhận tín hiệu điều khiển từ MCU |
| J2 | Load Power Input Connector | Cấp nguồn cho tải |
| J3 | Load Output CH1 | Kết nối tải channel 1 |
| J4 | Load Output CH2 | Kết nối tải channel 2 |

### J1 - MCU Control Connector

| Pin | Net |
|---:|---|
| 1 | 3V3 |
| 2 | GND |
| 3 | CH1_CTRL |
| 4 | CH2_CTRL |

### J2 - Load Power Input Connector

| Pin | Net |
|---:|---|
| 1 | VLOAD |
| 2 | GND |

### J3 - Load Output CH1

| Pin | Net |
|---:|---|
| 1 | VLOAD |
| 2 | LOAD1_SW |

### J4 - Load Output CH2

| Pin | Net |
|---:|---|
| 1 | VLOAD |
| 2 | LOAD2_SW |

Lưu ý:

- `GND` logic và `GND` tải phải nối chung trên board.
- `LOADx_SW` là node giữa tải và drain của MOSFET.
- Với low-side driver, MOSFET sẽ đóng/ngắt đường về GND của tải.

---

## 4. Linh kiện yêu cầu

| Ref | Linh kiện | Package đề xuất | Ghi chú |
|---|---|---|---|
| Q1, Q2 | Logic-level N-MOSFET | SOT-23 | Ví dụ AO3400, IRLML2502 hoặc tương đương |
| R1, R2 | Gate resistor | 0603 / 0805 | 47R - 220R |
| R3, R4 | Gate pull-down resistor | 0603 / 0805 | 47k - 100k |
| D1, D2 | Flyback diode | SOD-123 / SMA | Dùng nếu tải cảm |
| D3, D4 | Status LED | 0603 / 0805 | LED báo trạng thái channel |
| R5, R6 | LED resistor | 0603 / 0805 | 1k - 2.2k |
| C1 | Load input capacitor | 0805 / 1206 | 10uF - 47uF |
| C2 | Bypass capacitor | 0603 / 0805 | 100nF |
| J1 | MCU Control Connector | SMD connector | Nhận tín hiệu điều khiển |
| J2 | Load Power Input Connector | SMD connector / Terminal | Cấp nguồn tải |
| J3, J4 | Load Output Connector | SMD connector / Terminal | Kết nối tải |
| TPx | Test Point | SMD test pad | Debug tín hiệu và nguồn |

---

## 5. Yêu cầu Library

Bạn cần tự tạo hoặc chỉnh sửa library cho ít nhất **1 linh kiện SMD** trong bài này.

Bắt buộc chọn một trong các yêu cầu sau:

| Option | Yêu cầu |
|---|---|
| Option A | Tự tạo symbol và footprint cho N-MOSFET SOT-23 |
| Option B | Tự tạo footprint cho connector load output |
| Option C | Tự tạo footprint cho diode SOD-123 hoặc SMA |

Thư viện phải đặt trong project:

```text
lib/
├── symbols/
│   └── custom_driver_symbols.kicad_sym
└── footprints/
    └── custom_driver_footprints.pretty/
```

### Yêu cầu nếu tự tạo MOSFET SOT-23

Symbol MOSFET cần có:

| Pin | Name |
|---:|---|
| 1 | GATE |
| 2 | SOURCE |
| 3 | DRAIN |

Footprint cần có:

- Đúng package SOT-23 theo datasheet
- Đúng pad numbering
- Có pin 1 marker
- Có silkscreen
- Có courtyard
- Không để silkscreen đè pad

---

## 6. Yêu cầu Schematic

Schematic cần có đầy đủ:

1. MCU control connector `J1`:
   - `3V3`
   - `GND`
   - `CH1_CTRL`
   - `CH2_CTRL`
2. Load power input connector `J2`:
   - `VLOAD`
   - `GND`
3. 2 kênh MOSFET low-side driver.
4. Mỗi channel cần có:
   - N-MOSFET logic-level
   - Gate resistor
   - Gate pull-down resistor
   - Flyback diode
   - Load output connector
   - LED status
   - Test point cho control signal và switching node
5. Tụ input cho `VLOAD`.
6. Tụ bypass 100nF.
7. Test point:
   - `3V3`
   - `GND`
   - `VLOAD`
   - `CH1_CTRL`
   - `CH2_CTRL`
   - `LOAD1_SW`
   - `LOAD2_SW`

### Net name bắt buộc

| Net | Ý nghĩa |
|---|---|
| `3V3` | Nguồn logic |
| `GND` | Ground chung |
| `VLOAD` | Nguồn cấp cho tải |
| `CH1_CTRL` | Tín hiệu điều khiển MOSFET channel 1 |
| `CH2_CTRL` | Tín hiệu điều khiển MOSFET channel 2 |
| `LOAD1_SW` | Switching node channel 1 |
| `LOAD2_SW` | Switching node channel 2 |

### Kết nối MOSFET low-side

Mỗi channel phải nối theo nguyên tắc:

```text
VLOAD ---- Load ---- LOADx_SW ---- Drain MOSFET
                                  Source MOSFET ---- GND
                                  Gate MOSFET <---- Gate resistor <---- CHx_CTRL
                                  Gate MOSFET ---- Pull-down resistor ---- GND
```

### Kết nối diode flyback

Nếu giả định tải là relay, motor nhỏ hoặc tải cảm:

```text
Diode cathode  ---- VLOAD
Diode anode    ---- LOADx_SW
```

---

## 7. Yêu cầu Layout PCB

| Hạng mục | Yêu cầu |
|---|---|
| PCB layer | 2 lớp |
| Board size | Tối đa 50mm x 35mm |
| Track signal | 8-12 mil |
| Track 3V3 power | Tối thiểu 20 mil |
| Track load current | Tối thiểu 30-40 mil |
| Clearance | Tối thiểu 8 mil |
| GND Plane | Bắt buộc |
| Test point | Đặt ở top side |
| DRC | Pass |

### Net class yêu cầu

Tạo tối thiểu 3 net class:

| Net Class | Net áp dụng | Width đề xuất | Clearance |
|---|---|---:|---:|
| Signal | `CH1_CTRL`, `CH2_CTRL` | 8-12 mil | 8 mil |
| Logic_Power | `3V3` | >= 20 mil | 8 mil |
| Load_Current | `VLOAD`, `LOAD1_SW`, `LOAD2_SW` | >= 30-40 mil | 8-10 mil |

### Quy tắc placement

- J1 MCU Control đặt sát mép board
- J2 Load Power Input đặt sát mép board
- J3/J4 Load Output đặt sát mép board, dễ đấu tải
- MOSFET đặt gần load output connector
- Gate resistor đặt gần chân gate MOSFET
- Pull-down resistor đặt gần gate MOSFET
- Flyback diode đặt gần load output hoặc MOSFET
- Tụ `VLOAD` đặt gần J2
- LED status đặt dễ quan sát
- Test point đặt ở top side, dễ đo
- Đường dòng tải phải ngắn và rộng
- GND plane phủ toàn mạch
- Silkscreen không được đè pad
- Silkscreen phải ghi rõ `VLOAD`, `GND`, `CH1`, `CH2`, `LOAD1`, `LOAD2`

---

## 8. Cấu trúc thư mục nộp bài

Bạn cần nộp project theo cấu trúc sau:

```text
HW04_MOSFET_Load_Driver_Block/
├── datasheet/
│   ├── mosfet_datasheet.pdf
│   ├── diode_datasheet.pdf
│   └── connector_datasheet.pdf
├── docs/
│   └── README.md
├── lib/
│   ├── symbols/
│   │   └── custom_driver_symbols.kicad_sym
│   └── footprints/
│       └── custom_driver_footprints.pretty/
├── output/
│   ├── schematic.pdf
│   ├── bom.csv
│   ├── pcb_top.png
│   ├── pcb_bottom.png
│   ├── pcb_3d.png
│   ├── erc_report.txt
│   └── drc_report.txt
├── HW04_MOSFET_Load_Driver_Block.kicad_pro
├── HW04_MOSFET_Load_Driver_Block.kicad_sch
└── HW04_MOSFET_Load_Driver_Block.kicad_pcb
```

---

## 9. README yêu cầu

File `README.md` cần có các mục sau:

```markdown
# HW04 MOSFET Load Driver Block

## 1. Project Description

Mô tả ngắn chức năng board.

## 2. Input/Output Specification

- Logic voltage:
- Load voltage:
- Load current per channel:
- Number of channels:
- MOSFET used:
- Diode used:

## 3. Connector Pinout

### J1 - MCU Control Connector

| Pin | Net | Description |
|---:|---|---|
| 1 | 3V3 | Logic power |
| 2 | GND | Ground |
| 3 | CH1_CTRL | Control signal channel 1 |
| 4 | CH2_CTRL | Control signal channel 2 |

### J2 - Load Power Input Connector

| Pin | Net | Description |
|---:|---|---|
| 1 | VLOAD | Load supply |
| 2 | GND | Ground |

### J3 - Load Output CH1

| Pin | Net | Description |
|---:|---|---|
| 1 | VLOAD | Load positive |
| 2 | LOAD1_SW | Switched low-side node |

### J4 - Load Output CH2

| Pin | Net | Description |
|---:|---|---|
| 1 | VLOAD | Load positive |
| 2 | LOAD2_SW | Switched low-side node |

## 4. Custom Library

Liệt kê symbol/footprint tự tạo hoặc chỉnh sửa:

- Component:
- Symbol:
- Footprint:

## 5. Symbol-Footprint Mapping

| Component | Symbol Pin | Pin Name | Footprint Pad | Note |
|---|---:|---|---:|---|
| MOSFET | 1 | GATE | 1 | Control input |
| MOSFET | 2 | SOURCE | 2 | Ground |
| MOSFET | 3 | DRAIN | 3 | Load switching node |

## 6. Design Notes

Giải thích ngắn:

- Vì sao dùng low-side N-MOSFET
- Vai trò gate resistor
- Vai trò gate pull-down resistor
- Vai trò flyback diode
- Track width cho dòng tải
- Cách tạo net class
- Cách bố trí GND plane

## 7. ERC/DRC Result

Ghi kết quả ERC/DRC.

## 8. Known Issues

Ghi rõ nếu còn điểm chưa chắc chắn hoặc rủi ro thiết kế.
```

---

## 10. Kết quả cần đạt

Sau khi hoàn thành, bạn cần có:

- Project KiCad mở được đầy đủ
- Có tối thiểu 4 connector:
  - J1 MCU Control
  - J2 Load Power Input
  - J3 Load Output CH1
  - J4 Load Output CH2
- Có 2 channel MOSFET low-side driver
- Mỗi channel có gate resistor và gate pull-down
- Mỗi channel có flyback diode
- Có LED status cho từng channel
- Có tụ input cho `VLOAD`
- Có test point cho nguồn và tín hiệu chính
- Có ít nhất 1 custom symbol hoặc footprint trong thư mục `lib/`
- Có net class cho signal, logic power và load current
- ERC không có lỗi nghiêm trọng
- PCB layout 2 lớp đúng constraint
- Track dòng tải đạt tối thiểu 30-40 mil
- Có GND plane
- DRC pass
- Có BOM
- Có ảnh PCB top/bottom/3D
- README mô tả rõ thiết kế

---

## 11. Tiêu chí đánh giá

| Tiêu chí | Điểm |
|---|---:|
| Project structure đúng | 10 |
| Connector và pinout rõ ràng | 10 |
| Custom library đúng | 15 |
| Schematic MOSFET driver đúng nguyên lý | 20 |
| Gate resistor, pull-down, flyback diode hợp lý | 15 |
| Net class và track width đúng | 10 |
| Layout PCB hợp lý | 10 |
| ERC/DRC pass | 5 |
| README rõ ràng | 5 |

Tổng điểm: **100**

---

## 12. Checklist trước khi nộp

```text
[ ] Project mở được trên KiCad 9.
[ ] Có datasheet MOSFET, diode và connector sử dụng.
[ ] Có ít nhất 1 custom symbol hoặc footprint.
[ ] Pin symbol khớp pad footprint.
[ ] Có J1 MCU Control Connector.
[ ] Có J2 Load Power Input Connector.
[ ] Có J3 Load Output CH1.
[ ] Có J4 Load Output CH2.
[ ] GND logic và GND tải nối chung đúng cách.
[ ] Có 2 channel MOSFET low-side driver.
[ ] MOSFET source nối GND.
[ ] MOSFET drain nối LOADx_SW.
[ ] Gate MOSFET đi qua gate resistor.
[ ] Gate MOSFET có pull-down về GND.
[ ] Diode flyback đúng chiều.
[ ] Có LED status cho từng channel.
[ ] Có tụ input cho VLOAD.
[ ] Có test point cho 3V3, GND, VLOAD, CH1_CTRL, CH2_CTRL, LOAD1_SW, LOAD2_SW.
[ ] Net name đúng yêu cầu.
[ ] Có net class Signal, Logic_Power, Load_Current.
[ ] Track dòng tải đạt tối thiểu 30-40 mil.
[ ] ERC không có lỗi nghiêm trọng.
[ ] PCB 2 lớp.
[ ] Có GND plane.
[ ] Silkscreen không đè pad.
[ ] DRC pass.
[ ] Có schematic PDF.
[ ] Có BOM.
[ ] Có ảnh PCB top/bottom/3D.
[ ] README đầy đủ.
```