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
- URL: https://your-app.railway.app
- Screenshot: [Link to screenshot in repo]

## Part 4: API Security

### Exercise 4.1-4.3: Test results
[Paste your test outputs]

### Exercise 4.4: Cost guard implementation
[Explain your approach]

## Part 5: Scaling & Reliability

### Exercise 5.1-5.5: Implementation notes
[Your explanations and test results]
```

---

### 2. Full Source Code - Lab 06 Complete (60 points)

Your final production-ready agent with all files:

```
your-repo/
├── app/
│   ├── main.py              # Main application
│   ├── config.py            # Configuration
│   ├── auth.py              # Authentication
│   ├── rate_limiter.py      # Rate limiting
│   └── cost_guard.py        # Cost protection
├── utils/
│   └── mock_llm.py          # Mock LLM (provided)
├── Dockerfile               # Multi-stage build
├── docker-compose.yml       # Full stack
├── requirements.txt         # Dependencies
├── .env.example             # Environment template
├── .dockerignore            # Docker ignore
├── railway.toml             # Railway config (or render.yaml)
└── README.md                # Setup instructions
```

**Requirements:**
-  All code runs without errors
-  Multi-stage Dockerfile (image < 500 MB)
-  API key authentication
-  Rate limiting (10 req/min)
-  Cost guard ($10/month)
-  Health + readiness checks
-  Graceful shutdown
-  Stateless design (Redis)
-  No hardcoded secrets

---

### 3. Service Domain Link

Create a file `DEPLOYMENT.md` with your deployed service information:

```markdown
# Deployment Information

## Public URL
https://your-agent.railway.app

## Platform
Railway / Render / Cloud Run

## Test Commands

### Health Check
```bash
curl https://your-agent.railway.app/health
# Expected: {"status": "ok"}
```

### API Test (with authentication)
```bash
curl -X POST https://your-agent.railway.app/ask \
  -H "X-API-Key: YOUR_KEY" \
  -H "Content-Type: application/json" \
  -d '{"user_id": "test", "question": "Hello"}'
```

## Environment Variables Set
- PORT
- REDIS_URL
- AGENT_API_KEY
- LOG_LEVEL

## Screenshots
- [Deployment dashboard](screenshots/dashboard.png)
- [Service running](screenshots/running.png)
- [Test results](screenshots/test.png)
```

##  Pre-Submission Checklist

- [ ] Repository is public (or instructor has access)
- [ ] `MISSION_ANSWERS.md` completed with all exercises
- [ ] `DEPLOYMENT.md` has working public URL
- [ ] All source code in `app/` directory
- [ ] `README.md` has clear setup instructions
- [ ] No `.env` file committed (only `.env.example`)
- [ ] No hardcoded secrets in code
- [ ] Public URL is accessible and working
- [ ] Screenshots included in `screenshots/` folder
- [ ] Repository has clear commit history

---

##  Self-Test

Before submitting, verify your deployment:

```bash
# 1. Health check
curl https://your-app.railway.app/health

# 2. Authentication required
curl https://your-app.railway.app/ask
# Should return 401

# 3. With API key works
curl -H "X-API-Key: YOUR_KEY" https://your-app.railway.app/ask \
  -X POST -d '{"user_id":"test","question":"Hello"}'
# Should return 200

# 4. Rate limiting
for i in {1..15}; do 
  curl -H "X-API-Key: YOUR_KEY" https://your-app.railway.app/ask \
    -X POST -d '{"user_id":"test","question":"test"}'; 
done
# Should eventually return 429
```

---

##  Submission

**Submit your GitHub repository URL:**

```
https://github.com/your-username/day12-agent-deployment
```

**Deadline:** 17/4/2026

---

##  Quick Tips

1.  Test your public URL from a different device
2.  Make sure repository is public or instructor has access
3.  Include screenshots of working deployment
4.  Write clear commit messages
5.  Test all commands in DEPLOYMENT.md work
6.  No secrets in code or commit history

---

##  Need Help?

- Check [TROUBLESHOOTING.md](TROUBLESHOOTING.md)
- Review [CODE_LAB.md](CODE_LAB.md)
- Ask in office hours
- Post in discussion forum

---

**Good luck! **
