# NFL Analytics Website — Project Instructions

## Project Overview
I'm building a football analytics website with customizable charts and visuals based on NFL data. It's a personal portfolio project, hosted for free on GitHub Pages. I'm a beginner with Python and brand new to web development (HTML/CSS/JS).

## My Local Setup
- **Primary machine:** Windows PC (Python 3.12)
- **Secondary machine:** MacBook Air (Python 3.12.9)
- **VS Code** for website code
- **Jupyter Notebook** for data exploration
- Always launch Jupyter with the correct Python: `python3.12 -m jupyter notebook`
- Always use the **Python 3.12 kernel** in Jupyter (Kernel → Change Kernel → Python 3.12)

### Required Packages (install with Python 3.12)
```
python3.12 -m pip install nflreadpy polars pandas pyarrow jupyter ipykernel
python3.12 -m ipykernel install --user --name python312 --display-name "Python 3.12"
```

## Data Source
- **Library:** `nflreadpy` (Python port of nflreadr — official replacement for the now-archived `nfl_data_py`)
- **Note:** `nfl_data_py` was archived September 2025 and no longer works for 2025 data
- **Scope:** NFL data from 2020–2025 regular seasons
- **Important:** nflreadpy returns **Polars** dataframes — always call `.to_pandas()` to convert

### Key Functions
```python
import nflreadpy as nfl

nfl.load_player_stats([2020..2025])   # weekly player stats (passing, rushing, receiving) — all positions
nfl.load_schedules([2020..2025])      # game results, home/away, scores
nfl.load_rosters([2020..2025])        # full rosters
```

### Column Name Changes vs nfl_data_py
| Old (nfl_data_py)     | New (nflreadpy)         |
|-----------------------|-------------------------|
| `recent_team`         | `team`                  |
| `interceptions`       | `passing_interceptions` |
| `import_weekly_data`  | `load_player_stats`     |
| `import_schedules`    | `load_schedules`        |

### Home/Away Lookup Pattern
```python
sched = nfl.load_schedules([2020..2025]).to_pandas()
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

## Current Progress

### Files Built
- `player-props.html` — Player Prop Analyzer (QB, RB, WR, TE tabs)
- `nfl-dashboard.html` — Standings + Home/Away tabs (needs rebuild)

### player-props.html Features
- **4 position tabs:** QB, RB, WR, TE — each with position-specific stat buttons and game log columns
- **Season selector:** ALL | 2020 | 2021 | 2022 | 2023 | 2024 | 2025
- **Player search:** search by name, click result to load player
- **Player card:** season averages for all key stats
- **O/U Line input:** type any number, page reacts instantly
  - Hit rate % (green ≥60%, yellow ≥45%, red <45%)
  - Bar chart bars turn green/red for over/under
  - Game log rows show OVER/UNDER badges
  - Last 5 games dots color-code
- **Chart:** bar chart per game with season avg (blue dashed) and O/U line (yellow)
- **Game log:** full week-by-week table, most recent first, with YR column for multi-season view
- **Home/Away labels:** `@KC` for away, `vsKC` for home — on chart, game log, and last 5 dots
- **Leaders table:** top 20 players by selected stat/season on default view, click to load player
- **Data embedded directly in HTML** (no separate JSON files needed)

### Data JSON Files
All stored in the project folder alongside the HTML files:

| File | Position | Records | Players | Seasons |
|------|----------|---------|---------|---------|
| `qb_data.json` | QB | ~3,700 | ~143 | 2020–2025 |
| `rb_data.json` | RB | ~8,200 | ~317 | 2020–2025 |
| `wr_data.json` | WR | ~14,500 | ~497 | 2020–2025 |
| `te_data.json` | TE | ~7,300 | ~264 | 2020–2025 |

### JSON Schema (all positions)
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
  "fantasy_points_ppr": 24.5,
  // QB only:
  "completions": 22, "attempts": 31, "passing_yards": 290,
  "passing_tds": 2, "interceptions": 0, "passing_epa": 8.4,
  "passing_air_yards": 210, "pacr": 1.1, "carries": 5,
  "rushing_yards": 40, "rushing_tds": 0,
  // RB only:
  "carries": 18, "rushing_yards": 94, "rushing_tds": 1,
  "targets": 3, "receptions": 2, "receiving_yards": 15, "receiving_tds": 0,
  // WR only:
  "targets": 8, "receptions": 5, "receiving_yards": 72, "receiving_tds": 1,
  "carries": 0, "rushing_yards": 0,
  // TE only:
  "targets": 6, "receptions": 4, "receiving_yards": 48, "receiving_tds": 0
}
```

### Design Style
- Dark theme (bg: #0a0e17, cards: #111827)
- Fonts: Oswald (headers/labels) + Source Sans 3 (body) + Rajdhani (big numbers)
- Colors: Blue (#3b82f6) wins/accents, Red (#ef4444) losses/under, Green (#22c55e) positive/over, Yellow (#f59e0b) O/U line
- Accent: #38bdf8 (sky blue) for nav, team tags, season labels
- Clean, minimal, sports analytics aesthetic
- Sticky nav tabs, smooth animations

## What's Next (To Be Built)
- Rebuild `nfl-dashboard.html` (Standings + Home/Away) using nflreadpy data
- Player comparison mode (view two players side by side)
- L3 / L5 hit rate split vs full season
- Opponent-based filtering
- RB/WR/TE stat leaders on default view
- Deploy to GitHub Pages

## Important Notes
- Regular season only — filter with `df[df['season_type'] == 'REG']` (nflreadpy uses `season_type` not `game_type`)
- No betting/ATS data
- Keep code in single HTML files for now (data embedded directly)
- nflreadpy uses Polars — always `.to_pandas()` before working with data
- Explain things clearly — beginner-level web dev
- Test Python scripts in Jupyter before integrating into HTML
