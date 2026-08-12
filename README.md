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
