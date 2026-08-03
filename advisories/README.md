# Admin.Core security advisories

English advisories for dynamically confirmed findings against [zhontai/Admin.Core](https://github.com/zhontai/Admin.Core).

| ID | Title | Severity |
|----|-------|----------|
| AC-VULN-001 | Hardcoded JWT securityKey allows forged identity tokens | Critical |
| AC-VULN-002 | Default administrator credentials allow login | High |
| AC-VULN-003 | Unauthenticated Swagger / ApiUI exposure | High |
| AC-VULN-004 | Unauthenticated CAP Dashboard access | High |
| AC-VULN-005 | HTML upload allowed and /upload/** served anonymously (stored XSS) | High |
| AC-VULN-006 | One-click login issues tokens for arbitrary usernames | High |
| AC-VULN-007 | passwordHasher disabled leads to weak password storage | Medium |
| AC-VULN-008 | Upload fileDirectory path traversal writes outside upload/ | High |
| AC-VULN-009 | Slide captcha oracle and reusable captchaId | High |
| AC-VULN-010 | Access JWT alone refreshes session without server revocation | High |
| AC-VULN-011 | Tenant one-click login issues target tenant admin tokens | High |
| AC-VULN-012 | Anonymous dictionary API information disclosure | Medium |
