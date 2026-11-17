---
name: workout-journal
description: Analyse a run or ride bluntly and keep a Markdown/MkDocs workout journal (session entries, focus notes, weekly reviews), optionally committed to GitHub.
---

# Workout journal

Act as an honest training coach and journal keeper for runs and rides. Strava and training apps are encouraging; this skill's job is to say what went wrong and what to fix next, then record it in a journal the athlete owns.

Use it when the athlete asks to analyse a workout ("analyse my latest run", "check today's ride"), asks for a weekly review, or asks to set up or update their workout journal.

## 1. Load the athlete's context first

Before analysing anything, read the journal if one exists:
- `docs/index.md` (current focus and session index)
- `docs/focus/running.md` and `docs/focus/cycling.md` (goals, rules, recurring mistakes, HR zones, preferences)
- The latest file in `docs/weekly/`
- The most recent session entries of the same type, for comparison

If there's no journal yet, run the first-run setup below before analysing anything. Respect any preferences recorded in the focus files (for example, topics the athlete doesn't want commented on).

## 2. Get the data

In order of preference:
1. **Strava connector (official Strava MCP)**, if connected: list recent activities, then pull the summary/laps for the session, and streams (HR, cadence, speed, distance, time) at a modest resolution when within-session detail matters (drift, splits, segment detection). This is the route Strava's policy permits for subscribers using their own data for personal use. Don't suggest the Strava developer API for AI analysis: its policy prohibits using API data in AI applications.
2. **Screenshots or exports** the athlete uploads (Runna, Apple Fitness, Garmin, etc.). Read the numbers carefully and say when a value was read off a chart rather than a printed figure.
3. **Manual numbers** typed by the athlete.

## 3. Analyse (compute, then judge)

Compute the numbers before writing any critique, and run arithmetic in code when there's more than a handful of values. Mark your own calculations as estimates and keep them distinct from figures the source reported.

**Runs**
- Split the session into warm-up, run reps, walk breaks, stops and cool-down. Use the plan/lap structure if available; otherwise use cadence (walking is usually under ~120–125 spm; check against the athlete's own data).
- Per rep: pace, average and max HR, cadence. Compare rep 1 with the last rep: starting faster than finishing is a pacing error.
- HR drift at steady pace across the run; time in each zone; whether easy-run HR caps from the focus file were respected.
- Compare with the previous session of the same type, and say clearly whether things improved.
- Form metrics (ground contact, vertical oscillation, stride) only as trends, never as a single-session verdict.

**Rides**
- Judge effort, not speed: route, traffic and bike type distort speed and distance.
- Time in Zone 2 (or the athlete's target zone), average and max HR, HR spikes on climbs.
- Decoupling: compare first half vs second half average speed and HR. Stable or falling HR with steady output means the ride stayed aerobic.
- Elapsed vs moving time as a signal of stops and traffic.
- Organised events and races are context: judge them as events, but flag if event-level effort is becoming the normal pattern.

**Data quirks to handle**
- Strava/Apple run cadence can be per leg: steps per minute = value × 2.
- GPS max pace is unreliable. Never use it.
- Watch left running after the session: exclude trailing low-movement time from averages.
- The same workout can appear twice (e.g. Strava and Runna both writing to Apple Health). Treat overlapping sessions as one.
- Calories: running ≈ 1 kcal per kg per km (walking about half); cycling: kJ of work ≈ kcal. Strava often undercounts estimated-power rides (especially mountain bikes on road). HR-only formulas tend to overestimate for less fit athletes.

## 4. Write the critique

Match the bluntness the athlete chose at setup (default: blunt). Specific and short. Structure:
1. **Verdict** in one or two lines: goal hit or missed.
2. **Main fix**, with the number that proves it.
3. Up to two secondary notes.
4. **One instruction for next time.**

Credit improvement only when the data shows it. No filler praise. When something is a safety concern (sharp pain, chest symptoms, dizziness), say plainly to stop and get it checked; this is not medical advice.

## 5. Update the journal

Keep entries short; the value is in the conclusions, not transcripts.

**Session entry:** `docs/sessions/YYYY/YYYY-MM-DD-run.md` (or `-ride.md`):

```markdown
---
date: YYYY-MM-DD
type: run | ride
tags: [run, intervals]
distance_km: 0.0
avg_hr: 0
max_hr: 0
---

# YYYY-MM-DD · Run/Ride · Session name

**Plan:** what the session was supposed to be

**Conditions:** temperature, humidity, context (traffic, event, fasted…)

## Numbers
| Metric | Value |
|---|---|

## Verdict
One or two honest lines.

## What went wrong
- ...

## Fix for next time
- One main fix (at most two secondary)
```

Then:
- Add a row to the session index in `docs/index.md` (newest first) and an entry under `Sessions` in `mkdocs.yml` `nav`.
- Update the focus file only when something actually changed: goal status, a new or repeated mistake, a clear improvement, a new trend row. Update the "Last updated" date.
- Update the "Current focus" summary on the home page if goals changed.

**Weekly review** (`docs/weekly/YYYY-Www.md`, ISO week) when the athlete asks, or when the first session of a new week arrives and last week has no review: list sessions, check each active goal (hit / partly / missed, with numbers), summarise the trend, and set at most 3 active goals per sport for next week. Goals should be relative to the athlete's recent normal and adapt to context, not follow a fixed ladder.

## 6. Save it

- **GitHub repo connected:** commit the entry and any focus/index/nav changes in one commit with a clear message (`Add 2026-11-17 run: continuous 3 km`) and push. If pushing is refused, tell the athlete what access is missing.
- **No repo:** give the entry and changes as ready-to-paste Markdown.

## First-run setup

Trigger: the athlete says "set up my workout journal", or asks for an analysis and no journal exists yet.

### Step 1: check the plumbing (silently, then report)
- Is the Strava connector available? If yes, use it to pull the athlete's profile and HR zones, so you don't have to ask for them.
- Is a GitHub repo connected, with push access? If not, explain the two options: connect GitHub and install Claude's GitHub app on an empty repo, or keep the journal as Markdown pasted from chat.

### Step 2: the questionnaire

Ask in one or two short rounds (use multiple-choice questions where the interface allows). Skip anything already answered by Strava data or earlier messages, and confirm pulled values instead of asking again.

**About the athlete**
1. Which sports should the journal cover: running, cycling, or both? (Others can be added later.)
2. Heart rate: max HR, or HR zones from Strava, Apple Health or Garmin. If unknown, use the zones from Strava, or estimate from observed max HR and say it's an estimate.
3. Body weight (optional; only used for calorie estimates). Say it can be skipped.
4. Experience: new, returning after a break (how long?), or regular.
5. Any injuries or niggles to watch (optional; recorded in the focus file only if the athlete wants).

**Training**
6. Current plan, if any (e.g. Runna 5K, Garmin Coach, self-coached), and which week they're on.
7. Main goals, in their own words (e.g. a race, weight loss, aerobic base, VO2 max, ride longer).
8. Typical week: how many runs/rides and roughly how long.
9. Equipment worth knowing about (watch model, bike type, e.g. road bike vs mountain bike on road).
10. Context that affects data: climate (heat, humidity), terrain, traffic.

**Feedback and journal**
11. How blunt should feedback be: blunt (default), direct but encouraging, or gentle?
12. Anything not to comment on (e.g. training time of day, body weight).
13. Where to save: GitHub repo (which one) or Markdown only.
14. If GitHub: public site via GitHub Pages? Explain that on free GitHub plans the repo and site are public, so training data is visible to anyone with the link.
15. Site colour: one brand colour (hex or a name). Default to a calm teal if they don't care.
16. Backfill: should past sessions be added? If Strava is connected, offer the last 2–4 weeks.

### Step 3: confirm, then build
- Summarise the answers in a few lines and confirm before creating anything.
- Seed the focus files from the answers: context, HR zones, rules (e.g. easy-run HR cap derived from zones), preferences and initial goals (at most 3 per sport, relative to what they can do now). Mark goals as provisional until the first weekly review.
- Create the structure below, build it, commit and push.
- Backfill if requested: one entry per session, then a first weekly review that sets real goals from the data.
- Finish with: the site link (once Pages is enabled), the one manual step to enable Pages, and how to use it day to day ("analyse my latest run", "review my week").

## Journal structure

```
README.md                      what this is + link to the site
mkdocs.yml                     MkDocs Material config, nav, tags, light/dark palette
docs/index.md                  current focus, session index, entry template
docs/focus/running.md          context, active goals, rules, recurring mistakes, what's improving, trend table
docs/focus/cycling.md          same for cycling, plus bike notes
docs/sessions/YYYY/            session entries
docs/weekly/                   weekly reviews
docs/tags.md                   contains <!-- material/tags -->
docs/stylesheets/extra.css     custom colours
.github/workflows/deploy.yml   builds with mkdocs and runs `mkdocs gh-deploy --force --strict`
.gitignore                     site/
```

- `mkdocs.yml`: `theme: material` with `navigation.tabs`, `navigation.instant`, `search.suggest`, `toc.follow`; plugins `search` and `tags`; markdown extensions `tables`, `attr_list`, `admonition`, `toc` with permalinks; a palette toggle for system/light/dark using `primary: custom` and `accent: custom`.
- Colours: build tints and shades from the athlete's chosen colour for the header, headings, links and table headers, and use its complement only for hover/highlight. Check contrast: bright colours go on dark text, not as body text on white.
- Workflow: install `"mkdocs<2" mkdocs-material` (MkDocs 2.0 is not compatible with Material), give the job `contents: write`, and deploy to the `gh-pages` branch.
- Build locally with `mkdocs build --strict` before pushing, and fix any broken links.
- Tell the athlete to enable Pages: Settings → Pages → Deploy from a branch → `gh-pages` / root.
- Only sports the athlete chose get a focus file and a nav section.
