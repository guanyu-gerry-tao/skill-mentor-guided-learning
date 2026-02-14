# Mentor Guided Learning Skill

A project-level mentoring *skill* for Codex that helps learners move from idea to implementation with structured guidance, progress tracking, and optional learning-note consolidation.

## What This Is

This repository contains a reusable skill package centered on `mentor-guided-learning`.
It is designed for ongoing learning workflows in real projects, not one-off Q&A.

> [!TIP]
> What is a skill? 
> A skill is a modular unit of functionality that can be invoked in Codex or similar agents.
> Watch video [Agent Skills Explained: Why This Changes Everything for AI Development](https://www.youtube.com/watch?v=Ihoxov5x66k) for an introduction to skills.

## What It Can Do

- Bootstrap a learning workspace for a project (`.LEARNING`, profile, mastery tracking, AGENTS runtime block)
- Continue learning sessions based on prior progress instead of restarting from scratch
- Support different collaboration modes in project learning:
  - AI leads, user practices key logic
  - AI builds in steps and explains continuously
  - AI explains, human builds
- Guide milestone completion and optionally consolidate into learning notes
- Keep runtime prompts concise via progressive disclosure (`references/` + `assets/`)

## Use the Skill

Use this skill in CLI-first coding agents such as:

- CodeX
- Claude Code
- Gemini CLI (when local/custom skill loading is supported)

Currently not supported in:

- ChatGPT app
- Mobile app clients

Invoke with typing below in the chat:

```text
$mentor-guided-learning
```

## Installation

Choose one method below.

### Method 1: Skills CLI

If you have node, use `Terminal` and run:

`npx skills add guanyu-gerry-tao/skill-mentor-guided-learning`

install with instruction.

> [!TIP]
> Check out [Downloading and installing Node.js and npm](https://docs.npmjs.com/downloading-and-installing-node-js-and-npm) to use `npx` if you can't run it.

### Method 2: Direct Download (Beginner Friendly)

1. Download this repository as ZIP from GitHub (`Code` -> `Download ZIP`).
2. Unzip it.
3. Copy the folder `mentor-guided-learning` to your Codex skills directory:
   - CodeX: `~/.codex/skills/mentor-guided-learning`
     - or `~/.agents/skills/mentor-guided-learning` 
   - Claude Code: `~/.claude-code/skills/mentor-guided-learning`
   - Gemini CLI: `~/.gemini/skills/mentor-guided-learning`
4. In the chat (not in ChatGPT or Gemini, instead, in CodeX or Claude Code), invoke once to confirm it is available:

```text
$mentor-guided-learning
```

## Validate (Optional)

If you have Python3 and the `skill-creator` skill installed, validate with:

```bash
python3 ~/.codex/skills/.system/skill-creator/scripts/quick_validate.py ~/.codex/skills/mentor-guided-learning
```

If not, you can also validate by invoking the skill and checking for any error messages in the response.


## Structure

```text
.agents/
└── skills/
    ├── mentor-guided-learning/
    │   ├── SKILL.md
    │   ├── agents/
    │   ├── references/
    │   └── assets/
    └── (other-skills/)
```

## Staring My Project!

⭐️⭐️⭐️

If you like this skill or you actually learnt something with this skill, please consider starring the repository to show your support!

⭐️⭐️⭐️

## Contributing

Contributions are welcome.

1. Fork this repository and create a feature branch.
2. Update files under `skills/mentor-guided-learning/`.
3. If you have `skill-creator`, run optional validation:
   `python3 ~/.codex/skills/.system/skill-creator/scripts/quick_validate.py ~/.codex/skills/mentor-guided-learning`
4. Open a pull request with change summary and example usage.

## License

MIT. See `LICENSE`.
