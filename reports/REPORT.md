# Báo cáo Ngày 3 — Tracking Annotation

Chép file này thành `reports/REPORT.md` rồi điền. Giữ nguyên các tiêu đề.

Họ tên / nhóm: `Nguyễn Anh Tuấn`
Ngày: `15/9/2026`

---

## 1. Quá trình gán nhãn

| Mục | Giá trị |
| --- | --- |
| Công cụ | CVAT / khác: `CVAT` |
| Thời gian gán `clip_02` (warm-up) | `30` phút |
| Thời gian gán `clip_01` | `40` phút |
| Số track đã vẽ trong `clip_01` | `8` |
| Số keyframe trung bình mỗi track | `12` |

Ba tình huống khó nhất khi gán clip này, và bạn xử lý thế nào:

1. `Xe đi sát rìa/ra khỏi màn hình (như ID 1 ở frame 1–11): Chỉ gán bbox khi thấy rõ phần thân xe, ngắt track ngay khi xe chui hoàn toàn ra ngoài viền màn hình (cutting edge).`
2. `Xe đứng yên trong thời gian dài (như ID 2 từ frame 1–146):** Tạo keyframe đầu và cuối, sử dụng tính năng nội suy (interpolation) của CVAT để tránh phải chỉnh thủ công từng frame`
3. `Đối tượng mới chui vào màn hình ở giữa clip (như ID 4 xuất hiện từ frame 50):** Kiểm tra kỹ các đối tượng đã mất trước đó để tránh gán nhầm ID cũ; xác nhận xe mới hoàn toàn rồi mới tạo ID mới`

## 2. Tự kiểm và kiểm chéo

Ba lượt tua bắt được gì (lượt 1 nhìn ID, lượt 2 frame đầu/cuối, lượt 3 frame giữa):

- Lượt 1: `...`
- Lượt 2: `...`
- Lượt 3: `...`

Kiểm chéo với: `...`. Chi tiết ở `reports/review_partner.md`.
Số lỗi bạn tìm được trong bản của bạn ấy: `...`. Số lỗi bạn ấy tìm được trong bản của bạn: `...`.

Ca nào hai người quyết khác nhau, và luật nào còn thiếu trong `GUIDELINE_MINI.md`?

`...`

**Em làm 1 mình ạ ,chưa kịp kiểm chéo thì đã hết giờ ạ**

## 3. Pre-gold lock và chấm trước/sau rework

| Evidence | Giá trị |
| --- | --- |
| SHA-256 từ `evidence/pre-gold/clip_01/manifest.json` | `dd13d0e099d413310c27ffbb5779b9ba3c33add879126bbab1bbe26302c56ae8` |
| Thời điểm khóa | `2026-09-15T13:58:48` |
| Số row / frame / track trước khi mở reference | `647 rows / 190 frames / 8 tracks` |

| | HOTA | DetA | AssA | LocA | IDF1 | MOTA | MOTP | FP | FN | IDSW |
| --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| Bản pre-gold |0.780 |0.756 |0.808 |0.878 | 0.936| 0.864| 0.865|76 |2 |0 |
| Sau rework | | | | | | | | | | |
**chưa rework**
Qua cổng (`IDF1 >= 0.80`, `MOTA >= 0.75`, `MOTP >= 0.70`): **có / chưa**

Sau khi đọc danh sách lỗi, bạn đã sửa cụ thể những gì? Ghi theo frame và ID:

| Loại lỗi | Frame | ID | Đã sửa thế nào |
| --- | --- | --- | --- |
|BBOX thừa |51–78 | 5|Xóa bbox trước khi xe ID5 thực sự xuất hiện |
|BBOX thừa | 133–135| 8| Xóa bbox sau khi xe ID8 không còn tương ứng|
|BBOX lệch |80 |5 |Chỉnh lại bbox, thêm keyframe |
**Đã sửa thế nào là phương án sửa , trên thực tế em chưa kịp rework và sửa **
## 4. Kết quả model: ByteTrack control vs ReID treatment

Cấu hình từ `outputs/model_run_config.json`:

| Mục | Giá trị |
| --- | --- |
| Python / ultralytics / torch / lap | `Python 3.13.15 / Ultralytics 8.4.145 / Torch 2.11.0+cpu / LAP 0.5.13` |
| weights / hai tracker | `yolo26n.pt / ByteTrack (bytetrack.yaml) / BoT-SORT + ReID (botsort-reid.yaml)` |
| conf / IoU / imgsz / classes | `0.25 / 0.70 / 960 / [2, 5, 7]` |
| device | `cpu` |

| So sánh | HOTA | DetA | AssA | LocA | IDF1 | MOTA | MOTP | FP | FN | IDSW |
| --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| Bạn vs gold | 0.780 | 0.756 | 0.808 | 0.878 | 0.936 | 0.864 | 0.865 | 76 | 2 | 0 |
| ByteTrack control vs gold | 0.709 | 0.649 | 0.776 | 0.846 | 0.875 | 0.749 | 0.823 | 88 | 54 | 2 |
| BoT-SORT + ReID vs gold | 0.763 | 0.711 | 0.820 | 0.872 | 0.900 | 0.792 | 0.860 | 91 | 26 | 2 |
| ReID vs bạn | 0.759 | 0.698 | 0.826 | 0.919 | 0.858 | 0.719 | 0.914 | 86 | 95 | 1 |

## 5. Phân tích — năm câu hỏi

**1. MOTA của bạn cao hơn hay thấp hơn IDF1? Nếu MOTA cao mà IDF1 thấp thì điều đó nói gì, và vì sao MOTA không phạt nặng lỗi ID?**

MOTA của nhãn của em thấp hơn IDF1: MOTA = 0.864, trong khi IDF1 = 0.936. Nhãn của em có FP = 76, FN = 2 và IDSW = 0. Điều này cho thấy annotation có chất lượng tốt cả về detection lẫn duy trì identity. Nếu một trường hợp có MOTA cao nhưng IDF1 thấp thì có thể hiểu là model/annotation phát hiện đúng nhiều object, ít FP/FN, nhưng việc duy trì đúng ID giữa các frame còn kém.

MOTA chủ yếu bị ảnh hưởng bởi FN, FP và ID switch theo dạng cộng/trừ trong công thức, nên một số lỗi ID có thể không làm MOTA giảm mạnh nếu số lượng FP/FN lớn hơn nhiều. Vì vậy cần xem thêm IDF1/AssA để đánh giá riêng khả năng duy trì identity. Trong trường hợp của em, IDSW = 0 nên MOTA và IDF1 đều ở mức cao.

**2. ByteTrack control và BoT-SORT + ReID treatment khác nhau thế nào ở IDF1, AssA và IDSW? Dẫn một frame sequence để giải thích treatment tốt hơn, tệ hơn hoặc không đổi đáng kể. Nhắc rõ đây không cô lập causal effect của ReID vì hai tracker implementation khác.**

So với gold:

ByteTrack control: IDF1 = 0.875, AssA = 0.776, IDSW = 2.
BoT-SORT + ReID: IDF1 = 0.900, AssA = 0.820, IDSW = 2.

Như vậy treatment ReID tốt hơn về khả năng duy trì identity: IDF1 tăng 0.025, AssA tăng 0.044. Tuy nhiên IDSW không giảm, vẫn là 2. Với ByteTrack, một ID switch xảy ra ở frame 59 và một ở frame 94; với BoT-SORT + ReID, các ID switch xảy ra ở frame 87 và frame 113.
Một sequence đáng chú ý là frame 87: BoT-SORT + ReID đổi ID của gold track 5 từ ID 17 sang ID 18. Khi so với annotation của em, đây cũng là vị trí có khác biệt ID giữa hai bản.
Kết quả cho thấy ReID treatment cải thiện AssA và IDF1 nhưng không loại bỏ được ID switch. Tuy nhiên đây không phải causal effect được cô lập của ReID, vì control và treatment sử dụng hai tracker implementation khác nhau: ByteTrack và BoT-SORT + ReID.

**3. DetA, FP và FN đổi thế nào? Lỗi còn lại là detector hay association?**

So với ByteTrack, BoT-SORT + ReID có DetA tăng từ 0.649 lên 0.711, FN giảm từ 54 xuống 26, nhưng FP tăng nhẹ từ 88 lên 91. Vì vậy treatment cải thiện đáng kể khả năng giữ/phủ object, đặc biệt giảm số frame bị bỏ sót, nhưng đồng thời tạo thêm một số bbox dư.

Với annotation của em so với gold, DetA = 0.756, FP = 76, FN = 2, AssA = 0.808 và IDSW = 0. Diagnostic còn lại chủ yếu là bbox treo/bbox thừa và bbox trôi, ví dụ ID 5 có bbox ở frame 51–78 trước khi track tham chiếu xuất hiện; ngoài ra có các bbox IoU thấp ở frame 55, 80 và 96. Vì vậy với annotation của em, lỗi còn lại nghiêng nhiều hơn về localization/biên của bbox và thời điểm bắt đầu/kết thúc track, không phải association.

**4. Một chỗ bạn đúng và ReID sai (frame, ID, vì sao):**

Một điểm có thể dùng là frame 87, track 5. So sánh trực tiếp cho thấy annotation của em và ReID có khác biệt ID tại frame này; ReID chia track thành ID 17 và 18, trong khi annotation của em giữ track 5 thành một ID xuyên suốt. Quan trọng hơn, annotation của em đạt IDSW = 0 khi đánh giá với gold, trong khi ReID có ID switch tại frame 87. Vì vậy tại điểm này, evidence từ gold ủng hộ annotation của em hơn ReID.

**Stretch — ngưỡng ReID**

| cấu hình | HOTA | DetA | AssA | IDF1 | FP | FN | IDSW |
|---|---:|---:|---:|---:|---:|---:|---:|
| ReID appearance=0.7 | 0.763 | 0.711 | 0.820 | 0.900 | 91 | 26 | 2 |
| ReID appearance=0.8 | 0.763 | 0.711 | 0.820 | 0.900 | 91 | 26 | 2 |
| ReID appearance=0.9 | 0.763 | 0.710 | 0.820 | 0.899 | 91 | 27 | 2 |

**Insight:** Trong clip này, thay đổi ngưỡng ReID không phải là nút thắt chính của tracking: từ 0.7 → 0.9, IDSW vẫn cố định ở 2, trong khi biến động chủ yếu xuất hiện ở FN/DetA và chỉ ở mức rất nhỏ.

**5. Một chỗ ReID làm bạn xem lại annotation (frame, ID, vì sao), hoặc lý do evidence cho thấy model sai:**

Có thể dùng frame 113–117, track 6 để xem lại bbox. ReID có bbox với IoU thấp tại các frame này, cụ thể frame 113 và 114 chỉ khoảng 0.57, trong khi frame 104 còn khoảng 0.52. Đây là evidence để kiểm tra lại cách đặt bbox và keyframe trong annotation. Tuy nhiên evidence hiện có không đủ để kết luận annotation của em sai, vì đây là kết quả của model so với gold chứ không phải bằng chứng trực tiếp rằng annotation của em cần sửa.

Do đó, em ghi nhận đây là một điểm cần review chứ không tự ý sửa annotation chỉ vì model khác annotation.

## 6. Nếu phải gán thêm 10 clip nữa

Bạn sẽ sửa gì trong `GUIDELINE_MINI.md`, và đổi gì trong quy trình làm việc của mình?

Nếu phải gán thêm 10 clip, em sẽ sửa GUIDELINE_MINI.md theo hướng quy định rõ hơn ba trường hợp dễ gây lỗi:

Xe vào/ra khỏi khung: xác định rõ frame cuối cùng còn thấy xe và frame bắt đầu xuất hiện xe; không để bbox tồn tại trước khi xe xuất hiện hoặc sau khi xe đã ra khỏi khung.
Xe đứng yên hoặc chuyển động ít: dùng keyframe ở đầu/cuối và thêm keyframe khi bbox bắt đầu trôi thay vì chỉ phụ thuộc vào interpolation.
Xe mới xuất hiện: trước khi tạo ID mới cần kiểm tra các track trước đó để tránh reuse ID của xe cũ.

Trong quy trình làm việc, em sẽ dành một lượt kiểm tra cuối clip theo timeline để rà các lỗi start/end của track, sau đó rà các đoạn giữa hai keyframe có nguy cơ bbox bị trôi. Với những frame khó, ưu tiên kiểm tra continuity của ID trước rồi mới tinh chỉnh bbox.

Các lỗi diagnostic hiện tại cho thấy việc kiểm tra start/end của track và thêm keyframe ở những đoạn bbox trôi là hai điểm cần ưu tiên khi mở rộng sang nhiều clip.

## 7. Tệp đã nộp

- [ ] `annotations/clip_01/gt.txt`
- [ ] `annotations/clip_02/gt.txt`
- [ ] `evidence/pre-gold/clip_01/gt.txt` và `manifest.json`
- [ ] `GUIDELINE_MINI.md` đã điền
- [ ] `outputs/eval_vs_gold.json`
- [ ] `outputs/model_bytetrack_clip_01.txt`
- [ ] `outputs/model_reid_clip_01.txt`
- [ ] `outputs/model_run_config.json`
- [ ] `outputs/eval_bytetrack_vs_gold.json`, `outputs/eval_reid_vs_gold.json`, `outputs/eval_reid_vs_me.json`
- [ ] `reports/review_partner.md`
- [ ] `reports/REPORT.md` (file này)
