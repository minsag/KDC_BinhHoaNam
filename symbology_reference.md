# Symbology reference — trích xuất chính xác từ `Map.mapx` (CIM JSON)

Nguồn: parse trực tiếp `mapDefinition.layerDefinitions[*].renderer` / `.labelClasses`.
Không có giá trị nào bị ước lượng — toàn bộ hex/width/dash lấy thẳng từ `CIMRGBColor.values` và `CIMSolidStroke.width`.

## 1. DiaHinh_DuongBinhDo (line) — renderer: CIMUniqueValueRenderer, field `loaiDuongBinhDo`

| Giá trị field | Label | Màu | Width (pt) | Cap | Dash |
|---|---|---|---|---|---|
| "1" | Cơ bản | `#E69800` | 1.2 | Butt | không |
| "2" | Nửa khoảng cao đều | `#BAFDB5` | 1.0 | Round | không |
| "3" | Phụ | `#FFD37F` | 0.4 | Butt | không |
| "4" | Nháp | `#CCEDFF` | 0.4 | Butt | **[7,1,3,1]** (dashTemplate gốc, đơn vị point) |
| khác/rỗng | \<all other values\> | `#828282` | 1.0 | Round | không |

## 2. PhuBeMat_CayDocLap (point) — CIMSimpleRenderer

Marker vẽ bằng `CIMVectorMarker` (37 đoạn cung tròn, không phải ảnh nhúng) — đã giải mã lại chính xác thành `icons/cay_doc_lap.svg`.
- Fill: `#55FF00`
- Stroke viền: `#333333`, width 0

## 3. DanCu_Nha_Vung (polygon) — CIMSimpleRenderer

| Thành phần | Giá trị |
|---|---|
| Fill | `#F3EDD3` |
| Outline | `#E0D2B8`, width 0.6pt |

## 4. GiaoThong_MepDuong (line)

- Màu: `#BDBDC5`, width 1pt, cap Butt

## 5. ThuyVan_DuongBoNuoc (line)

- Màu: `#9EBBD7`, width 1pt, cap Round

## 6. DiaHinh_DiemDoCao (point + label)

- Marker: hình tròn đơn giản, bán kính 5 (đơn vị symbol), fill `#4D4D4D`~`#000000`, size 2pt
- **Label đang BẬT** (`labelVisibility: true`) — field: `doCao`, font Tahoma, size 10, màu chữ `#000000`
- Đây là layer DUY NHẤT trong 9 layer có labeling thực sự hoạt động.

## 7. GiaoThong_CauGiaoThong_Duong (line — kỹ thuật "casing")

Hai nét chồng nhau để tạo hiệu ứng viền:

| Layer (thứ tự vẽ) | Màu | Width |
|---|---|---|
| Dưới (casing) | `#B9B7B9` | 2.8pt |
| Trên (fill) | `#FFFFFF` | 1.3pt |

## 8. CoSoDoDac_DiemDoDacQuocGia (point) — CIMUniqueValueRenderer, field `maNhanDang`

| Giá trị | Hình dạng | Cấu trúc màu (từ ngoài vào trong) |
|---|---|---|
| DC | Vuông | viền đen mỏng → dải đỏ `#E60000` → tâm đen `#000000` |
| GPS | Tam giác | viền đen mỏng → dải đỏ `#E60000` → tâm đen `#000000` |
| khác | Tròn nhỏ | fill `#828282`, viền đen 0.7pt |

Icon chính xác: `icons/co_so_do_dac_DC.svg`, `icons/co_so_do_dac_GPS.svg` — dựng lại từ toạ độ ring gốc, verify bằng render.

## 9. ranhKS (polygon outline)

- Line: `#FF0000`, width 2pt, dash **[12,12]**
- Fill: alpha 0% (trong suốt, không hiển thị)

## ⚠️ Vấn đề phát hiện trong project gốc (không phải do tôi tạo ra)

`labelClasses` của 6 layer sau đều có `expression: "$feature.maNhanDang"` dù field này không thuộc về chúng (rõ ràng do copy-paste khi tạo layer template trong Pro):
DiaHinh_DuongBinhDo, PhuBeMat_CayDocLap, DanCu_Nha_Vung, GiaoThong_MepDuong, ThuyVan_DuongBoNuoc, GiaoThong_CauGiaoThong_Duong.

Hiện tại **vô hại** vì `labelVisibility` của cả 6 layer đều tắt. Nhưng nếu sau này ai đó bật labeling cho các layer này trong Pro mà không sửa expression trước, sẽ báo lỗi hoặc hiển thị rỗng vì field không tồn tại. Nên dọn lại trong project gốc.

## Lưu ý khi áp dụng sang web

- Width trong CIM là **point** ở scale tham chiếu của Pro, không phải pixel CSS — cần test trực quan ở zoom level thực tế và tinh chỉnh, không copy số 1:1.
- `dashTemplate` của Esri và `line-dasharray` của MapLibre đều là tỷ lệ tương đối, không cùng đơn vị tuyệt đối — copy đúng tỷ lệ [7,1,3,1] và [12,12] là hợp lý làm điểm khởi đầu, nhưng nên so trực quan lại.
- Toạ độ marker gốc dùng hệ trục Y hướng lên (kiểu toán học); khi build SVG tôi đã lật trục Y để khớp hệ hiển thị màn hình (Y hướng xuống) — nếu bạn tự dựng thêm marker khác từ `.mapx`, nhớ áp dụng lại phép lật này.
