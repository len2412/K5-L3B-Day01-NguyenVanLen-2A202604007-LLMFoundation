# Báo cáo cá nhân — Day 01: Lọc tin nhắn rác bằng ML, DL, Embedding và LLM

> Đổi tên file này thành `MSSV_HoTen.md` của bạn, ví dụ `2A20260000_NguyenVanAn.md`, rồi điền từng mục. Xoá các dòng hướng dẫn in nghiêng khi xong.

- **MSSV:** 2A202604007
- **Họ và tên:** Nguyễn Văn Lên
- **SEED** (in ở ô đầu notebook): 862
## 1. Bằng chứng

| Tệp | Có? |
| --- | --- |
| [`day01_lab.ipynb`](day01_lab.ipynb) — notebook đã chạy, còn output | ☑ |
| [`lab1_report.json`](lab1_report.json) — do ô "Báo cáo cuối" sinh ra | ☑ |
| [`hand_calc.jpg`](hand_calc.jpg) — ảnh tính tay có các bước | ☑ |

## 2. Kết quả chính

| Phương pháp | F1 test | F1 SUB (40) | p95 (ms/tin) | USD/1k tin |
|---|---:|---:|---:|---:|
| BoW + Logistic Regression | 0.904 | 0.919 | 0.079 | 0.000 |
| BoW + MLP | **0.960** | **0.974** | 0.581 | 0.000 |
| MiniLM + kNN | 0.892 | 0.895 | 66.252 | 0.000 |
| MiniLM + Logistic Regression | 0.960 | 0.947 | 62.812 | 0.000 |
| MiniLM + MLP head | 0.955 | 0.947 | 41.062 | 0.000 |
| LLM zero-shot / few-shot | Chưa đo | Chưa đo | Chưa đo | Chưa đo |

## 3. Dự đoán và thực tế

| Thí nghiệm | Dự đoán trước khi chạy | Kết quả thực tế | Nguyên nhân |
|---|---|---|---|
| ML — F1 | F1 khoảng 0.7; precision cao hơn recall do dữ liệu lệch nhãn | F1 test = 0.904; precision = 1.000; recall = 0.824 | Dự đoán F1 thấp hơn thực tế 0.204. Ước lượng F1 quá thấp. |
| DL — bỏ ReLU | Mạng không có ReLU sẽ gần tương đương mô hình linear và F1 giảm mạnh | F1 khi bỏ ReLU = 0.955, trong khi có ReLU = 0.960 | Dự đoán mức giảm mạnh không đúng. Chênh lệch chỉ 0.005 trên test; Quên có hàm sigmoid. |
| Trade-off | Dự đoán BoW + Logistic Regression nằm trên Pareto; nhiều mô hình embedding sẽ vượt SLA 50 ms | Pareto gồm BoW + Logistic Regression và BoW + MLP. Các mô hình đạt SLA là BoW + Logistic Regression, BoW + MLP và MiniLM + MLP head | Đúng một phần. MiniLM + MLP head đạt SLA, trong khi MiniLM + kNN và MiniLM + Logistic Regression vượt SLA. BoW + MLP không vượt SLA như dự đoán. |

## 4. Phân tích 50 case khó

Model tụt nhiều nhất là BoW + MLP ở case spam giọng tự nhiên.
- Case 1 — Spam giọng tự nhiên: Cơ chế gây sai: tin nhắn mở đầu giống một cuộc trò chuyện cá nhân và không có các từ khóa quảng cáo thường gặp.
- Case 2 — Tin giao dịch hợp lệ bị nhầm: - Cơ chế gây sai: tin có mã số, cụm từ cảnh báo và lời yêu cầu gọi ngân hàng. Mô hình dựa vào từ khóa và có thể chặn nhầm OTP.

## 5. Thử thách cá nhân

*Đề theo SEED của bạn, cách làm, kết quả, giải thích.*
tạo 4 tin nhắn:
| Loại | Nội dung rút gọn | Nhãn thật | BoW + LogReg | Embedding + LogReg |
|---|---|---|---|---|
| Spam né từ khóa | “B.ạ.n đã tr_úng 100 tr.iệu đ.ồ.ng ... bit.ly/qua-tang” | spam | ham — sai | ham — sai |
| Spam né từ khóa | “Tuyển cộng tác viên gõ văn bản tại gia ... kh0ng c0c kh0ng ph1 ...” | spam | ham — sai | ham — sai |
| Ham có `free` | “Tối nay tao rảnh nè (completely free), đi đá bóng được không ...” | ham | ham — đúng | ham — đúng |
| Ham có `win` | “Đội tuyển futsal trường mình vừa win trận bán kết rồi nhé ...” | ham | ham — đúng | ham — đúng |

Kết quả: Cả hai mô hình đều đúng 2/4 tin. Hai tin spam né từ khóa đều bị dự đoán thành ham; hai tin ham có chứa free hoặc win đều được dự đoán đúng là ham.

Giải thích:
- Hai tin spam sử dụng ký tự. Những từ này có thể không xuất hiện trong vocabulary của BoW.

## 6. Phương án cho sản phẩm thật

*Với yêu cầu p95 < 50 ms/tin cho 10 triệu SMS/ngày: chọn phương án nào, dẫn số liệu nào, chấp nhận đánh đổi gì.*

## 7. AI log và điều đã học

Hiện tại em chưa chuẩn bị được API key nên chưa thấy AI log.
