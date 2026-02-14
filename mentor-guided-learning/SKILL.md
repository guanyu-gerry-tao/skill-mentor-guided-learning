---
name: mentor-guided-learning
description: "Project-level mentor bootstrap and run skill. Use when the user invokes `$mentor-guided-learning`: detect first-time use vs ongoing learning. On first-time use, set up all learning system files (`.LEARNING`, learner profile, project-root `AGENTS.md`, `.gitignore`). On ongoing learning, read `AGENTS.md` and `.LEARNING` and continue. Also use this skill when the user asks to update profile settings."
---

# Mentor Guided Learning

This skill is an installer + orchestrator.
Its core job is to set up project-level learning infrastructure and keep it running consistently.

## First-Time vs Ongoing Detection

Treat as first-time if any condition is true:

1. Project-root `.LEARNING/learner-profile.md` does not exist
2. Project-root `.LEARNING/mastery-map.md` does not exist
3. Project-root `AGENTS.md` does not exist, or is missing both `<!-- mentor-guided-learning:begin -->` and `<!-- mentor-guided-learning:end -->` markers

Otherwise treat as ongoing learning.

## First-Time Use: Required Setup

Execute in order:

1. Create project-root `.LEARNING/`.
2. Initialize from skill assets `assets/LEARNING-template/` into `.LEARNING/`:
   - `learner-profile.md`
   - `mastery-map.md`
   - `learning-note-example.md` (template)
3. Read `references/mentor-bootstrap-questionnaire.md`, output its opener (tone can adapt to user style, core points must remain), then ask the full learner profile questionnaire in one message; fill defaults for missing answers.
4. Write `assets/AGENTS-template.md` into project-root `AGENTS.md` (if file exists, update only the `mentor-guided-learning` marker block and do not overwrite unrelated content).
5. Update project-root `.gitignore` to include `.LEARNING/`.
6. Tell the user `.LEARNING/` is the learning system folder, now ignored by git, and remind them to back up important notes.
7. Switch into ongoing learning mode.

## Ongoing Learning: Direct Run

1. Read project-root `AGENTS.md` as the primary runtime rule.
2. Read `.LEARNING/learner-profile.md` and `.LEARNING/mastery-map.md`.
3. Continue the current milestone according to `AGENTS.md`.

## Learner Profile (Ask Once on First Setup)

Ask all questions in natural language in one single message.
Do not ask the user to fill a form; use bullet points.

Fields:

1. `current_level`
2. `current_context`
3. `prior_experience`
4. `pace`
5. `style`
6. `language`
7. `goals`
8. `project_mentor_type` (`ai-lead-user-practice` / `ai-build-explain` / `ai-explain-human-build`)
9. `notes`

Question requirements:

1. Do not list only option names; explain behavior and use cases for each `project_mentor_type`.
2. If user does not choose explicitly, default to `ai-lead-user-practice` and mention it can be changed anytime.

Defaults for missing fields:

1. `current_level: beginner`
2. `current_context: current project`
3. `prior_experience: unknown`
4. `pace: medium`
5. `style: step-by-step`
6. `language: user's current language`
7. `goals: complete current milestone`
8. `project_mentor_type: ai-lead-user-practice`
9. `notes: pending refinement`

Example:
```text
To support your learning effectively, I need a few details. Please answer all of these in one reply:
- What is your current level (current_level)? e.g. beginner, intermediate, advanced
- What is your current context (current_context)? e.g. the project or topic you are working on
- What prior experience do you have (prior_experience)? e.g. related topics or projects you have done
...
```

Tone can be adjusted (concise, friendly, formal), but all questions must be asked in one message.

## Profile Update Flow (When User Requests Changes)

1. Read current `.LEARNING/learner-profile.md`.
2. Update only fields explicitly requested by the user.
3. Keep unspecified fields unchanged.
4. If `project_mentor_type` changes, sync collaboration rules in project-root `AGENTS.md`.
5. Update `last_updated`.

## Resource Entry Points

1. Project runtime rules template: `assets/AGENTS-template.md`
2. Learning templates: `assets/LEARNING-template/`
3. First-time questionnaire reference: `references/mentor-bootstrap-questionnaire.md`
4. Learning record spec reference: `references/learning-record-spec.md`
