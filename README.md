# 3D Studio

A Grok Bot agent that turns any machine or mechanism — real or imagined — into a single-page, offline, drivable 3D simulator: a code-built model in real units, one solver driven by one input, live panels, a button per subsystem, an auto tour, a critic pass, a recorded film with a scored soundtrack, and an optional publish step.

It drives [Elvar's `teardown` Claude Code skill](https://github.com/elvarofficial/teardown) on a Linux host. This repo holds the agent's persona, skill, prompts and templates — not teardown's code.

## What's here

| Path | What |
|---|---|
| `AGENT.md` | Persona and operating rules |
| `skills/teardown-simulator/SKILL.md` | The Grok Bot skill: 9-step workflow, rules, lessons |
| `prompts/` | Elvar's 9 prompts as `[bracketed]` templates |
| `templates/brief.md` | Machine brief: reference, single input, real numbers, parts |
| `templates/critic-report.md` | Critic pass report |

## Recreate it in Grok Bot

1. Create a new agent named **3D Studio**; paste `AGENT.md` as its description. Replace `<host>` with your machine.
2. Add `skills/teardown-simulator/SKILL.md` as a skill.
3. Register the Linux host the agent will run Claude Code on.

## Install teardown (on the host)

```
npx skills add elvarofficial/teardown -g -a claude-code
cd ~/.claude/skills/teardown && npm install && npx playwright install chromium
```

Needs Claude Code, Node.js 20+, `ffmpeg` (video) and Python 3 (sound).

## Getting started

```
mkdir -p ~/3d-studio/{projects,out,logs,smoke}
node ~/.claude/skills/teardown/sims/build.mjs cessna-live   # smoke test
```

Then ask the agent: *"Build me a simulator of a watch escapement I can take apart."* It will ask for a reference, fill the brief, confirm scope, and run the prompts in order. Long builds run in tmux with logs; publishing only happens on your explicit yes.

## Credits

- Workflow and prompts: **Elvar** ([@elvar_official](https://x.com/elvar_official)), *How I Turn Any Machine Into a 3D Simulator You Can Take Apart* (X article, 2026-10-30).
- [elvarofficial/teardown](https://github.com/elvarofficial/teardown) (MIT), built on [whaleyxbt/claude-motion](https://github.com/whaleyxbt/claude-motion) (MIT).
- Persona ideas adapted from [msitarzewski/agency-agents](https://github.com/msitarzewski/agency-agents): technical-artist, gis-3d-scene-developer, data-visualization-engineer, visual-storyteller.

MIT licence — see `LICENSE`.
