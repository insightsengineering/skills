# test-module: <X> - <BRANCH A> vs <BRANCH B>

## Summary

- Module output (<BRANCH A> vs <BRANCH B>): Same / Different / Unable to verify - <one-line reason>.
- Teal report preview (expanded cards): Same / Different - <one-line reason>.
- Show R Code / reproducibility: Identical / Different - <one-line reason>.
- Should code paths yield same results?: Yes / No / Unclear.
- Final status: Passed / Failed / Needs manual review.

## Screenshots

| Branch | Module output | Show R Code modal | Report -> Show Report |
|---|---|---|---|
| <BRANCH A> | ![](../.test-module-screenshots/<X>-<branch-a-slug>-module-output.png) | ![](../.test-module-screenshots/<X>-<branch-a-slug>-show-r-code.png) | ![](../.test-module-screenshots/<X>-<branch-a-slug>-report-show-report.png) |
| <BRANCH B> | ![](../.test-module-screenshots/<X>-<branch-b-slug>-module-output.png) | ![](../.test-module-screenshots/<X>-<branch-b-slug>-show-r-code.png) | ![](../.test-module-screenshots/<X>-<branch-b-slug>-report-show-report.png) |

## Show R Code files

- <BRANCH A>: .test-module-reports/<YYYY-MM-DD>-<X>-<branch-a-slug>-show-r-code.R
- <BRANCH B>: .test-module-reports/<YYYY-MM-DD>-<X>-<branch-b-slug>-show-r-code.R

## Show R Code diff

```diff
<Paste unified diff of branch A vs branch B Show R Code files>
```

---

## Appendix: example app source (@examples)

### Branch <BRANCH A>

```r
<Paste @examples code used to launch app on BRANCH A>
```

### Branch <BRANCH B>

```r
<Paste @examples code used to launch app on BRANCH B>
```

### Notes on the appendix

- Obtain code with git show <branch>:R/<filename>.R and extract only the @examples block, or read man/<X>.Rd on each worktree after devtools::document() if needed.
- The appendix is the example used to launch the app, not necessarily the same strings as Show R Code inside the running app.
