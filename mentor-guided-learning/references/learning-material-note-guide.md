# Deep Learning Note Writing Guide (Learning-Material Projects)

Use this guide when writing chapter-level deep learning notes for `learning-material` projects.

Benchmark style:
- Follow the structure, clarity, and detail level of a strong chapter exemplar note (for example, a linear regression chapter note).

## Note Structure

Create `notes-zh.md` (or the user's language equivalent) in each chapter directory with two parts:

1. Principle Section
2. Code Implementation Section

## Part 1: Principle Section

Goal:
- Integrate intuitive explanation and mathematical formulas into one coherent, self-contained narrative.
- For each concept: explain intuition and motivation first, then give the precise formula, then explain symbols and meaning.

Writing style:
- Explain as if to a smart friend, not in cold textbook style.
- Prefer vivid, plain phrasing that reduces cognitive load.

Concept rhythm:
1. Intuition first: what it does and why it is needed (use concrete scenarios/analogies).
2. Formula next: precise LaTeX expression.
3. Formula explanation: immediately follow with a blockquote explaining symbols and operation meaning.
4. Context next: why this form, links to nearby concepts, and design tradeoffs.

Formula rules:
- Use LaTeX for formulas.
- Number every non-trivial formula with `\tag{X.Y}` (X = section index, Y = increasing index inside section).
- After each non-trivial formula, add a blockquote with symbol-level explanation.
- Cross-reference formulas by number when needed (for example, "see Eq. (1.3)").

Must-cover topics:
- What the model does and why it is designed this way.
- What the loss measures and why this loss is chosen.
- Gradient intuition (direction and magnitude guidance for parameter updates).
- What happens at each optimization step.
- Causal chain across concepts (why A leads to B, and why B requires C).

Numerical walkthrough:
- After the core formula chain, include a small end-to-end numeric example (forward pass -> loss -> gradient) so formulas map to concrete values.

Comparison and analysis:
- Explain differences across methods, when to use each, and tradeoffs.
- Use concise comparison tables where helpful.

Section ending:
- Include a key concept summary table covering model, loss, gradient, optimization, and related dimensions.
- Optionally include a "next chapter preview" to connect chapter flow.

Visuals:
- Use Mermaid (`npx @mermaid-js/mermaid-cli`) to generate flowcharts.
- Save images under `img/`.
- Use HTML image tag with constrained size:
  - `<img src="img/xxx.png" alt="desc" style="max-width:640px; max-height:600px;">`

## Part 2: Code Implementation Section

Focus:
- Notebook-aligned code implementation and API usage.
- More code, fewer formulas.
- Comparison of from-scratch implementation vs framework-concise implementation.
- Key code snippets with concise annotations.
- Common pitfalls and cautions.

## Reference Linking

- At the top of the note, list chapter-related notebook links.
- For each subsection, attach the related notebook using a `<sub>` link.

## Constraints

- Do not modify source `.ipynb` notebooks; treat them as read-only references.
- Only create or edit note files and related images/assets for note documentation.
