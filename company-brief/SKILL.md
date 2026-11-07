---
name: company-brief
description: Researches any company in depth from its name or URL and delivers a designed PDF brief styled in that company's own colors and type, covering financials, full history, leadership, hiring stats and pay, employee reviews, brand guidelines and assets, advertising, activations, social, videos, podcasts, news, competitors, risks, and exactly how to talk to them in an interview or pitch. Use when someone asks for a company brief, company research or a deep dive on a company, interview prep for a specific company, whether to work for or accept an offer from a company, or how to pitch or approach a company as a client. Usage - /company-brief followed by a company name or URL, optionally "for" a role, optionally "shareable".
---

# Company Brief

Turn a company name or URL into a deep, sourced, designed PDF that tells the reader everything they need to walk into an interview, offer conversation, or client pitch: what the company is, how it's doing, who runs it, what working there is like, how its brand and marketing work, who it competes with, and how to talk to it. The PDF uses the company's own colors and type, so it feels like it belongs to them.

## Usage

```
/company-brief <company name or URL> [for <role>] [shareable]
```

Examples: `/company-brief linear.app`, `/company-brief Figma for Senior Brand Designer`, `/company-brief Notion shareable`. Plain requests work too: "make me a company brief on Notion before my interview."

- **Company**: a URL settles identity. A bare name may not (Step 1).
- **for <role>**: focuses hiring, people to know, and interview prep on that role and its team. Without it, default to design, brand, marketing, and creative leadership, or to whatever the person's background suggests.
- **Personal mode (default)**: written for the person asking. Use what you know about them (memory, past chats, this conversation, an attached resume or portfolio) for "Your angle", the pitch, and "Fit for you". If there is an offer in hand, add "Before you sign". If you know nothing about them, write those parts generically and offer in the reply to tailor them.
- **Shareable** ("shareable", "for a friend", "to send to someone"): nothing about the person. No "Prepared for" pill, no Fit score, no Before you sign. Part five becomes "How to talk to them" and "Your angle" becomes "Angles that land".

Don't stop to ask questions unless the company's identity is genuinely ambiguous. Start in the first reply: one sentence on what you're doing, the task list, then research.

## Skill files

Paths below use `${CLAUDE_SKILL_DIR}`. If that text isn't replaced with a real path where you're running, find the folder once with `find / -name brief-template.html -path '*company-brief*' 2>/dev/null | head -1` and use its absolute path in every command (shell variables may not persist between commands).

| File | What it's for |
|---|---|
| `assets/brief.css` | Base stylesheet. Copy it; don't edit it. |
| `assets/brief-template.html` | Skeleton of the whole brief with `{{placeholders}}` |
| `scripts/fonts.py` | Installs free fonts from Fontsource and prints `<link>` tags |
| `scripts/tokens.py` | Turns brand colors into the token `<style>` block, with contrast fixes |
| `scripts/render.cjs` | Renders the PDF with Node Playwright and audits fonts, contrast, placeholders |
| `scripts/render.py` | Same in Python, falling back to Chrome CLI, then WeasyPrint |
| `scripts/audit.js` | The audit both renderers run inside the page |
| `scripts/contact_sheet.py` | One image of every page for the layout check |
| `references/research-playbook.md` | Where to look for each section, and the efficient order |
| `references/brand-extraction.md` | Getting real colors, fonts, and logo description from the live site |
| `references/components.md` | Markup for every component, section order, writing rules |

## Hard rules

- **Disambiguate first.** Many companies share names, and funding databases and job boards often merge them. Confirm each financial and hiring fact belongs to the right entity (same domain, founders, HQ). If a collision exists, open the summary with a **Name check** callout.
- **Every number gets a source, a date, and a confidence tag**: `Verified` (primary: filings, the company's own site or careers page), `Reported` (credible press), `Company claim` (their marketing), `Estimate` (third-party or your inference; say from what), `Unknown` (searched, not found; say where).
- **Write findings to `research-notes.md` as you go.** Search results get cleared from context in long research. Anything not written down is lost.
- **Recency**: use today's date; put the year in news searches; mark anything older than 18 months as dated.
- **People**: public-facing leaders and people relevant to the role, with public professional information only. No personal contact details, home locations, family, or photos of people.
- **Brand assets**: describe the logo in words and link the official press kit. Don't redraw, trace, or embed logos, mascots, or ad imagery. Color swatches, type specimens, and abstract brand-colored patterns are fine.
- **Quotes**: employee review excerpts of 25 words or fewer, 4 or fewer in total, attributed to platform and date. Summarize articles; don't paste them.
- **Label it independent**: the cover says "Independent research brief. Not affiliated with or endorsed by <Company>."
- **Say what's missing** in one line instead of dropping a section silently.
- **Never claim a check you didn't do** ("Only Glassdoor was reviewed for this brief").
- **Shareable briefs** contain nothing private about the person asking, even if you know it.

## Workflow

Create this task list and tick items off as you finish them:

1. Identify the company and check for name collisions
2. Set up the working folder and notes file
3. Read the company's own site and careers pages
4. Research everything else in parallel batches
5. Extract brand colors and type; build tokens and fonts
6. Synthesize: bottom line, scorecard, how to talk to them
7. Write brief.html
8. Render and audit
9. Check facts, links, and layout
10. Deliver

### 1. Identify

- URL given: open the homepage and About page. Name given: search `"<name>" company` and `"<name>" official site`.
- Pin down brand and legal name, domain, HQ, founding year, founders, public or private (ticker), parent.
- Search `"<name>" -<domain>` for same-name companies.
- If two or more real companies plausibly match and nothing in the request tells them apart, ask one question naming the candidates. Otherwise pick the best match and state it at the top of your reply.
- In personal mode, also search past chats and memory (if available) for the company name: an application, recruiter thread, or offer changes what matters.

### 2. Working folder

```bash
mkdir -p briefs/<company-slug> && cd briefs/<company-slug>
cp "${CLAUDE_SKILL_DIR}/assets/brief.css" . && cp "${CLAUDE_SKILL_DIR}/assets/brief-template.html" brief.html
```

Use the current working directory, or a scratch folder if it isn't writable. Create `research-notes.md` with one heading per brief section, and append findings as `- <fact> | <tag> | <source title> | <URL> | <date>`.

### 3. The company's own site first

The live site and careers pages answer a third of the brief and are the most reliable source for mission, values, products, customers, offices, open roles, pay, interview process, social handles, and brand tokens.

- **If you have a browser tool** (Claude in Chrome, an in-app browser, or a cloud browser), use it for the company's domain; approve the whole site once if asked. Read pages as text, not screenshots, except for the logo and hero.
- Visit, in order: homepage (positioning, customer logos, footer handles and links) → About → Careers (count roles by department and location) → 2–4 postings closest to the target role (pay, interview steps, reporting line, tools) → Press/newsroom (timeline, press kit) → Blog/changelog (last 3 months) → Events → Customers.
- Run the brand extraction script on the homepage while you're there (references/brand-extraction.md, section 1).
- **No browser**: fetch the same pages with the fetch tool.
- The careers page beats job-board APIs, which are often stale. Use the ATS API list in the playbook only as a fallback.

Details: references/research-playbook.md, sections 1–2.

### 4. Everything else

Follow references/research-playbook.md sections 3–6. Send independent searches together in one turn (funding, revenue, news, reviews, competitors, campaigns, events, podcasts). Use standard search first and extended search for recent news, hiring and review data, or anything thin. If a fetch tool refuses a URL you typed, search for it and fetch the result link, or open it in the browser. Connectors for job sites (Indeed, ZipRecruiter, LinkedIn), if present, are good for ratings and roles.

40–80 lookups is normal for a mid-size company. When a section stays thin after two good searches, mark it `Unknown` and move on.

### 5. Brand tokens and fonts

From the extraction (references/brand-extraction.md):

```bash
python3 "${CLAUDE_SKILL_DIR}/scripts/fonts.py" "<Display Font>:400,500,600" "<Body Font>:400,600" "<Label Font>:400,500"
python3 "${CLAUDE_SKILL_DIR}/scripts/tokens.py" --brand "#…" --accent "#…" \
  --font-display "<Display Font>" --font-body "<Body Font>" --font-label "<Label Font>" \
  --display-weight 600 --tracking -0.02em --radius 8px \
  --footer "<Company> · Company Brief · <Mon YYYY>" [--cover-stops "#…,#…,#…"] > tokens.html
```

- Paste the `<link>` lines from fonts.py at the FONT LINKS marker and `tokens.html` at the TOKENS marker in brief.html.
- Every line of the tokens.py report should say `ok`. A `WARNING` means choose different colors (for a light signature color, make the dark one `--brand`).
- Proprietary brand font: run the stand-in test in brand-extraction.md section 2 and say in the brief which free font stands in.
- If npm can't install fonts, use an installed family (`fc-list : family`) and say so in the Type section.

### 6. Synthesize

Read the whole notes file, then decide the bottom line, the three things to know, the scorecard, their language, priorities → talking points, the 30-second pitch, the angle, interview prep, questions to ask, do's and don'ts, before you sign (offer only), and outreach. Guidance for each: references/research-playbook.md section 7.

### 7. Write brief.html

Replace every `{{placeholder}}` in the template, add rows and items as needed, and delete only the sections the template marks as conditional. Markup and writing rules: references/components.md. Keep per-brief CSS small and in the template's override block. Aim for 14–20 pages.

### 8. Render and audit

```bash
node "${CLAUDE_SKILL_DIR}/scripts/render.cjs" brief.html "<Company>-Company-Brief-<YYYY-MM>.pdf" \
  || python3 "${CLAUDE_SKILL_DIR}/scripts/render.py" brief.html "<Company>-Company-Brief-<YYYY-MM>.pdf"
```

- The Node renderer exits with code 2 if Playwright is missing, which triggers the Python one. render.py tries Playwright, then a Chrome binary, then WeasyPrint (`pip install weasyprint`).
- Fix every `FAIL` line (font not loaded, text under 4.5:1 contrast, leftover placeholder) and render again.
- If you're on someone's own computer and nothing renders, ask before installing a renderer (`pip install playwright && python -m playwright install chromium`).

### 9. Check

- Layout: `python3 "${CLAUDE_SKILL_DIR}/scripts/contact_sheet.py" <pdf> sheet.png`, then look at the image. The cover is full bleed with no footer; the summary fits on page 2; each part starts a new page; nothing is clipped; no table overflows; no near-empty pages; no heading is stranded at a page bottom. Render a doubtful page larger with `pdftoppm -r 150 -f N -l N -png <pdf> page` and look again.
- Facts: every number in the PDF matches the notes file, carries a tag, and appears in Sources. Dates are absolute. The other same-name company isn't mixed in.
- Copy: read references/components.md writing rules once more against the text.
- Links: open 5 or more Sources links to confirm they resolve.

### 10. Deliver

- Send the PDF as a file. If the person keeps briefs in a folder on their computer and you can reach it, save it there too.
- Reply in a few lines: the bottom line in two sentences, the 2–3 sharpest talking points, anything important you couldn't find, and one useful next step (a tailored cover letter, an outreach note, mock interview questions, or a shareable version).
