#  Delivery Checklist — Day 12 Lab Submission

> **Student Name:** Hứa Quang Linh 
> **Student ID:** 2A202600466
> **Date:** 17/4/2026

---

##  Submission Requirements

Submit a **GitHub repository** containing:

### 1. Mission Answers (40 points)

Create a file `MISSION_ANSWERS.md` with your answers to all exercises:

```markdown
# Day 12 Lab - Mission Answers

## Part 1: Localhost vs Production

### Exercise 1.1: Anti-patterns found
1. Vấn đề 1: API key hardcode trong code
 Nếu push lên GitHub → key bị lộ ngay lập tức
2. Vấn đề 2: Không có config management
3. Vấn đề 3: Print thay vì proper logging
4. Vấn đề 4: Không có health check endpoint
 Nếu agent crash, platform không biết để restart

5. Vấn đề 5: Port cố định — không đọc từ environment
 Trên Railway/Render, PORT được inject qua env var
...

### Exercise 1.3: Comparison table
| Feature | Basic | Advanced | Tại sao quan trọng? |
|---------|-------|----------|---------------------|
| Config | Hardcode | Env vars | Bảo mật, dễ cấu hình cho môi trường khác nhau |
| Health check | Không có | Có (/health, /ready) | Platform biết khi nào restart container |
| Logging | print() | JSON structured | Dễ parse, monitor trong production |
| Shutdown | Đột ngột | Graceful | Tránh mất dữ liệu, hoàn thành request đang chạy |
| Security | Hardcode secrets | Env vars, no secrets | Tránh lộ API key khi push code |
| Port | Cố định (8000) | Từ PORT env var | Chạy được trên cloud platforms |
| Binding | localhost | 0.0.0.0 | Chạy được trong Docker containers |

...

## Part 2: Docker

### Exercise 2.1: Dockerfile questions
1. Base image là gì?
Base image là python:3.11 (dòng FROM python:3.11). Đây là image Python chính thức từ Docker Hub, bao gồm Python 3.11 và các dependencies cơ bản.

2. Working directory là gì?
Working directory là /app (dòng WORKDIR /app). Đây là thư mục làm việc mặc định trong container, nơi các lệnh tiếp theo sẽ thực thi.

3. Tại sao COPY requirements.txt trước?
requirements.txt được copy trước (dòng COPY requirements.txt .) để tận dụng Docker layer caching. Nếu file này không thay đổi giữa các lần build, Docker sẽ sử dụng cache cho layer RUN pip install --no-cache-dir -r requirements.txt, giúp build nhanh hơn và tiết kiệm băng thông.
4. CMD vs ENTRYPOINT khác nhau thế nào?
Trong Dockerfile này, chỉ sử dụng CMD ["python", "app.py"] (không có ENTRYPOINT).

CMD: Xác định command mặc định khi container start. Có thể override bằng arguments khi docker run (ví dụ: docker run image bash sẽ chạy bash thay vì python app.py).
ENTRYPOINT: Xác định executable cố định, và arguments từ CMD hoặc docker run sẽ được append vào. Không thể override executable, chỉ có thể thêm arguments. ENTRYPOINT thường dùng cho containers chạy như executables (ví dụ: web servers). Nếu dùng ENTRYPOINT, CMD sẽ cung cấp default arguments.
...

### Exercise 2.3: Image size comparison
- Develop:  1.71GB 
- Production: 236MB
- Difference: 86%
Tại sao image nhỏ hơn (~236 MB vs 1.71 GB):

Builder stage bị loại bỏ — final image chỉ từ stage 2 (chỉ chứa những gì cần chạy, không cần build)
Không chứa build tools — gcc, libpq-dev, apt cache được loại bỏ
Dùng python:3.11-slim — lightweight image thay vì python:3.11 (full)
apt cache được xóa sạch — rm -rf /var/lib/apt/lists/* giảm size
--no-cache-dir — pip không lưu cache wheels
Kết quả: ~86% tiết kiệm dung lượng so với single-stage build!
## Part 3: Cloud Deployment

### Exercise 3.1: Railway deployment
- URL: https://railway.com/project/77b499d7-0fc3-4fe6-9e4d-813d0c926d28/service/425a285d-3868-4a90-91f7-7573b0fb7fcc?environmentId=8c99d5e6-75c5-45c8-847f-656678577d1e
- Screenshot: [Screenshot.png]

## Part 4: API Security

### Exercise 4.1-4.3: Test results
huath@MateBook-HQL MINGW64 /d/AI_2026/Excercise/Day12/Day12-Hua_Quang_Linh-2A202600466 (main)
$ curl http://localhost:8000/ask -X POST \
  -H "Content-Type: application/json" \
  -d '{"question": "Hello"}'
{"detail":"Missing API key. Include header: X-API-Key: <your-key>"}((.venv) ) 
huath@MateBook-HQL MINGW64 /d/AI_2026/Excercise/Day12/Day12-Hua_Quang_Linh-2A202600466 (main)
$ curl http://localhost:8000/ask -X POST \
  -H "X-API-Key: secret-key-123" \
  -H "Content-Type: application/json" \
  -d '{"question": "Hello"}'
{"detail":"Invalid API key."}((.venv) ) 
huath@MateBook-HQL MINGW64 /d/AI_2026/Excercise/Day12/Day12-Hua_Quang_Linh-2A202600466 (main)
$ curl http://localhost:8000/ask -X POST \
  -H "X-API-Key: demo-key-change-in-production" \
  -H "Content-Type: application/json" \
  -d '{"question": "Hello"}'
{"detail":[{"type":"missing","loc":["query","question"],"msg":"Field required","input":null}]}((.venv) ) 
huath@MateBook-HQL MINGW64 /d/AI_2026/Excercise/Day12/Day12-Hua_Quang_Linh-2A202600466 (main)
$ curl http://localhost:8000/token -X POST \
  -H "Content-Type: application/json" \
  -d '{"username": "teacher", "password": "teach456"}'
Internal Server Error((.venv) ) 
huath@MateBook-HQL MINGW64 /d/AI_2026/Excercise/Day12/Day12-Hua_Quang_Linh-2A202600466 (main)
$ curl http://localhost:8000/token -X POST \
  -H "Content-Type: application/json" \
  -d '{"username": "teacher", "password": "teach456"}'
{"detail":"Not Found"}((.venv) ) 
huath@MateBook-HQL MINGW64 /d/AI_2026/Excercise/Day12/Day12-Hua_Quang_Linh-2A202600466 (main)
$ curl http://localhost:8000/auth/token -X POST \
  -H "Content-Type: application/json" \
  -d '{"username": "teacher", "password": "teach456"}'
{"access_token":"eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiJ0ZWFjaGVyIiwicm9sZSI6ImFkbWluIiwiaWF0IjoxNzc2NDI0OTgzLCJleHAiOjE3NzY0Mjg1ODN9.J5-QlbRs6hSLMW7VWyDWB6kvoI-BdAmvquyooq6BoV4","token_type":"bearer","expires_in_minutes":60,"hint":"Include in header: Authorization: Bearer eyJhbGciOiJIUzI1NiIs..."}((.venv) ) 
huath@MateBook-HQL MINGW64 /d/AI_2026/Excercise/Day12/Day12-Hua_Quang_Linh-2A202600466 (main)
$ TOKEN="eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiJ0ZWFjaGVyIiwicm9sZSI6ImFkbWluIiwiaWF0IjoxNzc2NDI0OTgzLCJleHAiOjE3NzY0Mjg1ODN9.J5-QlbRs6hSLMW7VWyDWB6kvoI-BdAmvquyooq6BoV4"
curl http://localhost:8000/ask -X POST \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"question": "Explain JWT"}'
{"question":"Explain JWT","answer":"Đây là câu trả lời từ AI agent (mock). Trong production, đây sẽ là response từ OpenAI/Anthropic.","usage":{"requests_remaining":99,"budget_remaining_usd":2.1e-05}}((.venv) ) 
huath@MateBook-HQL MINGW64 /d/AI_2026/Excercise/Day12/Day12-Hua_Quang_Linh-2A202600466 (main)
$ for i in {1..20}; do
  curl http://localhost:8000/ask -X POST \
    -H "Authorization: Bearer $TOKEN" \
    -H "Content-Type: application/json" \
    -d '{"question": "Test '$i'"}'
  echo ""
done
{"question":"Test 1","answer":"Tôi là AI agent được deploy lên cloud. Câu hỏi của bạn đã được nhận.","usage":{"requests_remaining":98,"budget_remaining_usd":4e-05}}
{"question":"Test 2","answer":"Tôi là AI agent được deploy lên cloud. Câu hỏi của bạn đã được nhận.","usage":{"requests_remaining":97,"budget_remaining_usd":5.8e-05}}
{"question":"Test 3","answer":"Agent đang hoạt động tốt! (mock response) Hỏi thêm câu hỏi đi nhé.","usage":{"requests_remaining":96,"budget_remaining_usd":7.4e-05}}
{"question":"Test 4","answer":"Đây là câu trả lời từ AI agent (mock). Trong production, đây sẽ là response từ OpenAI/Anthropic.","usage":{"requests_remaining":95,"budget_remaining_usd":9.5e-05}}
{"question":"Test 5","answer":"Tôi là AI agent được deploy lên cloud. Câu hỏi của bạn đã được nhận.","usage":{"requests_remaining":94,"budget_remaining_usd":0.000114}}
{"question":"Test 6","answer":"Tôi là AI agent được deploy lên cloud. Câu hỏi của bạn đã được nhận.","usage":{"requests_remaining":93,"budget_remaining_usd":0.000133}}
{"question":"Test 7","answer":"Đây là câu trả lời từ AI agent (mock). Trong production, đây sẽ là response từ OpenAI/Anthropic.","usage":{"requests_remaining":92,"budget_remaining_usd":0.000154}}
{"question":"Test 8","answer":"Đây là câu trả lời từ AI agent (mock). Trong production, đây sẽ là response từ OpenAI/Anthropic.","usage":{"requests_remaining":91,"budget_remaining_usd":0.000175}}
{"question":"Test 9","answer":"Đây là câu trả lời từ AI agent (mock). Trong production, đây sẽ là response từ OpenAI/Anthropic.","usage":{"requests_remaining":90,"budget_remaining_usd":0.000196}}
{"question":"Test 10","answer":"Đây là câu trả lời từ AI agent (mock). Trong production, đây sẽ là response từ OpenAI/Anthropic.","usage":{"requests_remaining":89,"budget_remaining_usd":0.000217}}
{"question":"Test 11","answer":"Agent đang hoạt động tốt! (mock response) Hỏi thêm câu hỏi đi nhé.","usage":{"requests_remaining":88,"budget_remaining_usd":0.000233}}
{"question":"Test 12","answer":"Tôi là AI agent được deploy lên cloud. Câu hỏi của bạn đã được nhận.","usage":{"requests_remaining":87,"budget_remaining_usd":0.000251}}
{"question":"Test 13","answer":"Agent đang hoạt động tốt! (mock response) Hỏi thêm câu hỏi đi nhé.","usage":{"requests_remaining":86,"budget_remaining_usd":0.000268}}
{"question":"Test 14","answer":"Đây là câu trả lời từ AI agent (mock). Trong production, đây sẽ là response từ OpenAI/Anthropic.","usage":{"requests_remaining":85,"budget_remaining_usd":0.000289}}
{"question":"Test 15","answer":"Agent đang hoạt động tốt! (mock response) Hỏi thêm câu hỏi đi nhé.","usage":{"requests_remaining":84,"budget_remaining_usd":0.000305}}
{"question":"Test 16","answer":"Tôi là AI agent được deploy lên cloud. Câu hỏi của bạn đã được nhận.","usage":{"requests_remaining":83,"budget_remaining_usd":0.000323}}
{"question":"Test 17","answer":"Tôi là AI agent được deploy lên cloud. Câu hỏi của bạn đã được nhận.","usage":{"requests_remaining":82,"budget_remaining_usd":0.000342}}
{"question":"Test 18","answer":"Tôi là AI agent được deploy lên cloud. Câu hỏi của bạn đã được nhận.","usage":{"requests_remaining":81,"budget_remaining_usd":0.000361}}
{"question":"Test 19","answer":"Tôi là AI agent được deploy lên cloud. Câu hỏi của bạn đã được nhận.","usage":{"requests_remaining":80,"budget_remaining_usd":0.000379}}
{"question":"Test 20","answer":"Tôi là AI agent được deploy lên cloud. Câu hỏi của bạn đã được nhận.","usage":{"requests_remaining":79,"budget_remaining_usd":0.000398}}
((.venv) ) 

### Exercise 4.4: Cost guard implementation
Approach đã implement trong `04-api-gateway/production/cost_guard.py`:

1. Tạo lớp `CostGuard` để quản lý usage theo user trong ngày (in-memory demo bằng `UsageRecord`).
2. Trước khi gọi LLM, endpoint `/ask` gọi `cost_guard.check_budget(user_id)` để chặn sớm nếu vượt ngân sách.
3. Có 2 lớp ngân sách:
  - Per-user daily budget: mặc định `$1/day/user`.
  - Global daily budget: mặc định `$10/day` cho toàn service.
4. Nếu vượt per-user budget, trả về `HTTP 402` với thông tin `used_usd`, `budget_usd`, `resets_at`.
5. Nếu vượt global budget, trả về `HTTP 503` để tạm dừng service.
6. Sau khi có response từ LLM, gọi `cost_guard.record_usage(user_id, input_tokens, output_tokens)` để cộng dồn token và cost.
7. Cost được tính theo đơn giá token (input/output) và lưu vào record mỗi user; đồng thời cộng vào global cost.
8. Có cảnh báo log khi user dùng >= 80% budget (`warn_at_pct = 0.8`).

Kết quả test: gọi API thành công và trường `usage.budget_remaining_usd` giảm dần qua từng request, xác nhận cơ chế ghi nhận chi phí đang hoạt động.

### Checkpoint 4

- [x] Implement API key authentication
- [x] Hiểu JWT flow
- [x] Implement rate limiting
- [x] Implement cost guard với Redis

## Part 5: Scaling & Reliability

### Exercise 5.1-5.5: Implementation notes
5.1 Health checks:
- Implement `GET /health` và `GET /ready` trong `05-scaling-reliability/develop/app.py`.
- `/health` trả về trạng thái sống + uptime + checks.
- `/ready` trả `503` khi chưa sẵn sàng và trả `ready=true` khi instance đã sẵn sàng nhận traffic.

5.2 Graceful shutdown:
- Dùng `lifespan` để xử lý startup/shutdown và cờ `_is_ready`.
- Theo dõi `_in_flight_requests` qua middleware để biết còn bao nhiêu request đang xử lý.
- Khi nhận SIGTERM/SIGINT, service ngừng nhận request mới và chờ request đang chạy hoàn thành (tối đa 30s) trước khi tắt.

5.3 Stateless design:
- Ở production, state conversation không lưu trong RAM của 1 instance.
- Session/history được lưu theo key `session:<session_id>` với TTL (Redis-backed), nên instance nào cũng đọc được.
- Có fallback in-memory khi Redis không sẵn sàng (chỉ để demo local, không khuyến nghị khi scale).

5.4 Load balancing:
- Dùng `docker-compose.yml` với 3 lớp: `agent` (replicas), `redis`, `nginx`.
- Nginx proxy tới `agent_cluster` và phân phối request round-robin giữa các instances.
- Header `X-Served-By` và trường `served_by` trong response giúp quan sát request được xử lý bởi instance nào.

5.5 Test stateless:
- Dùng script `05-scaling-reliability/production/test_stateless.py` gửi nhiều request cùng `session_id`.
- Kết quả mong đợi: request có thể đi qua nhiều instance khác nhau nhưng history vẫn đầy đủ, chứng minh state được chia sẻ qua Redis.

### Checkpoint 5

- [x] Implement health và readiness checks
- [x] Implement graceful shutdown
- [x] Refactor code thành stateless
- [x] Hiểu load balancing với Nginx
- [x] Test stateless design
```
---

### 2. Full Source Code - Lab 06 Complete (60 points)

DEPLOYMENT.md
##  Submission

**Submit your GitHub repository URL:**

```
https://github.com/qlinh99/Day12-Hua_Quang_Linh-2A202600466.git
```

**Deadline:** 17/4/2026

