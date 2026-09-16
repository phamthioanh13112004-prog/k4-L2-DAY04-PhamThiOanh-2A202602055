# Kiểm chéo bài bạn cùng nhóm

Người gán: *(tên bạn cùng nhóm)*   Người kiểm: Phạm Thị Oanh   Ngày: 16/09/2026

Lệnh đã chạy trước khi soi bằng mắt:

```bash
python3 tools/check_pose_labels.py --images dataset/images/train --labels <bài của họ>
python3 tools/visualize_pose.py --images dataset/images/train --labels <bài của họ> --out /tmp/vis_review
python3 tools/visibility_report.py --labels dataset/labels/train --compare <bài của họ>
```

## Reviewer checklist

| # | Mục kiểm | Đạt? | Ghi chú / ảnh nào |
| --- | --- | --- | --- |
| 1 | Mọi người trong ảnh đều có đủ 17 điểm, không ai bị thiếu | ☑ | `check_pose_labels.py` báo 0 lỗi thiếu điểm |
| 2 | Bật đường nối: không có xương nào cắt chéo ở vai hoặc hông | ☑ | Kiểm `visualize_pose.py` — không thấy xương cắt chéo ở thân |
| 3 | Không có xương nào kéo dài sang một cơ thể khác | ☑ | Ảnh nhiều người: `train_03`, `train_13`, `train_15`, `train_19` — xương không kéo sang người bên cạnh |
| 4 | Khớp bị che dùng `v = 1` **và có chấm**, không phải `v = 0` | ☐ | Một số khớp hông và mắt cá bị che nhưng gán `v = 0` — xem bảng lỗi bên dưới |
| 5 | `v = 0` chỉ xuất hiện ở khớp thật sự ra ngoài mép ảnh | ☐ | `left_hip` / `right_hip` ở một số ảnh gán `v = 0` mặc dù hông còn trong khung |
| 6 | Không có dấu hiệu dùng `Hidden` (điểm `v = 2` nằm ở chỗ vô lý) | ☑ | Không phát hiện trường hợp nào |
| 7 | Export đúng **COCO Keypoints 1.0**: mảng `keypoints` có 51 số mỗi người | ☑ | Đếm mảng `keypoints` trong JSON: đúng 51 số/người |
| 8 | Bản YOLO Pose: mỗi dòng 56 số, `kpt_shape: [17, 3]` | ☑ | `check_pose_labels.py` không báo lỗi định dạng |
| 9 | Visibility report đã nộp, và hai bảng đã được đặt cạnh nhau | ☐ | `reports/visibility_report.md` có nhưng chưa có bảng so sánh với bài tôi |
| 10 | Mọi ca không rõ đều được ghi trong `GUIDELINE_MINI.md` | ☐ | `GUIDELINE_MINI.md` chưa điền mục 3 (ba ca mơ hồ) |
| 11 | `check_pose_labels.py` chạy 0 lỗi | ☑ | Chạy sạch |

## Lỗi tìm được

| Ảnh | Người thứ | Khớp | Lỗi gì | Sửa thế nào |
| --- | ---: | --- | --- | --- |
| train_03 | 1 | `left_hip` | Gán `v = 0` nhưng hông còn trong khung, chỉ bị quần che | Đổi sang `v = 1`, giữ chấm tại vị trí ước lượng giải phẫu |
| train_03 | 1 | `right_hip` | Gán `v = 0` nhưng hông còn trong khung, chỉ bị quần che | Đổi sang `v = 1`, giữ chấm tại vị trí ước lượng giải phẫu |
| train_10 | 1 | `left_hip` | Gán `v = 0` nhưng hông còn trong khung | Đổi sang `v = 1`, đặt chấm ước lượng ngang mào chậu |
| train_10 | 1 | `right_hip` | Gán `v = 0` nhưng hông còn trong khung | Đổi sang `v = 1`, đặt chấm ước lượng ngang mào chậu |
| train_15 | 1 | `right_ankle` | Gán `v = 0` nhưng mắt cá còn trong khung, bị ống quần che | Đổi sang `v = 1`, đặt chấm theo trục xương ống chân |
| train_16 | 2 | `nose` | Gán `v = 0` trong khi vùng mũi vẫn còn trong khung (bị che bởi góc quay) | Đổi sang `v = 1`, đặt chấm ước lượng theo đường viền khuôn mặt |

## Hai câu kết luận

- **Lỗi lặp đi lặp lại nhiều nhất của bài này:** Gán `v = 0` cho các khớp bị che (đặc biệt `left_hip`, `right_hip`, mắt cá) thay vì `v = 1` — xoá khớp khỏi tính điểm OKS không cần thiết.
- **Đây là lỗi guideline chưa rõ**, không phải lỗi thao tác: phần mô tả phân biệt Occluded và Outside trong `GUIDELINE_MINI.md` chưa có ví dụ cụ thể cho hông và mắt cá, dẫn đến cả hai người trong nhóm đều có xu hướng mặc định `v = 0` cho những khớp không nhìn thấy trực tiếp. Cần bổ sung rule cụ thể vào mục 2 của `GUIDELINE_MINI.md`.
