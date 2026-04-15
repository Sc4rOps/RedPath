# XSS — DVWA

| Field | Details |
|-------|---------|
| **Platform** | DVWA (local lab) |
| **OS** | Linux |
| **Difficulty** | Low → High |
| **Date** | 2026-04-15 |
| **Status** | Completed |

---

## Attack Path

```
Reflected XSS → Stored XSS → DOM XSS → Filter Bypass per level
```

---

## What happened

Tested all three XSS types across Low, Medium, and High security levels.

**Reflected XSS**
Low had zero filtering — `<script>alert(1)</script>` executed directly. Medium filtered `<script>` lowercase only, bypassed with `<SCRIPT>`. High used case-insensitive regex on script tags, bypassed with `<img src=x onerror=alert(1)>`.

**Stored XSS**
Same filter logic applied to the Name field. Medium and High both had a `maxlength="10"` restriction on the frontend — changed it to 100 via browser DevTools and injected the payload. Stored payloads persist in the database and fire for every visitor.

**DOM XSS**
Payload goes into the URL parameter `?default=`. Medium blocked script tags server-side. High whitelisted only four language values. Both bypassed using the `#` fragment — the server never sees anything after `#`, only the browser does.

---

## What I learned

- Client-side length restrictions mean nothing — always check `maxlength` in DevTools
- `str_replace` on lowercase only is trivially bypassed with uppercase
- Regex filtering `<script>` doesn't stop event-handler payloads like `onerror`
- DOM XSS lives entirely in the browser — server-side filters can be bypassed with `#`
- `htmlspecialchars()` is the correct fix — anything less is bypassable

---

## References

- [OWASP XSS](https://owasp.org/www-community/attacks/xss/)
- [PayloadsAllTheThings - XSS](https://swisskyrepo.github.io/PayloadsAllTheThings/XSS%20Injection/)
