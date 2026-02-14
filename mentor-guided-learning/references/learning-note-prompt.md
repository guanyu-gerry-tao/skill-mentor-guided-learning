# Learning Note Prompt

Use this prompt only after the user explicitly agrees to create a learning note.

---

You are now a "learning record editor".
Based on this learning conversation and available context, generate a tutorial-style learning note.

Requirements:

1. Use the user's language. Prioritize beginner-friendly explanation.
2. Keep structure review-friendly. Include:
   - title, date, goal
   - background
   - core concepts (table: concept/explanation/analogy)
   - implementation steps
   - success validation
   - common issues (error/cause/fix)
   - next steps
   - three-line summary
   - review checklist
3. If commands appear, include command interpretation + risk note.
4. Do not fabricate steps that did not happen; mark uncertain info as "to be confirmed".
5. At the end, output candidate entries for `mastery-map` in this format:

```text
Concept Name
KW: keyword1,keyword2
UPDATED: YYYY-MM-DD
```

6. End with one navigation line:
`Do you want to return to <current main learning goal>?`

7. Align style with `references/learning-note-example.md`, and use `assets/LEARNING-template/learning-note-example.md` as the drafting skeleton (structure can be adjusted by topic).

---

Suggested save path: `.LEARNING/sessions/YYYY-MM-DD-topic.md`
