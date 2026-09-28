# Phiếu Phản Ánh — K4 Level 3A, Ngày 12

> Họ và tên: Hoàng Trung Khải  |  Mã học viên: 2A202602947

---

### Câu 1 — Fail fast (CP1)

Nếu quên đặt `AGENT_API_KEY` trên Railway, app dừng ngay. Nếu dùng khóa mặc định `changeme`, người khác có thể gọi `/ask` và phát sinh chi phí trước khi mình phát hiện.

---

### Câu 2 — Log cho máy đọc (CP1)

```json
{"event":"ask_completed","level":"info","timestamp":"2026-09-28T11:12:58.761856+00:00","user_id":"exercise-log","tokens_in":2,"tokens_out":36,"cost_usd":2.19e-05}
```

Log này có thể lọc theo user/thời gian và tổng hợp token hoặc chi phí. Dòng chữ thường không có các trường đó.

---

### Câu 3 — Kích thước image (CP2)

| Bản | Dung lượng đo được |
|-----|--------------------|
| 1 stage (bản đầu) | 1.23 GB |
| Multi-stage | 271 MB disk usage; 63.9 MB content size |

Multi-stage dùng base `slim` và không giữ stage builder.

---

### Câu 4 — Thứ tự lệnh trong Dockerfile (CP2)

Sửa `main.py` thì layer cài dependency được cache, còn layer copy source phải chạy lại. Nếu copy source trước `pip install`, sửa code cũng làm bước cài package chạy lại.

---

### Câu 5 — Vì sao không chạy bằng root (CP2)

Nếu lỗ hổng cho phép chạy lệnh, kẻ tấn công thừa hưởng quyền của app. `USER 10001` giảm quyền của process và giới hạn thiệt hại, nhưng không ngăn mọi lỗ hổng.

---

### Câu 6 — Cửa sổ trượt (CP3)

Có thể gửi 20 request trong 2 giây: 10 ngay trước phút mới và 10 ngay sau khi bộ đếm reset. Sliding window 60 giây sẽ tính cả 20 request trong cùng cửa sổ.

---

### Câu 7 — Rate limit và cost guard (CP3)

Rate limit giới hạn số lần gọi; cost guard giới hạn tiền tháng. Hết ngân sách thì cost guard chặn dù còn quota. Đủ 10 request/phút thì rate limit chặn dù còn ngân sách.

---

### Câu 8 — /health khác /ready (CP4)

Redis mất kết nối làm `/ready` trả 503 để load balancer ngừng gửi request. `/health` vẫn trả 200 vì process còn sống; nếu gộp hai probe, cả ba container có thể bị đánh dấu unhealthy và bị restart không cần thiết.

---

### Câu 9 — Stateless (CP4)

Request đầu có `history_length: 0`, request tiếp theo cùng user có `2`. Nếu lưu bằng dict trong RAM, request sang replica khác có thể lại thấy `0`; restart cũng làm mất history.

---

### Câu 10 — Deploy thật (CP5)

Mình gặp `301 Moved Permanently` vì gọi URL Railway thiếu `https://`. Header `Location` chỉ sang HTTPS; thêm `https://` vào URL thì các endpoint trả đúng mã.
