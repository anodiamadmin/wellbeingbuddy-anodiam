# 🔐 Security & Compliance PR Checklist (MVP – Australia)

## 📌 Description
<!-- Briefly describe what this PR does -->

---

## ✅ Authentication & Access Control
- [ ] Passwords are hashed (bcrypt or equivalent)
- [ ] No plaintext credentials stored anywhere (code, logs, DB)
- [ ] Role-based access control enforced in backend (not just frontend)
- [ ] Auth tokens (JWT/session) have expiry and are securely handled
- [ ] Rate limiting implemented on login/auth endpoints

---

## ✅ Input Validation & API Security
- [ ] All inputs validated and sanitised (prevent XSS, SQL injection)
- [ ] Parameterized queries / ORM used (no raw SQL queries)
- [ ] API endpoints protected with authentication middleware
- [ ] Proper HTTP status codes used (no leaking system info)
- [ ] Request size limits implemented where applicable

---

## ✅ Data Protection (Privacy Act + ST4S)
- [ ] HTTPS enforced (TLS enabled)
- [ ] Sensitive data encrypted at rest (if applicable)
- [ ] Only required user data is collected (data minimisation)
- [ ] No sensitive data exposed in API responses
- [ ] Data schema reviewed for sensitive fields

---

## ✅ Logging & Monitoring
- [ ] Logs include user ID + timestamp for key actions
- [ ] Critical events logged (login, access, errors)
- [ ] No sensitive data (passwords, tokens, personal content) in logs
- [ ] Errors handled without exposing internal details

---

## ✅ Data Ownership & User Rights (ST4S)
- [ ] Data export functionality available (user can retrieve their data)
- [ ] Data deletion functionality implemented (user/admin request)
- [ ] Third-party data sharing clearly defined and limited

---

## ✅ Secrets & Configuration
- [ ] API keys, DB credentials stored in environment variables
- [ ] No secrets hardcoded in codebase
- [ ] `.env` files excluded from version control

---

## ✅ Security Testing
- [ ] OWASP Top 10 vulnerabilities checked
- [ ] No Critical/High vulnerabilities remain
- [ ] Authentication & authorization flows tested
- [ ] Basic abuse cases tested (invalid input, unauthorized access)

---

## ✅ Backup & Recovery
- [ ] Database backup mechanism configured
- [ ] Backup data is encrypted
- [ ] Restore process verified (at least once)

---

## ✅ Deployment Security
- [ ] Debug mode disabled in production
- [ ] Dependencies checked for known vulnerabilities
- [ ] Server/database access restricted (no public exposure)

---

## ✅ Privacy & OAIC Readiness
- [ ] Data collection purpose clearly defined in code/docs
- [ ] Minimal data stored (no unnecessary personal info)
- [ ] Logging supports breach investigation if needed

---

## 🚫 Blockers (Do NOT merge if any are true)
- [ ] Any critical/high security vulnerability exists
- [ ] Passwords or secrets exposed in any form
- [ ] Unauthorized access to data is possible
- [ ] Sensitive data logged or leaked

---

## 🧪 Testing
<!-- Describe how this was tested -->
- [ ] Unit tests added/updated
- [ ] Manual testing completed
- [ ] Edge cases considered

---

## 📄 Documentation
- [ ] Relevant documentation updated (if needed)
- [ ] API changes documented

---

## ✅ Final Check
- [ ] Feature meets functional requirements
- [ ] Meets non-functional requirements (security, performance, stability)

---

## 💡 Notes for Reviewer
<!-- Anything specific you want reviewers to focus on -->

---

**Rule of thumb:**  
> If this PR introduces risk to user data, it should NOT be merged until resolved.
