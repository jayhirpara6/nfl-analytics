# NFL Analytics Website — Project Instructions

## Project Overview
I'm building a football analytics website with customizable charts and visuals based on NFL data. It's a personal portfolio project, hosted for free on GitHub Pages. I'm a beginner with Python and brand new to web development (HTML/CSS/JS). I'm on Windows.

## My Local Setup
- **Python 3.12** installed (with PATH configured)
- **VS Code** for website code
- **Jupyter Notebook** for data exploration (`pip install notebook`)
- **nfl_data_py** installed via:
  ```
  pip install setuptools
  pip install numpy pandas
  pip install nfl_data_py --no-deps
  pip install appdirs fastparquet
  ```

## Data Source
- **Library:** `nfl_data_py` (from the nflverse project)
- **Scope:** NFL data from 2020 season to present
- **Current focus:** 2025 regular season
- **Key functions:**
  - `nfl.import_schedules([year])` — game results, scores, betting lines, weather, QBs, coaches
  - `nfl.import_weekly_data([year])` — weekly player stats (passing, rushing, receiving)
  - `nfl.import_seasonal_data([year])` — season-level player aggregates
  - `nfl.import_team_desc()` — team info, conferences, divisions, colors, logos
  - `nfl.import_rosters([year])` — full rosters

## Architecture
1. **Data layer (Python):** Pull data with `nfl_data_py`, export as JSON files
2. **Frontend (HTML/CSS/JS):** Single-page or multi-page site, no frameworks
3. **Charts:** Chart.js with chartjs-plugin-datalabels
4. **Hosting:** GitHub Pages (free)

## Current Progress

### What's Been Built
An `nfl-dashboard.html` file with two tabs:

#### Tab 1: Standings
- Sortable table (click column headers to sort)
- Columns: #, Team, W, L, T, PCT (with visual bar), PF, PA, DIFF (color-coded green/red)
- Computes all records dynamically from raw game data

#### Tab 2: Home vs Away (Win-Loss Record)
- Horizontal bar chart (Chart.js) with blue bars for wins, red bars for losses
- White data labels at the end of each bar showing the count
- X-axis fixed at 17 games regardless of filter
- Three filter controls that all work together:
  1. **Show Teams dropdown** (top left): All Teams / AFC / NFC / any division
  2. **Opponent dropdown** (top right): All Opponents / vs AFC / vs NFC / vs any division
  3. **Venue buttons** (far right): All Games / Home / Away
- Chart height adjusts dynamically based on number of teams shown

### Design Style
- Dark theme (bg: #0a0e17, cards: #111827)
- Fonts: Oswald (headers/labels) + Source Sans 3 (body)
- Colors: Blue (#3b82f6) for wins/accents, Red (#ef4444) for losses, Green (#22c55e) for positive diff
- Clean, minimal, sports analytics aesthetic
- Sticky nav tabs, smooth animations

### Data Structure
- All 272 regular season games stored as raw JSON in the HTML file
- Team conference/division lookup stored as JSON object
- Records computed on-the-fly from raw game data based on active filters
- Legacy team abbreviations (OAK, SD, STL, LAR) excluded from active teams

## What's Next (To Be Built)
- Player statistics section (passing, rushing, receiving leaders)
- Expand to multiple seasons (2020-2025)
- Season selector dropdown
- More chart types and visualizations
- Export to proper file structure (separate data JSON files, CSS, JS)
- Deploy to GitHub Pages

## Important Notes
- Regular season only (filter with `df[df['game_type'] == 'REG']`)
- No betting/ATS data for now (removed by request)
- When generating code, keep it in single HTML files for now
- Explain things clearly since I'm a beginner
- Test Python scripts in Jupyter Notebook before integrating
