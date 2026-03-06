# Franchise Analytics — Project Instructions

## Project Overview
I'm building a football analytics website called **Franchise Analytics** with customizable charts and visuals based on NFL data. It's a personal portfolio project, hosted for free on GitHub Pages. I'm a beginner with Python and brand new to web development (HTML/CSS/JS).

## GitHub Repository
- **Repo:** https://github.com/jayhirpara6/nfl-analytics
- **Live site:** https://jayhirpara6.github.io/nfl-analytics/
- **Pages:** `index.html`, `player-rankings.html`, `team-rankings.html`

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

### `index.html`
Franchise Analytics Home Page — landing page and navigation hub.

**Features:**
- Animated hero section with site title and tagline
- Stats ticker: 32 teams, 6 seasons, 1,615+ games, 23K+ player logs
- Two feature cards linking to each tool (Player Rankings, Team Rankings)
- Nav bar with logo linking to home, and links to Player Rankings and Team Rankings
- Dark theme with grid background, fade-up animations, card hover effects
- Player Rankings card: green accent (#22c55e)
- Team Rankings card: yellow accent (#f59e0b)

---

### `player-rankings.html`
Player Rankings — data embedded directly in the HTML file.
*(Previously named `player-props.html` — renamed and `player-props.html` deleted)*

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

### `team-rankings.html`
Team Rankings — team stats data embedded directly in the HTML file.

**Features:**
- 6 category tabs: Scoring, Rush Offense, Pass Offense, Rush Defense, Pass Defense, Turnovers
- Season selector: 2020–2025
- Conference filter: All / AFC / NFC
- Sortable columns — click any header to re-sort
- Visual bar on each primary stat
- Gold/silver/bronze highlights for top 3 teams
- AFC badge shown in red, NFC badge shown in blue
- Dual-panel layout for Scoring (PF vs PA) and Turnovers (INTs forced vs thrown)

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
| `team_stats_data.json` | Team offense/defense stats | 192 (32 teams × 6 seasons) | 2020-2025 |

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

### Team Stats JSON Schema
```json
{
  "season": 2024,
  "team": "KC",
  "points_for": 405,
  "points_against": 291,
  "games_played": 17,
  "rush_yards": 2100,
  "rush_tds": 18,
  "rush_carries": 450,
  "pass_yards": 4200,
  "pass_tds": 26,
  "pass_attempts": 550,
  "completions": 370,
  "interceptions": 10,
  "pass_epa": 95.4,
  "rush_yards_allowed": 1750,
  "rush_tds_allowed": 12,
  "pass_yards_allowed": 3800,
  "pass_tds_allowed": 22,
  "interceptions_forced": 14,
  "rush_ypg": 123.5,
  "rush_ypg_allowed": 102.9,
  "pass_ypg": 247.1,
  "pass_ypg_allowed": 223.5,
  "ppg": 23.8,
  "ppg_against": 17.1,
  "completion_pct": 67.3
}
```

### Python Script to Generate team_stats_data.json
```python
import nflreadpy as nfl
import pandas as pd
import json

seasons = [2020, 2021, 2022, 2023, 2024, 2025]

stats = nfl.load_player_stats(seasons).to_pandas()
stats = stats[stats['season_type'] == 'REG']

rush_off = stats.groupby(['season', 'team']).agg(
    rush_yards=('rushing_yards', 'sum'),
    rush_tds=('rushing_tds', 'sum'),
    rush_carries=('carries', 'sum')
).reset_index()

pass_off = stats[stats['position'] == 'QB'].groupby(['season', 'team']).agg(
    pass_yards=('passing_yards', 'sum'),
    pass_tds=('passing_tds', 'sum'),
    pass_attempts=('attempts', 'sum'),
    completions=('completions', 'sum'),
    interceptions=('passing_interceptions', 'sum'),
    pass_epa=('passing_epa', 'sum')
).reset_index()

rush_def = stats.groupby(['season', 'opponent_team']).agg(
    rush_yards_allowed=('rushing_yards', 'sum'),
    rush_tds_allowed=('rushing_tds', 'sum')
).reset_index().rename(columns={'opponent_team': 'team'})

pass_def = stats[stats['position'] == 'QB'].groupby(['season', 'opponent_team']).agg(
    pass_yards_allowed=('passing_yards', 'sum'),
    pass_tds_allowed=('passing_tds', 'sum'),
    interceptions_forced=('passing_interceptions', 'sum')
).reset_index().rename(columns={'opponent_team': 'team'})

sched = nfl.load_schedules(seasons).to_pandas()
sched = sched[sched['game_type'] == 'REG'][['season', 'week', 'home_team', 'away_team', 'home_score', 'away_score']].dropna()

home = sched[['season', 'week', 'home_team', 'home_score', 'away_score']].rename(
    columns={'home_team': 'team', 'home_score': 'points_for', 'away_score': 'points_against'})
away = sched[['season', 'week', 'away_team', 'away_score', 'home_score']].rename(
    columns={'away_team': 'team', 'away_score': 'points_for', 'home_score': 'points_against'})

scoring = pd.concat([home, away]).groupby(['season', 'team']).agg(
    points_for=('points_for', 'sum'),
    points_against=('points_against', 'sum'),
    games_played=('week', 'count')
).reset_index()

team_stats = scoring.copy()
team_stats = team_stats.merge(rush_off, on=['season', 'team'], how='left')
team_stats = team_stats.merge(pass_off, on=['season', 'team'], how='left')
team_stats = team_stats.merge(rush_def, on=['season', 'team'], how='left')
team_stats = team_stats.merge(pass_def, on=['season', 'team'], how='left')

gp = team_stats['games_played']
team_stats['rush_ypg']         = (team_stats['rush_yards']         / gp).round(1)
team_stats['rush_ypg_allowed'] = (team_stats['rush_yards_allowed'] / gp).round(1)
team_stats['pass_ypg']         = (team_stats['pass_yards']         / gp).round(1)
team_stats['pass_ypg_allowed'] = (team_stats['pass_yards_allowed'] / gp).round(1)
team_stats['ppg']              = (team_stats['points_for']         / gp).round(1)
team_stats['ppg_against']      = (team_stats['points_against']     / gp).round(1)
team_stats['completion_pct']   = (team_stats['completions'] / team_stats['pass_attempts'] * 100).round(1)

team_stats = team_stats.fillna(0)

int_cols = ['rush_yards','rush_tds','rush_carries','pass_yards','pass_tds',
            'pass_attempts','completions','interceptions','rush_yards_allowed',
            'rush_tds_allowed','pass_yards_allowed','pass_tds_allowed',
            'interceptions_forced','points_for','points_against','games_played']
for col in int_cols:
    if col in team_stats.columns:
        team_stats[col] = team_stats[col].astype(int)

with open('team_stats_data.json', 'w') as f:
    json.dump(team_stats.to_dict(orient='records'), f)

print(f"Exported {len(team_stats)} records")
```

---

## Design Style
- Dark theme (bg: #0a0e17, cards: #111827)
- Fonts: Oswald (headers/labels) + Source Sans 3 (body) + Rajdhani (big numbers)
- Colors: Blue (#3b82f6) wins/accents, Red (#ef4444) losses/under, Green (#22c55e) positive/over, Yellow (#f59e0b) O/U line
- Accent: #38bdf8 (sky blue) for nav, team tags, season labels
- AFC conference badge: red (#f87171 on #3f1e1e)
- NFC conference badge: blue (#38bdf8 on #1e3a5f)
- Clean, minimal, sports analytics aesthetic
- Sticky nav tabs, smooth animations
- All pages share a consistent nav bar with "FRANCHISE analytics" logo linking to index.html

---

## What's Next (To Be Built)
- Player comparison mode (view two players side by side)
- L3 / L5 hit rate split vs full season
- Opponent-based filtering on player rankings page
- More chart types and visualizations

## Important Notes
- Regular season only — filter with df[df['season_type'] == 'REG'] for player stats, df[df['game_type'] == 'REG'] for schedules
- No betting/ATS data
- Keep code in single HTML files (data embedded directly) for simple GitHub Pages hosting
- nflreadpy uses Polars — always .to_pandas() before working with data
- When embedding JSON data in HTML, define the JS variable in a `<script>` tag BEFORE the main `<script>` block that references it, otherwise you'll get a ReferenceError
- Explain things clearly — beginner-level web dev
- Test Python scripts in Jupyter before integrating into HTML
