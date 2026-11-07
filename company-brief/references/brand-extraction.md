# Brand extraction

`<skill-dir>` below means the company-brief skill folder (the one holding SKILL.md).

Goal: the brand's real colors, type, and look, used two ways: described in the Brand identity section, and applied to the PDF through `scripts/tokens.py` and `scripts/fonts.py`.

Sources in order of trust: the live site's CSS (Verified) → official brand guidelines or press kit (Verified) → Brandfetch or a design-press write-up (Reported) → search snippets (Estimate).

## 1. Live site, in a browser

Open the homepage and run this in the page. It returns the visible headline's real style, loaded font faces, colors weighted by on-screen area, hex counts from the stylesheets, CSS custom properties, large gradients, and logo info.

```js
(() => {
  const vis = (e) => { const r = e.getBoundingClientRect(), c = getComputedStyle(e); return r.width > 0 && r.height > 0 && c.visibility !== 'hidden' && c.display !== 'none' && +c.opacity > 0; };
  const st = (e) => { if (!e) return null; const c = getComputedStyle(e); return { text: (e.innerText || '').trim().slice(0, 60), font: c.fontFamily, weight: c.fontWeight, size: c.fontSize, tracking: c.letterSpacing, lineHeight: c.lineHeight, transform: c.textTransform, color: c.color, bg: c.backgroundColor, radius: c.borderRadius }; };
  const first = (sel) => [...document.querySelectorAll(sel)].find(vis);
  const headline = [...document.querySelectorAll('h1, h2, [class*="hero" i] *, [class*="headline" i]')].filter(vis)
    .sort((a, b) => parseFloat(getComputedStyle(b).fontSize) - parseFloat(getComputedStyle(a).fontSize))[0];
  const hex = (c) => { const m = (c || '').match(/[\d.]+/g); if (!m || (m[3] !== undefined && +m[3] === 0)) return null; return '#' + m.slice(0, 3).map((x) => Math.round(+x).toString(16).padStart(2, '0')).join(''); };
  const area = {};
  document.querySelectorAll('body *').forEach((e) => {
    if (!vis(e)) return; const r = e.getBoundingClientRect(), c = getComputedStyle(e);
    const bg = hex(c.backgroundColor); if (bg) area[bg] = (area[bg] || 0) + r.width * r.height;
    if ([...e.childNodes].some((n) => n.nodeType === 3 && n.textContent.trim())) { const fg = 'text ' + hex(c.color); area[fg] = (area[fg] || 0) + 1; }
  });
  const rules = [...document.styleSheets].flatMap((s) => { try { return [...s.cssRules]; } catch { return []; } });
  const hexCounts = {}; rules.forEach((r) => (r.cssText.match(/#[0-9a-f]{6}\b/gi) || []).forEach((h) => { h = h.toLowerCase(); hexCounts[h] = (hexCounts[h] || 0) + 1; }));
  const vars = rules.filter((r) => r.style && /:root|html|body/.test(r.selectorText || ''))
    .flatMap((r) => [...r.style].filter((p) => p.startsWith('--')).map((p) => `${p}: ${r.style.getPropertyValue(p).trim()}`))
    .filter((x) => /#[0-9a-f]{3,8}\b|rgb|hsl|oklch|font/i.test(x)).slice(0, 80);
  const gradients = [...document.querySelectorAll('body *')].filter((e) => vis(e) && getComputedStyle(e).backgroundImage.includes('gradient'))
    .map((e) => { const r = e.getBoundingClientRect(); return { area: r.width * r.height, size: Math.round(r.width) + 'x' + Math.round(r.height), bg: getComputedStyle(e).backgroundImage.slice(0, 300) }; })
    .sort((a, b) => b.area - a.area).slice(0, 5).map(({ size, bg }) => ({ size, bg }));
  const logo = first('header a[href="/"] svg, header svg, [class*="logo" i] svg, header img, [class*="logo" i] img');
  const logoInfo = logo && { tag: logo.tagName, label: logo.getAttribute('alt') || logo.getAttribute('aria-label'), size: Math.round(logo.getBoundingClientRect().width) + 'x' + Math.round(logo.getBoundingClientRect().height),
    fills: logo.tagName.toLowerCase() === 'svg' ? [...new Set([...logo.querySelectorAll('[fill]')].map((n) => n.getAttribute('fill')))] : null };
  return JSON.stringify({
    headline: st(headline), h2: st(first('h2')), body: st(first('main p, p')), button: st(first('button, a[class*="button" i], [class*="btn" i]')),
    eyebrow: st(first('[class*="eyebrow" i], [class*="overline" i], [class*="kicker" i], [class*="tag" i]')), nav: st(first('header a, nav a')),
    loadedFonts: [...new Set([...document.fonts].filter((f) => f.status === 'loaded').map((f) => `${f.family.replace(/"/g, '')} ${f.weight} ${f.style}`))].slice(0, 30),
    colorsByArea: Object.entries(area).sort((a, b) => b[1] - a[1]).slice(0, 16).map(([k, v]) => k.startsWith('text') ? `${k} ×${v}` : `${k} ${Math.round(v / 1000)}k px²`),
    stylesheetHex: Object.entries(hexCounts).sort((a, b) => b[1] - a[1]).slice(0, 20), vars, gradients, logo: logoInfo,
  }, null, 1);
})()
```

Then:
- **Logo**: take a screenshot and zoom on the header logo. Describe it in words for the brief: mark shape, wordmark case and typeface style, colors, how it sits on dark and light. Never trace, redraw, or embed it; link the press kit instead.
- **Hero art**: if `gradients` is empty but the hero looks like a gradient or photo, zoom a screenshot and name the colors you see, or, if you can save the screenshot as a file, sample it:
  ```python
  from PIL import Image
  im = Image.open("hero.png").convert("RGB"); w, h = im.size
  for x, y in [(.05,.1),(.5,.1),(.95,.1),(.05,.5),(.95,.5),(.05,.9),(.5,.9),(.95,.9)]:
      print(x, y, "#%02x%02x%02x" % im.getpixel((int(x*(w-1)), int(y*(h-1)))))
  ```
- **Sub-brands**: check the events page, the conference site, and any product with its own look. Note a sub-brand's fonts and colors separately; don't mix them into the main palette.
- **Scroll once** and rerun on a second page (pricing or a product page) if the homepage is mostly imagery.

Without a browser: fetch the homepage and look for linked stylesheet URLs, Brandfetch (`brandfetch.com/<domain>`, via a search result link), the press kit, and searches for `"<company>" brand colors hex` and `"<company>" font`.

## 2. Identifying the typeface

- `loadedFonts` gives the family names the site really uses. Custom names ("BrandSans", "Nuckle") are usually commercial fonts: search `"<font name>" font foundry`.
- Typewolf, Fonts In Use, and the foundry's own site often list the brand as a client. Note the foundry and whether it's commercial.
- If the font is on Google Fonts, use it directly. Otherwise pick a free stand-in and say so in the Type section ("set here in X, the closest free match for their Y").

Stand-in starting points:

| Their font | Free stand-in to test |
|---|---|
| Helvetica, Neue Haas Grotesk, Arial | Inter, Hanken Grotesk |
| SF Pro | Inter |
| Circular, Gilroy | DM Sans, Figtree |
| Graphik, Söhne, Nuckle | Inter Tight |
| GT America, Founders Grotesk | Public Sans, Schibsted Grotesk |
| Proxima Nova, Gotham | Montserrat, Figtree |
| Futura, Avenir | Jost, Nunito Sans |
| Apercu, Aeonik, Akkurat | Manrope, Space Grotesk |
| GT Walsheim, Brown | Outfit, Lexend |
| Acid Grotesk, Neue Machina | Space Grotesk, Unbounded |
| Tiempos, Times-like serifs | Newsreader, Source Serif 4 |
| Freight, Canela, high-contrast display serifs | Fraunces, Playfair Display |
| Brand mono (Suisse Mono, GT America Mono) | DM Mono, JetBrains Mono, IBM Plex Mono |

**Stand-in test** (2 minutes, worth it): install 2–3 candidates with `scripts/fonts.py`, render the brand's real headline in each, and compare with a zoomed screenshot of the live headline. Match width, x-height, and the shapes of a, g, R, and t.

```bash
python3 "<skill-dir>/scripts/fonts.py" "Inter Tight:500" "Manrope:500" "Space Grotesk:500"
python3 - <<'EOF'
from playwright.sync_api import sync_playwright
cands, text = ["Inter Tight", "Manrope", "Space Grotesk"], "Their real headline here"
links = "".join(f'<link rel="stylesheet" href="node_modules/@fontsource/{c.lower().replace(" ", "-")}/500.css">' for c in cands)
rows = "".join(f"<div style=\"font:500 54px '{c}';letter-spacing:-0.03em;margin:10px 0\">{text} <small style='font:12px monospace'>{c}</small></div>" for c in cands)
open("compare.html", "w").write(f"<html><head>{links}</head><body style='margin:24px;width:1400px'>{rows}</body></html>")
with sync_playwright() as p:
    b = p.chromium.launch(); pg = b.new_page(viewport={"width": 1450, "height": 400})
    pg.goto("file://" + __import__("os").path.abspath("compare.html")); pg.evaluate("document.fonts.ready")
    pg.screenshot(path="compare.png", full_page=True); b.close()
EOF
```
Look at `compare.png` next to the screenshot of their headline and pick the closest.

## 3. Turning findings into tokens

Pick:
- `--brand`: the most identifying dark-enough color (headings rules, numbers, timeline dots). It must reach 3:1 on paper. If the signature color is light (yellow, pastel, neon), make the brand's dark color `--brand` and use the bright one as `--accent` or a cover stop.
- `--accent`: a second brand color for links, list markers, and meters. `tokens.py` darkens it for small text and keeps the original for shapes.
- `--ink` / `--paper`: the site's text and background colors if they're a signature warm black or off-white; otherwise near-black on white.
- Cover: a solid `--brand` cover by default, or the hero gradient's stops via `--cover-stops` when the site has a signature gradient. `tokens.py` picks cover text for the worst-case stop.
- Fonts: display = the headline face or its stand-in, body = the body face, label = the brand's mono or eyebrow face if it has one.
- `--display-weight` and `--tracking`: copy from the headline's computed weight and letter-spacing (convert px to em by dividing by the font size).
- `--radius`: from the buttons and cards: 0 for sharp editorial brands, 6–10px for most, 12px+ for soft ones.

Run, from the working folder:
```bash
python3 "<skill-dir>/scripts/tokens.py" --brand "#141213" --accent "#2EA281" \
  --font-display "Inter Tight" --font-label "DM Mono" --display-weight 500 --tracking -0.03em --radius 10px \
  --footer "Acme · Company Brief · Nov 2026" --cover-stops "#F4BE5C,#E4C187,#9EB7B3" > tokens.html
```
Read the stderr report. Every line should say `ok`; a `WARNING` means pick different colors. Paste `tokens.html` into brief.html at the TOKENS marker.

Put only small, brand-specific touches in the per-brief `<style>` block: an abstract cover pattern from the brand's shapes (dots, grid, stripes, pixels), a dark callout in brand colors, the display size. Keep the base stylesheet unchanged.
