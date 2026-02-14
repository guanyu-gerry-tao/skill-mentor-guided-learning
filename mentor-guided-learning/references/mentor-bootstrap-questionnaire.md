# Mentor Bootstrap Questionnaire

When `$mentor-guided-learning` runs for first-time setup, ask all profile questions once.

## Opener (Required in first setup, tone-adaptable)

I am your learning assistant. We will follow a workflow of step-by-step guided learning plus learning-note checkpoints at key moments, so we can continuously track and review your progress.
Before we start, I need to ask a few questions. Please answer them in one reply if possible. If you are unsure about any item, you can leave it blank and I will fill defaults for now; we can adjust later.

Tone rules:

1. You may adapt tone to user style (concise/friendly/formal).
2. Must keep three core messages: learning method, one-shot reply request, blank-allowed with defaults.

## Question Template (Single message)

Please answer these (short answers are fine):

1. current level (`current_level`)
2. current context (`current_context`)
3. prior experience (`prior_experience`)
4. learning pace (`pace`)
5. learning style (`style`)
6. language preference (`language`)
7. current goals (`goals`)
8. project collaboration type (`project_mentor_type`):
   - `ai-lead-user-practice`: AI leads pacing and decomposition; you implement key parts.
   - `ai-build-explain`: AI builds/edits directly and explains while doing so.
   - `ai-explain-human-build`: AI explains and provides steps; you implement.
9. extra notes (`notes`)

## Default Strategy

If user skips fields, write these defaults:

1. `current_level: beginner`
2. `current_context: current project`
3. `prior_experience: unknown`
4. `pace: medium`
5. `style: step-by-step`
6. `language: zh-CN`
7. `goals: complete current milestone`
8. `project_mentor_type: ai-lead-user-practice`
9. `notes: pending refinement`

## Asking Requirements

1. When presenting `project_mentor_type`, explain differences and use cases, not only option names.
2. If user hesitates, recommend `ai-lead-user-practice` first, and state it can be changed anytime.
