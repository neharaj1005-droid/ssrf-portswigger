## Lab: SSRF with blacklist-based input filter

- **Category:** Server-Side Request Forgery (SSRF)
- **Difficulty:** Practitioner
- **PortSwigger lab link:** https://portswigger.net/web-security/ssrf/lab-ssrf-filter-bypass-via-blacklisted-input
- **Status:** ✅ Completed

### Objective

Bypass a blacklist-based filter (blocking strings like "127.0.0.1" and "admin") to reach the internal admin interface and delete a target user.

### Vulnerability Overview

The stock-check feature validates the destination URL using a blacklist of literal substrings (`127.0.0.1` and `admin`) rather than validating the URL's actual structure or destination against a strict allow-list. This fails for two independent reasons: there are many equivalent ways to represent the same IP address that a substring match won't catch, and the filter and the backend disagree on how many times to URL-decode the request — so a blocked keyword can be hidden from the filter with encoding while the backend still resolves it to the sensitive path.

### Steps to Reproduce

1. **Confirm the normal behaviour.** Each product page has a "Check stock" button, which sends a POST to `/product/stock` with a `stockApi` parameter containing a full URL (e.g. `http://weliketoshop.net:8080/product/stock/check?productId=1&storeId=1`). This confirms the server itself fetches whatever URL is supplied.
2. **Test the filter.** Sent the request to Burp Repeater and changed `stockApi` to `http://127.0.0.1/`. Got back an HTTP 400 with `"External stock check blocked for security reasons"` — confirming a filter blocks the loopback address by string match.
3. **Bypass the IP filter.** Changed the value to the shorthand `http://127.1/` — a functionally identical address that isn't caught by the literal `127.0.0.1` string match. This returned a normal 200 response (the app's own front page), confirming the bypass worked.
4. **Hit the admin path.** Changed the value to `http://127.1/admin`. Blocked again with the same message — the filter separately blacklists the literal word "admin".
5. **Bypass the keyword filter.** Double URL-encoded the letter `a` in `admin`: `a` → `%61` (single encode) → `%2561` (encode the `%` itself again). Payload became `http://127.1/%2561dmin`. This returned the full admin panel HTML, listing users `wiener` and `carlos` with delete links.
6. **Trigger the delete action.** Submitted `http://127.1/%2561dmin/delete?username=carlos` through the same `stockApi` parameter. Got a `302 Found` redirect to `/admin` — the app's normal behaviour after a successful admin action.
7. **Verify.** Refreshed the lab page in the browser — status flipped from "Not solved" to **"Solved"**, confirming `carlos` was deleted via the SSRF chain.

### Payload(s) Used

```
http://127.1/
http://127.1/%2561dmin
http://127.1/%2561dmin/delete?username=carlos
```

### Evidence

![Request to 127.0.0.1 blocked](screenshots/01-blocked-ip.png)
*Figure 1 – Request to `http://127.0.0.1/` blocked with "External stock check blocked for security reasons".*

![127.1 bypasses the filter](screenshots/02-bypass-ip.png)
*Figure 2 – `http://127.1/` bypasses the filter; the server returns its own front page.*

![admin path blocked](screenshots/03-blocked-admin.png)
*Figure 3 – Adding `/admin` to the bypassed IP triggers the block again, showing a second, keyword-based filter.*

![Double-encoded bypass reaches admin panel](screenshots/04-bypass-admin.png)
*Figure 4 – Double-encoded payload `http://127.1/%2561dmin` reaches the real admin panel, listing users `wiener` and `carlos`.*

![Delete request for carlos](screenshots/05-delete-carlos.png)
*Figure 5 – Delete request for user "carlos" submitted through the same double-encoded SSRF chain; server responds 302 Found.*

![Lab solved](screenshots/06-solved.png)
*Figure 6 – Lab status confirmed as Solved after the delete action was executed via SSRF.*

### Impact

An attacker could reach the admin interface and internal-only endpoints without authentication, view all usernames, and delete arbitrary user accounts — all by manipulating a client-supplied URL, with no direct network access to the internal system required.

### Remediation

Avoid blacklisting known-bad values. Validate outbound requests against a strict allow-list of permitted hosts, resolved and compared at the network layer rather than as a raw string. The filter and the request-routing logic should also use a single, consistent URL-parsing and decoding implementation, so what's inspected is guaranteed to match what's ultimately requested.

### Notes / Lessons Learned

There is no proper input validation being performed on the value supplied by the client — the application relies on matching known bad substrings rather than genuinely validating and restricting what the server is allowed to request. That's why a simple alternate IP format plus one layer of extra URL-encoding was enough to defeat both filters independently.
