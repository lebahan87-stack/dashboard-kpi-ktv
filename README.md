# Dashboard khung năng lực và KPI KTV

Mở `dashboard_knl.html` cùng `kpi-core.js` và `dashboard.js`, hoặc chạy `python -m http.server 8000` rồi truy cập `/dashboard_knl.html`.

## Sử dụng

1. Tải CSV mẫu từ dashboard và điền dữ liệu. Mỗi dòng là một acc KTV trong một tháng tại một chi nhánh.
2. Nhập CSV UTF-8, XLSX hoặc XLS (tối đa 10 MB). Excel cho phép chọn sheet. Tiêu đề phải nằm ở dòng đầu. CSV hỗ trợ dấu phẩy hoặc chấm phẩy và trường đặt trong dấu nháy.
3. Lọc chi nhánh, acc KTV và tháng. Acc có tiền tố `phuongnam.` được giữ nguyên. Mỗi lần nhập hợp lệ thay toàn bộ dữ liệu; file lỗi không thay dữ liệu cũ. Muốn theo dõi nhiều tháng, đưa các tháng vào cùng sheet/file.
4. Xem cảnh báo từng dòng, chỉnh ngưỡng tham khảo và chọn chỉ số xu hướng. Bảng hiển thị 20 dòng/trang. Xu hướng luôn dùng tất cả tháng trong chi nhánh/KTV đang chọn để không mất chuỗi khi chọn một tháng.

## Cột dữ liệu

| Cột | Ý nghĩa / đơn vị |
|---|---|
| `chi_nhanh`, `ktv`, `thang` | Bắt buộc; chi nhánh, acc, tháng `YYYY-MM` |
| `ho_ten` | Họ tên tùy chọn |
| `cll`, `csat12`, `dung_hen` | Tỷ lệ 0–100, nhập `8.5`, `8,5%` hoặc ô Excel định dạng % |
| `nsld` | Điểm NSLĐ không âm |
| `tgxl_bt` | Số giờ không âm |
| `scat1`, `scat2` | Số sự kiện nguyên không âm |
| `osml` | Loại OSML; dùng biểu đồ phân bổ |
| `doi_truong`, `diem_kpi` | Tên đội trưởng và điểm không âm; dùng biểu đồ điểm TB |

Nhận cả tiêu đề tiếng Việt như `Chi nhánh`, `Tháng`, `Họ tên`, `Đúng hẹn`, `TGXL BT`, `acc mobix`, `act_mobix`. Các tên khác cần đổi về schema trên. Không tự đoán schema dữ liệu nghiệp vụ chưa được cung cấp. Dòng trùng chi nhánh + acc + tháng bị từ chối; không tự gộp hoặc lấy dòng cuối.

Ô trống giữ là thiếu dữ liệu, không chuyển thành 0. CSAT12 trống không tạo cảnh báo nhưng hiện trong danh sách thiếu. Trung bình là trung bình cộng các dòng có số liệu; chưa có mẫu số để tính tỷ lệ toàn đơn vị có trọng số. OSML được đếm theo dòng KTV–tháng.

## Ngưỡng và giới hạn

Ngưỡng mặc định kế thừa mã cũ: CLL ≤ 8,5%, TGXL ≤ 8 giờ, đúng hẹn ≥ 95%, NSLĐ ≥ 7. CSAT12 ≥ 95% là **giá trị tham khảo mới**, không phải chuẩn FTEL đã xác nhận. SCAT 1/2 > 0 tạo cảnh báo. Không kết luận đạt khi thiếu dữ liệu và không tự áp dụng quy tắc không đạt 3/7 KPI vì chưa có định nghĩa đủ 7 KPI.

Ma trận bậc nghề được giữ để tham khảo, chưa xác minh là khung chính thức. Công cụ đánh giá thủ công chỉ kiểm tra 4 ngưỡng gốc, độc lập với cấu hình dashboard; bậc nghề chưa tham gia công thức. Không dùng kết quả để tự động quyết định nâng bậc.

Dữ liệu chỉ ở bộ nhớ trình duyệt, không được ứng dụng gửi lên máy chủ và mất khi tải lại trang. Chưa có đăng nhập, phân quyền, đồng bộ Google Sheets, lưu lịch sử hoặc cơ sở dữ liệu. Tailwind, Font Awesome, Chart.js và SheetJS tải từ CDN nên cần mạng; khi thiếu Chart.js, bảng và CSV vẫn hoạt động. Khi thiếu SheetJS, dùng CSV. Chưa xác minh an toàn chuỗi cung ứng/CDN hoặc khả năng vận hành với dữ liệu thực tế lớn.

## Rà soát và thay đổi

Đã đọc toàn bộ HTML gốc 17.130 byte tại commit `b5fe36f`. Bản gốc có 3 tab, ma trận năng lực, công cụ đánh giá 4 chỉ số và 2 biểu đồ ghi số liệu cố định. Không có import, bộ lọc, xu hướng hay dữ liệu nguồn; header cố định BDH/72 KTV. Input trống có thể bị xem là đạt do so sánh NaN; lựa chọn bậc nghề không được sử dụng.

Bản nâng cấp giữ ba tab và ma trận, chuyển hai biểu đồ cũ sang dữ liệu nhập (OSML/đội trưởng), bổ sung biểu đồ xu hướng, kiểm tra file, lọc, cảnh báo, phân trang và CSV mẫu. Đã bỏ số liệu cố định chưa có nguồn; bổ sung kiểm tra input đánh giá, bỏ kết luận tự động đủ điều kiện nâng bậc. Tách xử lý dữ liệu thuần khỏi giao diện để kiểm thử.

Chưa thể xác minh từ mã nguồn: tính chính xác của số liệu BDH gốc, định nghĩa OSML/CSAT12, chuẩn phân bậc, chuẩn 7 KPI, đơn vị NSLĐ trong file xuất thực tế, danh sách KTV/đội trưởng và quyền truy cập GitHub Pages. Cần file xuất nghiệp vụ đã ẩn thông tin nhạy cảm và bảng chuẩn KPI để mapping chính xác hơn.

## Kiểm tra

Chạy `node --test tests/kpi.test.cjs` (Node 20+). Các bài kiểm thử bao gồm tiếng Việt, số thập phân, biên ngưỡng, dữ liệu thiếu, SCAT, tháng sai, số sai, trùng dòng, CSV có nháy, lọc và tính trung bình. Không cần cài npm để chạy dashboard hoặc kiểm thử thuần.

Kiểm tra tích hợp DOM trong môi trường làm việc đã qua: nhập CSV, lọc liên kết, dữ liệu lỗi giữ nguyên, nội dung giống HTML được hiển thị như văn bản, Excel có định dạng %, đổi sheet, xóa dữ liệu và thiếu Chart.js. Chưa chạy được trình duyệt thật/kiểm tra trực quan vì Chromium không có sẵn và bộ cài tải về không hợp lệ; chưa xác minh biểu đồ render hoặc bố cục mobile thực tế.
