# Fraud Shield – Web app Streamlit

Bàn làm việc chống gian lận thanh toán số · Festival Chuyển đổi số 2026 · Bảng B.
Ứng dụng chạy **đúng lõi mô hình trong hồ sơ** (`fraud_core.py`). Trên Data Pack BTC cho ra
F1 0,874 · Recall 0,836 · PR-AUC 0,934 · 474 cảnh báo · 326 case · ngưỡng 0,7399.
Mọi đặc trưng chỉ dùng thông tin có trước hoặc tại thời điểm giao dịch (cửa sổ 5 phút và 30 phút nhìn lùi,
hồ sơ hành vi chỉ từ các giao dịch trước đó).

## Ứng dụng có gì

Thanh điều hướng bên trái, 5 màn hình:

- **Tổng quan**: 4 chỉ số chính (giao dịch đã chấm, giao dịch cảnh báo, hồ sơ cần xem xét, F1), phân bố 4 mức rủi ro,
  Precision/Recall/F1/PR-AUC, xu hướng cảnh báo theo tuần, top 5 loại rủi ro, tiến độ xử lý, 5 hồ sơ cần xử lý trước.
- **Giao dịch**: lọc theo mức, trạng thái, ngày; tìm theo mã giao dịch/khách/merchant; phân trang; tải CSV;
  xem chi tiết điểm từng lớp, luật đã bật, độ lệch thói quen của khách.
- **Hồ sơ cảnh báo**: hồ sơ (case) gom các cảnh báo cùng khách trong 30 phút; danh sách + bảng chi tiết, nguyên nhân chính,
  ô quyết định của chuyên viên (Xác nhận gian lận / Báo động giả / Cần thêm thông tin), giao dịch trong hồ sơ, thói quen
  của khách, sơ đồ khách – thiết bị – merchant, nhật ký quyết định.
- **Kiểm định**: kết quả theo nhóm gian lận, ma trận nhầm lẫn, hiệu quả từng luật, phân tích lỗi, so sánh cấu hình,
  thanh kéo ngưỡng, công bằng.
- **Dữ liệu & Chạy mô hình**: tải Data Pack, kiểm tra dữ liệu, chạy mô hình, tải kết quả, xoá kết luận đã ghi.
- "Đã xử lý" = có kết luận: nhãn đã xác minh trong Data Pack hoặc quyết định chuyên viên lưu trong app (SQLite trên
  máy chủ – còn đến khi app khởi động lại).

## Cấu trúc

```
streamlit_app.py        giao diện (tệp chính khi deploy)
fraud_core.py           lõi mô hình: hồ sơ hành vi + Rule + Logistic Regression + Isolation Forest
model/pretrained.json   mô hình học sẵn, dùng khi Data Pack tải lên không có nhãn
data/                   Data Pack BTC (9 tệp CSV) cho nút "Chạy với Data Pack BTC"
.streamlit/config.toml  màu sắc, font, giới hạn tải lên 100 MB  ← BẮT BUỘC phải có trên GitHub
requirements.txt        thư viện cần cài
```

## Đưa lên mạng (Streamlit Community Cloud, miễn phí)

**Bước 1 – Tạo repo GitHub**
1. Vào github.com → **New repository** → tên `fraudshield-app` → chọn **Private** → **Create repository**.
2. Bấm **uploading an existing file**, kéo **toàn bộ nội dung** thư mục này vào (kể cả thư mục `.streamlit`).
   - macOS: Finder ẩn thư mục bắt đầu bằng dấu chấm – bấm `Cmd + Shift + .` để hiện rồi kéo cả `.streamlit`.
3. Bấm **Commit changes**. Kiểm tra trên GitHub thấy đủ `.streamlit/config.toml`, `data/`, `model/`.

**Bước 2 – Deploy**
1. Vào https://share.streamlit.io → **Continue with GitHub**, cho phép Streamlit truy cập repo (kể cả repo private).
2. **Create app** → **Yup, I have an app**.
3. Repository: `<tài-khoản>/fraudshield-app` · Branch: `main` · Main file path: `streamlit_app.py`.
4. App URL: đặt tên dễ nhớ, ví dụ `fraudshield-tendoi` → link sẽ là `https://fraudshield-tendoi.streamlit.app`.
5. **Advanced settings** → Python **3.12** → Save → **Deploy**. Lần đầu mất khoảng 2–5 phút.

**Bước 3 – Mở link cho mọi người**
Repo private thì app mặc định cũng private. Trong app bấm **Share** (góc phải trên) → **Make this app public**
(hoặc Settings → Sharing → *This app is public and searchable*). Mở link bằng tab ẩn danh để kiểm tra.

## Lưu ý

- App không có người dùng 12 giờ sẽ "ngủ"; người mở bấm **Yes, get this app back up!** và chờ khoảng 1 phút.
  Nên tự mở link trước khi gửi giáo viên / giám khảo.
- Sửa code trên GitHub → app tự cập nhật sau ít phút.
- Điền tên đội: sửa dòng `TEAM_NAME = ""` đầu tệp `streamlit_app.py`.
- Nhật ký quyết định chỉ lưu trong phiên đang mở – tải `audit_log.csv` ở tab **Tải kết quả** trước khi đóng.
- Nếu BTC không cho phép công khai Data Pack, xoá thư mục `data/`: app vẫn chạy, người dùng tự tải tệp .zip lên.

## Chạy trên máy

```bash
pip install -r requirements.txt
streamlit run streamlit_app.py
```
