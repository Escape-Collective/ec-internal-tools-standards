# Escape Collective — Team Assets & Standards

This repo is a shared resource for the EC team. It contains brand assets (logos, colours) useful to anyone producing EC content or reports, as well as technical standards for developers building internal tools.

**Jump to what you need:**
- [Brand Assets — Logos](#logos) — for reports, presentations, documents, and web
- [Brand Assets — Colours](#colours) — colour values in multiple formats
- [Internal Tools Development](#internal-tools-development) — for developers building EC internal apps

---

## Brand Assets

<a name="logos"></a>
### Logos

All logo files live in `/assets/logos/` in this repo. Download the file you need for your context.

#### File inventory

| File | Description |
|------|-------------|
| `ESC_Master_Logo_Orange.svg` | "escape" wordmark — orange |
| `ESC_Master_Logo_White.svg` | "escape" wordmark — white |
| `ESC_Master_Logo_Black.svg` | "escape" wordmark — black |
| `ESC_Symbol_Orange.svg` | EC circular mark — orange |
| `ESC_Symbol_White.svg` | EC circular mark — white |
| `ESC_Symbol_Black.svg` | EC circular mark — black |

SVG files are vector and scale to any size without quality loss. Use SVG wherever the tool or application supports it. Use PNG (export from the SVG if needed) for contexts that don't support SVG, such as Word, Excel, or older PDF tools.

#### Which logo to use

**On dark or black backgrounds:** use the orange or white variants. The orange wordmark (`ESC_Master_Logo_Orange.svg`) is the primary choice; white is a clean alternative when orange feels too prominent.

**On white or light backgrounds:** use the black variant (`ESC_Master_Logo_Black.svg`). Don't use the orange wordmark on white — the contrast is too low for print and most screen contexts.

**Wordmark vs. symbol:** use the wordmark (`ESC_Master_Logo`) for headers, cover pages, and anywhere the logo is the primary identifier. Use the symbol (`ESC_Symbol`) for smaller contexts — footers, favicons, profile images, watermarks, or anywhere space is tight.

#### Usage by document type

**PDF reports:** Place the wordmark in the header or cover page. For a light/white background, use `ESC_Master_Logo_Black.svg`. For a dark cover, use `ESC_Master_Logo_Orange.svg` or `ESC_Master_Logo_White.svg`. Add the symbol to the footer if you want a subtle brand mark on body pages.

**Spreadsheets (Excel / Google Sheets):** Insert the logo image into a merged header row at the top of the sheet. Use `ESC_Master_Logo_Black` on a white background, or switch the header row fill to `#1a1a1a` (navy) and use `ESC_Master_Logo_Orange` or `ESC_Master_Logo_White`.

**Presentations (PowerPoint / Google Slides):** Use the wordmark on title slides. On dark-background slides, use the orange or white variant. On light slides, use black. The symbol works well as a subtle watermark in slide corners.

**Word documents / Google Docs:** Insert into the header using `ESC_Master_Logo_Black.svg` (or export as PNG if SVG isn't supported). Keep it modest in size — logo height of around 20–25pt works well for document headers.

**Web / internal tools:** See the [Internal Tools Development](#internal-tools-development) section.

---

<a name="colours"></a>
### Colours

| Name | Hex | RGB | Usage |
|------|-----|-----|-------|
| EC Orange | `#FF6F42` | `255, 111, 66` | Primary brand colour — CTAs, highlights, accents |
| EC Orange (hover) | `#E3633B` | `227, 99, 59` | Hover and pressed states on orange elements |
| EC Orange (tint) | `#FFF2EE` | `255, 242, 238` | Light orange background — callout boxes, highlights |
| EC Navy / Black | `#1a1a1a` | `26, 26, 26` | Dark backgrounds, headers, primary dark text, text on orange |
| Body text | `#1a1f2e` | `26, 31, 46` | Main text on light backgrounds |
| Secondary text | `#6b7280` | `107, 114, 128` | Labels, captions, supporting text |
| Light background | `#f7f8fa` | `247, 248, 250` | Page/section backgrounds |
| Border | `#eaecf0` | `234, 236, 240` | Dividers, table borders, input borders |

**Using these in design tools:** Most tools accept hex values directly. In Word/PowerPoint, go to custom colour and enter the hex code (without the `#`). In Adobe tools, you can use either hex or RGB. In Google Docs/Slides, use the hex code in the custom colour picker.

<a name="text-on-orange"></a>
#### Text on orange

**Always use dark text (`#1a1a1a`) on EC Orange. Never use white text on orange.**

White on `#FF6F42` has a contrast ratio of 2.76:1, which fails accessibility guidelines (WCAG AA) even for large or bold text. `#1a1a1a` on `#FF6F42` is 6.3:1 (5.1:1 on the `#E3633B` hover), which passes for all text sizes.

This applies anywhere orange is used as a fill: buttons, badges, pills, table header rows, slide title bars, cover-page bands, and labels on orange chart bars.

For the same reason, don't use EC Orange for body text or small text on white or light backgrounds. Use orange for fills, accents, borders, icons, and large display elements; use `#1a1f2e` for text.

---

## Internal Tools Development

The following sections are for developers building EC internal web tools and applications. If you're building a report or document rather than a web app, you don't need any of this.

---

### Tech Stack

- **Runtime:** Node.js
- **Framework:** Express
- **Config:** `dotenv` (`.env` file)
- **Session:** `express-session`
- **HTTP fetching:** `node-fetch` v2 (CommonJS-compatible — use `"node-fetch": "^2"` in package.json, not v3)
- **Frontend:** Vanilla HTML/CSS/JS — no frontend framework unless there's a compelling reason

---

### Project Structure

```
app/
  server.js       ← Express backend (all routes, middleware, API logic)
  login.html      ← Login page (served as static file)
  admin.html      ← Admin page (served as static file, requireAdmin protected)
  public/         ← Static assets served after auth (main app HTML, CSS, JS)
  config.json     ← Runtime config (allowed users, app-specific settings)
  uploads/        ← Temp file storage (gitignored)
.env              ← Secrets and environment config (gitignored)
package.json
```

---

### Authentication

All EC internal tools use **OTP (one-time password) authentication via Mailgun**. Do not use username/password auth. Do not use third-party OAuth unless there's a specific reason.

#### How it works

1. User enters their email address
2. Server checks whether the email is in `allowedUsers` (config.json) or `ADMIN_EMAILS` (.env)
3. If allowed, a 6-digit OTP is generated and emailed via Mailgun
4. User enters the code; server verifies it and creates a session
5. Session persists for 7 days

#### User tiers

- **Regular users** — listed in `config.json` → `allowedUsers[]`. Managed via the admin UI without a server restart.
- **Admins** — listed in `.env` → `ADMIN_EMAILS` (comma-separated). Never stored in config.json. Changes require a server-side edit. Admins have access to the `/admin` route and all `/api/admin/` endpoints.

#### Required npm packages

```json
"express": "^4",
"express-session": "^1",
"node-fetch": "^2",
"dotenv": "^16"
```

#### Required environment variables

```env
# Mailgun
MAILGUN_API_KEY=        # Your Mailgun API key
MAILGUN_DOMAIN=         # e.g. mg.escapecollective.com
MAILGUN_FROM=           # e.g. EC Tool Name <noreply@mg.escapecollective.com>
MAILGUN_API_URL=        # EU accounts only: https://api.eu.mailgun.net — omit for US

# Session
SESSION_SECRET=         # Long random string, keep secret

# Admin access
ADMIN_EMAILS=           # Comma-separated: jase@escapecollective.com,other@escapecollective.com
```

#### config.json baseline

```json
{
  "allowedUsers": []
}
```

Add any tool-specific config fields alongside `allowedUsers`.

#### Backend implementation (copy into server.js)

```javascript
// ── OTP store ─────────────────────────────────────────────────────────────────
// Lightweight in-memory store — no database required for a small internal tool.
// Each entry: { code, expiresAt, attempts, requestedAt }
const otpStore = new Map();

function generateOtp() {
  return Math.floor(100000 + Math.random() * 900000).toString();
}

async function sendOtpEmail(toEmail, code, toolName = "EC Internal Tool") {
  const apiKey = process.env.MAILGUN_API_KEY;
  const domain = process.env.MAILGUN_DOMAIN;
  const from   = process.env.MAILGUN_FROM || `${toolName} <noreply@${domain}>`;
  const apiUrl = process.env.MAILGUN_API_URL || "https://api.mailgun.net";

  if (!apiKey || !domain) {
    throw new Error("MAILGUN_API_KEY and MAILGUN_DOMAIN must be set in .env");
  }

  const body = new URLSearchParams({
    from,
    to:      toEmail,
    subject: `Your login code — ${toolName}`,
    text:    `Your login code is: ${code}\n\nThis code expires in 10 minutes.\n\nIf you didn't request this, you can safely ignore this email.`,
    html:    `<div style="font-family:sans-serif;max-width:400px;margin:40px auto">
      <p style="color:#6b7280;font-size:14px">${toolName}</p>
      <h2 style="margin:8px 0 24px">Your login code</h2>
      <div style="background:#fff2ee;border-left:4px solid #FF6F42;border-radius:8px;padding:24px;text-align:center;margin-bottom:24px">
        <span style="font-size:36px;font-weight:700;letter-spacing:10px;font-family:monospace;color:#1a1a1a">${code}</span>
      </div>
      <p style="color:#6b7280;font-size:13px">This code expires in 10 minutes. If you didn't request this, you can safely ignore this email.</p>
    </div>`,
  });

  const res = await fetch(`${apiUrl}/v3/${domain}/messages`, {
    method:  "POST",
    headers: {
      "Authorization": "Basic " + Buffer.from(`api:${apiKey}`).toString("base64"),
      "Content-Type":  "application/x-www-form-urlencoded",
    },
    body: body.toString(),
  });

  if (!res.ok) {
    const text = await res.text();
    throw new Error(`Mailgun error (${res.status}): ${text}`);
  }
}

// ── Admin helpers ─────────────────────────────────────────────────────────────
function getAdminEmails() {
  return (process.env.ADMIN_EMAILS || "")
    .split(",")
    .map(e => e.toLowerCase().trim())
    .filter(Boolean);
}

// ── Auth middleware ───────────────────────────────────────────────────────────
function requireAuth(req, res, next) {
  const exempt = req.path === "/login" || req.path.startsWith("/api/auth/");
  if (exempt) return next();
  if (req.session?.authenticated) return next();
  if (req.path.startsWith("/api/")) return res.status(401).json({ error: "Not authenticated." });
  res.redirect("/login");
}

function requireAdmin(req, res, next) {
  if (!req.session?.authenticated || !req.session?.isAdmin) {
    if (req.path.startsWith("/api/")) {
      return res.status(403).json({ error: "Admin access required." });
    }
    return res.redirect("/");
  }
  next();
}

// ── Session middleware ────────────────────────────────────────────────────────
app.use(session({
  secret:            process.env.SESSION_SECRET || "dev-secret-replace-in-production",
  resave:            false,
  saveUninitialized: false,
  cookie: {
    httpOnly: true,
    secure:   process.env.NODE_ENV === "production",
    maxAge:   7 * 24 * 60 * 60 * 1000, // 7 days
  },
}));
app.use(requireAuth);

// ── Auth routes ───────────────────────────────────────────────────────────────

// Step 1 — request OTP
app.post("/api/auth/request-otp", async (req, res) => {
  const email = (req.body.email || "").toLowerCase().trim();
  if (!email) return res.status(400).json({ error: "Email is required." });

  const config      = readConfig();
  const allowed     = (config.allowedUsers || []).map(e => e.toLowerCase());
  const adminEmails = getAdminEmails();
  const isAllowed   = allowed.includes(email) || adminEmails.includes(email);

  // Always respond { sent: true } to avoid revealing which addresses are authorised
  if (!isAllowed) {
    console.log(`  ℹ️  OTP requested for unlisted address: ${email}`);
    return res.json({ sent: true });
  }

  // Rate limit: one request per 60 seconds per address
  const existing = otpStore.get(email);
  if (existing && Date.now() - existing.requestedAt < 60 * 1000) {
    return res.status(429).json({ error: "Please wait a minute before requesting another code." });
  }

  const code = generateOtp();
  otpStore.set(email, {
    code,
    expiresAt:   Date.now() + 10 * 60 * 1000,
    attempts:    0,
    requestedAt: Date.now(),
  });

  try {
    await sendOtpEmail(email, code, "YOUR TOOL NAME HERE");
    console.log(`  ✓ OTP sent to ${email}`);
  } catch (err) {
    console.error("Failed to send OTP email:", err.message);
    otpStore.delete(email);
    return res.status(500).json({ error: "Failed to send the email. Please try again." });
  }

  res.json({ sent: true });
});

// Step 2 — verify OTP
app.post("/api/auth/verify-otp", (req, res) => {
  const email = (req.body.email || "").toLowerCase().trim();
  const code  = (req.body.code  || "").trim();

  const entry = otpStore.get(email);
  if (!entry) {
    return res.status(400).json({ error: "No code found for this address. Please request a new one." });
  }
  if (Date.now() > entry.expiresAt) {
    otpStore.delete(email);
    return res.status(400).json({ error: "Code has expired. Please request a new one." });
  }

  entry.attempts++;
  if (entry.attempts > 5) {
    otpStore.delete(email);
    return res.status(400).json({ error: "Too many incorrect attempts. Please request a new code." });
  }
  if (entry.code !== code) {
    const left = 6 - entry.attempts;
    return res.status(400).json({ error: `Incorrect code — ${left} attempt${left !== 1 ? "s" : ""} remaining.` });
  }

  otpStore.delete(email);
  req.session.authenticated = true;
  req.session.userEmail     = email;
  req.session.isAdmin       = getAdminEmails().includes(email);
  console.log(`  ✓ ${email} authenticated (admin: ${req.session.isAdmin})`);
  res.json({ success: true });
});

// Logout
app.post("/api/auth/logout", (req, res) => {
  req.session.destroy(() => res.json({ success: true }));
});

// Current user
app.get("/api/me", (req, res) => {
  res.json({
    email:   req.session.userEmail,
    isAdmin: !!req.session.isAdmin,
  });
});
```

#### Config helpers (also in server.js)

```javascript
const CONFIG_PATH = path.join(__dirname, "config.json");

function readConfig() {
  return JSON.parse(fs.readFileSync(CONFIG_PATH, "utf8"));
}

function writeConfig(config) {
  fs.writeFileSync(CONFIG_PATH, JSON.stringify(config, null, 2), "utf8");
}
```

#### Admin API routes (add these if the tool has an admin panel)

```javascript
app.get("/api/admin/config", requireAdmin, (req, res) => {
  try { res.json(readConfig()); }
  catch (err) { res.status(500).json({ error: "Failed to read config." }); }
});

app.post("/api/admin/config", requireAdmin, (req, res) => {
  try {
    const newConfig = req.body;
    if (!Array.isArray(newConfig.allowedUsers)) {
      return res.status(400).json({ error: "allowedUsers must be an array." });
    }
    newConfig.allowedUsers = newConfig.allowedUsers.map(e => e.toLowerCase().trim()).filter(Boolean);
    writeConfig(newConfig);
    console.log(`  ✓ Config saved by ${req.session.userEmail}`);
    res.json({ success: true });
  } catch (err) {
    res.status(500).json({ error: err.message });
  }
});
```

---

### UI / Design System

These CSS conventions apply to all EC internal web tools.

#### Header

The standard header is 60px tall, navy background, white text. It contains the EC logo, a vertical divider, and the tool name.

```html
<header>
  <img src="assets/logos/ESC_Master_Logo_Orange.svg" height="28" alt="Escape Collective" />
  <div class="divider"></div>
  <h1>Your Tool Name</h1>
</header>
```

```css
header {
  background: #1a1a1a;
  color: #fff;
  padding: 0 32px;
  height: 60px;
  display: flex;
  align-items: center;
  gap: 12px;
}
header .divider { width: 1px; height: 20px; background: rgba(255,255,255,0.2); }
header h1 { font-size: 16px; font-weight: 600; }
```

#### CSS custom properties

```css
:root {
  --navy:      #1a1a1a;
  --orange:    #FF6F42;
  --orange-h:  #E3633B;  /* hover / pressed */
  --orange-l:  #FFF2EE;  /* tint */
  --on-orange: #1a1a1a;  /* text on orange fills — never white */
  --gray-1:    #f7f8fa;
  --gray-2:    #eaecf0;
  --gray-3:    #c8cdd6;
  --gray-4:    #6b7280;
  --text:      #1a1f2e;
  --radius:    8px;
  --shadow:    0 2px 12px rgba(0,0,0,0.08);
}
```

#### Typography

- Font stack: `-apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif`
- Body: 14–15px, `var(--text)`
- Secondary/label: 13px, `var(--gray-4)`
- Monospace (OTP codes, IDs): system monospace

#### Buttons

Primary buttons use dark text on orange — see [Text on orange](#text-on-orange).

```css
.btn {
  padding: 11px 16px;
  background: var(--orange);
  color: var(--on-orange);
  border: none;
  border-radius: var(--radius);
  font-size: 15px;
  font-weight: 600;
  cursor: pointer;
  transition: background 0.15s;
}
.btn:hover:not(:disabled) { background: var(--orange-h); }
.btn:disabled { opacity: 0.5; cursor: not-allowed; }
```

#### Form inputs

```css
input {
  width: 100%;
  padding: 10px 12px;
  border: 1.5px solid var(--gray-3);
  border-radius: var(--radius);
  font-size: 14px;
  color: var(--text);
  outline: none;
  transition: border-color 0.15s;
}
input:focus { border-color: var(--orange); }
```

---

### Security notes

- `SESSION_SECRET` must be a long random string in production. Never commit it to git.
- `ADMIN_EMAILS` must be set in `.env` on the server. Admin status is checked live from env on every request — no restart needed for email changes, but the server process must be running with the updated env.
- The OTP endpoint always returns `{ sent: true }` for unrecognised addresses. This prevents user enumeration — don't change this behaviour.
- OTPs expire after 10 minutes and are invalidated after 5 failed attempts.
- Sessions are `httpOnly` and `secure` in production.

---

### Checklist for a new tool

- [ ] Copy auth block from this doc into `server.js`
- [ ] Update tool name string in `sendOtpEmail()` call
- [ ] Add all required env vars to `.env`
- [ ] Create `config.json` with at least `{ "allowedUsers": [] }`
- [ ] Copy needed logo files from `/assets/logos/` into the project and reference in header/favicon
- [ ] Apply CSS custom properties to stylesheets
- [ ] Check no white text sits on orange fills — use `var(--on-orange)`
- [ ] Add admin user management UI if users need to be managed without server access
- [ ] Add repo to the `escape-collective` GitHub org
