# Mini guideline – Bài làm cá nhân

## 1. Định nghĩa lớp

| Lớp | Định nghĩa của tôi | Ví dụ/điểm phân biệt |
|---|---|---|
| `car` | Xe ô tô con phục vụ di chuyển cá nhân | Sedan, SUV, taxi, pickup; phân biệt với xe chở hàng chuyên dụng |
| `truck` | Xe tải dùng để vận chuyển hàng hóa | Xe tải thùng, xe tải ben, xe đầu kéo; nhận biết qua kết cấu chở hàng |
| `bus` | Xe dùng để vận chuyển nhiều hành khách | Xe buýt, xe khách, minibus; thường có nhiều hàng ghế |
| `van` | Xe van hoặc minivan, thường có thân xe dạng hộp | Xe van, minivan; phân biệt với SUV và xe khách qua thân xe và công năng |

## 2. Quy tắc chung

- **Bounding Box:** Vẽ khung chữ nhật bám sát vật thể, hạn chế lấy dư nền ảnh.
- **Vật thể bị che khuất:** Gán nhãn khi còn đủ đặc điểm để xác định loại xe; trường hợp khó cần đối chiếu guideline.
- **Vật thể bị cắt bởi rìa ảnh:** Vẽ khung trong giới hạn ảnh, không mở rộng ra ngoài ảnh.
- **Vật thể rất nhỏ hoặc mờ:** Chỉ gán khi đủ thông tin nhận diện; cần áp dụng thống nhất quy tắc cho xe ở xa.
- **Phân loại:** `0 = car`, `1 = truck`, `2 = bus`, `3 = van`.

## 3. Quyết định cho ba ca mơ hồ thực tế

| Ảnh | Tình huống | Quyết định đã gán | Lý do theo schema |
|---|---|---|---|
| `drive_024.jpg` | Xe thân cao dạng hộp nằm cạnh các xe con, dễ nhầm với SUV | Gán `van` (Class 3) cho các xe dạng van/minivan | Schema quy định van/minivan thuộc lớp `van`, trong khi sedan, SUV và taxi thuộc `car` |
| `drive_042.jpg` | Xe tải chở hàng hóa chất cao, phần hàng lớn hơn thân xe | Gán `truck` (Class 1) | Phương tiện có kết cấu vận chuyển hàng hóa, thuộc nhóm `truck`; cần chú ý khung bao khi hàng vượt khỏi thùng |
| `drive_001.jpg` | Nhiều xe nhỏ ở xa, khó quan sát rõ đặc điểm | Gán các xe nhận diện là ô tô con vào `car` (Class 0) | Áp dụng lớp `car` cho xe con; qua Gold QC nhận thấy cần rà soát thêm phạm vi gán nhãn đối với xe rất nhỏ hoặc ở xa |

## 4. Bài học sau Gold QC
- Tôi đã hoàn thành gán nhãn 50 ảnh theo định dạng YOLO.
- Kết quả Gold QC đã ghi nhận: **F1 = 0,5564**, **Mean IoU = 0,7667**.
- Mean IoU đạt ngưỡng 0,70 nhưng F1 chưa đạt ngưỡng 0,80 của công cụ đánh giá.
- Một số vấn đề cần cải thiện gồm: lựa chọn đối tượng cần gán nhãn, xử lý xe nhỏ hoặc ở xa, phân biệt loại xe và kiểm soát khung bao.
- Tôi nhận thấy cần thống nhất quy tắc gán nhãn trước khi thực hiện, sau đó dùng Gold QC để phát hiện và cải thiện sai lệch.
