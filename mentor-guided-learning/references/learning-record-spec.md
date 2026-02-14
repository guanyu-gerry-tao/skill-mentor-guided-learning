# Learning Record Spec

Used to maintain `.LEARNING/` records and reusable prompt snippets.

## mastery-map.md Spec (Mermaid Kanban)

`mastery-map.md` uses Mermaid Kanban only.

Status columns:

1. `future`
2. `soon`
3. `learning`
4. `mastered`

Card format:

```text
Concept Name
KW: keyword1,keyword2
UPDATED: YYYY-MM-DD
```

## sessions/ Spec

Each learning session creates one file:
`.LEARNING/sessions/YYYY-MM-DD-topic.md`

Required sections:

1. title/date/goal
2. background (plain-language terms)
3. core concepts (prefer concept/explanation/analogy table)
4. implementation steps
5. validation result
6. common issues (error/cause/fix)
7. next steps

Optional sections:

1. project structure overview
2. key file notes (snippet + explanation)
3. glossary
4. references

Writing requirements:

1. Explain concepts before code/commands.
2. Every command must include interpretation and risk note.
3. Next steps must be actionable.
4. For `personal-project` notes, style can align with `references/project-note-example.md`.

## learner-profile Key Field

Maintain and read this for project learning:

`project_mentor_type` (pick one)

1. `ai-lead-user-practice`: AI leads, user implements key parts
2. `ai-build-explain`: AI builds and explains
3. `ai-explain-human-build`: AI explains, human builds

## Profile Update Rules

When user requests profile changes:

1. Read current `.LEARNING/learner-profile.md`
2. Update only explicitly requested fields
3. Keep all other fields unchanged
4. If `project_mentor_type` changes, sync project-root `AGENTS.md` collaboration strategy
5. Record `last_updated`

## Concept Sedimentation Prompt Snippet

After each key concept, generate minimal sedimentation using this snippet:

```text
For the concept we just finished, output:
1) one-line definition
2) one analogy
3) one minimal example
4) one common pitfall
5) KW (2-5)
6) mastery-map candidate card (in required format)
Use only current conversation context. Do not fabricate missing facts.
```
