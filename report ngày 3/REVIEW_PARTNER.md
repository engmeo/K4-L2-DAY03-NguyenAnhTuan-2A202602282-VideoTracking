# Peer review — Day 3

Chép file này thành `reports/review_partner.md`. Reviewer chỉ ghi finding; tác giả
tự sửa bài của mình và điền closure.

| Trường | Giá trị |
| --- | --- |
| Author | `Nguyễn Anh Tuấn` |
| Reviewer | `Làm một mình` |
| Pair ID | `không có` |
| CVAT version | `v2.74.1` |
| Thời điểm review | `chưa thực hiện peer review` |

## Danh sách finding

Mỗi finding bắt buộc có `frame + ID + lỗi + sửa thế nào`. Nếu chưa thống nhất,
dùng `needs-review`; không ép tác giả sửa theo cảm tính.

**Do em làm một mình và chưa có reviewer nên chưa thực hiện peer review độc lập. Các lỗi dưới đây là kết quả đối chiếu annotation với gold, không phải finding của peer reviewer.**
| # | CVAT frame | MOT frame | ID | Loại lỗi | Quan sát + rule áp dụng | Cách sửa đề xuất | Closure: fixed / not-a-defect / needs-review |
| ---: | ---: | ---: | ---: | --- | --- | --- | --- |
| 1 |51–78 |51–78 |5 |box treo |ID 5 có bbox trước khi track tham chiếu xuất hiện. Rule: không để bbox tồn tại trước khi xe xuất hiện. | Kiểm tra frame bắt đầu của ID 5 và đặt outside tại frame xe thực sự xuất hiện.|needs-review |
| 2 | 77–100| 77–100|6 | box treo| ID 6 có bbox trước khi track tham chiếu xuất hiện. Rule: entry/exit phải đúng, không box|Kiểm tra endpoint/startpoint của ID 6 và chỉnh lại frame bắt đầu. |needs-review |
| 3 |80 |80 |5 |box trôi |IoU của bbox track 5 tại frame 80 chỉ khoảng 0.55, cho thấy bbox có thể bị lệch giữa hai keyframe. |Kiểm tra bbox tại frame 80 và thêm keyframe nếu cần. |needs-review |

## Reviewer checklist

| Hạng mục | PASS / FINDING / N/A | Frame–ID–evidence |
| --- | --- | --- |
| Có tối thiểu 6 track hợp lệ; chỉ gồm xe bốn bánh | | |
| Một xe giữ một ID; không reuse ID cho xe khác | | |
| Occlusion ngắn giữ ID; crossing không đổi ID | | |
| Entry/exit đúng; không box treo sau khi xe rời khung |FINDING | ID 5: frame 51–78; ID 6: frame 77–100; còn các endpoint khác cần review|
| Bbox ôm phần nhìn thấy, không đoán phần bị che/ngoài khung | | |
| Frame giữa hai keyframe không bị interpolation drift |FINDING | ID 5 frame 80, IoU ≈ 0.55|
| Export đúng MOT 1.1; frame bắt đầu từ 1; cột 2 là track ID | | |
| Mọi finding có cách sửa và closure do tác giả điền | | |

## Self-QC attestation của reviewer

| Lượt | PASS / ĐÃ SỬA / NEEDS-REVIEW | Frame–ID–evidence |
| --- | --- | --- |
| 1 — identity/timeline | NEEDS-REVIEW|Chưa có reviewer độc lập |
| 2 — endpoint/scope |NEEDS-REVIEW |ID 5 frame 51–78; ID 6 frame 77–100 |
| 3 — geometry/interpolation | NEEDS-REVIEW| ID 5 frame 80, IoU ≈ 0.55|

## Exit ticket

1. Finding quan trọng nhất và rule dùng để kết luận: `Bbox treo ở endpoint/startpoint của track; rule: không để bbox tồn tại trước khi xe xuất hiện hoặc sau khi xe đã rời khung. Evidence: ID 5 frame 51–78 và ID 6 frame 77–100.`
2. Một finding tác giả đóng là `not-a-defect`, kèm lý do (nếu có): `Chưa có — chưa thực hiện peer review nên chưa đủ evidence để đóng finding là not-a-defect.`
3. Một rule cần Lab Coach làm rõ (nếu có): `Cần làm rõ cách xử lý các frame ở sát thời điểm xe vào/ra khỏi khung, đặc biệt khi chỉ còn một phần xe nhìn thấy.`
