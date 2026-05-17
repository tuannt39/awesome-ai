---
name: security-review
description: Kích hoạt khi thêm authentication, xử lý user input, làm việc với secrets, tạo API endpoints, hoặc implement payment/sensitive features. Cung cấp comprehensive security checklist và patterns.
origin: ecc
---

# Security Review Skill

Skill này đảm bảo mọi code tuân theo security best practices và xác định potential vulnerabilities.

## Khi nào Kích hoạt

- Implement authentication hoặc authorization
- Xử lý user input hoặc file uploads
- Tạo new API endpoints
- Làm việc với secrets hoặc credentials
- Implement payment features
- Lưu trữ hoặc truyền tải sensitive data
- Tích hợp third-party APIs

## Secrets Management

```typescript
// ❌ TUYỆT ĐỐI KHÔNG: Hardcode secrets
const apiKey = "sk-proj-xxxxx"        // NGUY HIỂM
const dbPassword = "password123"      // TUYỆT ĐỐI KHÔNG

// ✅ LUÔN LUÔN: Dùng environment variables
const apiKey = process.env.OPENAI_API_KEY
if (!apiKey) throw new Error('OPENAI_API_KEY not configured')
```

**Verification:**
- [ ] Không có hardcoded API keys, tokens, passwords
- [ ] Tất cả secrets trong environment variables
- [ ] `.env.local` trong .gitignore
- [ ] Không có secrets trong git history
- [ ] Production secrets trong hosting platform

## Input Validation

```typescript
import { z } from 'zod'

const CreateUserSchema = z.object({
  email: z.string().email(),
  name: z.string().min(1).max(100),
  age: z.number().int().min(0).max(150)
})

export async function createUser(input: unknown) {
  const validated = CreateUserSchema.parse(input)
  return await db.users.create(validated)
}
```

**Verification:**
- [ ] Tất cả user inputs validated với schemas
- [ ] File uploads restricted (size, type, extension)
- [ ] Không sử dụng trực tiếp user input trong queries
- [ ] Whitelist validation (không phải blacklist)
- [ ] Error messages không rò rỉ sensitive info

## SQL Injection Prevention

```typescript
// ❌ NGUY HIỂM: Nối chuỗi SQL
const query = `SELECT * FROM users WHERE email = '${userEmail}'`

// ✅ AN TOÀN: Parameterized queries
const { data } = await supabase.from('users').select('*').eq('email', userEmail)
// Hoặc raw SQL:
await db.query('SELECT * FROM users WHERE email = $1', [userEmail])
```

## Authentication & Authorization

```typescript
// ❌ SAI: localStorage (dễ bị XSS)
localStorage.setItem('token', token)

// ✅ ĐÚNG: httpOnly cookies
res.setHeader('Set-Cookie',
  `token=${token}; HttpOnly; Secure; SameSite=Strict; Max-Age=3600`)

// Authorization checks
export async function deleteUser(userId: string, requesterId: string) {
  const requester = await db.users.findUnique({ where: { id: requesterId } })
  if (requester.role !== 'admin') {
    return NextResponse.json({ error: 'Unauthorized' }, { status: 403 })
  }
  await db.users.delete({ where: { id: userId } })
}
```

## XSS Prevention

```typescript
import DOMPurify from 'isomorphic-dompurify'

function renderUserContent(html: string) {
  const clean = DOMPurify.sanitize(html, {
    ALLOWED_TAGS: ['b', 'i', 'em', 'strong', 'p'],
    ALLOWED_ATTR: []
  })
  return <div dangerouslySetInnerHTML={{ __html: clean }} />
}
```

## Rate Limiting

```typescript
const limiter = rateLimit({
  windowMs: 15 * 60 * 1000,  // 15 minutes
  max: 100,                    // 100 requests per window
  message: 'Too many requests'
})
app.use('/api/', limiter)
```

## Sensitive Data Logging

```typescript
// ❌ KHÔNG LOG: Dữ liệu nhạy cảm
console.log('User login:', { email, password })     // NGUY HIỂM

// ✅ LOG: Dữ liệu được ẩn
console.log('User login:', { email, userId })

// ❌ KHÔNG: Expose internal errors
catch (error) {
  return NextResponse.json({ error: error.message, stack: error.stack }, { status: 500 })
}

// ✅ ĐÚNG: Generic error messages
catch (error) {
  console.error('Internal error:', error)
  return NextResponse.json({ error: 'An error occurred. Please try again.' }, { status: 500 })
}
```

## Pre-Deployment Security Checklist

Trước BẤT KỲ production deployment nào:

- [ ] **Secrets**: Không hardcoded, tất cả trong env vars
- [ ] **Input Validation**: Tất cả user inputs validated
- [ ] **SQL Injection**: Tất cả queries parameterized
- [ ] **XSS**: User content sanitized
- [ ] **CSRF**: Protection enabled
- [ ] **Authentication**: Proper token handling (httpOnly cookies)
- [ ] **Authorization**: Role checks in place
- [ ] **Rate Limiting**: Enabled trên tất cả endpoints
- [ ] **HTTPS**: Enforced trong production
- [ ] **Security Headers**: CSP, X-Frame-Options configured
- [ ] **Error Handling**: Không có sensitive data trong errors
- [ ] **Logging**: Không có sensitive data được log
- [ ] **Dependencies**: Up to date, không có vulnerabilities

## Dependency Security

```bash
npm audit            # Check for vulnerabilities
npm audit fix        # Fix automatically fixable issues
npm outdated         # Check for outdated packages
npm ci               # Use trong CI/CD (reproducible builds)
```

## Nếu Phát hiện Security Issue

**STOP → Kích hoạt `security-reviewer` agent → Fix CRITICAL issues → Rotate exposed secrets → Review codebase cho similar issues.**
