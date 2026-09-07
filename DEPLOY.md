# Deploying jonmast.com

Static single file on Cloudflare Pages. DNS is already on Cloudflare
(`iris`/`damon.ns.cloudflare.com`), so there are no nameserver changes to make.

## ⚠️ Read this first

`jonmast.com` currently **302-redirects to `github.com/jonmast`** via an existing
Cloudflare Redirect Rule or Page Rule.

Redirect Rules are evaluated at the edge *before* Pages routing. If you skip
step 3, the Pages deploy will report success and the domain will still bounce to
GitHub. Nothing else will look wrong. This is the one step that costs an hour if
you miss it.

---

## 1. Create the Pages project

Cloudflare dashboard → **Workers & Pages** → **Create** → **Pages** →
**Connect to Git** → pick `jonmast/jonmast.com`.

Build settings — the defaults assume a build step, and this project has none:

| Setting | Value |
|---|---|
| Framework preset | **None** |
| Build command | **(leave empty)** |
| Build output directory | **`/`** |
| Production branch | **`main`** |

Click **Save and Deploy**. It publishes in a few seconds to
`<project>.pages.dev` — open that and confirm the page looks right *before*
touching the custom domain.

> Do not set the production branch to `prototype/splash-variants`. That branch
> holds the five throwaway design variants, not the site.

## 2. Attach the custom domains

Pages project → **Custom domains** → **Set up a custom domain**.

Add both:
- `jonmast.com`
- `www.jonmast.com`

Cloudflare rewrites the existing proxied `A`/`AAAA` records automatically. The
Fastmail `MX` records and the SPF `TXT` record are untouched — **email keeps
working**. Don't delete any `MX` or `TXT` record while doing this.

## 3. Delete the old GitHub redirect  ← the one people miss

Find and remove the rule sending the apex to GitHub. It's in one of:

- **Rules → Redirect Rules**, or
- **Rules → Page Rules** (older accounts)

Look for a rule matching `jonmast.com/*` with a forwarding target of
`https://github.com/jonmast`. Delete or disable it.

Verify it's actually gone:

```sh
curl -sI https://jonmast.com | head -1
```

`HTTP/2 200` means Pages is serving. `HTTP/2 302` means the rule is still live.

## 4. Point www at the apex

So there's a single canonical URL. **Rules → Redirect Rules → Create rule**:

- **If** — Custom filter expression: `http.host eq "www.jonmast.com"`
- **Then** — Dynamic redirect
  - Expression: `concat("https://jonmast.com", http.request.uri.path)`
  - Status: **301**
  - ☑ Preserve query string

## 5. Confirm

```sh
curl -sI https://jonmast.com     | head -1   # expect HTTP/2 200
curl -sI https://www.jonmast.com | head -1   # expect HTTP/2 301
dig +short MX jonmast.com                    # expect the two messagingengine hosts
```

That last one is worth running. It's the check that catches an accidental
email outage, which is a much worse failure than a broken splash page.

---

## Future edits

`main` is wired to auto-deploy:

```sh
# edit index.html
git commit -am "Update copy"
git push
```

Live in under a minute. Preview locally first with `python3 -m http.server 8000`.
