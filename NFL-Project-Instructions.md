# NFL Analytics Website — Project Instructions

## Project Overview
I'm building a football analytics website with customizable charts and visuals based on NFL data. It's a personal portfolio project, hosted for free on GitHub Pages. I'm a beginner with Python and brand new to web development (HTML/CSS/JS).

## GitHub Repository
- **Repo:** https://github.com/jayhirpara6/nfl-analytics
- **Live site:** https://jayhirpara6.github.io/nfl-analytics/
- **Pages:** `player-props.html` and `nfl-dashboard.html`

## My Local Setup
- **Primary machine:** Windows PC (Python 3.12)
- **Secondary machine:** MacBook Air (Python 3.12.9)
- **Project folder:** `/Users/jay/Library/CloudStorage/OneDrive-Personal/Documents/Football Website`
- **VS Code** for website code
- **Jupyter Notebook** for data exploration
- Always launch Jupyter with the correct Python: `python3.12 -m jupyter notebook`
- Always use the **Python 3.12 kernel** in Jupyter (Kernel → Change Kernel → Python 3.12)

### Required Packages (install with Python 3.12)
```
python3.12 -m pip install nflreadpy polars pandas pyarrow jupyter ipykernel
python3.12 -m ipykernel install --user --name python312 --display-name "Python 3.12"
```

### Pushing to GitHub
```bash
cd "/Users/jay/Library/CloudStorage/OneDrive-Personal/Documents/Football Website"
git add .
git commit -m "your message here"
git push
```
Use GitHub username `jayhirpara6` and a Personal Access Token (starts with `ghp_`) as the password.

## Data Source
- **Library:** `nflreadpy` (Python port of nflreadr — official replacement for the now-archived `nfl_data_py`)
- **Note:** `nfl_data_py` was archived September 2025 and no longer works for 2025 data
- **Scope:** NFL data from 2020–2025 regular seasons
- **Active players only:** All JSON files are filtered to players who appeared in the 2025 season (retired players like Brady, Gronk, Matt Ryan excluded)
- **Important:** nflreadpy returns **Polars** dataframes — always call `.to_pandas()` to convert

### Key Functions
```python
import nflreadpy as nfl

nfl.load_player_stats([2020,2021,2022,2023,2024,2025])  # weekly player stats
nfl.load_schedules([2020,2021,2022,2023,2024,2025])     # game results, home/away, scores
nfl.load_rosters([2020,2021,2022,2023,2024,2025])       # full rosters
```

### Column Name Changes vs nfl_data_py
| Old (nfl_data_py)     | New (nflreadpy)         |
|-----------------------|-------------------------|
| `recent_team`         | `team`                  |
| `interceptions`       | `passing_interceptions` |
| `import_weekly_data`  | `load_player_stats`     |
| `import_schedules`    | `load_schedules`        |

### Regular Season Filter
```python
# nflreadpy uses season_type for player stats
df = df[df['season_type'] == 'REG']

# For schedules use game_type
sched = sched[sched['game_type'] == 'REG']
```

### Home/Away Lookup Pattern
```python
sched = nfl.load_schedules([2020,2021,2022,2023,2024,2025]).to_pandas()
sched = sched[sched['game_type'] == 'REG']
location_lookup = {}
for _, row in sched.iterrows():
    location_lookup[(int(row['season']), int(row['week']), row['home_team'])] = 'home'
    location_lookup[(int(row['season']), int(row['week']), row['away_team'])] = 'away'
```

## Architecture
1. **Data layer (Python):** Pull data with `nflreadpy`, export as JSON files
2. **Frontend (HTML/CSS/JS):** Single-page or multi-page site, no frameworks
3. **Charts:** Chart.js with chartjs-plugin-datalabels
4. **Hosting:** GitHub Pages (free)

---

## Files Built

### `player-props.html`
Player Prop Analyzer — data embedded directly in the HTML file.

**Features:**
- 4 position tabs: QB, RB, WR, TE — each with position-specific stat buttons and game log columns
- Season selector: ALL | 2020 | 2021 | 2022 | 2023 | 2024 | 2025
- Player search: search by name, click result to load player
- Player card: season averages for all key stats
- O/U Line input: type any number, page reacts instantly
  - Hit rate % (green >=60%, yellow >=45%, red <45%)
  - Bar chart bars turn green/red for over/under
  - Game log rows show OVER/UNDER badges
  - Last 5 games dots color-code
- Chart: bar chart per game with season avg (blue dashed) and O/U line (yellow)
- Game log: full week-by-week table, most recent first, with YR column for multi-season view
- Home/Away labels: @KC for away, vsKC for home — on chart, game log, and last 5 dots
- Leaders table: top 20 players by selected stat/season on default view, click to load player

---

### `nfl-dashboard.html`
Team Dashboard — schedule data embedded directly in the HTML file.

**Tab 1 — Standings:**
- Season selector: 2020 | 2021 | 2022 | 2023 | 2024 | 2025 (defaults to 2025)
- Filter by All Teams / AFC / NFC
- Sortable columns: W, L, T, PCT, PF, PA, DIFF (click header to sort, click again to reverse)
- When sorted by PCT with All Teams selected, groups by division with division headers
- PCT shown with a visual bar, DIFF color-coded green/red

**Tab 2 — Home/Away:**
- Season selector: 2020-2025
- Show Teams filter: All / AFC / NFC / any division
- vs Opponent filter: All / AFC / NFC / any division
- Venue filter: All Games / Home / Away
- Horizontal bar chart sorted by win %, x-axis fixed at 17

---

## Data JSON Files
All stored in the project folder alongside the HTML files:

| File | Content | Records | Seasons |
|------|---------|---------|---------|
| `qb_data.json` | QB weekly stats | ~2,900 | 2020-2025 |
| `rb_data.json` | RB weekly stats | ~5,200 | 2020-2025 |
| `wr_data.json` | WR weekly stats | ~9,800 | 2020-2025 |
| `te_data.json` | TE weekly stats | ~5,400 | 2020-2025 |
| `schedule_data.json` | All game results | ~1,615 | 2020-2025 |

### Player Stats JSON Schema
```json
{
  "season": 2024,
  "week": 1,
  "player_id": "00-0036945",
  "player_name": "J.Daniels",
  "player_display_name": "Jayden Daniels",
  "recent_team": "WAS",
  "opponent_team": "TB",
  "location": "home",
  "fantasy_points": 24.5,
  "fantasy_points_ppr": 24.5
}
```
QB adds: completions, attempts, passing_yards, passing_tds, interceptions, passing_epa, passing_air_yards, pacr, carries, rushing_yards, rushing_tds
RB adds: carries, rushing_yards, rushing_tds, targets, receptions, receiving_yards, receiving_tds
WR adds: targets, receptions, receiving_yards, receiving_tds, carries, rushing_yards
TE adds: targets, receptions, receiving_yards, receiving_tds

### Schedule JSON Schema
```json
{
  "season": 2024,
  "week": 1,
  "home_team": "KC",
  "away_team": "BAL",
  "home_score": 27,
  "away_score": 20
}
```

---

## Design Style
- Dark theme (bg: #0a0e17, cards: #111827)
- Fonts: Oswald (headers/labels) + Source Sans 3 (body) + Rajdhani (big numbers)
- Colors: Blue (#3b82f6) wins/accents, Red (#ef4444) losses/under, Green (#22c55e) positive/over, Yellow (#f59e0b) O/U line
- Accent: #38bdf8 (sky blue) for nav, team tags, season labels
- Clean, minimal, sports analytics aesthetic
- Sticky nav tabs, smooth animations

---

## What's Next (To Be Built)
- Player comparison mode (view two players side by side)
- L3 / L5 hit rate split vs full season
- Opponent-based filtering on props page
- More chart types and visualizations
- Team stats / analytics page

## Important Notes
- Regular season only — filter with df[df['season_type'] == 'REG'] for player stats, df[df['game_type'] == 'REG'] for schedules
- No betting/ATS data
- Keep code in single HTML files (data embedded directly) for simple GitHub Pages hosting
- nflreadpy uses Polars — always .to_pandas() before working with data
- Explain things clearly — beginner-level web dev
- Test Python scripts in Jupyter before integrating into HTML
