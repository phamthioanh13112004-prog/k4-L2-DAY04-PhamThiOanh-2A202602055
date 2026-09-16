# Mini guideline - nhóm: T007  |  người gán: Phạm Thị Oanh  |  ngày: 16/09/2026

> Điền file này **trong lúc** gán nhãn, không phải sau khi xong. Mỗi lần bạn dừng lại
> hơn 10 giây để phân vân, đó là một dòng phải ghi vào đây.

## 1. Luật bắt buộc (đã thống nhất cả lớp - không sửa)

- Bộ 17 điểm COCO, đúng tên, đúng thứ tự. Lấy từ file `.SVG` chung.
- Mọi người trong ảnh đều có **đủ 17 điểm**. Điểm không dùng được thì gắn cờ, không xoá.
- Trái/phải tính theo **cơ thể người**, không theo bức ảnh.
- Bị che, còn trong khung -> `v = 1`, **vẫn đặt chấm** ở vị trí ước lượng.
- Ra ngoài mép ảnh -> `v = 0`, **không** đặt chấm.
- Không dùng `Hidden` (`h`) - nó không được lưu vào file.

## 2. Luật của nhóm (đã thống nhất)

| Tình huống | Luật nhóm chọn | Vì sao |
| --- | --- | --- |
| Hông của người mặc quần áo dài | Ước lượng vị trí hông theo giải phẫu (ngang mào chậu), ghi `v = 2` nếu đường viền quần/váy cho thấy vị trí; ghi `v = 1` nếu quần áo che hoàn toàn không đoán được | Hông là điểm giải phẫu, không phải điểm nhìn thấy trực tiếp — luôn phải đặt chấm |
| Tai bị tóc hoặc mũ bảo hiểm che một phần | Ghi `v = 1` và đặt chấm ở vị trí ước lượng theo đường viền tai/mũ | Tai còn trong khung, chỉ bị che — đúng định nghĩa Occluded |
| Người bị cắt ở mép ảnh (chỉ thấy từ hông trở lên) | Các khớp bị cắt ra ngoài (gối, mắt cá) ghi `v = 0`; các khớp còn trong khung dù bị che vẫn ghi `v = 1` | Ra ngoài mép = Outside; bị che trong khung = Occluded — hai trường hợp khác nhau |
| Cổ tay nằm sau tay lái / sau thân mình | Ghi `v = 1`, đặt chấm ở vị trí ước lượng theo hướng khuỷu tay | Cổ tay bị che nhưng còn trong khung — không được dùng `v = 0` |
| Hai người chồng lên nhau | Làm xong hẳn một người (17 điểm) rồi mới sang người kế tiếp; khớp nào của người này nằm trên người kia thì vẫn đặt đúng vị trí giải phẫu của người đang gán | Tránh lỗi "nhầm người" — xương không được kéo sang cơ thể bên cạnh |
| Người nhỏ đến mức nào thì không gán nữa | Gán tất cả người mà mắt thường nhận ra được là người, kể cả ở xa; chỉ bỏ qua nếu kích thước skeleton dự kiến < 20 px chiều cao | Bộ ảnh đã được chọn sẵn đủ lớn — nếu phân vân thì gán và ghi vào mục 3 |

## 3. Ba ca mơ hồ đã gặp

### Ca 1 - ảnh `train_03`, người thứ `1`, khớp `right_elbow` và `right_wrist`

- **Mơ hồ ở chỗ nào:** Hai người đứng rất gần nhau, cánh tay phải của người 1 chồng lên thân người 2. Khó phân biệt `right_elbow` của người 1 hay của người 2.
- **Bạn quyết thế nào:** Theo nguyên tắc "làm xong hẳn một người rồi mới sang người kế tiếp", xác định trục xương từ vai phải người 1 kéo dài để ước lượng vị trí khuỷu và cổ tay; đặt chấm ở đó với `v = 1` vì bị che bởi người kia.
- **Vì sao:** Chấm phải gắn đúng cơ thể đang gán; kéo chấm sang cơ thể người 2 là lỗi `nham_nguoi`.
- **Nếu người khác quyết ngược lại:** Model học right_elbow/right_wrist nằm ở vị trí của người 2 → khi augment lật ảnh, nó lại học thêm sai lần nữa → bộ xương luôn bị lệch ở tình huống người đứng gần nhau.

### Ca 2 - ảnh `train_15`, người thứ `1`, khớp `right_wrist`

- **Mơ hồ ở chỗ nào:** Cổ tay phải gần như khuất hẳn sau thân mình người, chỉ thấy một phần nhỏ cạnh sườn. Không rõ đặt `v = 1` hay `v = 0`.
- **Bạn quyết thế nào:** Ghi `v = 2` (nhìn thấy rõ phần cổ tay) và đặt chấm tại rìa cạnh sườn nơi cổ tay nhô ra. Kết quả OKS cho thấy lệch 70 px — nên sửa lại thành `v = 1` ở vị trí ước lượng kỹ hơn.
- **Vì sao:** Lỗi phổ biến: đặt `v = 2` khi chỉ thấy một phần nhỏ mà không chắc vị trí chính xác — nên dùng `v = 1` và ước lượng cẩn thận hơn.
- **Nếu người khác quyết ngược lại (v = 0):** Khớp bị loại khỏi OKS hoàn toàn, model không học được pose với tay gập vào người — mất thông tin huấn luyện quan trọng.

### Ca 3 - ảnh `train_16`, người thứ `2`, khớp `nose` và `right_eye`

- **Mơ hồ ở chỗ nào:** Người đứng nghiêng và đội mũ, phần mặt gần như không thấy. Phân vân giữa `v = 0` (ra ngoài góc nhìn) và `v = 1` (bị che mà còn trong khung).
- **Bạn quyết thế nào:** Ghi `v = 0` ban đầu vì nghĩ mặt đã xoay ra khỏi ảnh. Gold cho thấy `nose` phải là `v = 1` (gold có `v = 1`) và `right_eye` có `v = 2` — tức là gold thấy mặt vẫn còn trong khung. Đây là lỗi `xoa_khop_bi_che` và `thieu_khop` trong file JSON.
- **Vì sao:** Phải kiểm tra đường viền đầu và cổ trong ảnh — nếu vùng mặt vẫn nằm trong ranh giới ảnh dù không thấy chi tiết, thì đặt `v = 1`, không phải `v = 0`.
- **Nếu người khác quyết ngược lại (v = 2 cho mũi không nhìn thấy):** Model học mũi ở vị trí không thật → điểm OKS giảm và model dự đoán sai pose khi người quay nghiêng.

## 4. Sau khi so visibility report với bạn cùng nhóm

- Khớp lệch `%v=1` nhiều nhất: `right_ankle` (bạn `3%` / họ cần so sánh thêm)
- **Nguyên nhân:** Guideline chưa phân biệt rõ "mắt cá bị giày/quần ống rộng che" (→ `v = 1`) và "mắt cá ra ngoài mép ảnh" (→ `v = 0`). Nhóm gán `v = 0` cho cả hai trường hợp.
- **Luật mới bổ sung vào mục 2 sau khi thống nhất:**
  - Mắt cá bị ống quần hoặc giày che nhưng vẫn còn trong khung ảnh → `v = 1`, đặt chấm tại vị trí khớp mắt cá ước lượng theo trục xương ống chân. Chỉ dùng `v = 0` khi bàn chân/mắt cá thật sự bị cắt ra ngoài mép ảnh.
