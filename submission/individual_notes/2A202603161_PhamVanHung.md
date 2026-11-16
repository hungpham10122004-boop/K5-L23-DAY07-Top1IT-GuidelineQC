# Ghi chú cá nhân — 2A202603161 - Phạm Văn Hùng

## 1. Pha 1: ba ca khó tôi tìm ra trong 40 ảnh

| Ảnh | v0 không trả lời được gì | Quyết định ban đầu của tôi |
|---|---|---|
| img_020 | Có phương tiện xuất hiện trong gương chiếu hậu. v0 chưa nói có gán vật thể phản chiếu hay chỉ gán phương tiện xuất hiện trực tiếp trong cảnh. | Chưa chốt: tick `can_review` để nhóm thống nhất một nguyên tắc chung; không tự ý gán hoặc bỏ qua khác nhau giữa các ảnh. |
| img_033 | Có ma-nơ-canh (mannequin) hình dáng giống người. v0 chưa quy định có loại trừ tượng, ma-nơ-canh hoặc hình nộm hay không. | Không gán thành `person` vì không phải người thật; chỉ gán người thật khi xuất hiện và nhận diện được. |
| img_038 | Có ô tô đồ chơi / mô hình trưng bày, hình dáng giống `car`. v0 chưa quy định cách xử lý đồ chơi hoặc mô hình. | Không gán thành `car`; đề xuất bổ sung quy tắc loại trừ vào guideline. |

## 2. Pha 2: chỗ tôi bất đồng với nhóm

Chưa họp nhóm nên chưa có kết luận. Các điểm cần nhóm thống nhất: phương tiện trong gương (img_020), ma-nơ-canh (img_033), xe mô hình / đồ chơi (img_038). Tôi nghiêng về việc không gán vật mô phỏng thành người/phương tiện thật; riêng vật phản chiếu cần một nguyên tắc chung.
## 3.Pha 3: ảnh tôi đã gán

- Số ảnh được giao: … Số box đã vẽ: … Số lần tôi tick `can_review`: …
- Ca tôi lưỡng lự nhất khi gán (ảnh, vì sao): …

Dự kiến: img_020 sẽ là ca khó gán nhất vì v0 không xác định có gán phương tiện trong gương hay không; nếu mỗi người hiểu khác nhau, nhãn sẽ thiếu nhất quán.

## 4. Pha 4–5: tôi lệch ở đâu

- Các ca có tên tôi, và tôi lệch vì guideline mơ hồ hay vì chưa nắm quy tắc: …
- Quyết định calibration tôi thấy quan trọng nhất: …

Dự kiến: quyết định quan trọng là cách xử lý vật không phải vật thật (ma-nơ-canh, đồ chơi, tượng) và vật xuất hiện trong gương.

