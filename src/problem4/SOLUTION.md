# Problem 4 – Solution

---

## Issue 1: Page downloaded instead of displaying at `http://localhost:8080`

- **Diagnosed by:** Browsing `http://localhost:8080` directly in the browser
- **Root cause:** nginx `return 200` response had no `Content-Type` header, so the browser treated it as a file download
- **Fix:** Added `add_header Content-Type text/plain;` to the root location in `nginx/conf.d/default.conf`

---

## Issue 2: Error 502 on `GET /api/users`

- **Diagnosed by:** Running `curl http://localhost:8080/api/users`
- **Root cause:** nginx was proxying to the wrong upstream port (`3001`) while the API listens on `3000`
- **Fix:** Changed `proxy_pass http://api:3001` → `proxy_pass http://api:3000` in `nginx/conf.d/default.conf`

---

## Issue 3: Error 404 on `GET /status`

- **Diagnosed by:** Running `curl http://localhost:8080/status`
- **Root cause:** The `/status` route was missing from the nginx config, so requests never reached the API
- **Fix:** Added a `location /status` block in `nginx/conf.d/default.conf` proxying to the api service

---

**What monitoring/alerts you would add**

1. HTTP 5xx/4xx error rate alerts on nginx to catch proxy misconfigurations early
2. Upstream health check alerts — alert when the API service is unreachable from nginx (502/504 spike)

**How you would prevent this in production**

1. Use IAC (Terraform, Helm) so port numbers and routes are defined once and reused — avoids mismatches like `3001` vs `3000` typo
2. Add integration or smoke tests in CI that curl endpoints for checking status before deploying
3. Enforce `Content-Type` headers via a shared nginx snippet or template so it is never accidentally omitted
4. Use environment variables for upstream host, ports instead of hardcoding them in nginx config
5. Lint nginx configuration with `nginx -t` in CI to catch syntax and misconfiguration errors before they reach production