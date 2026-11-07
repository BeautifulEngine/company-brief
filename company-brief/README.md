# company-brief

A Claude skill that researches a company from its name or URL and returns a designed PDF brief in the company's own colors and type, plus a one-page cheat sheet to keep open on a call. The brief covers financials, history, leadership, hiring and pay, employee reviews, a brand kit (links to official logos, brand guidelines, fonts and color specs), campaigns, events, social, news, competitors, risks, and how to talk to them.

## Install

**Claude app (web, desktop):**
1. Turn on code execution: Settings > Capabilities > "Code execution and file creation" (called "Create and edit files" in the newer settings). On Team and Enterprise plans, an owner may need to allow skills.
2. Go to Customize > Skills, click "+", then "+ Create skill" > "Upload a skill", and upload `company-brief.zip` as is (don't unzip it).
3. Make sure the skill is toggled on.

**Claude Code:** unzip into `~/.claude/skills/` so the file lands at `~/.claude/skills/company-brief/SKILL.md`, then start `claude`.

## Use

Ask in plain words:

- "Make me a company brief on Figma."
- "Company brief on Figma for a Senior Brand Designer interview."
- "We just signed Oatly as a client. Make a brief with a brand kit."
- "Compare Figma and Canva" or "help me choose between these two offers."
- "Refresh my Figma brief" (attach the old PDF if it's from another chat).
- Add "shareable" for a version with nothing personal in it.

In Claude Code you can also type `/company-brief linear.app`.

A full brief takes a while because it reads many sources. Results are better when Claude has a browser available and when you attach your resume or portfolio link.

The brief links to brand files; it never copies logos or fonts. Those files belong to the brand and its usage terms apply. Briefs are independent research, not affiliated with the company, and not legal or financial advice.
