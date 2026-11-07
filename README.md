# company-brief

A Claude skill for job seekers, freelancers, designers, strategists, and anyone getting ready to talk to or work with a company. Give it a company name or URL and it researches the company in depth, then hands back a designed PDF brief styled in that company's own colors and type.

It's built to answer one question: **do I know enough to walk into this interview, offer conversation, pitch, or first project with a new brand?**

## What's in a brief

| Part | What it covers |
|---|---|
| **The 60-second read** | Bottom line, key numbers, three things to know before you walk in, and a scorecard for stability, momentum, culture, hiring and pay, and brand ambition |
| **The company** | At a glance, what they do and how they make money, mission and goals, full history timeline, funding, revenue, and valuation |
| **People & culture** | Leaders to know, open roles by team and location, pay ranges from real postings, interview process, employee review ratings and themes, values |
| **Brand & marketing** | Logo described in words, brand colors and fonts pulled from the live site, voice, a brand kit (links to official vector logos, the latest brand guidelines, fonts, color specs, and usage rules), who made the brand, ad campaigns, events and activations, social, videos and podcasts, news from the last 12 months |
| **Market & competition** | Market, competitors compared, customers and partners, risks and red flags |
| **Your play** | Their internal language, what they care about and how to talk about it, a 30-second pitch, your angle, likely interview questions, questions to ask, do's and don'ts, outreach, and "before you sign" if you have an offer |

Every number carries a source, a date, and a confidence tag (Verified, Reported, Company claim, Estimate, or Unknown), and the brief says plainly when something couldn't be found. A typical brief runs 14–20 pages.

## Install

### Claude app (web or desktop)

1. Download **[company-brief.zip](company-brief.zip)** from this repo (open the file, then use the download button). Don't unzip it.
2. In Claude, turn on code execution: **Settings > Capabilities > Code execution and file creation** (called "Create and edit files" in newer settings). On Team and Enterprise plans an owner may need to allow skills.
3. Go to **Customize > Skills**, click **+**, then **+ Create skill > Upload a skill**, and upload the zip.
4. Make sure the skill is switched on.

> GitHub's green "Code > Download ZIP" button won't work for this; it wraps everything in an extra folder. Use `company-brief.zip`.

### Claude Code

```bash
git clone https://github.com/BeautifulEngine/company-brief.git
mkdir -p ~/.claude/skills && cp -r company-brief/company-brief ~/.claude/skills/
```

Then start `claude` in any folder.

## Use

**Claude Code:**

```
/company-brief linear.app
/company-brief Figma for Senior Brand Designer
/company-brief notion.so shareable
/company-brief patagonia.com brand kit
```

**Claude app:** ask in plain words, and Claude picks the skill up on its own:

> Make me a company brief on Figma. I'm interviewing for a Senior Brand Designer role.

> We just signed Oatly as a client. Make me a company brief with a brand kit so the team can get started.

Options:
- **for \<role\>** focuses hiring, people, and interview prep on that role and its team.
- **brand kit** (or say you're starting work with the brand) is for designers, strategists, and agencies. The brief collects every asset it can find (logo variants, guidelines, fonts, color specs, press images, templates, boilerplate, usage terms), lists what to ask the client for, swaps interview prep for kickoff prep, and adds a `Brand-Kit.md` of links you can paste into a project doc.
- **shareable** (or "for a friend") leaves out everything personal, so you can send the PDF to someone else.

Tips for a better brief:
- Attach your resume or portfolio link. The "Your angle" section and the pitch get much more specific.
- If you have an offer, say so. The brief adds a "Before you sign" section.
- If Claude has a browser available (Claude in Chrome or the desktop app's browser), it reads the company's live site and careers page directly, which gives the most accurate roles, pay, and brand colors.

A full brief takes a while, since it runs 40–80 searches and reads the company's own pages.

## How it works

```
company-brief/
├── SKILL.md                     the workflow Claude follows
├── assets/
│   ├── brief.css                the PDF's base stylesheet
│   └── brief-template.html      skeleton of every section
├── scripts/
│   ├── fonts.py                 installs free fonts (Fontsource) for the brand's type
│   ├── tokens.py                turns brand colors into a readable color system
│   ├── render.cjs / render.py   renders the PDF and checks fonts, contrast, and blanks
│   ├── audit.js                 the check both renderers run
│   └── contact_sheet.py         a one-image preview of every page
└── references/
    ├── research-playbook.md     where to look for each section
    ├── brand-extraction.md      pulling colors and type from the live site, and building the brand kit
    └── components.md            layout pieces and writing rules
```

Rendering tries Node Playwright, then Python Playwright, then a Chrome install, then WeasyPrint, so it works in most setups. Brand colors are darkened where needed so text stays readable.

## Ground rules the skill follows

- Public information only. People are covered by title and public work, with no photos, personal contact details, or private life.
- Logos, fonts, and brand images are linked, never copied. The brief prefers the brand's own press kit, brand portal, or guidelines, labels third-party logo sites as third-party, opens every link to confirm the file format, and checks that the logo matches the one on the live site. The files belong to the brand, and its usage terms apply.
- Review quotes are short and attributed; articles are summarized, not pasted.
- Every brief is labeled as independent research, not affiliated with the company, and not legal or financial advice.

Facts come from public sources and can be out of date or wrong. Check anything you plan to rely on, especially pay, funding, and headcount.

## Updating the zip

If you change files in `company-brief/`, rebuild the upload file from the repo root:

```bash
rm -f company-brief.zip && zip -r company-brief.zip company-brief -x '*__pycache__*' '*.DS_Store'
```

## License

MIT. See [LICENSE](LICENSE).
