# Guideline gán nhãn — bản cá nhân (v0.1-cá-nhân)

- Tác giả: 2A202603161 - Phạm Văn Hùng
- Dựa trên: `guideline/guideline_v0_starter.md`
- Loại nhãn: bounding box (hộp chữ nhật) trên ảnh đường phố
- Công cụ: CVAT, xuất COCO 1.0

---

## 1. Mục tiêu

Nhãn dùng để huấn luyện mô hình phát hiện **người và phương tiện thật** trên ảnh đường phố (camera giao thông, xe tự lái).
Vì vậy chỉ gán vật **thật, đang hiện diện trực tiếp trong cảnh**. Tượng, mô hình, hình in, ảnh phản chiếu không phải vật thật, không dạy mô hình coi chúng là người/xe.

Nguyên tắc chung:
1. Mỗi vật thuộc đúng một lớp.
2. Không đoán: nếu không xác định được lớp hoặc ranh giới thì không vẽ box phỏng đoán.
3. Ca không chắc thì tick `can_review` và ghi *chưa chốt, cần họp*. Không tự đặt quy tắc riêng giữa chừng.

## 2. Lớp và thuộc tính

### 2.1 Lớp

| Lớp | Định nghĩa | Ví dụ | Phản ví dụ / ca dễ nhầm |
|---|---|---|---|
| `car` | Ô tô con thật, đang hiện diện trong cảnh. | Ô tô ở bãi đỗ, trên đường (img_015, img_040). | Xe mô hình, xe đồ chơi, xe trưng bày trên bệ (img_019, img_024, img_027, img_038). |
| `truck` | Xe tải, xe có thùng hàng riêng, xe chuyên chở. | Xe vận chuyển ô tô (img_002). | Hàng hoá chở trên xe: không gán. Xe bán tải thùng hở: *chưa chốt* (img_001, img_012). |
| `van` | Xe thùng kín cỡ nhỏ. | Xe thùng kín màu xanh / trắng (img_036, img_037). | Xe thùng kín lớn, xe buýt: không tự ép vào `van` (img_009, img_037). |
| `motorcycle` | Xe máy, mô tô. | Xe máy trong bãi, trên đường (img_005, img_006, img_030). | Xe đạp: **không** thuộc `motorcycle` (img_003, img_006, img_023, img_039). Tuk-tuk: không tự ép vào `motorcycle`. |
| `person` | Người **thật**, kể cả người lái và người ngồi sau trên xe. | Người đi bộ, người bán hàng, người đạp xe. | Tượng, ma-nơ-canh, hình nộm, hình người in trên biển quảng cáo / màn hình (img_011, img_017, img_018, img_026, img_033). |
| `ignore_region` | Vùng không gán từng vật; bỏ sót trong vùng này không tính là lỗi. | Vùng đám đông không tách được, vật quá nhỏ. | Không dùng để né những vật đã rõ ràng. |

**Vật không thuộc lớp nào** (xe đạp, xe buýt, tuk-tuk, động vật, thanh chắn, cọc tiêu, hàng hoá, giỏ): **không gán** vào lớp gần giống. Riêng tuk-tuk, xe buýt, xe bán tải, xe đạp còn *chưa chốt, cần họp* xem có thêm lớp hay không (xem mục 4).

### 2.2 Thuộc tính

| Thuộc tính | Giá trị | Ngưỡng đo được |
|---|---|---|
| `occluded_level` | `0` | thấy rõ, che dưới 10% diện tích vật |
| | `1` | che 10% đến dưới 40% |
| | `2` | che 40% đến 70% |
| | `3` | che trên 70%, vẫn còn nhận diện được lớp |
| `truncated` | `no` / `yes` | `yes` khi vật bị cắt bởi mép ảnh (khung ảnh), không phải bị vật khác che |
| `can_review` | tick / không | tick khi không chắc lớp, ranh giới hoặc mức che; ghi *chưa chốt, cần họp* |
## 3. Quy tắc vẽ box

1. **Ôm sát phần nhìn thấy.** Với vật bị che, box chỉ bao phần quan sát được, không bao vật đang che.
2. **Một vật một box.** Không gộp nhiều xe/người thành một box, kể cả khi xếp sát nhau (img_021, img_030), miễn vẫn phân biệt được từng vật.
3. **Người và xe tách riêng.** Người lái và mỗi người ngồi sau là một box `person`; xe là một box riêng (img_005, img_006, img_026).
4. **Xe chở hàng.** Chỉ gán box xe; hàng hoá, giỏ, thùng không gán (img_001, img_003, img_010, img_012).
5. **Xe chở xe.** Gán `truck` cho xe chuyên chở và `car` riêng cho từng ô tô được chở nếu là xe thật, nhìn rõ. Không gộp xe được chở vào box xe chở (img_002).
6. **Cắt mép.** Vật bị cắt bởi mép ảnh vẫn gán nếu nhận diện được; box kết thúc tại mép ảnh, không vẽ ra ngoài; đặt `truncated = yes` (img_015, img_032, img_040).
7. **Che một phần.** Vẫn gán nếu nhận diện được lớp; đánh `occluded_level` theo ngưỡng ở 2.2 (img_008, img_029).
8. **Vật mô phỏng không gán:** tượng, điêu khắc, ma-nơ-canh, hình nộm, xe mô hình, xe đồ chơi, xe trưng bày trên bệ, ô tô trong tủ kính. Không vẽ box.
9. **Hình in và phản chiếu:** người/xe in trên biển quảng cáo, màn hình, hình nền không phải vật thật, không gán (img_018, img_026). Đối tượng nhìn thấy qua gương chiếu hậu: *chưa chốt, cần họp* (img_020).
10. **Đèn, vệt sáng, bóng phản chiếu** trên đường không phải xe/người, không gán (img_013, img_031).
11. **Vật quá nhỏ:** box cao dưới 20 px thì không gán từng vật, vẽ `ignore_region` (img_025, img_032, img_039).
12. **Đám đông / mật độ quá dày:** nếu không thể tách được từng vật và ranh giới, vẽ `ignore_region` thay vì đoán (img_021, img_030).
13. **Nhoè chuyển động:** gán nếu vẫn xác định được hình dáng và ranh giới hợp lý; không vẽ box theo vệt nhoè quá rộng. Không xác định được thì tick `can_review`, không vẽ box phỏng đoán (img_014, img_034).
14. **Ảnh quá tối:** gán vật xác định được lớp và ranh giới; không xác định được thì không đoán, tick `can_review` (img_013, img_031).
15. **Động vật** không thuộc lớp nào, không gán (img_022).
16. **Thanh chắn, cọc tiêu, hàng rào, quầy hàng** không thuộc lớp nào, không gán (img_010, img_029, img_032).

## 4. Ca khó

| Ảnh | Tình huống | Quyết định của mình | Lý do |
|---|---|---|---|
| img_001, img_012 | Xe bán tải thùng hở / xe chở hàng | *Chưa chốt, cần họp.* Tạm tick `can_review`, không bỏ sót box. | v0 không định nghĩa bán tải: `car` hay `truck`? |
| img_002 | Xe vận chuyển ô tô | `truck` cho xe chở + `car` riêng cho từng xe được chở (nếu thật, nhìn rõ). | Nhiều đối tượng lồng nhau, không gộp box. |
| img_003, img_006, img_023, img_039 | Xe đạp | Không gán vào `motorcycle`. Người đạp xe vẫn gán `person`. Có thêm lớp xe đạp hay không: *chưa chốt, cần họp*. | Xe đạp không thuộc lớp v0, không ép lớp gần giống. |
| img_004, img_016, img_035, img_040 | Tuk-tuk / xe ba bánh | *Chưa chốt, cần họp* (thêm lớp, hay loại trừ). Tạm không ép thành `motorcycle`, tick `can_review`. | v0 không có lớp phù hợp. |
| img_005, img_037, img_009 | Xe buýt / xe khách / xe thùng kín lớn | *Chưa chốt, cần họp.* Không tự ép vào `van` hoặc `truck`; tick `can_review`. | Định nghĩa `van`/`truck` của v0 không đủ để phân biệt. |
| img_007 | Xe trong gara / xưởng | *Chưa chốt, cần họp* về phạm vi dữ liệu (có gán xe trong gara không). | v0 không nêu phạm vi. |
| img_011, img_017, img_033 | Tượng, điêu khắc, ma-nơ-canh | Không gán `person`. | Không phải người thật. |
| img_018, img_026 | Hình người/xe trên biển quảng cáo, màn hình | Không gán. | Hình in, không phải vật thật. |
| img_019, img_024, img_027, img_028, img_038 | Xe mô hình, xe đồ chơi, xe trưng bày, xe trong tủ kính | Không gán `car`/`truck`. Xe cổ trưng bày (img_028): chỉ gán nếu xác định là xe thật; không xác định được thì tick `can_review`. | Không phải phương tiện đang lưu thông. |
| img_020 | Phương tiện trong gương chiếu hậu | *Chưa chốt, cần họp.* Phải áp dụng nhất quán cho mọi ảnh. | v0 không có quy tắc phản chiếu. |
| img_015, img_032, img_040 | Xe bị cắt bởi mép ảnh | Gán, box dừng ở mép, `truncated = yes`. | Quy tắc 6, mục 3. |
| img_008, img_029 | Xe/người bị che một phần | Gán riêng từng vật, box ôm phần thấy, `occluded_level` theo ngưỡng 2.2. | Quy tắc 7, mục 3. |
| img_021, img_030 | Đám đông xe/người, xe xếp sát | Gán riêng từng vật khi còn phân biệt được; nếu không tách được thì `ignore_region`. | Quy tắc 2 và 12, mục 3. |
| img_025, img_032, img_039 | Người/xe rất nhỏ ở xa | Cao dưới 20 px: `ignore_region`. Từ 20 px trở lên: gán nếu nhận diện được. | Tránh mỗi người đặt một ngưỡng khác nhau. |
| img_013, img_031 | Ảnh tối, vệt sáng, phản chiếu | Không biến vệt sáng thành xe/người. Không xác định được thì `can_review`. | Không đoán. |
| img_014, img_034 | Nhoè chuyển động | Gán nếu xác định được; không thì `can_review`, không vẽ box phỏng đoán. | Quy tắc 13, mục 3. |
| img_022 | Động vật | Không gán. | Không thuộc lớp nào. |

### Câu hỏi chưa chốt (mang ra họp Pha 2)

1. Xe bán tải: `car` hay `truck`?
2. Tuk-tuk, xe đạp, xe buýt: thêm lớp mới, hay loại trừ (không gán / `ignore_region`)?
3. Phản chiếu trong gương (img_020): gán hay không?
4. Vật mô phỏng, hình in: không gán hay vẽ `ignore_region`?
5. Phạm vi dữ liệu: có gán xe trong gara / bãi / khu trưng bày không (img_007, img_015)?
6. Ngưỡng `occluded_level` (10 / 40 / 70%) và ngưỡng 20 px có được cả nhóm đồng ý không?
7. Ngưỡng độ nhoè và độ tối để chuyển từ "gán" sang `can_review` hoặc `ignore_region`.

## 5. QC và phiên bản

- Ca mơ hồ: tick `can_review`; không tự đoán, đem ra calibration.
- Gán độc lập, không xem nhãn của người khác trước khi nộp.
- Mọi thay đổi làm đổi nhãn đều tăng phiên bản guideline (v1.0 sau họp nhóm, v1.1 sau calibration).
- Khi IAA thấp: nếu cả hai người cùng lệch trên một loại ca thì sửa guideline; nếu chỉ một người lệch thì hướng dẫn lại người đó.

### Lịch sử thay đổi

| Phiên bản | Thay đổi | Ca liên quan |
|---|---|---|
| v0 | Bản giảng viên. | — |
| v0.1-cá-nhân | Thêm định nghĩa, ví dụ, phản ví dụ cho từng lớp; thêm ngưỡng `occluded_level` và ngưỡng 20 px; thêm quy tắc vật mô phỏng, hình in, che, cắt mép, xe chở xe, đám đông, nhoè, tối; liệt kê các câu hỏi chưa chốt. | img_001 – img_040 |
