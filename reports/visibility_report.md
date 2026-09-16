# Visibility report

- Thư mục nhãn: `dataset\labels\train`
- 20 ảnh, 29 skeleton, trung bình 14.72 khớp có v > 0 mỗi người
- Tổng: v=2 426 | v=1 1 | v=0 66

| # | Khớp | v=2 | v=1 | v=0 | %v=1 |
| ---: | --- | ---: | ---: | ---: | ---: |
| 0 | nose | 24 | 0 | 5 | 0% |
| 1 | left_eye | 25 | 0 | 4 | 0% |
| 2 | right_eye | 23 | 0 | 6 | 0% |
| 3 | left_ear | 22 | 0 | 7 | 0% |
| 4 | right_ear | 25 | 0 | 4 | 0% |
| 5 | left_shoulder | 29 | 0 | 0 | 0% |
| 6 | right_shoulder | 29 | 0 | 0 | 0% |
| 7 | left_elbow | 27 | 0 | 2 | 0% |
| 8 | right_elbow | 28 | 0 | 1 | 0% |
| 9 | left_wrist | 27 | 0 | 2 | 0% |
| 10 | right_wrist | 27 | 0 | 2 | 0% |
| 11 | left_hip | 29 | 0 | 0 | 0% |
| 12 | right_hip | 28 | 0 | 1 | 0% |
| 13 | left_knee | 22 | 0 | 7 | 0% |
| 14 | right_knee | 22 | 0 | 7 | 0% |
| 15 | left_ankle | 20 | 0 | 9 | 0% |
| 16 | right_ankle | 19 | 1 | 9 | 3% |

## Đọc bảng này thế nào

1. Khớp nào có **%v=1 cao**: khớp hay bị che. Cổ tay và hông thường là hai vị trí cần xem lại guideline trước khi kết luận.
2. Khớp nào có **v=0 cao bất thường**: mọi người đang dùng Outside ở chỗ đáng lẽ là Occluded. Đó là lỗi số 3 của slide 46, và nó xoá thẳng khớp đó khỏi bảng điểm OKS.
3. Khi so hai người: **lệch lớn = bất đồng về guideline**, không phải về bức ảnh. Sửa guideline trước, sửa nhãn sau.
