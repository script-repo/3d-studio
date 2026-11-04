---
name: teardown-simulator
description: Use when the user wants any machine or mechanism (real or imagined) turned into an interactive, offline, single-page 3D simulator they can take apart — a cutaway, exploded view, "how does X work" explainer, drivable model, or a recorded film of one with a scored soundtrack. Runs the teardown Claude Code skill.
---

# Teardown simulator

One machine, one offline HTML page: a code-built model in real units, one solver as pure functions of one input, live panels, a button per subsystem, an auto tour, a critic pass, a recorded 60 fps film with a scored soundtrack, and an optional publish step.

## Rules

- **Reference first.** Before building, ask for or find a cutaway, photo or clip. No reference, no build.
- **Never invent numbers silently.** Every number is sourced or flagged as an estimate, with where it came from.
- **Confirm scope first.** Heavy builds use a lot of Claude Code usage: agree on machine, beats, film or no film.
- **Publishing needs an explicit yes, every time.**
- On the host: user-level only, no sudo, leave cluster services alone, long renders in tmux with logs.

## Pick a good machine

One moving input (crank angle, stroke 0..1, wind, time) and real published numbers. Good starters: bicycle derailleur, watch escapement (4 Hz), car differential, sewing-machine lockstitch, excavator arm. Avoid machines with many independent inputs or secret dimensions.

## Simulator anatomy

1. Machine — built from code in real units. 2. Model — one solver, pure functions of one input. 3. Panels — live charts from the same model. 4. Buttons — isolate/cut/show-alone per subsystem, views, sliders. 5. Window — the floor/stage shows what the solver says. 6. Tour — auto cursor tour; any input hands control; 25 s quiet hands it back.

## Install and run (on <host>)

```
npx skills add elvarofficial/teardown -g -a claude-code
cd ~/.claude/skills/teardown && npm install && npx playwright install chromium
# needs Node >= 20, ffmpeg, python3
mkdir -p ~/3d-studio/{projects,out,logs,smoke}
cd ~/3d-studio/projects/<machine>
tmux new -s <machine> 'claude 2>&1 | tee ~/3d-studio/logs/<machine>.log'
# inside Claude Code: /teardown ...   (never --dangerously-skip-permissions)
```

Smoke test: `node ~/.claude/skills/teardown/sims/build.mjs cessna-live` and open the HTML from `teardown-out/dist/`.

## Workflow (swap the [brackets])

0. Brief: fill templates/brief.md (reference, single input, real numbers, parts) and confirm scope.

### 1. Build

```
/teardown Build [the Cessna 337 main landing gear] as an interactive 3D simulator. Reference attached: [a cutaway drawing of the gear]. Build it from code in real units: [parts]. Before you draw anything, write the model as pure functions of one input: [stroke from 0 to 1]. Use the real numbers: [rack travels 201 mm, pinion 32 teeth at module 4 mm, legs turn exactly 180°]. Check them by hand and show me the check. If you are not sure a number is real, say so and tell me where it comes from. Every part, every panel and every label reads from that one model. Nothing is animated by hand.
```

### 2. Console

```
Lay out a console around the machine at 1600×900: the title and the main live number top left, big: [engine speed in rpm]. Live charts that move with the machine: [valve lift over 720° of crank, PV loop]. A schematic panel that redraws the mechanism in 2D from the same model. One button per subsystem: isolate, cut, show alone. Camera views as buttons: [hero, front, side, top, inside]. Callouts with thin leader lines. Every panel redraws from the frame's state. No number on screen is typed in.
```

### 3. Look

```
Style: a clean study model on a pale studio. Light background, soft shadows on an invisible floor, one accent colour for the part working now: [accent colour]. The machine fills 60–70% of the view. Dark parts glossy, liners glass, metal roughness at least 0.45. Near-white parts get a thin ink outline or a darker ground. Pick the camera for the mechanism: [3/4 view] if face-on collapses into a lattice.
```

### 4. Critic

```
Before you show me anything: 1. Render a still at every beat of the tour and read each one for: a part passing through another, a part floating off its mount, a label covering what it names, a number disagreeing with the pose. 2. Scrub every transition at 0.2 s steps for jumps. 3. Fix collisions with a number in the parameter block, never hide them with the camera. Tell me what you checked and what you fixed. Show me the stills.
```

### 5. Drive

```
The tour plays by itself with a cursor pressing the buttons: [tour beats]. Any click, drag, scroll or key hands control to the visitor; 25 s of quiet hands it back. Drag to orbit, scroll to zoom, [key] hides the panels. Warm every shader before the first frame. Test on an integrated GPU with quality steps: no AO, lower pixel ratio, smaller shadows, panels every other frame.
```

### 6. Record

```
Make render(t) a pure function of time over [20 s]. Draw every frame at 60 fps, 1920×1080 in headless Chrome, and pipe it to ffmpeg as H.264 yuv420p with faststart. Give me a contact sheet of every beat.
```

### 7. Score

```
Score it in code on the same timeline: [Am9 chords at 92 bpm, a thump on each power stroke, a click per button press, rising air under the throttle drag]. Master to -14 LUFS, true peak under -1 dB, and mux it into the video.
```

### 8. Publish

```
Get the HTML under 1 MB (large models go in sibling files). Make a 1200×630 preview card from the best frame, with absolute image URLs. Deploy to [Vercel / host] only after I say yes.
```

### 9. Break it (bonus)

```
Add one slider for [wind 0–15 m/s] and run the model live with it; show the effect on the same panels.
```

## Lessons

- Keep the page under 1 MB: a 15.9 MB page killed the X link card; 967 KB fixed it.
- Integrated GPUs: quality steps (no AO, lower pixel ratio, smaller shadows, panels every other frame) took one build from 15 fps to 75 fps.
- Stills catch collisions that motion hides. Always run the critic pass.
- Fix collisions in the parameter block, never with the camera.

## Deliverable

Folder with: `<machine>.html` (offline, <1 MB), critic report (templates/critic-report.md) with stills, contact sheet, `<machine>.mp4` (60 fps, -14 LUFS), the model's hand-check of numbers with sources/flags, and a preview card if publishing.

## Credits

Workflow and prompts from Elvar (@elvar_official), "How I Turn Any Machine Into a 3D Simulator You Can Take Apart" (X, 2026-10-30). Runs Elvar's teardown skill, github.com/elvarofficial/teardown (MIT), built on whaleyxbt/claude-motion (MIT).
