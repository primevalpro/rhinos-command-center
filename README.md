# Rhinos Dynasty Command Center

An interactive dynasty fantasy football dashboard for a 12-team FFPC Superflex TE-premium league. Built as a single standalone HTML file — no backend, no framework, no cost-per-use APIs.

**Live:** [primevalpro.github.io/rhinos-command-center](https://primevalpro.github.io/rhinos-command-center/)

---

## What It Does

The Command Center is a personal scouting and management tool for dynasty league decisions. It combines live FantasyCalc dynasty values with Claude AI-generated grades, upside ratings, and championship window analysis across all 12 teams.

Key capabilities:

- **League Overview** — full roster grid with a layered lens system for grades, fragility, ages, trends, upside, FC values, and custom team grades. Lens toggles include Excel-style filter dropdowns for slicing the view by grade, fragility, or your own ratings.
- **Power Rankings** — dynasty outlook (draggable) and season rankings (Claude-assigned early, auto-switches to PPG formula at week 6)
- **Team Compare** — side-by-side team production (PPG/PROJ) or roster construction (FC value by position)
- **Trade Targets** — ranked trade partner analysis for the Rhinos
- **Win the Flex** — league-wide flex pool rankings, flex battle comparison, and upside concentration breakdown
- **Age & Draft** — dynamic age profile with starter/depth toggle, championship windows, and rookie draft strategy
- **Dynasty Clock** — visual 2026–2031 championship window timeline, bar width dynamically sized from core player ages
- **Assets view** — pick capital scoring, depth grades, trade capacity, and team needs for trade evaluation

---

## Data Sources

| Source | What it provides | How it enters |
|---|---|---|
| **FFPC CSV export** | Rosters, stats, picks | Import button → upload CSV |
| **FantasyCalc API** | Dynasty player values | Auto-fetched on load, manual Refresh available |
| **Claude AI** | Grades, windows, upside, philosophy, needs | Copy Re-Assess prompt → fresh chat → Import Results |

---

## Architecture

Single `index.html` file. No build step, no dependencies beyond Google Fonts CDN.

**Persistence:** Firebase Firestore (anonymous auth) syncs all Claude-generated data and roster state across devices automatically. localStorage is used as a fallback when Firebase is unreachable.

| localStorage key | Contains |
|---|---|
| `rhinos_assess_data` | Claude grades, windows, philosophy, needs, surplus, rankings |
| `rhinos_roster_data` | Roster data and picks from CSV import |
| `rhinos_custom_grades` | User-set team grades (My Grades lens) |
| `fcHistory` | Daily FC value snapshots for trends tracking (30-day rolling) |

Data survives `git push` deployments — no need to bake state into the file before pushing.

---

## League Settings

- **Platform:** FFPC (myffpc.com)
- **Format:** $100 Superflex RotoViz TriFlex Dynasty · 12 teams
- **Lineup:** 1 QB · 1 Superflex · 2 RB · 3 WR · 2 Flex (WR/RB/TE) · 1 TE
- **Scoring:** FFPC TE Premium

---

## Workflow

### After trades or waiver moves
1. **Import → Rosters Only** — upload new FFPC CSV. Picks are auto-detected and imported.
2. **Per-team re-assess** for affected teams — expand the team row, click Copy Re-Assess, run in a fresh Claude chat (no project, web search on), paste JSON into Import Results.
3. Optionally run a **full league re-assess** to refresh power/season rankings.

> Run max 3 per-team re-assess prompts per chat — beyond that, context crowding causes player-team confusion errors.

### Weekly during season
1. **Import → Full Import** — upload FFPC CSV, enter current week number.
2. Season Rankings auto-switch to PPG formula at week 6.

### Grading scale
`S` = Elite · `A` = Strong · `B` = Average · `C` = Weak

Floor grades are auto-computed from FC values using positional quintiles — never include them in re-assess JSON. Ceiling grades, fragility, upside, and window labels all come from Claude.

### Championship windows
| Label | Meaning |
|---|---|
| Win Now | Aging core, 1–2 year runway |
| Contending | Competitive now and sustainably built |
| Rising | 1–2 years away, trajectory up |
| Retooling | Strong core, needs to convert aging assets for youth |
| Rebuilding | Future-focused, starting over |

---

## Local Setup & Deploy

```bash
# Clone
git clone https://github.com/primevalpro/rhinos-command-center
cd rhinos-command-center

# No install step — open index.html directly in a browser

# Deploy
git add index.html
git commit -m "description"
git push
```

Deployed via GitHub Pages from the `main` branch.

### Claude Code workflow
1. Scope and confirm changes in a claude.ai chat
2. `cd ~/Projects/rhinos-command-center && claude`
3. Switch model if needed: `/model` → Sonnet 4.6
4. Paste the confirmed prompt, approve edits, commit and push

---

## Pending

- MHJ name fix — suffix stripping in `getFCValue()` so `Marvin Harrison` matches `Marvin Harrison Jr.`
- Trade Targets tab — side-by-side roster view highlighting assets matching Rhinos' needs array
- Long-term value trajectory — multi-week FC trend view for trade targets and free agents
- Free agent browser — FA rows currently skipped on import; groundwork in place
- Trade value index
- Waiver wire ranker

---

*Built for brandont1223 · Rhinos #630 · Updated June 2026*
