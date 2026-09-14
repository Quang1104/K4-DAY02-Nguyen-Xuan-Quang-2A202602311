# Báo cáo — Ngày 2: phát hiện vật thể

**Họ và tên:** NGUYỄN XUÂN QUANG<br>
**MSSV:** 2A202602311<br>
**Hình thức:** CÁ NHÂN<br>
**Mã cặp:** SOLO

## 1. Bài độc lập và nguồn dữ liệu

- Mã SHA-256 của ZIP ảnh được cấp:f7d99888f21440fb0374d84962b93213bd8c14e665d093cc8d37f4c61b71ed33
- Bốn mã ảnh:drive_022, drive_033, drive_038, drive_008
- Số vật thể thực tế:90
- Mã SHA-256 của gói YOLO của bạn:25e2857db52adf04523cb200b0ce0b9fe3dd0c0a92667b3f0a2d0189ac86bd28
- Mã SHA-256 của gói CVAT gốc của bạn:9d7f8a68d8c2e0a14e0d787d95a93d145a1dc79d916e210a4dab0d78c51cb85a
- Nguồn đối chiếu: bạn cùng cặp hoặc bộ tham chiếu do người hướng dẫn thực hành cấp:
- Mã SHA-256 của gói đối chiếu: c8bbc767d8bb9a29f4ca5abf0c3516e5c2af94c58143a980b0148cfe0b500d2b
- Nếu làm cá nhân, ghi mã lần phát và thời điểm nhận bộ tham chiếu: Nhận bộ tham chiếu lúc 3:35 pm 14.09.2026 

Giải thích vì sao bài của bạn vẫn độc lập trước khi đối chiếu: 
Đã hoàn tất các bước gồm gán nhãn cho các vật thể trong 4 ảnh , tự kiểm tra lại, export 2 file CVAT và YOLO và khóa mã SHA-256 trước khi nhận bộ đối chiếu từ coach lab nên bộ bài làm của tôi là độc lập trước khi tham chiếu .

## 2. Quyết định phân lớp

| Ảnh/vật thể | Lớp | Dấu hiệu nhìn thấy | Quy tắc áp dụng |
| --- | --- | --- | --- |
| drive_022, xe lớn ở giữa ảnh | bus | thân dài, nhiều cửa sổ hàng ngang, dáng xe khách | thân xe khách dài, nhiều cửa sổ hoặc hàng ghế |

Nêu một ví dụ cho thấy lớp và thuộc tính là hai loại thông tin khác nhau: Ở ảnh drive_008, một xe ô tô con (lớp=car) bị xe khác che một phần thân. Lớp vẫn là `car` vì đủ bằng chứng nhận dạng, nhưng thuộc tính visibility=occluded (bị che) và review_state=confident. Lớp mô tả *loại phương tiện*, thuộc tính mô tả *điều kiện quan sát* — hai thông tin hoàn toàn khác nhau. 

## 3. Tự kiểm tra và sửa nhãn

| Trước khi sửa | Loại lỗi | Cách phát hiện | Sau khi sửa và quy tắc |
| --- | --- | --- | --- |
| drive_008, hộp tại vị trí [44.5, 48.0] gán là car | lớp |  Zoom to lên, thấy thân hộp kín không phải sedan | Đổi thành van; quy tắc: thân hộp nhỏ kín → van |

- Số hộp `needs_review` trước và sau khi kiểm: trước 10 hộp -> sau 5 hộp
- Một quyết định chưa đủ bằng chứng và cách bạn xin hỗ trợ:Ở drive_008 có vật thể nhỏ mờ, không rõ van hay car; đặt needs_review và hỏi Lab Coach khi đối chiếu.


## 4. Một dòng nhãn YOLO

- Dòng `class x_center y_center width height`:3 0.937883 0.458719 0.092641 0.106062
- Tên lớp và tọa độ điểm ảnh `xyxy`: lớp 3 (van), pixel xyxy: [570.6, 259.6, 629.9, 327.5]
- Vì sao dòng đúng định dạng vẫn có thể sai lớp, phạm vi hoặc hình học?
Dòng YOLO chỉ lưu 5 số (class + tọa độ). Định dạng đúng nghĩa là 5 trường đều hợp lệ về mặt cú pháp, nhưng: (1) lớp có thể gán sai — ví dụ gán van thay vì car vì nhìn nhầm; (2) phạm vi có thể sai — gán một vật thể không thuộc 4 lớp quy định hoặc bỏ sót vật thể; (3) hình học có thể sai — hộp quá rộng bao cả nền hoặc quá hẹp cắt mất phần xe nhìn thấy. Không có trường nào trong file .txt kiểm tra được những lỗi này.

## 5. Huấn luyện và dự đoán thử

- Ba mã ảnh huấn luyện:	drive_022, drive_033, drive_038
- Mã ảnh thẩm định:	drive_008
- Mô tả một dự đoán trong `detect_result.jpg`: Vật thể ở vị trí [66.7, 104.9] có phần đầu xe giống xe con và phần đuôi xe với khoang sau rộng rãi dùng để chở hàng hoặc kết hợp thiết kế lắp đặt thêm ghế ngồi vào.
- Dự đoán đó gợi ý cần kiểm lại quy tắc hoặc dữ liệu nào? Cần kiểm lại giữa ranh giới van/car
- Minh chứng nào có thể bác bỏ nhận định của bạn? Nếu phóng ảnh 100% thấy thân xe có thùng hàng mở → bác bỏ việc gán van, nên là truck.
- Vì sao kết quả trên bốn ảnh không phải phép đánh giá mô hình dùng thực tế?
Chỉ có 3 ảnh huấn luyện và 1 ảnh kiểm tra, quá ít để đánh giá khả năng tổng quát hóa. mAP trên 4 ảnh không đại diện cho dữ liệu thực tế đa dạng hơn. Mô hình có thể overfit trên 3 ảnh train và kết quả trên 1 ảnh val không đủ ý nghĩa thống kê.



## 6. Đối chiếu nhãn

- Số hộp ghép được:48
- IoU trung bình và trung vị: IoU trung bình = 0.840281,IoU trung vị = 0.859310
- Mức đồng thuận lớp:0.729167
- Số hộp phía bạn không ghép được:42
- Số hộp phía đối chiếu không ghép được:2
- Một điểm khác biệt cụ thể:Tôi gán 90 vật thể, bộ tham chiếu gán 50. Có 42 hộp phía tôi không ghép được và chỉ 2 hộp phía tham chiếu không ghép — cho thấy tôi gán nhiều vật thể nhỏ/mờ mà bộ tham chiếu bỏ qua theo quy tắc phạm vi.
- Quy tắc hoặc hành động sửa phát sinh: Cần áp dụng nghiêm hơn quy tắc "vật thể quá nhỏ hoặc mờ đến mức không thể phân lớp có căn cứ: không đoán" để giảm số hộp thừa.
- Vì sao mức đồng thuận cao không chứng minh mọi nhãn đều đúng?
Mức đồng thuận chỉ đo hai bên gán *giống nhau* hay không, không đo *đúng* hay sai. Hai người có thể cùng gán sai một lớp (ví dụ cùng gán car cho một van) thì đồng thuận = 100% nhưng vẫn sai. Ngoài ra, IoU cao chỉ nghĩa hộp chồng khít, không kiểm tra lớp hay thuộc tính.


## 7. Kiểm tra kho GitHub cá nhân

- [x] Có phiếu quy tắc với ba tình huống mơ hồ.
- [x] Có kết quả kiểm hai gói xuất.
- [x] Có thông tin lần huấn luyện và ảnh dự đoán.
- [x] Có tóm tắt, bảng và ảnh phủ của bước đối chiếu.
- [x] Không có gói xuất thô, bộ nhãn tham chiếu hoặc trọng số mô hình.
- [x] Không có dữ liệu VinFast/khách hàng/ảnh cá nhân/mật khẩu/mã truy cập.

Minh chứng mạnh nhất trong bài và câu hỏi còn lại cho Lab Coach: Hai gói xuất YOLO và CVAT khớp hoàn toàn 90/90 hộp, chứng tỏ dữ liệu xuất từ cùng trạng thái nhất quán. Bước đối chiếu với bộ tham chiếu cho thấy 48 hộp ghép được với IoU trung vị 0.840281.

