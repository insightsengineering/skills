---
name: test-modules-after-migration
description: >-
  Compares a teal package module example on BRANCH A vs
  BRANCH B: waits for outputs, screenshots module Show R Code
  modal and Report preview (with reporter cards expanded), writes a markdown
  summary comparing module output, report content, and reproducible code. Use
  when the user invokes test-module or wants branch parity checks for tm_*
  modules with screenshots and a written report file.
disable-model-invocation: true
metadata:
  author: Nest CoreDev
  version: "0.1"
---

# test-module — branch comparison for one module (`X`)

## Input

- **`X`** — Module name: the exported `tm_g_*` function (e.g. `tm_g_butterfly`). Use it to locate `R/` source, roxygen `@examples`, and `tests/testthat/` files.
- **`PACKAGE_NAME`** — Exact package directory name provided by the user (e.g. `teal.somepackage`).

## Branches (defaults — override if the user specifies otherwise)

- **Branch A — `main`**
- **Branch B — user-provided feature branch** (confirm it exists locally or on `origin` and fetch if needed.)

## Repository

Use the **`PACKAGE_NAME`** package root (where `DESCRIPTION` and `R/` live). If the skill runs from a monorepo, `cd` into that package directory first.

## Goals (verbatim checklist from the user)

For **`X`**, compare the **module example** on **BRANCH A** and on **BRANCH B**. Run the Shiny apps from those two branches and determine:

1. If you get the **same code** when you click **Show R Code**.
2. Whether you are **able to add a card to the report**.
3. Whether the module is **presenting the same information**.
4. Whether you can **play with the encoding** so the module shows **different output**, and whether the **same change in encodings** for the two implementations **behaves the same**.
5. Capture **screenshots** of **both** apps as specified below **and** write the **markdown summary** (including both example code blocks at the end).

## Recommended git approach

Prefer **two git worktrees** (one per branch) so both apps can run without losing local work, or **sequential checkouts** after `git stash push`. Never force-push or change git config.

```bash
PACKAGE_NAME="<package-name-provided-by-user>"
BRANCH_A="main"
BRANCH_B="<feature-branch>"

git fetch origin "$BRANCH_A" "$BRANCH_B" 2>/dev/null || true
git worktree add "../${PACKAGE_NAME}-${BRANCH_A//\//-}" "$BRANCH_A"
git worktree add "../${PACKAGE_NAME}-${BRANCH_B//\//-}" "$BRANCH_B"
```

## Running the example app (each branch)

1. `cd` to package root on that branch; ensure dependencies (`devtools::install_deps()`, `pkgload::load_all()`).
2. Run the **`@examples`** for **`X`** (`example(X, local = TRUE)`, temp script from roxygen, etc.). Avoid setting env var **`SHINY_PORT`** (it confuses Shiny); pass **`port`** only inside **`shiny::runApp(app, host = "0.0.0.0", port = …)`**.
3. Use distinct ports (e.g. **3838** = BRANCH A, **3840** = BRANCH B).

## Waiting before screenshots (required)

Do **not** screenshot the module while a busy state is visible (e.g. toast **“Computing…”**, **“Loading data…”**, or an empty output panel still pending).

- **Browser / MCP**: After navigation, **wait** until those indicators **disappear** and the **primary output** (table, plot, etc.) is **visible** (or a clear empty-state message replaces loading). Retry with short polls (1–3 s) rather than one long blind wait.
- **Programmatic (preferred for reliability)**: Use **`shinytest2::AppDriver`** / **`teal::TealAppDriver`** (see **`tests/testthat/helper-TealAppDriver.R`** and **`init_teal_app_driver`**) and call **`$wait_for_idle()`** (and/or **`$wait_for_js()`** on a selector for the rendered output) before any screenshot.

Only then take **module output** screenshots (see below).

## Screenshot set (each branch — repeat for BRANCH A and BRANCH B)

Use **browser MCP** or **headless Chrome** only if you can satisfy **wait** rules; otherwise prefer **TealAppDriver** + screenshot API where available.

| Step | What to capture |
|------|-----------------|
| **A. Module with output** | Full viewport: **encoding panel + rendered output** after computing **finished**. |
| **B. Show R Code modal** | Open **Show R code**; wait until the **modal** is visible (header typically **“Show R Code”**). Screenshot the **modal** (code area legible). **Copy** the modal’s R code text for the markdown appendix. |
| **C. Report → Show Report** | Add a card (**+ Add to Report**), open **Report → Show Report** / report preview (`#teal-preview_report-preview_button` or equivalent). **Expand every collapsed reporter accordion / teal card** (e.g. click **`.accordion-button.collapsed`** inside `#teal-preview_report-preview_content-reporter_cards`) so **card body text and table fragments are visible**, not just collapsed headers. Wait for paint, then screenshot. |

**Naming files** (example pattern — stay consistent):

- `<PACKAGE_NAME>/.test-module-screenshots/<X>-<branch-a-slug>-module-output.png`
- `<PACKAGE_NAME>/.test-module-screenshots/<X>-<branch-a-slug>-show-r-code.png`
- `<PACKAGE_NAME>/.test-module-screenshots/<X>-<branch-a-slug>-report-show-report.png`
- Same triple for BRANCH B (e.g. `<X>-<branch-b-slug>-module-output.png`, …).

Branch slug guidance: replace `/` with `-` and keep the branch label readable (e.g. `main`, `redesign_extraction@main`, `feature-abc-123`).

Optional: repeat **A** after a **encoding** change (same change on both branches) for parity checks.

## Markdown summary file (required)

Create **one** markdown file per run, for example:

**`<PACKAGE_NAME>/.test-module-reports/<YYYY-MM-DD>-<X>-<branch-a-slug>-vs-<branch-b-slug>.md`**

Also create a **GitHub comment draft markdown** file, for example:

**`<PACKAGE_NAME>/.test-module-reports/<YYYY-MM-DD>-<X>-gh-comment.md`**

Treat these as two required output assets with separate templates:

1. Full technical report (detailed, includes appendix and diff).
2. GitHub comment draft (concise PR-ready summary linking to report and artifacts).

Use template files from the skill assets directory:

- `assets/report-template.md` for the full report.
- `assets/github-comment-template.md` for the GitHub comment draft.

Programmatic runs should, where possible, **compare captured text** (module output region, expanded report card region, **Show R Code** modal) between branches after **whitespace normalization**. State clearly:

1. **Module output** — Whether the **same** rendered output (table/plot text) appears for the **same default encodings** (Same / Different / Unable to verify), with a one-line reason.
2. **Teal report content** — Whether **report preview** text (expanded cards) is the **same** or **different** (Same / Different).
3. **Code & reproducibility** — Whether **Show R Code** text is **identical**; if not, whether it is still **plausibly the same analysis** (e.g. same `ANL` rows and same model call) vs **materially different** pipelines. Say whether the two code paths **should produce the same numerical results** given identical inputs, or **not** (e.g. different merge/filter code).

Populate placeholders in `assets/report-template.md` and write to:

- `<PACKAGE_NAME>/.test-module-reports/<YYYY-MM-DD>-<X>-<branch-a-slug>-vs-<branch-b-slug>.md`
## Report back (chat)

Populate placeholders in `assets/github-comment-template.md` and write to:

- `<PACKAGE_NAME>/.test-module-reports/<YYYY-MM-DD>-<X>-gh-comment.md`

In the user’s chat message, give short pointers to:

1. Full report path.
2. GitHub comment draft path.
3. Screenshot paths.
4. One-paragraph summary (do not duplicate full appendix unless asked).