# Moodle App Download Page — Prototype Handoff

> Everything you need to pick this project up on a new machine and continue exactly where we left off.

---

## What this is

A fully functional, high-fidelity HTML prototype of the **Moodle App download page**, built from Figma designs using Claude Code + Figma MCP. It is used for user testing and design critiques.

The prototype is a **single-page static site** — no build step, no framework, no npm. Just HTML, CSS, and vanilla JS served by Python's built-in HTTP server.

---

## File structure

```
moodle-download-prototype/
├── index.html          ← Main landing page (desktop + responsive)
├── bridge.html         ← Mobile bridge page (QR code destination, detects iOS/Android)
├── presentation.html   ← 11-slide presentation deck about the process
├── moodle-logo.svg     ← Moodle logo used in bridge.html
├── qrcode-moodle.png   ← Static QR code PNG (for Figma)
└── assets/             ← All icons and images (local, never expire)
    ├── nav-logo.svg
    ├── nav-search.svg
    ├── hero-orange.svg
    ├── hero-arrow.svg
    ├── hero-left.svg
    ├── cta-lead.svg
    ├── cta-trail.svg
    ├── phone-system-bar.png
    ├── phone-avatar.png
    ├── phone-cloud-download.svg
    ├── phone-search.svg
    ├── phone-chevron.svg
    ├── phone-sort.svg
    ├── phone-grid.svg
    ├── phone-ellipsis.svg
    ├── fc-bell.svg
    ├── fc-assignment-stroke.svg
    ├── feat-wifi.svg
    ├── feat-chart.svg
    ├── feat-mobile.svg
    ├── inst-wifi.svg
    ├── inst-arrow.svg
    ├── store-apple.svg
    ├── store-google.svg
    ├── store-download.svg
    └── footer.png
```

---

## Design tokens

| Token | Value | Used for |
|---|---|---|
| `--moodle-orange` | `#f98012` | Primary CTA, feature icons, accents |
| `--moodle-blue` | `#194866` | Headings, phone bar, nav |
| `--medium-peach` | `#ffecdb` | Features section background |
| `--border-default` | `#dee2e6` | Card borders |
| `--text-default` | `#1d2125` | Body text |
| `--text-muted` | `#6a737b` | Secondary text |

**Fonts** (loaded via Google Fonts, no install needed):
- `DM Serif Display` — page headings
- `Open Sans` — body, nav, buttons
- `Roboto` — UI labels inside the phone mockup

---

## Figma source

**File:** `Input form components`
**File key:** `G5hwVQpQPbHhi7NJnUVU4i`
**URL:** `https://www.figma.com/design/G5hwVQpQPbHhi7NJnUVU4i/`

Key nodes used:
| Node | Description |
|---|---|
| `56:1666` | Full responsive design (desktop + mobile) |
| `56:1750` | Phone mockup with screen content |
| `56:3220` | Offline ready floating card |
| `56:3264` | New assignment floating card |
| `56:3274` | Assignment status floating card |
| `56:1143` | Scan to download modal |
| `56:1384` | QR code bridge page (mobile) |
| `56:1748` | CTA button |
| `56:1758` | Phone top app bar with avatar |

---

## How to run locally

### Requirements
- macOS or Linux
- Python 3 (comes pre-installed on macOS)
- No npm, no Node, no installs needed for the prototype itself

### 1. Start the server

```bash
cd /path/to/moodle-download-prototype
python3 -m http.server 8080 --bind 0.0.0.0
```

Open in browser: `http://localhost:8080`

From your phone (same Wi-Fi): `http://YOUR_LOCAL_IP:8080`

To find your local IP on Mac:
```bash
ipconfig getifaddr en0
```

### 2. Share publicly (no-interstitial tunnel)

First, install cloudflared (requires Homebrew):
```bash
brew install cloudflared
```

Then run (keep this terminal open while sharing):
```bash
cloudflared tunnel --url http://localhost:8080
```

This outputs a public HTTPS URL like:
```
https://some-words-here.trycloudflare.com
```

Share that URL — visitors land directly on the prototype with no warning pages.

### 3. Generate a new QR code (if tunnel URL changes)

```bash
python3 -m pip install qrcode pillow
python3 -c "
import qrcode
url = 'https://YOUR-NEW-TUNNEL-URL.trycloudflare.com/bridge.html'
qr = qrcode.QRCode(error_correction=qrcode.constants.ERROR_CORRECT_M, box_size=12, border=2)
qr.add_data(url)
qr.make(fit=True)
qr.make_image(fill_color='#000000', back_color='#ffffff').save('qrcode-moodle.png')
print('Done')
"
```

> **Note:** The QR code in the live modal (`index.html`) auto-generates dynamically using `qrcode.js` via CDN. It always points to `window.location.origin + '/bridge.html'`, so it adapts to whatever URL the page is served from — no code change needed.

---

## How the prototype works

### Flow
1. User lands on **index.html** — sees the Moodle App download page
2. Clicks **"Get the Moodle App"** CTA button
3. A **modal with a QR code** slides up over a scrim
4. User scans QR code on their phone → lands on **bridge.html**
5. **bridge.html** detects iOS or Android → redirects to App Store or Google Play

### Key interactions
- **Modal**: opens with fade+slide animation, closes on Cancel or clicking the scrim
- **Floating cards**: fade+slide up entrance animation on page load (once only)
- **Background circles**: subtle drift-in animation on load (3s, once only)
- **QR code**: dynamically generated via `qrcode.js` CDN library

### Phone mockup
Built with pure CSS — no image:
```css
.phone-shell {
  border: 9.9px solid #000;
  border-bottom-width: 20px;
  border-radius: 40px;
}
.phone-screen {
  border-radius: 31px 31px 0 0;
  overflow: hidden;
}
```

---

## Continuing in Claude Code

When you open a new Claude Code session on the new machine, paste this at the start:

> "I'm continuing work on the Moodle App download page prototype. The project is at `/path/to/moodle-download-prototype`. It's a static HTML prototype (no build step) built from Figma file `G5hwVQpQPbHhi7NJnUVU4i`. The main files are `index.html` (landing page), `bridge.html` (mobile QR destination), and `presentation.html` (process deck). All assets are local in `assets/`. The server runs with `python3 -m http.server 8080 --bind 0.0.0.0` and public sharing uses `cloudflared tunnel --url http://localhost:8080`."

---

## Known quirks

| Issue | Fix |
|---|---|
| Cloudflare tunnel URL changes every restart | Re-run `cloudflared tunnel --url http://localhost:8080` to get a new URL. QR code in modal auto-updates. Re-export `qrcode-moodle.png` if needed. |
| SVGs must have pixel dimensions | All SVGs in `assets/` have been fixed to use pixel `width`/`height` from their viewBox. Do not replace them with fresh Figma MCP downloads without running the fix script below. |
| Figma MCP asset URLs expire in 7 days | All assets are now local files — this is no longer an issue unless you add new assets from Figma. |

### SVG fix script (run if you add new assets from Figma)
```bash
python3 << 'EOF'
import os, re
for fname in os.listdir("assets"):
    if not fname.endswith(".svg"): continue
    path = f"assets/{fname}"
    content = open(path).read()
    vb = re.search(r'viewBox="0 0 ([\d.]+) ([\d.]+)"', content)
    if not vb: continue
    w, h = vb.group(1), vb.group(2)
    content = re.sub(r'width="100%"', f'width="{w}"', content)
    content = re.sub(r'height="100%"', f'height="{h}"', content)
    content = content.replace(' preserveAspectRatio="none"', '')
    open(path, "w").write(content)
    print(f"Fixed {fname}")
EOF
```

---

## Dependencies summary

| Dependency | How it's used | Install |
|---|---|---|
| Python 3 | Local HTTP server | Pre-installed on macOS |
| Homebrew | To install cloudflared | `/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"` |
| cloudflared | Public HTTPS tunnel (no interstitial) | `brew install cloudflared` |
| qrcode + pillow | Generate static QR PNG for Figma | `python3 -m pip install qrcode pillow` |
| qrcode.js | Live QR in modal (loaded via CDN) | No install — loaded from `cdnjs.cloudflare.com` |
| Google Fonts | DM Serif Display, Open Sans, Roboto | No install — loaded via `<link>` in HTML |

---

*Built with Claude Code + Figma MCP — March 2026*
