# Mini annotation guideline — Ngày 3 (tracking)

> Điền file này **trong lúc gán nhãn**, không phải sau khi xong. Mỗi lần bạn dừng
> lại nghĩ "cái này tính sao nhỉ?" thì đó là một dòng phải ghi vào đây.
>
> Đây là tài liệu mà người gán nhãn tiếp theo sẽ đọc để làm giống bạn. Nếu hai
> người trong nhóm gán khác nhau, gần như luôn là vì file này chưa nói rõ — chứ
> không phải vì ai kém.

Nhóm / tên: `Nguyễn Anh Tuấn`
Clip: `clip_01`, `clip_02`

---

## 1. Phạm vi: gán cái gì, không gán cái gì

Một lớp duy nhất: **`vehicle`** — xe bốn bánh (xe con, van, xe buýt, xe tải).

| Gán | Không gán |
| --- | --- |
| xe con, SUV, taxi, xe bán tải | người đi bộ |
| van, minivan | xe đạp |
| xe buýt, minibus | **xe máy / mô tô** |
| xe tải, xe đầu kéo | xe trong ảnh quảng cáo, trong gương, dưới bóng nước |

Bổ sung của nhóm (nếu có): `không`

## 2. Luật ID — phần quan trọng nhất

| Tình huống | Luật của nhóm | Vì sao |
| --- | --- | --- |
| Xe bị che một phần rồi hiện lại | giữ nguyên ID nếu bị che **dưới 25 frame** (mặc định của lab: 25 frame = 2 giây @ 12.5 fps) | `Đảm bảo tính liên tục của track theo chuẩn 2 giây` |
| Xe bị che lâu hơn ngưỡng trên | `Tạo ID mới` | `Tránh nhầm lẫn ID khi vật thể xuất hiện lại sau thời gian quá dài` |
| Xe rời khung hình rồi quay lại | mặc định: **track mới** | `Coi như một đối tượng mới đi vào scene` |
| Hai xe cắt nhau / chồng lên nhau | `Giữ nguyên ID gốc cho từng xe` | `Duy trì nhất quán nhận dạng thị giác trước và sau khi overlap` |

## 3. Luật bbox

| Tình huống | Luật của nhóm |
| --- | --- |
| Xe bị cắt bởi rìa ảnh | bbox chạm đúng rìa, không đoán phần ngoài ảnh |
| Xe bị xe khác che một phần | bbox ôm phần **nhìn thấy được** |
| Xe vừa xuất hiện, còn rất nhỏ / rất mờ | bắt đầu track từ frame đầu tiên xác định được là xe bốn bánh; ngưỡng nhóm chọn: `nhìn rõ ít nhất 50% diện tích xe` |
| Xe đang đỗ, không di chuyển | `Giữ nguyên 1 ID xuyên suốt từ frame đầu đến frame cuối` |
| Keyframe đặt dày ở đâu | `Đặt dày ở các đoạn xe chuyển hướng, khuất bóng hoặc tăng/giảm tốc đột ngột` |

## 4. Ít nhất ba ca mơ hồ đã gặp thật

Ghi **frame cụ thể** và **ID cụ thể**, không ghi chung chung.

### Ca 1
- Clip / frame / ID: `clip_01 / Frame 1–11 / ID 1`
- Tình huống: `ID 1 xuất hiện ở góc trái màn hình (x=22, y=22) với kích thước rất lớn (419x380), sau đó biến mất hoàn toàn từ Frame 12 trở đi`
- Quyết định: `Giữ nguyên nhãn ID 1 từ Frame 1 đến 11, xoá bỏ/ngừng tracking ID này từ Frame 12`
- Lý do: `ID 1 di chuyển ra khỏi viền màn hình (cutting edge) và không còn xuất hiện lại trong clip.`

### Ca 2
- Clip / frame / ID: `clip_01 / Frame 1–146 / ID 2`
- Tình huống: `ID 2 đứng yên/di chuyển rất ít tại vị trí x ≈ 208-209, y ≈ 230-231 liên tục qua 146 frames.`
- Quyết định: `Giữ nguyên một ID 2 duy nhất cho toàn bộ clip, không ngắt hay gán ID mới.`
- Lý do: `Đối tượng liên tục visible và vị trí không đổi, đảm bảo tính nhất quán (ID consistency).`

### Ca 3
- Clip / frame / ID: `clip_01 / Frame 50–51 / ID 4`
- Tình huống: `ID 4 bắt đầu xuất hiện lần đầu tiên ở Frame 50 (x=101, y=200).  `
- Quyết định: `Tạo ID mới (ID 4) từ Frame 50 thay vì nối vào các ID cũ đã mất trước đó (như ID 1 hay ID 3).`
- Lý do: `Khoảng cách vị trí và thời gian xuất hiện không trùng khớp với các đối tượng cũ, tuân thủ quy tắc tạo ID mới khi xe mới đi vào khung hình.`

## 5. Sửa gì sau khi chấm với gold và sau khi kiểm chéo

Luật nào trong file này hoá ra còn thiếu hoặc còn mơ hồ? Viết lại cho rõ:

-Quy tắc cắt viền (Cutting edge): Cần quy định rõ tỉ lệ phần trăm visible bbox khi xe chạm viền (ví dụ: dưới 10% diện tích thì ngắt track) để tránh việc mỗi người gắn nhãn ngắt ID ở các frame khác nhau.

-Quy tắc gán lại ID (Re-identification): Rà soát lại số khung hình tối đa cho phép mất dấu (ví dụ: 25 frames); nếu xe bị che khuất quá số frame này rồi xuất hiện lại thì bắt buộc phải cấp ID mới chứ không nối ID cũ.
