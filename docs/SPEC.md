# Coaching Dashboard — Spec (DRAFT, awaiting approval)

Personal coaching dashboard for Tyler's Hybrid Athlete program. Single user, browser first,
installable to the iPhone home screen later. Must stay free to run.

Source program: [`program/hybrid-athlete-v5.json`](../program/hybrid-athlete-v5.json)
(Hybrid Athlete v5.0, Cycle 1 – Cut, as of 2026-10-07).

Items marked **OPEN** need an answer before or during the build.

---

## 1. Goal tracking

- Goal: **203 lb by 2026-12-05**.
- Trend weight: smoothed moving average of daily weigh-ins, which hides water swings.
- Projection: date you'll hit 203 at the current trend rate, compared with Dec 5.
  The dashboard shows the weekly loss rate needed to make Dec 5.
- Weigh-ins come from Apple Health (MacroFactor and scale data). Manual entry is available as a backup.

## 2. Today screen

- Today's plan from the weekly schedule: AM session, extra (bull riding prep or core), PM activity.
- WHOOP recovery, HRV, resting HR, sleep hours (flag if under the 6.5 hr minimum).
- Calories: **consumed**, **MacroFactor target**, **left = target − consumed**,
  and **estimated burned** (from WHOOP).
- Macros against the plan's targets: protein 178 g, fat ≥ 80 g, fiber 25–30 g.
  Thursday and Saturday are marked as high-calorie days.
- Alerts: deload, two-a-day load warning, pain restrictions, low sleep.

## 3. Lift logging and progression

### How lift data gets in

WHOOP's public API and bulk data export do **not** include Strength Trainer sets, reps or weights.
Candidate paths:

1. **Strava relay (preferred if it works):** turn on Strength Trainer sharing in WHOOP so each
   strength workout posts to Strava with its exercise list and weights. The dashboard
   reads it from Strava's free API and parses it. **OPEN:** confirm the format from one real activity.
2. **Screenshot upload:** free in-browser text recognition reads a WHOOP summary screenshot,
   then you confirm the numbers.
3. **Quick entry in the dashboard:** a fallback.

### Progression rule (from the program, plus equipment awareness)

- **Increase:** double progression. Hit the top of the rep range on all sets for **2 sessions in a row**,
  then add load: **+5 lb on compounds, +2.5 lb on isolation**.
- **Equipment steps:** the increase rounds up to the next weight the equipment can actually load.
  Each exercise has an equipment profile (barbell, dumbbell rack, machine stack, cable,
  bodyweight or added load). Machine and cable steps are set once per machine.
  - If the smallest step is more than ~10% of the working load, first build reps past the
    top of the range (or add a set), then jump. The dashboard tells you to expect reps to drop
    back to the bottom of the range after the jump.
- **Hold:** below the bottom of the range for 2 sessions → keep the weight.
- **Reduce:** below the bottom of the range for 3 sessions → drop 5–10%, rounded to an available step.
- **Shared exercises:** the same exercise in two sessions is tracked per session.
  Examples: bench Mon (8–10) and Thu (5–7), hip thrust Wed and Fri, rear delt fly Mon and Thu.
  Estimated 1RM is shared across both.
- **Paired machines:** hip adduction and abduction go up together. Adduction sets the pace.
- **Bodyweight to weighted:** weighted dip adds load once 3×12 is reached.
- **%1RM targets:** an estimated 1RM is computed from logged sets, so "70–75% 1RM"
  shows as an actual weight.
- **Hack Squat / Leg Press:** tracked as separate exercises depending on which you did.

## 4. Deload logic

- **Scheduled:** every 4th week. Sets −1, loads −10%, rounded to equipment steps.
- **Recovery triggered:** WHOOP recovery **red on 3 or more days in a row** recommends a deload.
  A single red day suggests a lighter version of that day's session.
- **Stall triggered:** 3 or more lifts held or reduced in the same week recommends a deload week.
- **Sleep:** under 6.5 hr on 2 or more nights in a row adds a caution.

## 5. Two-a-day load guard

Training days with a second hard session: Thursday (Upper Strength + Zumba) and
Saturday (Zumba). Friday's heavy lower day also follows Thursday's Zumba.

- **Recovery:** red today → the second session becomes easy (walk or mobility).
- **Load spike:** 7-day strain more than ~30% above the 4-week average → warning,
  with a suggestion to scale the second session.
- **Muscle overlap:** hard work on the same muscles within 48 hours gets flagged
  (e.g. Thursday Zumba before Friday squats and deadlifts).
- **Labor-heavy day:** a toggle that caps the day's effort at 80%, per the program rules.
- **Heart rate:** conditioning workouts whose max HR went over 160 are flagged after the fact.
  WHOOP heart rate can't be read live.
- **OPEN:** do walks count as a session for this guard? Proposed: no, only lifts, Zumba
  and bull riding prep.

## 6. Pain log

- Log pain 0–10 by body area.
- Above 4/10 → the dashboard restricts that area to isometrics until **7 pain-free days**
  are logged.

## 7. Progress photos

- Upload photos with a date. Each one is linked to that day's trend weight and scale weight.
- Timeline and side-by-side comparison.
- Search, e.g. "photo from the day I last weighed 210", finds the most recent weigh-in
  near that number and shows the photo from that day or the closest one.

## 8. Progressive overload reference

- A built-in searchable guide: the rules above, why they exist, and what to do in edge cases
  (missed sessions, equipment changes, travel).
- Every suggestion links to the rule that produced it.
- **Later:** ask Claude questions answered from your own data (needs an API key; paused).

## 9. Update my workout plan

- Import a plan as JSON (this format) or build one in a form:
  - days per week
  - minutes per day
  - 75 Hard on/off (when on: two sessions every day, at least one outdoors; the two-a-day guard adjusts)
  - focus: strength / muscle mass / functional / cardiovascular
  - goal: lose fat / gain muscle / recomp / coast
- Plan versions are saved, so history and progression carry over between plans.
- **OPEN:** fill in sets and reps for the Bull Riding Prep and Core Routine (marked "verify" in the plan).

## 10. Data sources

| Source | Data | How | Cost |
|---|---|---|---|
| WHOOP API | Recovery, HRV, RHR, sleep, strain, workouts, calories burned | OAuth + webhooks | Free |
| Strava API | Strength Trainer exercises, sets, weights (via relay) | OAuth | Free |
| Apple Health | Weight, calories eaten, macros (from MacroFactor), Apple Watch runs | iPhone Shortcuts automation POSTs daily | Free |
| Manual | Photos, pain, labor-day toggle, fallback lift entry | Dashboard | Free |

## 11. Stack (proposed)

- Next.js web app as an installable PWA, hosted on the Vercel free tier.
- Supabase free tier: Postgres database, login, and photo storage (1 GB).
- Scheduled syncs via Vercel cron. WHOOP webhooks for near-real-time updates.
- Later: a native iOS app. Apple's developer account costs $99/yr, so this is deferred.

## 12. Later phases

- Claude Q&A on your data.
- Replacing WHOOP: needs overnight sleep data from another source, either wearing
  the Apple Watch to bed or a cheap sleep tracker that writes to Apple Health.
- Native iPhone app.
