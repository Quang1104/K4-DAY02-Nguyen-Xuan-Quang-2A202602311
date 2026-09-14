# Phiếu quy tắc gán nhãn — Ngày 2

**Họ và tên:** NGUYỄN XUÂN QUANG<br>
**MSSV:** 2A202602311<br>
**Hình thức:** CÁ NHÂN<br>
**Mã cặp:** SOLO

## 1. Phạm vi

- Chỉ gán phương tiện thuộc bốn lớp bên dưới.
- Mỗi phương tiện là một hộp; không gộp nhiều xe.
- Không gán người, xe máy, xe đạp, biển báo hoặc phần phản chiếu.
- Vật thể quá nhỏ hoặc mờ đến mức không thể phân lớp có căn cứ: không đoán; ghi lý do vào nhật ký quyết định.

## 2. Bốn lớp cố định

| Mã | Lớp | Gán khi nhìn thấy | Không gán vào lớp này |
| ---: | --- | --- | --- |
| 0 | `car` (ô tô con) | sedan, hatchback, SUV, taxi, xe bán tải dùng như xe con | xe có thùng/ben rõ; thân xe buýt; xe van thân hộp |
| 1 | `truck` (xe tải) | thùng, ben, sàn hàng hoặc thiết bị công vụ rõ ràng | ô tô con; thân xe buýt; xe van kín một khối |
| 2 | `bus` (xe buýt) | thân xe khách dài, nhiều cửa sổ hoặc hàng ghế | xe van nhỏ; xe tải; ô tô con |
| 3 | `van` (xe van) | thân hộp nhỏ, kín, dùng chở người hoặc hàng | thân xe buýt; khoang hàng tách biệt như xe tải |

Thứ tự lớp là cố định: `0 car, 1 truck, 2 bus, 3 van`.

## 3. Hộp giới hạn

- Vẽ sát phần vật thể nhìn thấy.
- Không ước lượng phần bị xe khác che.
- Vật thể chạm mép ảnh vẫn được gán nếu đủ bằng chứng phân lớp.
- Không để hộp chứa nhiều nền hoặc nhiều phương tiện.

## 4. Ba thuộc tính

| Thuộc tính | Giá trị | Ý nghĩa |
| --- | --- | --- |
| `visibility` (mức nhìn thấy) | `clear` (rõ), `occluded` (bị che), `unclear` (không rõ) | mức bằng chứng nhìn thấy |
| `boundary` (quan hệ mép ảnh) | `inside` (trong ảnh), `truncated` (bị cắt) | vật thể có bị mép ảnh cắt hay không |
| `review_state` (trạng thái xem lại) | `confident` (tự tin), `needs_review` (cần xem lại) | đánh dấu quyết định cần quay lại |

YOLO không lưu ba thuộc tính này. Vì vậy phải xuất thêm `CVAT for images 1.1` từ cùng công việc.

## 5. Ba tình huống mơ hồ



Hoàn thành trước khi xem bài của người khác hoặc bộ nhãn tham chiếu.

### Tình huống A — xe buýt hay xe van?

- Ảnh và mã vật thể: drive_008, xe tại vị trí [66.7, 104.8]
- Dấu hiệu nhìn thấy: Thân xe dài vừa, kín, có vài cửa sổ nhưng ít hơn xe buýt thông thường
- Quy tắc áp dụng: bus cần "thân xe khách dài, nhiều cửa sổ"; van cần "thân hộp nhỏ, kín"
- Quyết định: Gán van vì chỉ có 2–3 cửa sổ, thân ngắn hơn xe buýt điển hình
- Nếu vẫn thiếu bằng chứng, bạn sẽ làm gì? Đặt review_state=needs_review và hỏi Lab Coach; không đoán

### Tình huống B — xe tải hay xe van/ô tô con?

- Ảnh và mã vật thể: drive_038, xe tại vị trí [181.3, 146.4]
- Dấu hiệu nhìn thấy: thân xe kín giống van nhưng có thiết bị công vụ rõ ràng phía sau
- Quy tắc áp dụng: mục 2, dòng truck — "thiết bị công vụ rõ ràng" (cần cẩu phía sau chính là bằng chứng này).
- Quyết định: gán lớp truck
- Nếu vẫn thiếu bằng chứng, bạn sẽ làm gì? Phóng to phần nối giữa cabin và thiết bị phía sau để xác nhận đây là kết cấu cơ khí cố định (bu lông, khung thép) chứ không phải hàng hóa chất tạm trên nóc/thùng xe khác; nếu ảnh mờ hoặc góc chụp không cho thấy rõ điểm nối, đánh dấu visibility = unclear và review_state = needs_review theo đúng định nghĩa mục 4, không tự ý đoán

### Tình huống C — bị che, bị mép ảnh cắt hay không đủ bằng chứng?

- Ảnh và mã vật thể: drive_033, xe tại vị trí [189.2, 201.9]
- Dấu hiệu nhìn thấy khi phóng 100%: bị khung ảnh cắt ngang, chỉ thấy được phần đuôi xe
- Giá trị `visibility`: clear - vì rõ nét, không bị vật khác che, dẫn quy tắc mục 4 phân biệt rõ với occluded/unclear
- Giá trị `boundary`: truncated — trích đúng định nghĩa mục 4 "vật thể có bị mép ảnh cắt hay không"
- Trạng thái `review_state`: needs_review — vì bằng chứng phân lớp (chỉ thấy đầu xe, không thấy hông/cửa sổ) chưa đủ để chốt tự tin
- Lý do: trích nguyên văn mục 3 ("Vật thể chạm mép ảnh vẫn được gán nếu đủ bằng chứng phân lớp") làm căn cứ vẫn gán nhãn, đồng thời giải thích vì sao bằng chứng chưa "đủ" để đạt confident

## 6. Xác nhận tự kiểm tra

- [x] Đã rà đủ bốn ảnh.
- [x] Đã kiểm vật thể thiếu và trùng.
- [x] Đã kiểm lớp và hình học từng hộp.
- [x] Mỗi hộp có đủ ba thuộc tính.
- [x] Đã xử lý mọi hộp `needs_review`.
- [x] Đã hoàn thành ba tình huống trước khi xem nguồn đối chiếu.
- [x] Nếu làm theo cặp, hai người đã xuất bài độc lập trước khi trao đổi.
- [x] Nếu làm cá nhân, bài riêng đã được kiểm trước khi nhận bộ tham chiếu.
- [x] Số vật thể thực tế: CHƯA ĐIỀN — 40–60 là mục tiêu khối lượng, không phải điểm cắt.
