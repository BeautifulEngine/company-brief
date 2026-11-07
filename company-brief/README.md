# company-brief

A Claude skill that researches a company from its name or URL and returns a designed PDF brief in the company's own colors and type: financials, history, leadership, hiring and pay, employee reviews, a brand kit (links to official vector logos, current brand guidelines, fonts and color specs), campaigns, events, social, videos, podcasts, news, competitors, risks, and how to talk to them in an interview or pitch.

## Install

**Claude app (web, desktop):**
1. Turn on code execution: Settings > Capabilities > "Code execution and file creation" (called "Create and edit files" in the newer settings). On Team and Enterprise plans, an owner may need to allow skills.
2. Go to Customize > Skills, click "+", then "+ Create skill" > "Upload a skill", and upload `company-brief.zip` as is (don't unzip it).
3. Make sure the skill is toggled on.

**Claude Code:** unzip into `~/.claude/skills/` so the file lands at `~/.claude/skills/company-brief/SKILL.md`, then start `claude`.

## Use

- Claude Code: `/company-brief linear.app` or `/company-brief Figma for Senior Brand Designer`
- Claude app: "Make a company brief on Figma for a Senior Brand Designer interview." Claude picks the skill up from the request; you can also say "use the company-brief skill".
- Add `brand kit` (or say you're starting work with the brand) for brand-work mode: the full asset list, a "what to ask the client for" list, kickoff prep, and a separate `Brand-Kit.md` of links to paste into a project doc. Example: `/company-brief patagonia.com brand kit`.
- Add `shareable` (or "for a friend") for a version with nothing personal in it.

A full brief takes a while: it runs 40–80 searches and reads the company's site and careers pages. Results are better when Claude has a browser available and when you attach your resume or portfolio link for the "your angle" section.

The brief links to brand files; it never copies logos or fonts. Those files belong to the brand and its usage terms apply.

The brief is independent research, not affiliated with the company, and not legal or financial advice.
