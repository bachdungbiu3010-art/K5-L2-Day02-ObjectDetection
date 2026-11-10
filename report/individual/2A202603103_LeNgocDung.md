# Báo cáo cá nhân — Ngày 2 Object Detection

## 1. Thông tin học viên

- **Họ tên:** Lê Ngọc Dũng
- **MSSV:** 2A202603103
- **Email:** bachdungbiu3010@gmail.com
- **Lớp:** K5-L2A
- **Link repo bài làm:** https://github.com/bachdungbiu3010-art/K5-L2-Day02-ObjectDetection
- **Link Colab đã chạy:** [Bổ sung sau khi huấn luyện và lưu Notebook]

## 2. Schema và mini guideline

Tôi sử dụng bốn lớp phương tiện theo schema của bài thực hành:

- `car` (0): Sedan, SUV, taxi, pickup.
- `truck` (1): Xe tải, xe ben, xe đầu kéo.
- `bus` (2): Xe buýt, xe khách, minibus.
- `van` (3): Xe van, minivan.

**Ba tình huống mơ hồ đã gặp:**

1. **drive_024.jpg:** Phân biệt xe thân hộp với SUV. Tôi gán lớp `van` cho các phương tiện được xác định là van/minivan.
2. **drive_042.jpg:** Xe tải chở hàng cồng kềnh. Tôi gán lớp `truck`, lưu ý cách xác định phạm vi Bounding Box.
3. **drive_001.jpg:** Xe nhỏ ở xa, khó xác định đặc điểm. Tôi gán những phương tiện nhận diện được là ô tô con vào lớp `car`.

**Nội dung cần giảng viên làm rõ:** Quy tắc xử lý xe quá nhỏ, xe bị che khuất và cách khoanh Bounding Box cho xe chở hàng vượt khỏi thân xe.

## 3. Annotation và self-QC

- **Số file nhãn train:** 50 file YOLO.
- **Kết quả Self-QC:** PASS – 50/50 file hợp lệ về cấu trúc.
- **Lỗi cấu trúc:** Không ghi nhận lỗi trong lần kiểm tra đã hoàn thành.
- **Commit khóa nhãn ban đầu:** `d5bd644`.

**Các ảnh được xác định cần ưu tiên rà soát sau Gold QC:**

`drive_001`, `drive_008`, `drive_009`, `drive_019`, `drive_024`, `drive_026`, `drive_027`, `drive_029`, `drive_038`, `drive_041`.

Đây là danh sách ảnh ưu tiên kiểm tra, không đồng nghĩa tôi đã hoàn thành việc rà soát và sửa tất cả các ảnh này.

**Nhận xét:**

- Bộ nhãn đáp ứng yêu cầu định dạng YOLO và số lượng file.
- Kết quả Gold QC cho thấy còn nhiều đối tượng chưa được ghép đúng.
- Cần tiếp tục rà soát lỗi bỏ sót, nhầm loại xe và Bounding Box.
- Chưa đủ bằng chứng để kết luận nguyên nhân cụ thể chỉ dựa vào FP và FN.

## 4. Gold QC và rework

Tôi đã chạy công cụ `tools/evaluate_annotations.py` để đối chiếu 50 file nhãn với Gold Set của giảng viên.

| Chỉ số | Kết quả lần đầu | Sau Rework |
|---|---:|---|
| TP | 350 | Chưa thực hiện |
| FP | 255 | Chưa thực hiện |
| FN | 303 | Chưa thực hiện |
| Precision | 0,5785 | Chưa thực hiện |
| Recall | 0,5360 | Chưa thực hiện |
| F1 | **0,5564** | Chưa thực hiện |
| mean_iou_of_matches | **0,7667** | Chưa thực hiện |
| passed | false | Chưa thực hiện |

File kết quả: [annotation_evaluation.json](../../outputs/annotation_evaluation.json).

### Nhận xét kết quả

- F1 đạt **0,5564**, vượt mức 0,50.
- Mean IoU đạt **0,7667**, vượt ngưỡng 0,70.
- Theo tiêu chí được cấu hình trong công cụ, F1 phải đạt từ 0,80 nên kết quả trả về `passed: false`.
- Trong buổi hướng dẫn ngày **09/11/2026**, Coach chia sẻ rằng **F1 > 0,50 là mức chấp nhận được đối với lần đầu học viên thực hành**, để có thể tiếp tục trải nghiệm bước huấn luyện mô hình.
- Tôi hiểu đây là mức chấp nhận cho mục tiêu học tập ban đầu, không đồng nghĩa chất lượng annotation đã đạt tiêu chí F1 ≥ 0,80 trong công cụ đánh giá.

### Tình trạng Rework

**Tôi chưa thực hiện Rework chính thức** do thời gian thực hành có hạn.

Sau khi xem kết quả Gold QC, tôi đã xác định những ảnh có sai lệch lớn để ưu tiên kiểm tra ở lần cải thiện tiếp theo.

Kế hoạch cải thiện:

1. So sánh trực quan nhãn học viên với Gold Set để tìm nguyên nhân.
2. Rà soát các đối tượng bị bỏ sót, gán thừa, sai lớp hoặc sai phạm vi Bounding Box.
3. Sửa nhãn trên CVAT, xuất lại YOLO và chạy Gold QC để đo mức cải thiện.

Tôi giữ nguyên kết quả đánh giá lần đầu, không sử dụng kết quả giả định để thay thế.

## 5. Kết quả YOLO

- **Mô hình:** YOLO26n.
- **Dữ liệu:** 50 ảnh train và 10 ảnh eval.
- **Cấu hình huấn luyện:** 50 Epochs theo Notebook của giảng viên.
- **Runtime, GPU/CPU, phiên bản Ultralytics và Torch:** [Bổ sung từ Colab]
- **Các tham số thay đổi:** [Xác nhận sau khi chạy xong]
- **Đường dẫn best checkpoint:** [Bổ sung từ kết quả huấn luyện]

### Đánh giá trên 10 ảnh eval

| Chỉ số | Giá trị |
|---|---|
| Precision | Chờ kết quả |
| Recall | Chờ kết quả |
| mAP50 | Chờ kết quả |
| mAP50–95 | Chờ kết quả |

Tài sản kết quả cần bổ sung sau khi huấn luyện:

- [Ảnh dự đoán trên eval](assets/eval_predictions/)
- [Log và biểu đồ đánh giá YOLO](assets/yolo26_runs/)

**Nhận xét ảnh minh họa:** [Bổ sung ít nhất một ảnh eval và một đối tượng cụ thể sau khi có kết quả].

### Trả lời bốn câu hỏi cuối Notebook

**1. Lớp nào Recall thấp nhất? Ảnh nào minh họa?**

Chưa xác định do cần kết quả đánh giá theo từng lớp sau huấn luyện.

**2. Nếu mAP50 cao nhưng mAP50–95 thấp, cần kiểm tra điều gì trên Bounding Box?**

Cần kiểm tra độ chính xác vị trí và kích thước Bounding Box. Khung có thể đạt mức chồng lấp IoU 0,50 nhưng chưa đủ chính xác ở ngưỡng IoU cao hơn, do bị lệch tâm, quá rộng hoặc quá hẹp.

**3. Lỗi Class nào có thể liên quan Guideline? Dữ kiện nào hỗ trợ?**

Các nhóm dễ nhầm như `car/van` hoặc `bus/van` cần được kiểm tra. Chỉ kết luận khi có ảnh dự đoán và kết quả đánh giá theo từng lớp làm bằng chứng.

**4. Vì sao không sử dụng 10 ảnh eval để train? Vai trò validation của chúng trong Notebook là gì?**

Tập train được dùng để mô hình học. Tập eval được dùng để đánh giá khả năng nhận diện trên dữ liệu không đưa trực tiếp vào huấn luyện, theo dõi chất lượng và lựa chọn checkpoint.

Nếu sử dụng cả 10 ảnh eval để train, kết quả đánh giá dễ bị thiên lệch và không còn phản ánh khách quan khả năng khái quát hóa của mô hình.

Trong Notebook này, tập eval đóng vai trò validation, không phải tập kiểm thử độc lập cuối cùng.

## 6. Khó khăn và cách xử lý

| Bước/thời điểm | Lỗi quan sát | Cách đã thử | Kết quả/còn thiếu |
|---|---|---|---|
| Gán nhãn CVAT | Khó phân biệt xe có hình dạng tương tự | Đối chiếu schema và xây dựng Mini Guideline | Đã hoàn thành gán nhãn |
| Self-QC | Cần kiểm tra định dạng 50 file YOLO | Sử dụng công cụ kiểm tra cấu trúc | PASS 50/50 |
| Gold QC | F1 = 0,5564, thấp hơn tiêu chí 0,80 | Phân tích FP, FN và mức sai lệch từng ảnh | Đã xác định hướng cải thiện, chưa Rework |
| Google Colab | Bộ Gold có thời điểm bị đọc thành 0 Bounding Box | Kiểm tra và khôi phục dữ liệu trên máy | Đã chạy Gold QC với 653 khung Gold |
| VS Code | Chưa quen Terminal và cấu trúc thư mục | Tìm hiểu và thực hành chạy lệnh Python | Đã chạy thành công |
| Xuất JSON | Gặp lỗi PermissionError | Sử dụng chức năng xuất JSON trực tiếp | File JSON hợp lệ, đã Commit lên GitHub |
| Colab | Đường dẫn Repository ban đầu chưa đúng | Thay bằng URL GitHub cá nhân | Đã kiểm tra đủ 50 train + 10 eval |

## 7. Tự đánh giá theo Rubric

Tài liệu: [RUBRIC.md](../../RUBRIC.md).

| Thành phần | Tình trạng |
|---|---|
| Schema và Mini Guideline | Đã hoàn thiện |
| Annotation | Đã có 50 file nhãn |
| Self-QC | PASS về cấu trúc |
| Gold QC | Đã đánh giá, F1 = 0,5564 |
| Rework | Chưa thực hiện |
| Huấn luyện YOLO26 | Đang thực hiện |
| Đánh giá trên eval | Chưa có kết quả |
| Báo cáo và file minh chứng | Đang hoàn thiện |

Tôi đã thực hiện được quy trình từ gán nhãn đến đánh giá chất lượng annotation và bắt đầu huấn luyện mô hình.

Hạn chế hiện tại là chất lượng gán nhãn chưa đạt ngưỡng F1 0,80 trong công cụ và chưa có thời gian Rework.

## 8. Công cụ AI và tài liệu tham khảo

**Công cụ sử dụng:**

- **CVAT:** Gán nhãn Bounding Box cho 50 ảnh.
- **Google Colab:** Chạy Python, kiểm tra dữ liệu và huấn luyện YOLO26.
- **VS Code/Python:** Chạy kiểm tra và đánh giá Gold QC.
- **GitHub:** Quản lý mã nguồn, dữ liệu, tài liệu và kết quả.
- **ChatGPT:** Hỗ trợ giải thích kiến thức, hướng dẫn thao tác, phân tích chỉ số và biên soạn báo cáo. Các kết quả kỹ thuật được đối chiếu với dữ liệu và công cụ thực tế.
- https://github.com/VinUni-AI20k/K5-L2-Day02-ObjectDetection
- `GUIDE.md`, `RULES.md`, `SUBMISSION.md`, `RUBRIC.md`.
- Notebook `day2_annotation_to_yolo.ipynb` của chương trình.
