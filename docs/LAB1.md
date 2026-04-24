# LAB 01 - Power Block 3.3V LDO

## 1. Mục tiêu

Thiết kế một mạch nguồn cơ bản dùng **KiCad 9**:

- Input: **DC Jack 9-12V**
- Output: **3.3V**
- IC nguồn: **AMS1117-3.3 hoặc LDO tương đương**
- Có bảo vệ đầu vào bằng **PPTC fuse** và **Schottky diode**
- Có LED báo nguồn
- Có layout PCB 2 lớp và GND plane

Sau bài lab này, bạn cần nắm được:

- Tạo project KiCad đúng cấu trúc
- Tạo symbol và footprint custom
- Đọc package dimension từ datasheet
- Vẽ schematic mạch nguồn cơ bản
- Layout đường nguồn và đổ đồng GND
- Chạy ERC/DRC
- Xuất file kết quả cơ bản

---

## 2. Yêu cầu thiết kế

### 2.1. Chức năng mạch

Thiết kế mạch nguồn theo luồng sau:

```text
DC Jack 9-12V
    |
    v
PPTC Fuse
    |
    v
Schottky Diode Reverse Protection
    |
    v
Input Capacitors
    |
    v
AMS1117-3.3 LDO
    |
    v
Output Capacitors
    |
    +---- 3.3V Output Connector
    +---- Power LED
    +---- Test Points
```

---

## 3. Linh kiện yêu cầu

| Ref | Linh kiện | Package đề xuất | Ghi chú |
|---|---|---|---|
| J1 | DC Jack | Theo datasheet | Bắt buộc tự tạo symbol/footprint |
| F1 | PPTC Fuse | 1206 / 1812 | Bảo vệ quá dòng |
| D1 | Schottky Diode | SOD-123 / SMA / SMB | Chống ngược cực |
| U1 | AMS1117-3.3 | SOT-223 | Bắt buộc tự tạo symbol/footprint |
| C1 | Input capacitor | 0805 / 1206 | 10uF |
| C2 | Input bypass capacitor | 0603 / 0805 | 100nF |
| C3 | Output capacitor | 0805 / 1206 | 10uF |
| C4 | Output bypass capacitor | 0603 / 0805 | 100nF |
| R1 | LED resistor | 0603 / 0805 | 1k - 2.2k |
| D2 | Power LED | 0603 / 0805 | LED báo 3.3V |
| J2 | Output connector | SMD connector | 3.3V output |
| TPx | Test point | SMD test pad | Debug nguồn |

---

## 4. Yêu cầu Library

Bạn **không được dùng thư viện mặc định** cho các linh kiện sau:

| Linh kiện | Yêu cầu |
|---|---|
| DC Jack | Tự tạo symbol và footprint |
| AMS1117-3.3 | Tự tạo symbol và footprint SOT-223 |

Thư viện phải đặt trong project:

```text
lib/
├── symbols/
│   └── custom_power_symbols.kicad_sym
└── footprints/
    └── custom_power_footprints.pretty/
```

### Yêu cầu symbol/footprint

#### DC Jack

- Symbol đúng số chân theo datasheet
- Footprint đúng kích thước cơ khí
- Có pad/hole đúng vị trí
- Có silkscreen outline
- Có courtyard
- Pin numbering rõ ràng

#### AMS1117-3.3

Symbol cần có:

| Pin | Name |
|---:|---|
| 1 | GND/ADJ |
| 2 | VOUT |
| 3 | VIN |
| TAB | VOUT |

Footprint SOT-223 cần có:

- 3 pad chân nhỏ
- 1 pad tab lớn
- Tab nối đúng net `VOUT`
- Có silkscreen
- Có courtyard
- Có pin 1 marker

---

## 5. Yêu cầu Schematic

Schematic cần có đầy đủ:

1. DC Jack input 9-12V
2. PPTC fuse sau DC Jack
3. Schottky diode chống ngược cực
4. Tụ input:
   - 10uF
   - 100nF
5. AMS1117-3.3
6. Tụ output:
   - 10uF
   - 100nF
7. LED báo nguồn 3.3V
8. Output connector:
   - `3V3`
   - `GND`
9. Test point:
   - `VIN_RAW`
   - `VIN_PROTECTED`
   - `3V3`
   - `GND`

### Net name bắt buộc

| Net | Ý nghĩa |
|---|---|
| `VIN_RAW` | Nguồn ngay sau DC Jack |
| `VIN_FUSED` | Nguồn sau PPTC |
| `VIN_PROTECTED` | Nguồn sau diode Schottky |
| `3V3` | Nguồn đầu ra LDO |
| `GND` | Ground |

---

## 6. Yêu cầu Layout PCB

| Hạng mục | Yêu cầu |
|---|---|
| PCB layer | 2 lớp |
| Board size | Tối đa 40mm x 30mm |
| Track nguồn chính | Tối thiểu 25-30 mil |
| Track LED/signal | 8-12 mil |
| Clearance | Tối thiểu 8 mil |
| GND Plane | Bắt buộc |
| Test point | Đặt ở top side |
| DRC | Pass |

### Quy tắc placement

- DC Jack đặt sát mép board
- PPTC đặt gần DC Jack
- Diode Schottky đặt sau PPTC
- Tụ input đặt gần chân `VIN` của LDO
- Tụ output đặt gần chân `VOUT` của LDO
- AMS1117 cần có vùng copper hỗ trợ tản nhiệt ở tab
- LED power đặt dễ nhìn
- Output connector đặt sát mép board
- Test point dễ đo
- Silkscreen không được đè pad

---

## 7. Cấu trúc thư mục nộp bài

Bạn cần nộp project theo cấu trúc sau:

```text
HW01_Power_Block_Protected_3V3/
├── datasheet/
│   ├── dc_jack_datasheet.pdf
│   └── ams1117_datasheet.pdf
├── docs/
│   └── README.md
├── lib/
│   ├── symbols/
│   │   └── custom_power_symbols.kicad_sym
│   └── footprints/
│       └── custom_power_footprints.pretty/
├── output/
│   ├── schematic.pdf
│   ├── bom.csv
│   ├── pcb_top.png
│   ├── pcb_bottom.png
│   ├── pcb_3d.png
│   ├── erc_report.txt
│   └── drc_report.txt
├── HW01_Power_Block_Protected_3V3.kicad_pro
├── HW01_Power_Block_Protected_3V3.kicad_sch
└── HW01_Power_Block_Protected_3V3.kicad_pcb
```

---

## 8. README yêu cầu

File `README.md` cần có các mục sau:

```markdown
# HW01 Power Block Protected 3V3

## 1. Project Description

Mô tả ngắn chức năng board.

## 2. Input/Output Specification

- Input voltage:
- Output voltage:
- Estimated load current:
- LDO used:
- Protection components:

## 3. Custom Library

Liệt kê symbol/footprint tự tạo:

- DC Jack symbol
- DC Jack footprint
- AMS1117 symbol
- AMS1117 SOT-223 footprint

## 4. Design Notes

Giải thích ngắn:

- Vai trò PPTC fuse
- Vai trò Schottky diode
- Vai trò tụ input/output
- Track width nguồn đã dùng
- Cách bố trí GND plane

## 5. ERC/DRC Result

Ghi kết quả ERC/DRC.

## 6. Known Issues

Ghi rõ nếu còn điểm chưa chắc chắn hoặc rủi ro thiết kế.
```

---

## 9. Kết quả cần đạt

Sau khi hoàn thành, bạn cần có:

- Project KiCad mở được đầy đủ
- Symbol và footprint custom nằm trong thư mục `lib/`
- Schematic đúng nguyên lý
- ERC không có lỗi nghiêm trọng
- PCB layout 2 lớp đúng constraint
- Có GND plane
- Track nguồn đạt tối thiểu 25-30 mil
- DRC pass
- Có BOM
- Có ảnh PCB top/bottom/3D
- README mô tả rõ thiết kế

---

## 10. Tiêu chí đánh giá

| Tiêu chí | Điểm |
|---|---:|
| Project structure đúng | 10 |
| Custom symbol đúng | 15 |
| Custom footprint đúng datasheet | 20 |
| Schematic đúng nguyên lý | 20 |
| Layout nguồn và GND plane hợp lý | 20 |
| ERC/DRC pass | 10 |
| README rõ ràng | 5 |

Tổng điểm: **100**

---

## 11. Checklist trước khi nộp

```text
[ ] Có đầy đủ datasheet sử dụng.
[ ] DC Jack symbol/footprint tự tạo.
[ ] AMS1117 symbol/footprint tự tạo.
[ ] Pin symbol khớp pad footprint.
[ ] Schematic có PPTC, Schottky diode, LDO, tụ input/output, LED power.
[ ] Net name đúng yêu cầu.
[ ] ERC không có lỗi nghiêm trọng.
[ ] PCB 2 lớp.
[ ] Track nguồn đạt 25-30 mil.
[ ] Có GND plane.
[ ] Tụ input/output đặt gần LDO.
[ ] Silkscreen không đè pad.
[ ] DRC pass.
[ ] Có schematic PDF.
[ ] Có BOM.
[ ] Có ảnh PCB top/bottom/3D.
[ ] README đầy đủ.
```