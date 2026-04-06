---
name: onboarding-plan-generator
description: >
  Generate a structured 1-week onboarding plan for new joiners across SPX, SLS, and WMS
  product lines (Shopee Express ecosystem). Use this skill whenever a manager asks to create,
  build, generate, or draft an onboarding plan or schedule for a new team member. Triggers
  include: "create onboarding plan", "new joiner plan", "onboarding for [name]", "first week
  plan", "set up onboarding", or any request to prepare a structured welcome plan.
  Currently available sub-domains:
    SPX — In-Station, Cache, Exception, Workforce (all available)
    SLS — Template/General (available); Core Service, Buyer Experience, Finance (coming soon)
    WMS — Inbound, Inventory, Outbound, All (all available)
  Always use this skill — never attempt to generate onboarding plans without it.
---

# Onboarding Plan Generator — SPX / SLS / WMS

## Overview

This skill generates a personalised 5-day onboarding plan for new joiners across three
product lines: **SPX**, **SLS**, and **WMS**. The output is an **interactive React artifact**
the new joiner can navigate directly in Claude — no download required. A .docx export option
is also available if requested.

Each plan includes:
- Day 1 universal foundation block (same for ALL sub-domains — admin setup, company basics,
  reporting expectations)
- Days 2 to 5 of sub-domain-specific learning with reading materials and tasks
- A 10-question end-of-day quiz per day (mix of MCQ and open-ended)
- Support for spanning multiple sub-domains within the same product line

---

## Product Line and Sub-domain Map

```
SPX (Shopee Express)
  └── In-Station       → references/spx/in-station.md
  └── Cache            → references/spx/cache.md
  └── Exception        → references/spx/exception.md
  └── Workforce        → references/spx/workforce.md

SLS (Supply and Logistics Services)
  └── Template/General → references/sls/sls.md
  └── Core Service     → references/sls/core-service.md   [🔒 Coming Soon]
  └── Buyer Experience → references/sls/buyer-exp.md      [🔒 Coming Soon]
  └── Finance          → references/sls/finance.md        [🔒 Coming Soon]

WMS (Warehouse Management System)
  └── Inbound          → references/wms/inbound.md
  └── Inventory        → references/wms/inventory.md
  └── Outbound         → references/wms/outbound.md
  └── All              → references/wms/all.md
```

Sub-domains marked [🔒 Coming Soon] do not yet have a reference file. If selected, inform
the manager and offer to generate a skeleton plan or wait for materials to be provided.

---

## Step 1: Prompt for Product Line and Sub-domain

When triggered, first ask for the product line, then the sub-domain. Present these as clear
options — do not ask for free text. Use this exact sequence:

```
PROMPT 1 — Product Line:
  "Which product line is the new joiner joining?"
  Options: SPX / SLS / WMS

PROMPT 2 — Sub-domain(s):
  Show the sub-domains available under the chosen product line.
  Mark unavailable ones clearly as 🔒 Coming Soon.
  Allow selection of one or more sub-domains.

  SPX options:   In-Station / Cache / Exception / Workforce
  SLS options:   Template/General / Core Service 🔒 / Buyer Experience 🔒 / Finance 🔒
  WMS options:   Inbound / Inventory / Outbound / All
```

If the manager selects a 🔒 Coming Soon sub-domain:
- Inform them that reference materials for this sub-domain are not yet available
- Offer two options: (1) proceed with a skeleton plan using placeholders, or
  (2) pause and ask the manager to supply materials first

If multiple sub-domains are selected, merge their Days 2 to 5 content:
- Distribute sub-domains across Days 2 to 5 (one sub-domain per day where possible)
- If more sub-domains than days, combine the most closely related ones into a single day
- Always keep Day 1 as the universal foundation block

---

## Step 2: Collect Joiner Details

After product line and sub-domain are confirmed, collect:

```
Required:
  - New joiner name
  - Start date

Optional (prompt the manager):
  - Any specific tasks to include this week?
    (e.g. shadow a route check, attend ops standup, sit in on a claims review)
  - Any additional Confluence or Google Drive links to include?
  - Buddy / supervisor name (to reference in tasks)
```

If the manager doesn't supply tasks, use the sub-domain default tasks from the reference file.
If they don't supply extra links, use only the hardcoded defaults from the reference file.

---

## Step 2a: Refresh Drive Links (if Google Workspace MCP is available)

If Google Workspace MCP is available, use gdrive_fetch to retrieve
the latest file list from the source Drive folders before generating
the plan, so reading material links are always up to date.

Source folders to refresh:
- Warehouse (general): `16SXtO0hHd3RZfLUqt3SaM1cWCk6mpLpj`
- Warehouse Workforce: `1rx3ty1HVtYOIJ0k03YtgHigdkb2cxsWO`
- SPX In-Station: `1c-xBEPI8kKgODeanvXcSfIxLhOHCGXGQ`
- SPX Cache: `10kFrHieEeIggQx0IPFoZNzplrSuyTUJJ`

Updated links should override the defaults in the reference files. If MCP
is not available, skip this step and proceed with the hardcoded defaults.

---

## Step 3: Load References

Always load BOTH of the following:

1. `references/universal.md` — ALWAYS loaded for every plan, regardless of product line or
   sub-domain. Provides Day 1 content (admin setup, company fundamentals, reporting
   expectations, universal quiz questions, stakeholder map).

2. The sub-domain-specific reference file(s) as mapped in the Product Line and Sub-domain Map
   above. Load one file per selected sub-domain.

Reference files provide Days 2 to 5 content:
- Day-by-day learning topics and themes
- Hardcoded default reading materials (Confluence + Drive links)
- Default practical tasks
- Quiz question bank (10 per day)

---

## Step 4: Build the 5-Day Plan

**Day 1 is ALWAYS the universal foundation block** (from references/universal.md).
Days 2 to 5 come from the sub-domain reference file(s).

**Single sub-domain:**
```
Day 1   — Universal foundation (admin, company basics, reporting)
Day 2   — Sub-domain fundamentals
Day 3   — Sub-domain processes and tools
Day 4   — Sub-domain deep dive (advanced topics)
Day 5   — Integration, stakeholder alignment, and reflection
```

**Multiple sub-domains (example: 2 sub-domains):**
```
Day 1   — Universal foundation
Day 2   — Sub-domain A: fundamentals
Day 3   — Sub-domain A: deep dive
Day 4   — Sub-domain B: fundamentals
Day 5   — Sub-domain B: deep dive + cross-domain reflection
```

**Multiple sub-domains (example: 3 or more sub-domains):**
```
Day 1   — Universal foundation
Days 2 to 5 — Distribute sub-domains, combining closely related ones where needed
            — Always end Day 5 with a reflection and integration section
```

**Weaving in manager-supplied tasks:**
- Slot tasks into the most relevant day based on topic alignment
- If buddy/supervisor name is provided, reference them in task instructions
- If extra links are provided, append them to the most relevant day's reading list

**Quiz rules:**
- 7 MCQ (4 options each, one correct answer — show answer key in artifact)
- 3 open-ended reflection questions
- Questions should directly test that day's reading and tasks
- Vary difficulty: 4 recall, 3 application, 3 reflection

---

## Step 5: Generate the Interactive Artifact

Output a **React artifact** (JSX) — not a Word doc. The artifact is a self-contained
interactive onboarding microsite the new joiner navigates directly in Claude.

### Artifact Structure and UX

```
Header bar
  SPX red (#E31837) background
  "Welcome, [Name]!" title
  Product Line badge + Sub-domain badge(s) + Start date

Day navigation tabs (Day 1 to Day 5)
  Clicking a tab reveals that day's full content

Each day panel contains:
  Day title and theme (with date)
  "What You'll Learn" intro paragraph
  Admin Checklist (Day 1 only) — interactive checkboxes, ticked state persists per session
  Reading Materials — card list, each with topic name, estimated time badge, and clickable link
  Tasks — numbered list with interactive checkboxes so new joiner can tick off completed tasks
  End-of-Day Quiz section:
    MCQ (Q1 to Q7): radio button options, submit reveals correct/incorrect inline
    Open-ended (Q8 to Q10): textarea for free-text answers
    "Check Answers" button reveals MCQ answer key inline with colour coding
      (green = correct, red = incorrect, shows correct answer)

Progress bar at top
  Shows % of tasks ticked across all 5 days

Footer
  "Prepared by [Product Line] Onboarding" | Joiner name | Sub-domain(s) | Start date
```

### Visual Design

- Primary: #E31837 (SPX red)
- Background: #F8F8F8
- Cards: white with subtle shadow
- Correct answer highlight: #22C55E (green)
- Wrong answer highlight: #EF4444 (red)
- Font: system-ui / sans-serif
- Fully responsive, mobile-friendly
- Use Tailwind utility classes only (no custom CSS)
- Use lucide-react for icons (CheckCircle, BookOpen, ClipboardList, ChevronRight)

### State Management

Use React useState for:
- Active day tab
- Checkbox tick states (admin setup + tasks)
- Quiz answer selections
- Quiz submitted state per day

---

## Step 6: Output

Render the artifact directly in the conversation.

After rendering, confirm with the manager:
> "Here's [Name]'s interactive Week 1 onboarding plan for [Product Line] — [Sub-domain(s)]!
> They can navigate it day by day, tick off tasks, and self-check their quiz answers.
> Let me know if you'd like to adjust any content or generate a .docx version as well."

If the manager explicitly asks for a Word doc, ALSO follow the docx skill at
`/mnt/skills/public/docx/SKILL.md` to generate a downloadable version.

---

## Reference File Index

| Product Line | Sub-domain      | Reference File                      | Status         |
|---|---|---|---|
| SPX          | In-Station      | references/spx/in-station.md        | Available      |
| SPX          | Cache           | references/spx/cache.md             | Available      |
| SPX          | Exception       | references/spx/exception.md         | Available      |
| SPX          | Workforce       | references/spx/workforce.md         | Available      |
| SLS          | Template        | references/sls/sls.md               | Available      |
| SLS          | Core Service    | references/sls/core-service.md      | Coming Soon 🔒 |
| SLS          | Buyer Experience| references/sls/buyer-exp.md         | Coming Soon 🔒 |
| SLS          | Finance         | references/sls/finance.md           | Coming Soon 🔒 |
| WMS          | Inbound         | references/wms/inbound.md           | Available      |
| WMS          | Inventory       | references/wms/inventory.md         | Available      |
| WMS          | Outbound        | references/wms/outbound.md          | Available      |
| WMS          | All             | references/wms/all.md               | Available      |
| Universal    | (All)           | references/universal.md             | Available      |

---

## Notes

- Never fabricate Confluence or Drive links. Use only hardcoded defaults from reference files
  or links explicitly provided by the manager.
- If a manager selects a Coming Soon sub-domain, always inform them clearly and offer
  alternatives before proceeding.
- Quizzes are for learning check-ins only — keep the tone encouraging, not evaluative.
- Keep task descriptions actionable: start each with a verb (e.g. "Shadow", "Review",
  "Attend", "Complete", "Discuss").
- When spanning multiple sub-domains, ensure Day 5 always includes a cross-domain reflection
  so the joiner can connect the dots across what they've learned.
- The footer product line label should reflect the actual product line selected, not default
  to "SPX" for SLS or WMS joiners.
