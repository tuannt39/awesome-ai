# Rule 02 — Security (Bảo mật)

> **Nguồn:** Tổng hợp từ ECC security-review SKILL.md và ECC code-reviewer security checklist
> **Chế độ áp dụng:** Always On — Áp dụng cho mọi code change, đặc biệt với API, auth, input

---

## Checklist Bảo mật trước mọi Commit

- [ ] Không có API keys, passwords, tokens hardcode trong source code
- [ ] Tất cả đầu vào bên ngoài đã được validate
- [ ] SQL queries sử dụng parameterized queries (không nối chuỗi trực tiếp)
- [ ] HTML output đã được sanitize (chống XSS)
- [ ] CSRF protection đã bật
- [ ] Authentication/authorization đã kiểm tra trên các route nhạy cảm
- [ ] Rate limiting đã áp dụng trên tất cả API endpoints
- [ ] Error messages không rò rỉ thông tin nhạy cảm

## Secrets Management — Quy tắc Bắt buộc

```
❌ TUYỆT ĐỐI KHÔNG: Hardcode secrets trong source code
const apiKey = "sk-proj-xxxxx"      # NGUY HIỂM

✅ LUÔN LUÔN: Dùng environment variables
const apiKey = process.env.OPENAI_API_KEY
if (!apiKey) throw new Error('OPENAI_API_KEY not configured')
```

- Secrets chỉ được lưu trong environment variables hoặc secret manager.
- `.env.local` và `.env` phải có trong `.gitignore`.
- Production secrets phải lưu trên nền tảng hosting (Vercel, Railway...).
- Nếu phát hiện secret bị lộ → Rotate ngay lập tức.

## Input Validation — Quy tắc Bắt buộc

- Validate TẤT CẢ user input tại ranh giới hệ thống với schema-based validation (Zod, Joi...).
- Dùng whitelist validation (chấp nhận rõ ràng) thay vì blacklist.
- File uploads: Kiểm tra size, MIME type, extension.
- Error messages không được tiết lộ chi tiết nội bộ.

## SQL Injection Prevention

```
❌ NGUY HIỂM: Nối chuỗi trực tiếp
const query = `SELECT * FROM users WHERE id = ${userId}`

✅ AN TOÀN: Parameterized queries
const query = `SELECT * FROM users WHERE id = $1`
const result = await db.query(query, [userId])
```

## Authentication & Authorization

- Tokens/sessions lưu trong **httpOnly cookies** (KHÔNG phải localStorage — dễ bị XSS).
- Kiểm tra authorization TRƯỚC khi thực hiện mọi thao tác nhạy cảm.
- Bật Row Level Security (RLS) trong database.

## Logging — Quy tắc Bắt buộc

```
❌ KHÔNG LOG: Dữ liệu nhạy cảm
console.log('User login:', { email, password })   # NGUY HIỂM

✅ LOG: Dữ liệu được ẩn
console.log('User login:', { email, userId })
```

## Nếu Phát hiện Vấn đề Bảo mật

**STOP → Kích hoạt `security-reviewer` agent → Fix CRITICAL issues → Rotate secrets bị lộ → Review codebase để tìm lỗi tương tự.**
