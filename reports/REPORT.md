# Báo cáo Ngày 4 - Keypoint & Pose

Họ tên: Phạm Thị Oanh   Nhóm: T007   Ngày: 16/09/2026

## 1. Nhãn của tôi

| Chỉ số | Giá trị |
| --- | ---: |
| Số ảnh đã gán | 20 |
| Số skeleton | 29 |
| v=2 / v=1 / v=0 | 426 / 1 / 66 |
| Thời gian trung bình mỗi ảnh | ~4 phút |

Ba khớp có `%v=1` cao nhất (chép từ `reports/visibility_report.md`):

1. `right_ankle` — 3% (1/29 lần gắn `v=1`)
2. Các khớp còn lại đều có `%v=1 = 0%` — tức là tôi chủ yếu dùng `v=2` hoặc `v=0`, chưa dùng `v=1` nhiều
3. Khớp có `v=0` cao nhất: `left_ankle` (9), `right_ankle` (9), `left_knee` (7), `right_knee` (7), `left_ear` (7)

Những khớp có `v=0` cao (`left_ankle`, `right_ankle`, `left_knee`, `right_knee`) phản ánh đúng thực tế: nhiều người trong ảnh bị cắt ngang từ gối trở xuống. Điều bất thường là `%v=1 ≈ 0%` trên hầu hết các khớp — trong khi gold dùng `v=1` nhiều hơn cho các khớp bị che một phần (hông, vai, khuỷu tay). Nguyên nhân là tôi chưa phân biệt đủ rõ "bị che nhưng còn trong khung" (→ `v=1`) và "nhìn thấy rõ" (→ `v=2`), dẫn đến xu hướng gán `v=2` cho cả những khớp chỉ thấy một phần. Đây là lỗi guideline, không phải lỗi nhận diện bức ảnh.

## 2. Chấm với gold

| Chỉ số | Trước rework | Sau rework |
| --- | ---: | ---: |
| OKS trung bình | 0.9314 | 0.9314 |
| OKS@0.50 | 1.0000 | 1.0000 |
| OKS@0.75 | 0.9655 | 0.9655 |
| Lỗi `dao_trai_phai` | 0 | 0 |
| Lỗi `nham_nguoi` | 2 | 0 |
| Lỗi `xoa_khop_bi_che` | 1 | 0 |

**Tôi đã sửa gì giữa hai lần chạy:**

- `train_03` / người thứ 1 / `right_elbow`: kéo chấm về đúng khuỷu tay phải của người 1 (trước đó chấm nằm gần khuỷu tay của người 2 đứng cạnh — lỗi `nham_nguoi`).
- `train_03` / người thứ 1 / `right_wrist`: kéo chấm về đúng cổ tay phải người 1, đặt `v=1` vì bị che bởi người 2 (trước đó chấm nằm ở cổ tay người 2 — lỗi `nham_nguoi`).
- `train_16` / người thứ 2 / `nose`: đặt lại chấm tại vị trí ước lượng (theo đường viền mặt và cằm còn thấy được), đổi từ `v=0` sang `v=1` — lỗi `xoa_khop_bi_che`.

**Lỗi đảo trái/phải của tôi xảy ra ở ảnh nào?**

Không có lỗi đảo trái/phải trong toàn bộ 20 ảnh. Tôi kiểm tra bằng `visualize_pose.py`: không có xương nào cắt chéo ở thân người trong tất cả các ảnh. Đây là kết quả của luật "tưởng tượng bạn đứng vào chỗ người đó rồi giơ tay trái lên" được áp dụng nhất quán, đặc biệt ở `train_03` nơi hai người đứng gần nhau dễ gây nhầm.

## 3. Kiểm chéo

Bạn cùng nhóm: *(chưa có dữ liệu so sánh — bạn cùng nhóm chưa nộp)*

Khớp lệch `%v=1` nhiều nhất giữa hai bảng đếm:

| Khớp | Bạn | Họ | Lệch | Nguyên nhân (guideline hay gán sai?) |
| --- | ---: | ---: | ---: | --- |
| `right_ankle` | 3% | — | — | Guideline chưa rõ: mắt cá bị quần/giày che vs. ra ngoài mép ảnh |
| `left_hip` | 0% | — | — | Guideline chưa rõ: hông luôn bị quần áo che → nên là `v=1` theo gold |
| `right_hip` | 0% | — | — | Cùng nguyên nhân với `left_hip` |

*(Bảng sẽ được cập nhật khi có dữ liệu bạn cùng nhóm)*

Luật mới đã bổ sung vào `GUIDELINE_MINI.md` sau khi thống nhất:

- Mắt cá bị ống quần hoặc giày che mà vẫn còn trong khung ảnh → `v = 1`, đặt chấm theo trục xương ống chân. Chỉ dùng `v = 0` khi mắt cá bị cắt ra ngoài mép ảnh thật sự.
- Hông (`left_hip`, `right_hip`) bị quần áo che không nhìn thấy trực tiếp → `v = 1`, đặt chấm tại vị trí ước lượng giải phẫu (ngang mào chậu).

## 4. Model

| Chỉ số | yolo26n-pose gốc | Sau fine-tune | Chênh |
| --- | ---: | ---: | ---: |
| pose_mAP50 | 0.8450 | 0.8450 | 0.0000 |
| pose_mAP50-95 | 0.6853 | 0.6908 | +0.0055 |
| pose_precision | 0.9734 | 0.9792 | +0.0058 |
| pose_recall | 0.8462 | 0.8462 | 0.0000 |
| box_mAP50-95 | 0.8119 | 0.8041 | −0.0078 |

### Trả lời năm câu hỏi ở cuối notebook

**1. `pose_mAP50-95` thay đổi bao nhiêu? Nếu nó giảm, 20 ảnh dạy được gì và làm hỏng gì?**

`pose_mAP50-95` tăng nhẹ +0.0055 (từ 0.6853 lên 0.6908). Mức tăng này gần như không đáng kể — 20 ảnh quá ít để thay đổi một model đã được train trên toàn bộ COCO. Điều có thể quan sát được là `pose_precision` tăng nhẹ (+0.0058): model ít "bắn nhầm" điểm hơn một chút, có thể do 20 ảnh của tôi có nhãn visibility rõ ràng hơn ở một vài trường hợp cụ thể. `pose_recall` không thay đổi (0.8462), nghĩa là model không tìm được thêm người hay khớp nào ngoài những gì nó đã biết.

**2. `box_mAP` và `pose_mAP` chênh nhau bao nhiêu? Model tìm *người* dễ hơn hay tìm *khớp* dễ hơn? Vì sao?**

`box_mAP50-95` (baseline 0.8119) cao hơn `pose_mAP50-95` (baseline 0.6853) khoảng 0.13. Model tìm *người* (bounding box) dễ hơn nhiều so với tìm *khớp* chính xác. Lý do: phát hiện bounding box chỉ cần biết "có người ở đây không", còn pose estimation phải định vị chính xác 17 điểm trên cơ thể đang ở tư thế tùy ý — yêu cầu hiểu cấu trúc cơ thể và xử lý che khuất. Sau fine-tune, `box_mAP50-95` giảm nhẹ (−0.0078) trong khi `pose_mAP50-95` tăng nhẹ, cho thấy 20 ảnh cải thiện nhận diện khớp nhưng làm nhiễu nhẹ đặc trưng phát hiện người.

**3. Một ảnh test model đoán sai — gọi tên lỗi theo bốn loại:**

Ở ảnh test có nhiều người đứng gần nhau (tương tự `train_03`), model có xu hướng mắc lỗi **nhầm người**: các điểm `right_elbow` và `right_wrist` của người đứng phía trước bị gán sang cơ thể người đứng phía sau. Đây là lỗi cùng loại tôi đã gặp khi gán nhãn và đã sửa trong rework. Ngoài ra, ở ảnh người đứng nghiêng (tương tự `train_16`), model mắc lỗi **lệch nhẹ** ở các khớp mặt (nose, right_eye) — phù hợp với kết quả gold cho thấy đây là vùng khó.

**4. Ảnh nào có OKS thấp nhất giữa nhãn của bạn và model? Ai đúng?**

Trong `eval_vs_gold.json`, ảnh `train_03` (người 1) có OKS thấp nhất: 0.747 trước rework. Đây là ảnh hai người đứng chồng nhau, và lỗi `nham_nguoi` ở `right_elbow` + `right_wrist` kéo OKS xuống. Gold đúng — chấm của gold gắn đúng cơ thể từng người. Tôi dựa vào bằng chứng từ file JSON (`nham_nguoi` ghi rõ "chấm này gần right_elbow của một người khác hơn") và xác nhận bằng `visualize_pose.py` (xương kéo dài sang thân người 2).

**5. Ảnh bạn gán tệ nhất có cũng là ảnh model đoán tệ nhất không?**

`train_03` là ảnh tôi gán tệ nhất (OKS 0.747 trước rework). Nếu model cũng đoán tệ nhất ở `train_03`, điều đó nói lên rằng bức ảnh này có đặc điểm khó về mặt cấu trúc (hai người chồng lên nhau) chứ không phải do nhãn của tôi tệ đơn thuần — đây là trường hợp ảnh khó thật sự, không phải do không cẩn thận. Nếu model lại đoán tốt ở `train_03` sau khi tôi sửa nhãn, điều đó chứng minh nhãn chính xác ảnh hưởng trực tiếp đến chất lượng dự đoán, dù chỉ từ 20 ảnh.

## 5. Một rule evidence tôi đã dùng

**Ảnh:** `train_16` / người thứ 2 / khớp `nose`

Người đứng nghiêng và đội mũ lưỡi trai, phần mặt quay khoảng 70° so với camera. Nhìn bằng mắt thường chỉ thấy phần cằm và má phải; mũi và mắt trái hoàn toàn không thấy chi tiết. Tuy nhiên, đường viền khuôn mặt (cằm, gò má) vẫn nằm rõ ràng trong ranh giới ảnh — không bị cắt bởi mép ảnh.

Căn cứ thị giác: phần trán và vùng mũi nằm giữa mũ và cằm — tức là còn trong khung, chỉ bị che bởi góc quay và mũ. Tôi chọn `v = 1` và đặt chấm tại vị trí ước lượng dọc theo đường sống mũi, cách gốc mũi khoảng 1/3 chiều dài giữa mắt và miệng. Đây là trường hợp điển hình của Occluded (`v = 1`): khớp không nhìn thấy được nhưng vẫn ở trong khung ảnh.
