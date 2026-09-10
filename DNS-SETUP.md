# DNS for gonetspeedfiber.com — Namecheap

Your domain is registered at **Namecheap** (created 2026-09-10) and still on
Namecheap's default nameservers:

```
dns1.registrar-servers.com
dns2.registrar-servers.com
```

That's good news — you don't need Cloudflare. Everything happens in Namecheap's
own DNS panel.

Right now `gonetspeedfiber.com` resolves to `192.64.119.220` and
`www` points at `parkingpage.namecheap.com` — that's the Namecheap parking page.
Both have to go.

---

## Step 1 — open the panel

Namecheap → **Domain List** → **Manage** next to gonetspeedfiber.com →
**Advanced DNS** tab.

## Step 2 — DELETE the two default records

Namecheap pre-fills these on every new domain. If you leave them, they fight
with the records below and the site won't load:

| Delete this | Host | Current value |
|---|---|---|
| **URL Redirect Record** | `@` | parking page |
| **CNAME Record** | `www` | `parkingpage.namecheap.com.` |

Hit the trash icon on both.

## Step 3 — ADD these five records

| Type | Host | Value | TTL |
|---|---|---|---|
| A Record | `@` | `185.199.108.153` | Automatic |
| A Record | `@` | `185.199.109.153` | Automatic |
| A Record | `@` | `185.199.110.153` | Automatic |
| A Record | `@` | `185.199.111.153` | Automatic |
| CNAME Record | `www` | `mwaseemz.github.io.` | Automatic |

Notes:
- Yes, **four separate A records all on `@`** — that's GitHub's redundancy, not
  a mistake. Add them one at a time.
- The CNAME value needs the **trailing dot**: `mwaseemz.github.io.`
- Do not add an A record for `www`, and do not add a CNAME for `@`.

Click the green **Save All Changes** checkmark.

## Step 4 — tell me, and I'll finish it

Namecheap usually propagates in 5–30 minutes. Once it's live I run:

```
gh api -X PUT /repos/mwaseemz/gonetspeedfiber/pages \
  -f cname=gonetspeedfiber.com -F https_enforced=true
```

That attaches the domain to GitHub Pages and provisions the HTTPS certificate
(a few more minutes). I held this back on purpose — running it before DNS
resolves would redirect the working preview URL to a domain that doesn't answer
yet, and you'd lose the live site in the meantime.

## Checking propagation yourself

```
dig +short gonetspeedfiber.com
```

You want to see the four `185.199.1xx.153` addresses. While it still shows
`192.64.119.220`, it hasn't propagated.

---

## Live now (works today, no DNS needed)

**https://mwaseemz.github.io/gonetspeedfiber/**

Use this to review the page, and for any ad testing before the domain cuts over.
