# LAB 03 - I2C Sensor Communication Block
> Mức độ: Trung bình (3-4 ngày)

> [Link Submit](https://forms.gle/m5Ad7Kv8nSxDZNik7)

## 1. Mục tiêu

Thiết kế một mạch **I2C Sensor Communication Block** dùng KiCad 9.

Mạch có nhiệm vụ kết nối MCU với cảm biến hoặc module cảm biến qua giao tiếp **I2C**, có pull-up resistor, connector mở rộng, test point và jumper cấu hình.

Sau bài lab này, bạn cần nắm được:

- Thiết kế schematic cho giao tiếp I2C
- Hiểu vai trò pull-up resistor trên bus I2C
- Biết cách bố trí connector cho sensor và MCU
- Biết thêm jumper cấu hình địa chỉ hoặc bật/tắt pull-up
- Tạo hoặc chỉnh sửa footprint cho connector SMD hoặc sensor module
- Layout PCB 2 lớp cho bus tín hiệu đơn giản
- Đặt test point phục vụ debug
- Chạy ERC/DRC
- Xuất file kết quả cơ bản

---

## 2. Yêu cầu thiết kế

### 2.1. Chức năng mạch

Thiết kế mạch giao tiếp I2C theo luồng sau:

```text
MCU Interface Connector
    |
    +---- SDA
    +---- SCL
    |
    v
I2C Pull-up Resistors
    |
    +---- I2C Sensor Connector
    |
    +---- I2C Expansion Connector
    |
    +---- Test Points
```

### 2.2. Nguồn hoạt động

| Hạng mục | Yêu cầu |
|---|---|
| Logic voltage | 3.3V |
| Communication | I2C |
| Signal lines | SDA, SCL |
| PCB layer | 2 lớp |

### 2.3. Connector yêu cầu

Board cần có tối thiểu **3 connector**:

| Connector | Tên | Chức năng |
|---|---|---|
| J1 | MCU Interface Connector | Kết nối về MCU |
| J2 | I2C Sensor Connector | Kết nối cảm biến I2C |
| J3 | I2C Expansion Connector | Mở rộng thêm thiết bị I2C khác |

#### J1 - MCU Interface Connector

| Pin | Net |
|---:|---|
| 1 | 3V3 |
| 2 | GND |
| 3 | SDA |
| 4 | SCL |

#### J2 - I2C Sensor Connector

| Pin | Net |
|---:|---|
| 1 | 3V3 |
| 2 | GND |
| 3 | SDA |
| 4 | SCL |

#### J3 - I2C Expansion Connector

| Pin | Net |
|---:|---|
| 1 | 3V3 |
| 2 | GND |
| 3 | SDA |
| 4 | SCL |

---

## 3. Linh kiện yêu cầu

| Ref | Linh kiện | Package đề xuất | Ghi chú |
|---|---|---|---|
| J1 | MCU Interface Connector | SMD connector | Kết nối MCU |
| J2 | I2C Sensor Connector | SMD connector | Kết nối cảm biến |
| J3 | I2C Expansion Connector | SMD connector | Daisy-chain I2C |
| R1 | SDA Pull-up | 0603 / 0805 | 2.2k - 10k |
| R2 | SCL Pull-up | 0603 / 0805 | 2.2k - 10k |
| JP1 | SDA Pull-up Jumper | Solder jumper | Bật/tắt pull-up SDA |
| JP2 | SCL Pull-up Jumper | Solder jumper | Bật/tắt pull-up SCL |
| D1 | Power LED | 0603 / 0805 | LED báo 3.3V |
| R3 | LED Resistor | 0603 / 0805 | 1k - 2.2k |
| C1 | Bypass Capacitor | 0603 / 0805 | 100nF |
| TPx | Test Point | SMD test pad | Debug I2C |

---

## 4. Yêu cầu Library

Bạn cần tự tạo hoặc chỉnh sửa library cho ít nhất **1 linh kiện SMD** trong bài này.

Bắt buộc chọn một trong các yêu cầu sau:

| Option | Yêu cầu |
|---|---|
| Option A | Tự tạo footprint cho connector I2C SMD |
| Option B | Tự tạo footprint cho solder jumper |
| Option C | Tự tạo symbol/footprint cho sensor I2C nếu dùng IC/module cụ thể |

Thư viện phải đặt trong project:

```text
lib/
├── symbols/
│   └── custom_i2c_symbols.kicad_sym
└── footprints/
    └── custom_i2c_footprints.pretty/
```

### Yêu cầu footprint connector

Footprint connector cần có:

- Đúng số chân
- Đúng pad spacing theo datasheet
- Đúng pin numbering
- Có silkscreen outline
- Có courtyard
- Có pin 1 marker
- Không để silkscreen đè pad

### Yêu cầu solder jumper

Footprint solder jumper cần có:

- Pad rõ ràng, dễ hàn nối/cắt
- Silkscreen label rõ chức năng
- Khoảng cách pad phù hợp
- Không gây short ngoài ý muốn sau khi đổ đồng

---

## 5. Yêu cầu Schematic

Schematic cần có đầy đủ:

1. MCU interface connector `J1`:
   - `3V3`
   - `GND`
   - `SDA`
   - `SCL`
2. I2C sensor connector `J2`:
   - `3V3`
   - `GND`
   - `SDA`
   - `SCL`
3. I2C expansion connector `J3`:
   - `3V3`
   - `GND`
   - `SDA`
   - `SCL`
4. Pull-up resistor cho SDA và SCL.
5. Solder jumper để bật/tắt pull-up SDA/SCL.
6. LED báo nguồn 3.3V.
7. Tụ bypass 100nF giữa `3V3` và `GND`.
8. Test point:
   - `3V3`
   - `GND`
   - `SDA`
   - `SCL`

### Net name bắt buộc

| Net | Ý nghĩa |
|---|---|
| `3V3` | Nguồn logic |
| `GND` | Ground |
| `SDA` | I2C data |
| `SCL` | I2C clock |
| `SDA_PU` | Nhánh pull-up SDA nếu cần tách net |
| `SCL_PU` | Nhánh pull-up SCL nếu cần tách net |

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

- J1 MCU Interface đặt sát mép board
- J2 Sensor Connector đặt sát mép board, dễ cắm module/cảm biến
- J3 Expansion Connector đặt sát mép board để dễ mở rộng
- Pull-up resistor đặt gần đường bus I2C
- Solder jumper đặt dễ thao tác bằng mỏ hàn
- LED power đặt dễ quan sát
- Tụ bypass 100nF đặt gần connector nguồn hoặc vùng trung tâm cấp nguồn
- Test point `SDA`, `SCL`, `3V3`, `GND` đặt ở top side, dễ đo bằng oscilloscope/logic analyzer
- Đường SDA/SCL nên đi song song tương đối gọn, tránh vòng vèo không cần thiết
- GND plane phủ toàn mạch
- Silkscreen không được đè pad
- Silkscreen phải ghi rõ `3V3`, `GND`, `SDA`, `SCL`

---

## 7. Cấu trúc thư mục nộp bài

Bạn cần nộp project theo cấu trúc sau:

```text
HW03_I2C_Sensor_Communication_Block/
├── datasheet/
│   └── connector_or_sensor_datasheet.pdf
├── docs/
│   └── README.md
├── lib/
│   ├── symbols/
│   │   └── custom_i2c_symbols.kicad_sym
│   └── footprints/
│       └── custom_i2c_footprints.pretty/
├── output/
│   ├── schematic.pdf
│   ├── bom.csv
│   ├── pcb_top.png
│   ├── pcb_bottom.png
│   ├── pcb_3d.png
│   ├── erc_report.txt
│   └── drc_report.txt
├── HW03_I2C_Sensor_Communication_Block.kicad_pro
├── HW03_I2C_Sensor_Communication_Block.kicad_sch
└── HW03_I2C_Sensor_Communication_Block.kicad_pcb
```

---

## 8. README yêu cầu

File `README.md` cần có các mục sau:

```markdown
# HW03 I2C Sensor Communication Block

## 1. Project Description

Mô tả ngắn chức năng board.

## 2. Input/Output Specification

- Logic voltage:
- Communication interface:
- Number of I2C connectors:
- Pull-up value:
- Pull-up enable method:
- Sensor/connector used:

## 3. Connector Pinout

### J1 - MCU Interface Connector

| Pin | Net | Description |
|---:|---|---|
| 1 | 3V3 | Logic power |
| 2 | GND | Ground |
| 3 | SDA | I2C data |
| 4 | SCL | I2C clock |

### J2 - I2C Sensor Connector

| Pin | Net | Description |
|---:|---|---|
| 1 | 3V3 | Sensor power |
| 2 | GND | Ground |
| 3 | SDA | I2C data |
| 4 | SCL | I2C clock |

### J3 - I2C Expansion Connector

| Pin | Net | Description |
|---:|---|---|
| 1 | 3V3 | Expansion power |
| 2 | GND | Ground |
| 3 | SDA | I2C data |
| 4 | SCL | I2C clock |

## 4. Custom Library

Liệt kê symbol/footprint tự tạo hoặc chỉnh sửa:

- Component:
- Symbol:
- Footprint:

## 5. Symbol-Footprint Mapping

| Component | Symbol Pin | Pin Name | Footprint Pad | Note |
|---|---:|---|---:|---|
| Connector | 1 | 3V3 | 1 | Power |
| Connector | 2 | GND | 2 | Ground |
| Connector | 3 | SDA | 3 | I2C data |
| Connector | 4 | SCL | 4 | I2C clock |

## 6. Design Notes

Giải thích ngắn:

- Vì sao I2C cần pull-up resistor
- Giá trị pull-up đã chọn
- Vai trò solder jumper bật/tắt pull-up
- Cách bố trí connector I2C
- Cách bố trí test point để debug
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
- Có tối thiểu 3 connector:
  - J1 MCU Interface
  - J2 I2C Sensor
  - J3 I2C Expansion
- Có pull-up resistor cho `SDA` và `SCL`
- Có solder jumper bật/tắt pull-up I2C
- Có LED báo nguồn 3.3V
- Có tụ bypass 100nF
- Có test point cho `3V3`, `GND`, `SDA`, `SCL`
- Có ít nhất 1 custom symbol hoặc footprint trong thư mục `lib/`
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
| Connector và pinout rõ ràng | 15 |
| Custom library đúng | 15 |
| Schematic I2C đúng nguyên lý | 20 |
| Pull-up và jumper cấu hình hợp lý | 15 |
| Layout PCB hợp lý | 10 |
| ERC/DRC pass | 10 |
| README rõ ràng | 5 |

Tổng điểm: **100**

---

## 11. Checklist trước khi nộp

```text
[ ] Project mở được trên KiCad 9.
[ ] Có datasheet connector hoặc sensor sử dụng.
[ ] Có ít nhất 1 custom symbol hoặc footprint.
[ ] Pin symbol khớp pad footprint.
[ ] Có J1 MCU Interface Connector.
[ ] Có J2 I2C Sensor Connector.
[ ] Có J3 I2C Expansion Connector.
[ ] Cả 3 connector đều có 3V3, GND, SDA, SCL.
[ ] Có pull-up resistor cho SDA.
[ ] Có pull-up resistor cho SCL.
[ ] Có solder jumper bật/tắt pull-up SDA/SCL.
[ ] Có LED báo nguồn 3.3V.
[ ] Có tụ bypass 100nF.
[ ] Có test point cho 3V3, GND, SDA, SCL.
[ ] Net name đúng yêu cầu.
[ ] ERC không có lỗi nghiêm trọng.
[ ] PCB 2 lớp.
[ ] Đường 3V3 đạt tối thiểu 20 mil.
[ ] Đường SDA/SCL routing gọn, dễ kiểm tra.
[ ] Có GND plane.
[ ] Silkscreen không đè pad.
[ ] DRC pass.
[ ] Có schematic PDF.
[ ] Có BOM.
[ ] Có ảnh PCB top/bottom/3D.
[ ] README đầy đủ.
```