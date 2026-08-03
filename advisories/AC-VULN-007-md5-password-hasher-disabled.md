# Admin.Core — passwordHasher disabled leads to weak password storage (AC-VULN-007)

| Field | Value |
|-------|-------|
| Vendor | zhontai |
| Product | Admin.Core (中台Admin) |
| Version | 10.1.0 |
| Type | Risky Cryptographic Algorithm |
| CWE | CWE-328 / CWE-916 |
| Authentication | N/A (defense-in-depth / credential theft) |
| Severity | Medium |

## Summary

`AppConfig.passwordHasher` defaults to false, so passwords use reversible/weak MD5-style storage instead of ASP.NET Identity PasswordHasher.

## Root cause

Default config disables standard password hashing.

## Exploit / reproduction

Inspect `ZhonTai.Admin.Host/ConfigCenter/appconfig.json`:

- `passwordHasher: false`
- `defaultPassword: 123asd`


## PoC (Yakit)

```http
# Config confirmation (Host ConfigCenter/appconfig.json):
#   "passwordHasher": false
#   "defaultPassword": "123asd"
# Login still succeeds with MD5-seeded admin password.
```

Expected: passwordHasher=false in default config; admin login works with 123asd

## Screenshots

Config snippet in report; default login screenshot shared with AC-VULN-002.

## Impact

Offline cracking trivial if DB leaked; weak credential protection.

## Remediation

Set passwordHasher=true; migrate hashes; enforce strong password policy.

## References

- Local report: `../POC_VERIFICATION_REPORT.md`
- Scripts: `poc_verify/admincore_poc_verify.py`, `admincore_jwt_forge.py`
- Upstream: https://github.com/zhontai/Admin.Core
