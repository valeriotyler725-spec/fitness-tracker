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

- Today's plan from the weekly schedule: AM session and PM activity.
- WHOOP recovery, HRV, resting HR, sleep hours (flag if under the 6.5 hr minimum).
- Calories: **consumed**, **MacroFactor target**, **left = target − consumed**,
  and **estimated burned** (from WHOOP).
- Macros against the plan's targets: protein 178 g, fat ≥ 80 g, fiber 25–30 g.
  Thursday and Saturday are marked as high-calorie days.
- Alerts: deload, two-a-day load warning, pain restrictions, low sleep.

## 3. Lift logging and progression

### How lift data gets in

WHOOP's public API and bulk data export do **not** include Strength Trainer sets, reps or weights,
and the Strava relay was ruled out (tested 2026-10-07). **Decision: lifts are logged in the
dashboard's own Workout Tracker (below).** WHOOP still records a plain "Weightlifting" activity
for strain and HR, which the dashboard matches by time.

### Workout Tracker (replaces WHOOP Strength Trainer)

**During the workout (phone-first screen):**
- Today's session loads from the plan: exercises in order, supersets grouped (A1/A2), with sets,
  rep range, tempo, rest and notes ("Stop at parallel", "6-sec eccentric mandatory").
- Each set is pre-filled with the **suggested weight** and target reps. Logging a set as prescribed
  is one tap. If not, adjust weight or reps with +/- and save.
- Per-set fields: weight (DB total or per arm, see equipment rules), reps, optional RIR
  (reps in reserve, 0–4), optional pain flag (opens the pain log).
- Warm-up sets can be logged and are excluded from progression and volume.
- Swap exercise: e.g. bench → DB bench per the shoulder-pain note, or Hack Squat ↔ Leg Press.
  The swap is recorded and tracked separately.
- Add or skip an exercise. Skips are recorded (e.g. "Face Pull: never skip" warns).
- Last time's numbers are shown beside each exercise.
- Deload, guard or pain restrictions show up as already-adjusted targets with the reason.

**Rest timer:**
- **Phase 1 (core need): shows how long you've been resting.** A large count-up clock
  (e.g. "1:42 resting") starts automatically when a set is saved and stops when the next set is saved.
  - The plan's rest target is shown beside it ("target 2:00"). The clock changes color when
    the target is reached and again when you go well over it, with no alerts.
  - Supersets: no rest between A1 and A2. The clock runs after A2.
  - The clock keeps correct time if the phone locks or you switch apps, because it's based on timestamps.
  - Actual rest is recorded per set. The summary shows planned vs actual rest.
- **Later:** an optional sound/notification at the target, and **vibration**.
  Web apps can't vibrate on iPhone, so vibration comes with the native iOS app.

**Tempo cue (optional):** a per-set metronome that counts the tempo (e.g. 3-1-1-0) for exercises
where it's mandatory, like calf raises at 2-2-6-0.

**What's tracked per workout (WHOOP Strength Trainer equivalents plus more):**

| Metric | Source |
|---|---|
| Exercises, sets, reps, weight, RIR | Workout Tracker |
| Volume load (sets × reps × weight), per exercise and per muscle group | Calculated |
| Muscular load by muscle group (WHOOP-style body map) | Calculated from volume, effort (RIR) and exercise-to-muscle mapping |
| Estimated 1RM per lift and PRs (weight, reps, e1RM, volume) | Calculated |
| Time under tension | Calculated from tempo × reps |
| Duration, rest planned vs actual | Workout Tracker |
| Strain, avg/max HR, HR zones, calories | WHOOP workout matched by time; later from Apple Watch |

**After the workout:**
- Summary: PRs, volume vs last time, muscle load map, strain/HR once WHOOP syncs,
  and the progression suggestions for next session.
- History: per-exercise charts (weight, e1RM, volume) and a calendar of sessions.

**Offline:** the tracker works without gym signal and syncs when back online.

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
- **Labor-heavy day** (from the journal): caps the day's effort at 80%, per the program rules.
- **Heart rate:** conditioning workouts whose max HR went over 160 are flagged after the fact.
  WHOOP heart rate can't be read live.
- Guard actions are suggestions with a reason ("recovery 28% and 31% after Wednesday legs"),
  never silent changes.

## 6. Daily journal (simpler WHOOP Journal) and pain log

One short check-in a day, under 30 seconds, mostly taps.

**Morning (about the night before):**
- Energy 1–5
- Soreness 1–5, with an optional body area
- Stress 1–5
- Pain: none, or body area + 0–10 (feeds the pain rule below)
- Yes/no from yesterday: alcohol, caffeine after 2 pm, late meal (within 2 hr of bed), screens in bed

**Anytime:**
- Labor-heavy day (yes/no). This is the program's 80% effort cap, moved here from a separate toggle.
- Free-text note

**Customizable:** add, remove or rename yes/no questions and 1–5 scales in settings.
The defaults above are **OPEN** for your edits.

**Insights:** after ~30 days of entries, show how recovery, HRV and sleep differ on "yes" vs "no"
days for each item (e.g. "Recovery averages 12 points lower after alcohol").
Shown only once there are enough of each answer to compare.

**Feeds the coaching rules:**
- **Pain above 4/10** in an area → isometrics only for that area until **7 pain-free days** are logged.
  Workouts for that area show the restriction.
- **Labor-heavy day** → the effort cap and guard adjustments for that day.
- **High soreness or low energy** together with low recovery → shows as context on guard and deload alerts.

The journal also shows next to the day's recovery, sleep, calories and workouts in history.

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
- Native iPhone app (adds rest-timer vibration).
