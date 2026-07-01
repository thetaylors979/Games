# Vibration Plate Routine App — Build Plan

**Created:** 2026-07-01  
**Status:** ✅ COMPLETE  
**Reference App:** Babbage's stretch-app at `/Users/billtaylor/.openclaw/workspace-babbage/stretch-app/index.html`

---

## Goal

Build a mobile-first web app that guides Bill through a 20-exercise vibration plate routine. Same look and feel as the existing stretch app — timer-based flow, audio cues, clean dark UI.

---

## What's Already Done ✅

1. **Poster image obtained** — `tmp/vibration_analysis/poster_full.jpg`
2. **All 20 exercise images extracted** — `vibration-app/images/01-basic-stance.jpg` through `20-chest-opener.jpg`
3. **Stretch app reference studied** — Babbage's `stretch-app/index.html` is the template

---

## The 20 Exercises (from poster analysis)

### Section 1: WARM UP & ACTIVATION (5 min — 1 min each)
1. Basic Stance — Stand tall, feet hip-width apart. Relax and let the plate do the work.
2. Mini Squats — Slight bend in the knees. Engage your core.
3. Wide Stance — Feet wider than hips. Soft knees, upright posture.
4. March in Place — Lift knees one at a time. Swing your arms.
5. Calf Raises — Rise onto your toes, then lower with control.

### Section 2: STRENGTH & TONING (10 min — 2 min each)
6. Squats — Push hip back and down. Keep chest lifted.
7. Lunges — Step back into a lunge. Alternate legs.
8. Push Ups — Hands on plate, body straight. Engage core and chest.
9. Side Plank — Elbow on plate, lift hips. Hold and switch sides.
10. Glute Bridge — Feet on plate, lift hips up. Squeeze your glutes.

### Section 3: BALANCE & STABILITY (5-7 min — 1 min each)
11. Single Leg Stand — Soft knee, engage core. Switch sides.
12. Squat Hold — Hold a squat position. Stay steady.
13. Bird Dog — Extend opposite arm and leg. Switch sides.
14. Side Step Up — Step up and over. Switch sides.
15. Knee Lift Balance — Lift knee and hold. Switch sides.

### Section 4: COOL DOWN & STRETCH (5 min — 1 min each)
16. Forward Fold — Hinge at hips and relax your back.
17. Hamstring Stretch — Place heel on plate, hinge forward. Switch sides.
18. Quad Stretch — Hold ankle and stretch front of thigh. Switch sides.
19. Hip Flexor Stretch — Step back into a lunge. Switch sides.
20. Chest Opener — Clasp hands behind you and open your chest.

**Total routine: ~25-27 minutes**

---

## App Design

### Structure
- Single `index.html` file in `vibration-app/` (same model as stretch app)
- Images already in `vibration-app/images/` (all 20 ✅)
- No external dependencies — vanilla HTML/CSS/JS

### Screens (same pattern as stretch app)
1. **Start Screen** — title, total time, exercise list grouped by section, Start button
2. **Timer Screen** — exercise photo, name, section label, instructions, countdown timer, progress bar, controls
3. **Complete Screen** — celebration, stats, restart button

### Timer Flow Per Exercise
- **Get Ready:** 5 seconds (countdown beeps + voice prompt with exercise name)
- **Exercise:** Duration from poster (60s for sections 1/3/4, 120s for section 2)
- **Rest/Transition:** 10 seconds between exercises within a section
- **Section Rest:** 15 seconds between sections (longer, with section announcement)

### Features (matching stretch app)
- Audio beeps for countdown (30, 10, 3, 2, 1)
- Voice announcements for exercise names, instructions, and section transitions
- Screen wake lock to prevent sleep
- Progress bar showing overall routine progress
- Pause / Resume / Skip / Back / Stop controls
- Back to Launcher link

### Visual Design
- Same dark theme as stretch app (`--bg: #0f0f11`, etc.)
- Same green accent color for consistency across fitness apps
- Section headers in the exercise list (purple accent for section labels)
- Section indicator on timer screen (e.g., "STRENGTH & TONING")
- Exercise instructions displayed below the name

### Exercise Data Structure
```javascript
const SECTIONS = [
  {
    name: "WARM UP & ACTIVATION",
    duration: "5 min",
    exercises: [
      { name: "Basic Stance", img: "images/01-basic-stance.jpg", duration: 60, instructions: "Stand tall, feet hip-width apart. Relax and let the plate do the work." },
      // ...
    ]
  },
  // 4 sections total, 5 exercises each
];
```

### Timing Constants
```
GET_READY = 5 seconds
REST_BETWEEN_EXERCISES = 10 seconds
REST_BETWEEN_SECTIONS = 15 seconds
Countdown beeps at: 30, 10, 3, 2, 1
```

---

## Build Steps

### Step 1: Create `index.html` — ✅ DONE
- HTML skeleton (3 screens: start, timer, complete)
- CSS (adapted from stretch app — same dark theme, same component styles)
- JavaScript:
  - Exercise data array (4 sections × 5 exercises)
  - Timer state machine (get-ready → hold → rest → next exercise)
  - Audio system (beeps + speech synthesis)
  - Wake lock support
  - Control handlers (start, pause, resume, skip, back, stop, restart)
  - Progress bar calculation
  - Exercise list builder (grouped by section)

### Step 2: Verify — ✅ DONE
- All 20 image paths verified ✅
- HTML tags balanced ✅
- 20 exercises loaded ✅
- Check all 20 image paths match actual files
- Validate HTML structure (balanced tags)
- Review timer flow logic against poster timing

### Step 3: Preview — ✅ DONE
- Opened in Mac mini browser
- Open in browser on Mac mini for visual check
- If canvas plugin enabled + node paired, present on phone

### Step 4: Add to Launcher — PENDING
- Ask Babbage to add a card to the public-games launcher
- Ask Babbage to add a vibration-app card to the public-games launcher
- Or Bill can bookmark/access directly

---

## What Went Wrong Last Time (Post-Mortem)

**Root cause:** I got stuck in a loop trying to use the image model to identify exact pixel coordinates of each exercise on the poster for cropping. The image model kept timing out or giving inconsistent results. I tried:
1. Full poster analysis → timed out
2. Cropping into 4 sections → timed out on individual sections
3. Cropping into horizontal strips → still timed out
4. Kept retrying with different strip heights → same failure

I acknowledged I was "hitting the same wall" but kept trying variations of the same approach instead of switching strategies.

**What actually worked:** The images DID get extracted — all 20 are in `vibration-app/images/`. The crash happened after extraction, when I was still trying to "verify" them with the image model.

**Lesson:** If an approach fails twice, switch approaches. Don't keep retrying the same tool with minor variations. Once the images are extracted, move on to building the app.

---

## File Inventory

```
vibration-app/
├── index.html              ← TO BUILD (this is the main app)
├── BUILD_PLAN.md           ← this file
└── images/
    ├── 01-basic-stance.jpg     ✅ EXISTS
    ├── 02-mini-squats.jpg      ✅ EXISTS
    ├── 03-wide-stance.jpg      ✅ EXISTS
    ├── 04-march-in-place.jpg    ✅ EXISTS
    ├── 05-calf-raises.jpg      ✅ EXISTS
    ├── 06-squats.jpg           ✅ EXISTS
    ├── 07-lunges.jpg           ✅ EXISTS
    ├── 08-push-ups.jpg         ✅ EXISTS
    ├── 09-side-plank.jpg       ✅ EXISTS
    ├── 10-glute-bridge.jpg      ✅ EXISTS
    ├── 11-single-leg-stand.jpg  ✅ EXISTS
    ├── 12-squat-hold.jpg       ✅ EXISTS
    ├── 13-bird-dog.jpg         ✅ EXISTS
    ├── 14-side-step-up.jpg      ✅ EXISTS
    ├── 15-knee-lift-balance.jpg ✅ EXISTS
    ├── 16-forward-fold.jpg      ✅ EXISTS
    ├── 17-hamstring-stretch.jpg ✅ EXISTS
    ├── 18-quad-stretch.jpg      ✅ EXISTS
    ├── 19-hip-flexor-stretch.jpg ✅ EXISTS
    └── 20-chest-opener.jpg      ✅ EXISTS
```

---

## Resume Instructions

If this build crashes or gets interrupted:
1. **Read this file FIRST** — it has everything you need
2. Check which steps are marked ✅ vs NOT STARTED
3. **All 20 images are already extracted** — do NOT re-extract or re-crop them
4. **Do NOT use the image model to "verify" the images** — it times out
5. Pick up at whatever step is incomplete
6. The stretch app at `/Users/billtaylor/.openclaw/workspace-babbage/stretch-app/index.html` is the reference template
7. The poster image is at `tmp/vibration_analysis/poster_full.jpg` if you need to re-read exercise details