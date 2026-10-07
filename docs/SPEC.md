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

Strava relay ruled out: tested 2026-10-07, and Strava does not receive the set data.
**OPEN: pick one:**

1. **Screenshot import:** keep logging in WHOOP Strength Trainer. After the session, upload
   screenshots of the summary. Free in-browser text recognition reads exercise, sets, reps and
   weight, and you confirm or fix them in one review screen. Needs sample screenshots to build the reader.
2. **Dashboard logger plus WHOOP activity:** log sets in the dashboard, with today's workout
   pre-filled and the suggested weight shown. Start a plain "Weightlifting" activity on WHOOP so
   strain and HR are still captured, and the dashboard matches it by time. No double entry,
   and this is the long-term path when WHOOP is replaced.

### Progression rule (from the program, plus equipment awareness)

- **Increase:** double progression. Hit the top of the rep range on all sets for **2 sessions in a row**,
  then add load at the **smallest step the equipment allows** (below). This replaces the
  program's +5 / +2.5 lb, because those increments aren't available.
- **How dumbbell weight is recorded:**
  - **Both arms together** (e.g. incline DB press, hammer curl): logged as the **total of both dumbbells**.
    Two 25s = 50 lb.
  - **One arm at a time:** logged **per arm**, only when the exercise is set up as single-arm.
- **Equipment steps** (no change plates on free weights):

  | Equipment | Smallest step | Example |
  |---|---|---|
  | Barbell | **10 lb** total (a 5 lb plate each side) | 185 → 195 |
  | Dumbbells, both arms (logged as total) | **10 lb** total (5 lb heavier per dumbbell) | 2×25 = 50 → 2×30 = 60 |
  | Dumbbell, single arm (logged per arm) | **5 lb** per arm | 30 → 35 |
  | Machine stack / cable | set once per machine (**OPEN:** default 5 lb until entered) | |
  | Bodyweight / added load | add reps, then the smallest plate or dumbbell available | dips at 3×12 → +10 lb |

  - **Big jumps:** when the step is more than ~10% of the working load, which is common on light
    dumbbell work (lateral raises 2×15 = 30 → 40 is +33%), first build reps 2 past the
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

## 5. Load / overwork guard

Always on, every day. **Walks count** toward daily load.
Heavier exposure points: Thursday (Upper Strength + Zumba), Saturday (Zumba), and
Friday's heavy lower day following Thursday's Zumba.

Triggers (thresholds adjustable in settings):

- **Heavy lift, then a recovery drop:** after a heavy session (e.g. legs, then a walk), if WHOOP
  recovery falls for **2 days in a row** (red, or more than 15 points below the 30-day average),
  the guard steps in. The next sessions for those muscles get reduced load or volume, and
  extra sessions become easy.
- **Big calorie drop:** intake more than **25% below the MacroFactor target on 2 or more days in a row**,
  or the 3-day average down more than 20%. The guard caps intensity, drops the second session
  to easy, and puts any planned load increases on hold until intake recovers.
- **Recovery:** red today → the second session becomes easy (walk or mobility).
- **Load spike:** 7-day strain more than ~30% above the 4-week average → warning,
  with a suggestion to scale the second session.
- **Muscle overlap:** hard work on the same muscles within 48 hours gets flagged
  (e.g. Thursday Zumba before Friday squats and deadlifts).
- **Labor-heavy day:** a toggle that caps the day's effort at 80%, per the program rules.
- **Heart rate:** conditioning workouts whose max HR went over 160 are flagged after the fact.
  WHOOP heart rate can't be read live.
- Guard actions are suggestions with a reason ("recovery 28% and 31% after Wednesday legs"),
  never silent changes.

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
- Replacing WHOOP:
  - **Overnight:** sleep duration and score, overnight HRV, resting HR and breath rate
    from the **Sleep Number bed (SleepIQ)**.
  - **Daytime:** strain, calories and workouts from the **Apple Watch**.
  - **Recovery score:** our own, built from bed HRV, resting HR and sleep against a personal 30-day baseline.
    Sleep Number reports HRV as SDNN, a different measure than WHOOP's (RMSSD), so the two numbers aren't
    comparable directly. Scoring each against its own baseline handles that.
  - **How bed data gets in (OPEN):** through Apple Health if the Sleep Number app writes to it.
    Otherwise through the unofficial SleepIQ API, which works but can break without notice.
  - **Gap:** nights away from the bed have no data. Fall back to wearing the Apple Watch to bed, or
    skip the recovery score that day.
  - **Validation:** run 2–4 weeks side by side with WHOOP before canceling.
- Native iPhone app.
