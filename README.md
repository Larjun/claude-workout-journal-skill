# Claude Workout Journal Skill

A Claude skill that turns Claude into an honest running and cycling coach, and keeps a workout journal you own: a Markdown repo that publishes itself as a website.

Strava and training apps are encouraging. This skill tells you what went wrong and what to fix next.

**Live example:** [my own journal](https://larjun.github.io/Workout-Journal/) ([repo](https://github.com/Larjun/Workout-Journal)), kept with this skill.

## What it does

- **Analyses each run or ride.** Pacing, heart-rate drift, time in zones, cadence and climbs, compared with your previous similar session.
- **Gives a blunt critique.** A verdict, the one main fix (with the number that proves it), and one instruction for next time. You choose how blunt.
- **Keeps the journal.** A short entry per session, living "focus" notes per sport (goals, recurring mistakes, what's improving), and weekly reviews that adjust your goals to your recent form.
- **Saves it.** Commits to your GitHub repo, which builds a searchable [MkDocs Material](https://squidfunk.github.io/mkdocs-material/) site with light and dark modes in your own colour. No repo? You get Markdown to paste wherever you like.

It handles common data quirks: per-leg cadence, GPS pace glitches, watches left running, duplicate workouts, and calorie estimates that apps get wrong.

## What you need

| | Required? | Why |
|---|---|---|
| Claude with custom skills | Yes | To install the skill |
| Strava connector in Claude | Recommended | Pulls your activities automatically (needs a Strava subscription). Without it, upload screenshots or type your numbers |
| GitHub connected to Claude + an empty repo | Recommended | Lets Claude commit entries and publish your journal site. Install Claude's GitHub app on the repo so it can push |

Strava's API policy doesn't allow its developer API data to be used in AI applications. This skill uses the official Strava connector, which Strava allows for subscribers analysing their own data for personal use.

## Install

1. Download [`workout-journal.zip`](workout-journal.zip).
2. Add it to Claude as a custom skill (where skills are managed in your Claude settings).

The zip contains `workout-journal/SKILL.md`, the same file that's in this repo.

## First run

Say **"Set up my workout journal."** Claude will:

1. **Check connections.** If Strava is connected, it pulls your profile and heart-rate zones so it doesn't have to ask.
2. **Ask a short questionnaire** (one or two rounds):
   - Sports to track, heart-rate zones or max HR, weight (optional, only for calories), experience, injuries to watch
   - Current plan and week, goals in your own words, a typical week, equipment, conditions (heat, traffic, terrain)
   - How blunt the feedback should be, anything not to comment on, where to save, a public site or not, a site colour, and whether to backfill past sessions
3. **Confirm, then build.** After you approve the summary, it creates the journal, seeds your goals (provisional until the first weekly review), pushes it, and backfills recent sessions if you asked.
4. **One manual step:** enable GitHub Pages (Settings → Pages → Deploy from a branch → `gh-pages` / root).

## Day to day

- After a workout: **"Analyse my latest run"** or **"Check today's ride."**
- Once a week: **"Review my week."** Goals get checked and adjusted (at most 3 per sport).

## Journal structure it creates

```
README.md
mkdocs.yml                     site config, navigation, tags, light/dark palette
docs/index.md                  current focus + session index
docs/focus/running.md          goals, rules, recurring mistakes, what's improving
docs/focus/cycling.md          same for cycling, plus bike notes
docs/sessions/YYYY/            one short entry per session
docs/weekly/                   weekly reviews
docs/stylesheets/extra.css     your colours
.github/workflows/deploy.yml   builds and deploys to GitHub Pages on every push
```

## Privacy

On free GitHub plans, Pages sites need a public repo, so your training data (heart rate, paces, notes) will be visible to anyone with the link. If that's a problem, keep the journal as Markdown only, or use a private repo without Pages.

## Limits

- It doesn't create training plans. Keep using Runna, Garmin Coach or similar.
- It can only analyse data it can see: the Strava connector, your screenshots, or numbers you give it.
- **Not medical advice.** Stop and get checked for sharp pain, chest symptoms or dizziness.

## Contributing

Personal project, shared as is. Fork it and make it yours.

## License

[MIT](LICENSE)
