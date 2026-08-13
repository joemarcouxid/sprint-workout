# Lane 4 — Sprint Week

A phone app for a high school sprinter. Put in your next meet date and it builds the whole
week — track session and gym session, day by day — working backward from that race.

No build tools, no npm, no App Store. It's plain HTML/CSS/JavaScript in one file. Drop it in
a GitHub repo, turn on Pages, open the link on your phone, add it to your home screen. Done.

---

## Put it online in 5 minutes

1. On GitHub, click **New repository**. Name it `sprint-week`. Make it **Public**. Create it.
2. Click **uploading an existing file** on the empty repo page.
3. Drag in everything from this folder: `index.html`, `manifest.json`, `sw.js`, `.nojekyll`,
   and the whole `icons` folder. Commit.
4. Go to **Settings → Pages**. Under *Source*, pick **Deploy from a branch**, branch `main`,
   folder `/ (root)`. Save.
5. Wait about a minute, then refresh. GitHub shows your link:
   `https://YOUR-USERNAME.github.io/sprint-week/`

## Put it on the home screen

- **iPhone:** open the link in Safari → Share button → *Add to Home Screen*.
- **Android:** open in Chrome → three-dot menu → *Install app* or *Add to Home screen*.

It then opens full screen with no browser bars, and works with no signal — useful at a track
with bad reception.

## Run it on your computer first (optional)

Double-clicking `index.html` mostly works, but the offline part needs a real server:

```bash
cd sprint-week
python3 -m http.server 8000
```

Then open `http://localhost:8000`.

---

## What it actually does

You give it four things: **meet date**, **main event**, **years of training**, and **which days
you take off**. Everything else is calculated.

### You choose the days off; the week builds around them

Pick any combination of rest days in setup (default is Thursday and Sunday). The generator
then lays out the remaining days itself:

1. It places the **quality days** — two high-neural track sessions plus one lift-led day —
   spread as evenly as possible around the week, wrapping Sunday to Monday, so two hard days
   can never land back to back.
2. The first leftover day becomes the **tempo day**; the rest become recovery.
3. The three lift days attach to the quality days in order: **Day A** on the first,
   **Day B** on the middle lift-led day, **Day C** on the last.
4. The single jump session attaches to the first quality day.

Take four or more days off and it drops the lift-led middle day first, giving two quality days
and two lifts. Take only one day off and you get an extra recovery day, not extra hard work.

So a week with Thursday and Sunday off comes out Monday / Wednesday / Friday. Take weekends
off instead and it becomes Monday / Wednesday / Friday with Thursday as recovery. Take Monday
and Friday off and it shifts to Tuesday / Thursday / Saturday. Same structure, your calendar.

The lift days always follow the same wave: 8–12 reps → 5–7 → 3–5. Olympic derivatives stay at
3 reps on all of them — the wave applies to the squats, pulls and presses. Nobody cleans for ten.

### Every event gets trained: 60, 100, 200, 400

The first quality day is **always** acceleration or max velocity. That never gets traded away,
because top speed is what the 60 and 100 are made of and it's the quality that disappears
fastest when you neglect it.

The **third** quality day rotates on the three-week cycle, so each energy system gets touched
once per cycle and no single week ever carries two lactic sessions:

| Cycle week | Session | Serves |
|---|---|---|
| Introduce | Speed endurance — 150m reps, full recovery | 200 |
| Stabilize | Special endurance — 300m reps at 90%, 12–15 min rest | 400 |
| Unload | Intensive tempo — 2 × 4 × 200m at 80–85%, short rest | lactate buffering |

In the sharpening phase the Stabilize session becomes the **split 400** — two sets of 2 × 200m
at race-split pace with only 45 seconds between the pair. It's the most race-specific 400m
workout there is, and it's held back until the base work is done.

In race season the rotation drops to short speed endurance, because racing a 200 or a 400 *is*
a lactic session and doesn't need doubling up.

### Target paces

Enter a 100, a 200, and a 400 goal in setup, and every relevant session prints a real time
instead of a percentage. Fill in what you have — the model fills the rest in from standard
sprint relationships (a 150 is about 0.745 of an open 200, an 80 about 0.83 of a 100, and the
first 300 of a 400 about 0.715 of the full race). A rep "at 90%" is then that best divided
by 0.90.

Race day also prints a split plan: first 200, second 200, and the goal. The differential
matters more than the total — going through 200 at anywhere near open-200 pace is what causes
tying up in the last 100.

### Reading the 200-to-400 gap

Double the open 200 and compare to the open 400. Roughly:

| Gap | Reading |
|---|---|
| 3.5–4.5s | Well conditioned for the 400 |
| 5–6s | Moderately trained |
| 7s+ | The event hasn't been trained — lactic power is the limiter |

Do the same with 100→200: doubled 100 versus open 200. Under about 1.2 seconds means good
speed endurance. A small 100→200 gap with a large 200→400 gap is the classic profile of a
sprinter with real speed and an untrained fourth lap — which is what the **400** event setting
is built for. It runs special endurance two weeks in three, while still protecting the max
velocity day every single week.

### About lactate clearance

Three things in the program do this work, and they're deliberate:

- **Extensive tempo** on the low day builds the capillary density and mitochondrial capacity
  that clear lactate between reps and between rounds at a meet. This is the aerobic support a
  400m runner needs — and it's the reason the tempo day survives even in race season.
- **Intensive tempo** at 80–85% with short rests is the buffering stimulus proper: acidity
  accumulates and the body adapts to tolerating and shifting it.
- **Active recovery** is printed on every lactic session. Walking through the rest clears
  lactate roughly twice as fast as sitting, and the same applies between rounds at a meet.

Note the honest disagreement in the source material here: Tony Holler argues aerobic work has
no place in sprint training at all. That's a defensible position for a pure 60/100 athlete. For
someone racing the 400, the Classifying Sprint Training Methods material and the ALTIS 200m
program both retain tempo work, and this app follows them.

### A note on MED days

Adding intensive tempo breaks the strict Francis high–low rule on purpose, and it's the one
place the app does so. The justification is event-specific: the 400 is roughly 30% phosphate
and 70% lactate, so a program that only ever runs at ≥95% or ≤75% simply never trains the
system that decides the race. It appears once every three weeks, on the Unload week, when the
rest of the load is lightest.

### Phase comes from how far out the meet is

| Weeks to the meet | Phase | What changes |
|---|---|---|
| 17+ | Off-season | Hills and bounding Monday, uphill 200s or max velocity Friday |
| 11–16 | Pre-season | Max velocity Monday, speed endurance Friday |
| 5–10 | Sharpening | Same shape, extended bounding, 200m runners get special endurance |
| 0–4 | Race season | Two lifts, two high days, Wednesday becomes recovery |

### Volume rides a 3-week wave

Introduce (80%) → Stabilize (100%) → Unload (60%), repeating. The session *structure* never
changes; only the number of reps and sets. That consistency is deliberate — it's what makes it
possible to tell whether the training is working.

### The uphill 200s

They run on Stabilize weeks only, off-season and pre-season only — roughly once every three
weeks, and never inside two weeks of a meet. Distance is scaled by training age: 3 × 100m for
a first-year athlete, 2 × 150m at two to three years, 2 × 180m after that. The grade caps
velocity, which is what makes a maximal effort safe for a young hamstring.

### The Olympic lifts are gated by training age

| Years training | Day A | Day B | Day C |
|---|---|---|---|
| 1st year | Clean-grip shrug + mid-thigh pull | Clean-grip high pull | Jump shrug |
| 2nd–3rd | Mid-thigh pull | Hang power clean from the knee | Power clean from blocks |
| 4+ | Mid-thigh pull | Hang power clean | Power clean |

A first-year athlete never catches a bar. The pulls deliver a large share of the power
adaptation without the technical cost, so she builds force from week one while the catch is
still being taught. Front squat sits on Day B every week regardless of level, because the
front rack is the prerequisite for everything in the clean family.

### The last 7 days are a taper

Once the meet falls inside the displayed week, the template is discarded and days are assigned
by distance from race day:

```
-6  off
-5  last hard session
-4  easy tempo
-3  sharpener (short, fast, no fatigue)
-2  shakeout
-1  pre-meet (warm-up + 2 build-ups + 2 starts, 40 min, go home)
 0  RACE
+1  off
+2  recovery
+3  easy tempo
```

No lifting inside three days either side of a race. Racing most weekends? Update the meet date
after each one and the next gets the same treatment.

### Hard rules the generator will not break

- Every day is **HIGH** (≥95%) or **LOW** (≤75%). Nothing in between, ever — that zone costs
  recovery and returns nothing.
- Never two HIGH days back to back; 48 hours minimum.
- Max three HIGH days a week, two in race season.
- Race week ignores the layout and tapers by distance from the meet — but it still respects
  your days off, apart from race day itself.
- Rest on speed work is roughly one minute per 10m sprinted, printed on every rep.
- Exactly one jump session a week, on Monday, following the in-place → short-response →
  extended-bounding progression across the year.
- Low days get bodyweight circuits and med ball only — no lifting, no jumps.

Volumes scale by training age: a first-year athlete gets about 70% of the reps an experienced
one does.

## Where the training model comes from

The weekly shape, the three lift days and the 8–12 / 5–7 / 3–5 wave come from a Division I
program's published training week. The exercise menu — power clean, hang clean, clean-grip
shrug, mid-thigh pull, back squat, quarter squat, push press, push jerk, weighted jumps,
stiff-leg deadlift, incline press, dumbbell rows — mirrors what that program actually uses,
which in turn lines up with survey data on Olympic sprint and jump coaches (essentially all of
whom use squat variations and weightlifting derivatives).

The intensity rules on top of it are Charlie Francis's high–low split (via Derek Hansen's
write-ups). The three-week Introduce/Stabilize/Unload cycling and the warm-up structure are
Dan Pfaff and Stuart McMillan's ALTIS model. The bias toward short, fast, well-rested sprinting
is Tony Holler. The jump progression and general strength circuits are Boo Schexnayder. Wickets
and sprint-float-sprint are Jonas Dodoo. Rest intervals, volume caps and the youth-lifting
progression follow NSCA guidance for adolescent athletes.

## Editing the workouts

Everything lives in `index.html`, in the section marked `session library`. Each session is one
small function — for example:

```js
D.accel_hills = c => {
  const h = sc(8, c), f = sc(4, c);   // sc() scales reps by phase and training age
  return {
    title: 'Hills & acceleration', tag: 'HIGH', warm: WARM_FULL,
    track: [ I('HILL', h + ' × 20m hill sprint', 'coaching note', 'rest 3–4 min') ],
    gym: [ GYM[c.phase].A, PLYO[c.phase], THROWS ],
    vol: h * 20 + f * 20, mins: 85,
    why: 'one or two sentences'
  };
};
```

Change the numbers, the notes, or the lifts in the `GYM` table. Save, push, and bump `CACHE`
in `sw.js` to `lane4-v2` so phones pick up the new version.

## A note on safety

This is a planning tool. A real coach who can watch you run beats it every time, and if your
coach's plan conflicts with this one, follow your coach. Sharp or specific pain means stop and
get it looked at — sore and heavy is a different thing from hurt. Anything to do with maximal
lifting needs a coach in the room teaching technique first.

## License

MIT — use it, change it, share it.
