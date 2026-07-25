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

You give it four things: **meet date**, **main event**, **years of training**, **days per week**.
Everything else is calculated.

### Phase comes from how far out the meet is

| Weeks to the meet | Phase | Emphasis |
|---|---|---|
| 17+ | Off-season | Hills, resisted acceleration, hypertrophy lifting, big tempo volume |
| 11–16 | Pre-season | Blocks, max velocity, max-strength lifting, speed endurance appears |
| 5–10 | Sharpening | Max velocity leads, power lifting, special endurance for 200m runners |
| 0–4 | Race season | Two high days a week, low volume, very high quality |

### Volume rides a 3-week wave

Introduce (80%) → Stabilize (100%) → Unload (60%), repeating. The session structure stays the
same week to week; only the number of reps moves. That consistency is deliberate — it's what
makes it possible to tell whether training is working.

### The last 7 days are a taper

Once the meet is inside the displayed week, the normal template is thrown out and days are
assigned by how far they sit from race day:

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

Racing most weekends? Update the meet date after each race and the next one gets the same
treatment.

### Hard rules the generator will not break

- Every day is either **HIGH** (≥95% effort) or **LOW** (≤75%). Nothing in the middle, ever —
  that zone costs recovery and returns nothing.
- Never two HIGH days back to back. Minimum 48 hours between them.
- Max three HIGH days a week in training phases, two in race season.
- Rest on speed work is roughly one minute per 10m sprinted, and it's printed on every rep.
- Gym work is stacked on HIGH days, right after the track session. LOW days get bodyweight
  circuits and med ball only — no lifting, no jumps.
- No lifting inside three days of a meet.

Volumes scale by training age: a first-year athlete gets about 70% of the reps an experienced
one does.

## Where the training model comes from

Charlie Francis's high–low intensity split (via Derek Hansen's write-ups), Dan Pfaff and
Stuart McMillan's ALTIS Introduce/Stabilize/Unload cycling and warm-up structure, Tony Holler's
"Feed the Cats" bias toward short, fast, well-rested sprinting, Boo Schexnayder's plyometric
progression and general strength circuits, Jonas Dodoo's wickets and sprint-float-sprint work,
and Randy Huntington's resisted-sprint use. Rest intervals and volume caps follow NSCA
guidance for adolescent athletes.

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
