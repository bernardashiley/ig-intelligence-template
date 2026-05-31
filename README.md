# IG Intelligence Pipeline — Template

A reusable, config-driven pipeline that turns a brand's **public** Instagram data into a signed intelligence report: Apify scrape → clean & classify → statistical analysis + Monte Carlo → plain-English and full LaTeX/PDF reports, compiled in CI.

This is the **canonical template**. Each client is a copy of this repo with its own config and data. The analysis code is identical across clients, so a fix here benefits everyone.

---

## Quick start (new client)

```bash
# 1. Copy this template to a new client folder, then:
npm run init "Client Display Name" theirhandle      # writes config + inputs/*.json

# 2. Add your Apify token
cp config.ps1.example config.ps1                     # then paste your token (gitignored)

# 3. Scrape (Windows PowerShell; init prints exact commands)
powershell -File scripts/run_apify_actor.ps1 "inputs/profile_details.json" "<slug>_profile_details" 1
#   ...posts_full, reels_full, mentions...
node scripts/make_comments_input.js                  # build comment input from top posts
powershell -File scripts/run_apify_actor.ps1 "inputs/comments_top_posts.json" "<slug>_comments_top_posts" 200

# 4. Analyse + report
npm run all                                          # analyse + advanced + stakeholder + combined
npm run audit                                        # independent statistical-validity checks

# 5. Push — CI compiles the PDFs
```

## What's adaptive vs what you configure

**Automatic (no edits):** client identity, brand colours, owner/third-party split (from handle), reels-vs-posts wording, **small-sample demotion** (under 40 owned posts the stats move to a clearly-labelled illustrative appendix), pillar rankings, timing, comments, Monte Carlo inputs, fixed-seed reproducibility.

**You configure per client:** `config/client.config.json` (identity/brand/limits) and, **for non-restaurant industries**, `config/pillar_rules.json` (the keyword classifier ships tuned for restaurants/venues).

**Known limitations (restaurant-coupled, edit for other industries):**
- The Monte Carlo `STRATEGIES` mixes in `src/advanced_analysis.js` reference restaurant pillar names; for other industries update them to match your `pillar_rules.json`.
- Some narrative in the stakeholder/combined generators uses hospitality framing ("book a table", "menu"); adjust for other sectors.

## Statistical honesty (built in)
- Fully reproducible: fixed seed, committed data, deterministic — bit-identical re-runs.
- Engagement-score circularity is disclosed and reels-vs-posts is reported views-free alongside.
- Small accounts auto-render as a **descriptive audit**, with hypothesis tests and Monte Carlo demoted to an illustrative appendix. The pipeline never claims confirmatory statistics it cannot support.
- `npm run audit` reproduces the independent checks (circularity, outlier robustness, sample sizes, hashes).

## Scripts
| Command | Does |
|---|---|
| `npm run init "Name" handle` | scaffold client config + Apify inputs |
| `npm run analyse` | clean + classify raw data → CSVs |
| `npm run advanced` | hypothesis tests + Monte Carlo (seeded) |
| `npm run stakeholder` | plain-English report + SVG charts |
| `npm run combined` | full combined report (config + CSVs → LaTeX) |
| `npm run audit` | independent validity checks |
| `npm run all` | analyse → advanced → stakeholder → combined |

Raw data, `config.ps1`, and the real `client.config.json` are gitignored.
