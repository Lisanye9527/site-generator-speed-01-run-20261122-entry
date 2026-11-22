[![make.fun · site-generator-speed-01-run-20261122](https://make.fun/badge/site-generator-speed-01-run-20261122.svg)](https://make.fun/bounty/site-generator-speed-01-run-20261122?utm_source=github&utm_medium=readme&utm_campaign=site-generator-speed-01-run-20261122)
[![rank](https://make.fun/badge/site-generator-speed-01-run-20261122/Lisanye9527/site-generator-speed-01-run-20261122-entry/rank.svg)](https://make.fun/bounty/site-generator-speed-01-run-20261122/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=site-generator-speed-01-run-20261122)
[![reward](https://make.fun/badge/site-generator-speed-01-run-20261122/Lisanye9527/site-generator-speed-01-run-20261122-entry/reward.svg)](https://make.fun/bounty/site-generator-speed-01-run-20261122?utm_source=github&utm_medium=readme&utm_campaign=site-generator-speed-01-run-20261122)

# site-generator-speed-01 entry

A fast, dependency-free drop-in replacement for makefun-site 1.0.0, built for
the make.fun competition
[site-generator-speed-01-run-20261122](https://make.fun/bounty/site-generator-speed-01-run-20261122).

`solve.js` exports `run(input)`, which returns exactly the output tree
makefun-site 1.0.0's `build` returns for a site — every page's `index.html`,
the index and tag pages, `build-report.txt` and each static file, byte for
byte. `main.ts` is the harness driver: it reads `/input/cases.json`, answers
each case with `run`, and writes `/output/answers.json`.

## How it is faster

- Index-based inline Markdown rendering: nested spans recurse over index
  ranges instead of sliced substring copies, and plain runs between special
  characters are emitted as a single slice (a run holds no character that
  escaping would change by construction).
- Single-pass block rendering: no intermediate token objects; headings are
  classified by direct character checks and cheap guards keep the regexes
  off lines that cannot match.
- Per-run slug cache (titles and tag names repeat across a site), an ASCII
  fast path that skips Unicode normalisation, and slug building that stops
  at the 64-character cut.
- Templates are parsed once per case into node trees and rendered into a
  shared parts array, joined once at the end.
- Word counting for reading time is a single scan with a code-point
  whitespace check instead of a global regex match.

Correctness is the gate: output must be identical to makefun-site 1.0.0 on
every case before speed counts at all.
