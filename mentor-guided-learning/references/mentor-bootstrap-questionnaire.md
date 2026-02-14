# Mentor Bootstrap Questionnaire

When `$mentor-guided-learning` runs for first-time setup, ask all profile questions once.

## Opener (Required in first setup, tone-adaptable)

Before we start, I need to ask a few questions. Please answer them in one reply if possible. If you are unsure about any item, you can leave it blank and I will fill defaults for now; we can adjust later.

Tone rules:

1. You may adapt tone to user style (concise/friendly/formal).
2. Use the user's current language, and keep the opener friendly.
3. Must keep three core messages: learning method, one-shot reply request, blank-allowed with defaults.

## Question Template (Single message)

Please answer these (short answers are fine):

1. current level (`current_level`)
2. prior experience (`prior_experience`)
3. learning pace (`pace`)
4. learning style (`style`)
5. language preference (`language`)
6. current goals (`goals`)
7. project collaboration type (`project_mentor_type`) [ask only when `project_kind = personal-project`]:
   - `ai-lead-user-practice`: AI leads pacing and decomposition; you implement key parts.
   - `ai-build-explain`: AI builds/edits directly and explains while doing so.
   - `ai-explain-human-build`: AI explains and provides steps; you implement.
8. extra notes (`notes`)

Do not ask `current_context` directly in the questionnaire.
Infer it from the pre-setup project scan, and let the user correct it if needed.

## Default Strategy

If user skips fields, write these defaults:

1. `current_level: beginner`
2. `current_context: inferred from project scan (fallback: current project)`
3. `prior_experience: unknown`
4. `pace: medium`
5. `style: step-by-step`
6. `language: zh-CN`
7. `goals: complete current milestone`
8. `project_mentor_type:`
   - `ai-lead-user-practice` (if `project_kind = personal-project`)
   - `ai-explain-human-build` (if `project_kind = learning-material`)
9. `notes: pending refinement`

## Asking Requirements

1. Before questionnaire, do a short project scan and classify `project_kind` (`personal-project` or `learning-material`).
2. When presenting `project_mentor_type`, explain differences and use cases, not only option names.
3. If user hesitates in `personal-project`, recommend `ai-lead-user-practice` first, and state it can be changed anytime.
4. If `project_kind = learning-material`, do not ask `project_mentor_type`; use default and continue.
