# BÁO CÁO CÁ NHÂN — NGÀY 2 OBJECT DETECTION

## 1. Thông tin học viên

- **Họ tên:** Lê Ngọc Dũng
- **MSSV:** 2A202603103
- **Email:** bachdungbiu3010@gmail.com
- **Lớp:** K5-L2A
- **Link Repository:** https://github.com/bachdungbiu3010-art/K5-L2-Day02-ObjectDetection
- **Link Notebook Colab:** https://colab.research.google.com/github/bachdungbiu3010-art/K5-L2-Day02-ObjectDetection/blob/main/notebooks/day2_annotation_to_yolo.ipynb

## 2. Schema và Mini Guideline

Tài liệu: [GUIDELINE_MINI.md](../../GUIDELINE_MINI.md)

### 2.1. Định nghĩa bốn lớp

| Class | Tên lớp | Định nghĩa và cách nhận diện |
|---|---|---|
| 0 | `car` | Xe ô tô con, sedan, SUV, taxi, pickup |
| 1 | `truck` | Xe tải, xe ben, xe đầu kéo, phương tiện vận chuyển hàng hóa |
| 2 | `bus` | Xe buýt, xe khách, minibus |
| 3 | `van` | Xe van, minivan, xe có thân dạng hộp thuộc nhóm van |

### 2.2. Ba tình huống mơ hồ trong quá trình gán nhãn

**Tình huống 1 – drive_024.jpg: Phân biệt car và van**

Trong ảnh có các xe thân cao dạng hộp, dễ nhầm với SUV.

- Quyết định: Gán `van` khi xác định phương tiện thuộc nhóm van/minivan.
- Cơ sở: Phân biệt dựa vào hình dạng và đặc điểm phương tiện theo schema.

**Tình huống 2 – drive_042.jpg: Xe tải chở hàng cồng kềnh**

Phương tiện có hàng hóa chất cao trên thùng, gây khó khăn khi xác định phạm vi Bounding Box.

- Quyết định: Gán lớp `truck`.
- Cơ sở: Phương tiện có kết cấu và chức năng vận chuyển hàng hóa.

**Tình huống 3 – drive_001.jpg: Xe nhỏ ở xa**

Trong ảnh xuất hiện nhiều phương tiện nhỏ, khó nhận diện chính xác loại xe.

- Quyết định: Gán `car` cho những phương tiện xác định được là ô tô con.
- Cơ sở: Phân loại theo đặc điểm có thể quan sát được; cần áp dụng nhất quán quy tắc đối với xe nhỏ.

### 2.3. Nội dung cần giảng viên làm rõ

- Ngưỡng kích thước và mức độ rõ nét tối thiểu để gán nhãn xe ở xa.
- Cách xác định Bounding Box đối với phương tiện bị che khuất hoặc bị cắt bởi rìa ảnh.
- Phạm vi Bounding Box đối với xe tải có hàng hóa vượt khỏi thùng xe.

## 3. Annotation và Self-QC

### 3.1. Kết quả thực hiện

- **Số ảnh huấn luyện:** 50 ảnh.
- **Số file nhãn YOLO:** 50 file.
- **Công cụ gán nhãn:** CVAT.
- **Phương pháp:** Vẽ Bounding Box và gán một trong bốn Class.
- **Định dạng xuất:** YOLO 1.1.
- **Kết quả Self-QC:** PASS — 50/50 file nhãn hợp lệ về cấu trúc.
- **Commit khóa nhãn ban đầu:** `d5bd644`.

### 3.2. Những vấn đề đã nhận diện

Trong quá trình thực hành và phân tích kết quả Gold QC, tôi nhận thấy các khó khăn:

- Một số phương tiện có hình dạng tương tự nhau, dễ nhầm Class.
- Các phương tiện nhỏ hoặc nằm xa camera khó xác định chính xác.
- Các phương tiện bị che khuất gây khó khăn khi xác định Bounding Box.
- Việc xác định phạm vi cần gán nhãn chưa hoàn toàn thống nhất với Gold Set.

### 3.3. Danh sách ảnh ưu tiên rà soát

Từ kết quả Gold QC, tôi đã xác định 10 ảnh có nhiều lượt không ghép khớp để ưu tiên rà soát:

`drive_026`, `drive_041`, `drive_024`, `drive_027`, `drive_009`, `drive_019`, `drive_029`, `drive_038`, `drive_008`, `drive_040`.

Đây là danh sách xác định bằng phân tích kết quả đánh giá; tôi chưa hoàn thành việc rà soát trực quan và chỉnh sửa toàn bộ 10 ảnh.

## 4. Gold QC và Rework

### 4.1. Kết quả Gold QC lần đầu

Tôi sử dụng công cụ `tools/evaluate_annotations.py` để đánh giá chất lượng nhãn với Gold Set của giảng viên.

| Chỉ số | Kết quả lần đầu | Sau Rework |
|---|---:|---|
| True Positive (TP) | 350 | Chưa thực hiện |
| False Positive (FP) | 255 | Chưa thực hiện |
| False Negative (FN) | 303 | Chưa thực hiện |
| Precision | 0,5785 | Chưa thực hiện |
| Recall | 0,5360 | Chưa thực hiện |
| F1 | **0,5564** | Chưa thực hiện |
| Mean IoU of Matches | **0,7667** | Chưa thực hiện |
| Passed | false | Chưa thực hiện |

File đánh giá: [annotation_evaluation.json](../../outputs/annotation_evaluation.json).

### 4.2. Nhận xét

- F1 = 0,5564, lớn hơn 0,50.
- Mean IoU = 0,7667, vượt ngưỡng 0,70.
- Theo tiêu chí cấu hình trong công cụ, bài gán nhãn cần F1 ≥ 0,80, do đó kết quả hiện tại là `passed: false`.

Trong buổi hướng dẫn ngày 09/11/2026, Coach có chia sẻ rằng **F1 > 0,50 là mức chấp nhận được đối với lần đầu học viên thực hành**, nhằm giúp học viên tiếp tục trải nghiệm quy trình huấn luyện mô hình.

Tôi hiểu rằng mức chấp nhận cho lần thực hành đầu tiên không đồng nghĩa chất lượng gán nhãn đã đáp ứng ngưỡng F1 ≥ 0,80 của công cụ.

### 4.3. Tình trạng Rework

**Tôi chưa thực hiện Rework chính thức do giới hạn thời gian thực hành.**

Từ kết quả Gold QC, tôi đã xác định được những ảnh có mức sai lệch lớn để phục vụ cải thiện sau này.

Hướng cải thiện:

1. So sánh nhãn học viên với Gold Set trên từng ảnh để xác định nguyên nhân.
2. Rà soát lỗi bỏ sót, gán thừa, nhầm Class hoặc Bounding Box không khớp.
3. Chỉnh sửa trên CVAT và chạy lại Gold QC để đo mức cải thiện.

Tôi không suy luận nguyên nhân cụ thể chỉ dựa trên số FP và FN.

## 5. Kết quả huấn luyện YOLO26

### 5.1. Thông tin huấn luyện

- **Mô hình:** YOLO26n.
- **Dữ liệu train:** 50 ảnh.
- **Dữ liệu eval:** 10 ảnh.
- **Cấu hình:** 50 Epochs theo Notebook của giảng viên.
- **Nền tảng:** Google Colab.
- **Ultralytics:** 8.4.208.
- **Python:** 3.13.8.
- **Torch:** 2.14.1+cpu (ghi nhận ở bước đánh giá).
- **Thiết bị đánh giá:** CPU Intel Xeon.
- **Thiết bị huấn luyện:** [Cần xác nhận từ log train].
- **Tham số thay đổi:** [Cần xác nhận; nếu giữ nguyên Notebook, ghi "Không thay đổi"].
- **Best checkpoint trong phiên chạy:** `/content/Day2-Lab/outputs/yolo26_runs/vehicle_annotation/weights/best.pt`.

### 5.2. Kết quả đánh giá trên 10 ảnh eval

| Chỉ số | Giá trị |
|---|---:|
| Precision | **0,423** |
| Recall | **0,261** |
| mAP50 | **0,278** |
| mAP50–95 | **0,168** |

### 5.3. Kết quả theo từng lớp

| Class | Precision | Recall | mAP50 |
|---|---:|---:|---:|
| `car` | 0,705 | 0,761 | 0,760 |
| `truck` | 0,812 | 0,200 | 0,265 |
| `bus` | 0 | 0 | 0,0165 |
| `van` | 0,177 | 0,0833 | 0,069 |

### 5.4. Phân tích kết quả

Mô hình nhận diện lớp `car` tốt nhất trong bốn lớp, với Recall 0,761 và mAP50 0,760.

Các lớp `truck`, `bus` và `van` có Recall thấp hơn đáng kể.

Đặc biệt:

- `bus` có Recall thấp nhất, bằng 0.
- `van` có Recall 0,0833.
- `truck` có Precision khá cao nhưng Recall chỉ 0,200.

Những kết quả này cho thấy mô hình còn gặp khó khăn khi phát hiện đầy đủ các phương tiện thuộc các lớp ít phổ biến hoặc có đặc điểm dễ nhầm lẫn.

Tuy nhiên, tập eval chỉ có 10 ảnh và số lượng đối tượng giữa các lớp không đồng đều. Vì vậy, chưa thể kết luận các chỉ số trên phản ánh đầy đủ năng lực mô hình trên những dữ liệu khác.

### 5.5. Tài sản minh chứng

- [Ảnh dự đoán trên tập eval](assets/eval_predictions/)
- [Log và biểu đồ huấn luyện, đánh giá YOLO26](assets/yolo26_runs/)
- [Notebook Colab đã chạy](https://colab.research.google.com/github/bachdungbiu3010-art/K5-L2-Day02-ObjectDetection/blob/main/notebooks/day2_annotation_to_yolo.ipynb)

Các hình ảnh dự đoán thể hiện Bounding Box, nhãn Class và điểm confidence của mô hình trên tập eval.

**Ảnh minh họa được lựa chọn:**

`drive_057.jpg` – có xe buýt lớn trong khung hình, phù hợp để kiểm tra khả năng nhận diện lớp `bus`.

Đây là ví dụ cần đối chiếu thêm với nhãn chuẩn trước khi kết luận chính xác từng lỗi dự đoán.

### 5.6. Trả lời bốn câu hỏi cuối Notebook

**Câu 1. Lớp nào có Recall thấp nhất? Ảnh nào minh họa?**

Lớp `bus` có Recall thấp nhất, bằng 0.

Ảnh `drive_057.jpg` có xe buýt lớn, được lựa chọn để minh họa việc kiểm tra khả năng nhận diện xe buýt.

Kết quả Recall = 0 trên tập eval cho biết mô hình không có True Positive đối với lớp `bus` theo tiêu chí đánh giá. Cần kiểm tra riêng nhãn chuẩn và dự đoán trong ảnh để chỉ ra đối tượng sai cụ thể.

**Câu 2. Nếu mAP50 cao nhưng mAP50–95 thấp, cần kiểm tra điều gì trên Bounding Box?**

Cần kiểm tra độ chính xác của Bounding Box.

Một Bounding Box có thể đạt độ chồng lấp IoU từ 0,50 nhưng không đạt ở những ngưỡng IoU cao hơn.

Những vấn đề có thể gặp:

- Khung bao quá rộng.
- Khung bao quá hẹp.
- Vị trí khung bị lệch.
- Chưa bao sát đối tượng theo quy tắc gán nhãn.

Vì vậy, cần kiểm tra lại chất lượng annotation và hình dự đoán thực tế.

**Câu 3. Lỗi Class nào có thể liên quan đến Guideline? Dữ kiện nào hỗ trợ nhận xét?**

Hai nhóm cần ưu tiên kiểm tra là `car/van` và `bus/van`, do có những loại phương tiện có hình dạng tương tự nhau.

Dữ kiện từ kết quả eval:

- `car`: Recall 0,761.
- `van`: Recall 0,0833.
- `bus`: Recall 0.

Chênh lệch lớn giữa các lớp cho thấy cần điều tra tính nhất quán của dữ liệu gán nhãn và khả năng phân biệt Class của mô hình.

Tuy nhiên, các chỉ số này chưa đủ để khẳng định nguyên nhân chắc chắn là nhầm Class. Cần kiểm tra thêm hình dự đoán, nhãn chuẩn và các trường hợp phân loại sai cụ thể.

**Câu 4. Vì sao không dùng 10 ảnh eval để train? Vai trò validation của chúng trong Notebook là gì?**

Tập train dùng để mô hình học đặc điểm và điều chỉnh tham số.

Tập eval dùng để theo dõi, đánh giá mô hình trên những ảnh không trực tiếp được sử dụng để cập nhật trọng số, đồng thời hỗ trợ lựa chọn checkpoint tốt nhất.

Nếu đưa 10 ảnh eval vào train, kết quả đánh giá có thể bị thiên lệch do mô hình đã học trực tiếp từ các ảnh này.

Trong Notebook hiện tại, tập eval đóng vai trò validation và không phải một tập test độc lập hoàn toàn.

## 6. Khó khăn và cách xử lý

| Bước/thời điểm | Lỗi quan sát | Cách đã thử | Kết quả/còn thiếu |
|---|---|---|---|
| CVAT | Khó phân biệt một số loại xe | Đối chiếu schema và lập Mini Guideline | Hoàn thành 50 file nhãn |
| Self-QC | Cần kiểm tra dữ liệu YOLO | Chạy công cụ kiểm tra | PASS 50/50 |
| Gold QC | F1 thấp hơn ngưỡng 0,80 | Phân tích FP, FN và mức sai lệch từng ảnh | Đã xác định hướng cải thiện, chưa Rework |
| Google Colab | Bộ Gold có thời điểm bị đọc thành 0 Bounding Box | Kiểm tra lại và sử dụng dữ liệu Gold trên máy | Đã đánh giá với 653 khung Gold |
| VS Code | Chưa quen Terminal và đường dẫn thư mục | Tìm hiểu thao tác, kiểm tra Python | Chạy thành công |
| Xuất JSON | PermissionError | Sử dụng chức năng xuất JSON trực tiếp | JSON hợp lệ và đã đưa lên GitHub |
| Colab | Sai đường dẫn `REPO_URL` | Thay bằng link Repository cá nhân | Tải dữ liệu thành công |
| Huấn luyện YOLO | Thời gian huấn luyện kéo dài | Theo dõi tiến trình và giữ nguyên phiên chạy | Đã hoàn thành |
| Đánh giá mô hình | Recall thấp ở một số lớp | Phân tích chỉ số theo lớp và ảnh dự đoán | Cần tiếp tục cải thiện mô hình/dữ liệu |

## 7. Tự đánh giá theo Rubric

Tài liệu: [RUBRIC.md](../../RUBRIC.md).

| Thành phần | Kết quả tự đánh giá |
|---|---|
| Schema và Mini Guideline | Đã hoàn thành |
| Annotation | Hoàn thành 50 file nhãn YOLO |
| Self-QC | PASS 50/50 về cấu trúc |
| Gold QC | Đã đánh giá, F1 = 0,5564 |
| Rework | Chưa thực hiện |
| Huấn luyện YOLO26 | Đã hoàn thành |
| Đánh giá trên eval | Đã có Precision, Recall, mAP50, mAP50–95 |
| Báo cáo cá nhân | Đã lập và cập nhật kết quả |
| Minh chứng | Đã chuẩn bị từ Colab; cần xác nhận đầy đủ file trên GitHub |

### Kết quả đạt được

Qua bài thực hành, tôi đã trải nghiệm quy trình:

**Gán nhãn dữ liệu → Kiểm tra chất lượng → Đối chiếu Gold Set → Huấn luyện mô hình → Đánh giá kết quả.**

Tôi hiểu được sự khác biệt giữa chất lượng annotation và chất lượng mô hình AI, đồng thời nhận thấy việc chuẩn hóa dữ liệu đầu vào có vai trò quan trọng đối với kết quả nhận diện.

### Hạn chế và hướng cải thiện

- Chưa thực hiện Rework để nâng F1 lên ngưỡng 0,80 trong công cụ.
- Mô hình còn Recall thấp ở các lớp `truck`, `bus` và `van`.
- Tập dữ liệu còn nhỏ, kết quả eval chưa đủ để kết luận rộng về năng lực mô hình.

Trong các lần thực hành tiếp theo, tôi cần ưu tiên kiểm tra chất lượng nhãn và những Class dễ nhầm lẫn trước khi điều chỉnh mô hình.

## 8. Công cụ AI và tài liệu tham khảo

### Công cụ sử dụng

| Công cụ | Mục đích |
|---|---|
| CVAT | Gán nhãn Bounding Box cho phương tiện |
| Google Colab | Chạy Python, huấn luyện và đánh giá YOLO26 |
| Ultralytics YOLO26 | Huấn luyện mô hình Object Detection |
| VS Code / Python | Kiểm tra và đánh giá Gold QC |
| GitHub | Quản lý dữ liệu, mã nguồn và kết quả thực hành |
| ChatGPT | Hỗ trợ tìm hiểu kiến thức, hướng dẫn thao tác, phân tích chỉ số và biên soạn báo cáo |

Các kết quả kỹ thuật được kiểm tra dựa trên dữ liệu thực tế và công cụ của chương trình. Tôi không sử dụng AI để thay thế kết quả đo lường hoặc tạo ra chỉ số không có trong quá trình thực hành.
- `RUBRIC.md`
- `GUIDELINE_MINI.md`
- Notebook `day2_annotation_to_yolo.ipynb`.
