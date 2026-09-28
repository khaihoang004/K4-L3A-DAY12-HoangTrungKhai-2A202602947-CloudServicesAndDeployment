# Thông Tin Deploy — Checkpoint 5

> Điền file này sau khi deploy xong. `pytest tests/test_cp5.py` đọc file này
> để tìm địa chỉ service của bạn và gọi thử.
>
> **Chỉ ghi TÊN biến môi trường, tuyệt đối không dán giá trị API key vào đây.**
> Repo này công khai — dán khóa vào là mất khóa.

## Thông Tin Học Viên

| Mục | Nội dung |
|-----|----------|
| Họ và tên | Hoàng Trung Khải |
| Mã học viên | 2A202602947 |
| Repo | https://github.com/khaihoang004/K4-L3A-DAY12-HoangTrungKhai-2A202602947-CloudServicesAndDeployment |

## Service

| Mục | Nội dung |
|-----|----------|
| Public URL | https://victorious-curiosity-production-a4f2.up.railway.app |
| Platform | Railway |
| Ngày deploy | 2026-09-28 (ngày xác minh hoạt động) |

## Biến Môi Trường Đã Set Trên Cloud

Ghi tên biến và **nguồn giá trị**, không ghi giá trị:

| Biến | Đã set | Ghi chú |
|------|--------|---------|
| `AGENT_API_KEY` | ✅ | 
| `REDIS_URL` | ✅ | Tham chiếu tới Redis service trên Railway; `/ready` xác nhận kết nối thành công |
| `RATE_LIMIT_PER_MINUTE` | ✅ | 10 |
| `MONTHLY_BUDGET_USD` | ✅ | 10.0 |
| `LOG_LEVEL` | ✅ | INFO |

Các lệnh dưới đây dùng Public URL của service:

```bash
# Nạp DEPLOY_API_KEY từ .env vào shell mà không in giá trị ra màn hình.
set -a; source .env; set +a

# 1. Liveness — mong đợi 200 {"status":"ok"}
curl -i https://victorious-curiosity-production-a4f2.up.railway.app/health

# 2. Readiness — mong đợi 200 {"status":"ready"} (đã nối được Redis)
curl -i https://victorious-curiosity-production-a4f2.up.railway.app/ready

# 3. Không có API key — mong đợi 401
curl -i -X POST https://victorious-curiosity-production-a4f2.up.railway.app/ask \
  -H "Content-Type: application/json" \
  -d '{"question":"Hello"}'

# 4. Có API key — mong đợi 200 kèm câu trả lời
curl -i -X POST https://victorious-curiosity-production-a4f2.up.railway.app/ask \
  -H "Content-Type: application/json" \
  -H "X-API-Key: $DEPLOY_API_KEY" \
  -H "X-User-Id: sv-test" \
  -d '{"question":"Deploy là gì?"}'

# 5. Rate limit — gọi 15 lần, những lần cuối phải trả 429
TEST_USER_ID="cp5-rate-check-$(date +%s)-$$"
for i in $(seq 1 15); do
  curl -s -o /dev/null -w "%{http_code} " -X POST https://victorious-curiosity-production-a4f2.up.railway.app/ask \
    -H "Content-Type: application/json" \
    -H "X-API-Key: $DEPLOY_API_KEY" \
    -H "X-User-Id: $TEST_USER_ID" \
    -d '{"question":"test"}'
done; echo
```

## Kết Quả Chạy Thật

Kết quả xác minh trực tiếp ngày 2026-09-28:

```
GET /health: 200 {"status":"ok","service":"day12-agent","version":"1.0.0"}
GET /ready: 200 {"status":"ready","redis":true}
POST /ask không có API key: 401 {"detail":"invalid or missing API key"}
POST /ask có API key: 200, có câu trả lời.
Rate limit (user kiểm tra riêng): 10 request đầu trả 200, 5 request sau trả 429.
```

## Ảnh Chụp Màn Hình

Đặt ảnh trong thư mục `screenshots/`:

- `screenshots/railway_deploy.png` — trang quản lý service trên platform
- `screenshots/test.png` — kết quả gọi `/health` từ trình duyệt hoặc curl

---

## Phương Án Triển Khai

Đã deploy service công khai trên Railway; không sử dụng phương án dự phòng local.
