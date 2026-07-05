<!-- Japanese version: v1-initial-output-axe-violations.ja.md -->

# Axe violations present in the AI's initial output (v1) — quantitative data

- Target commit: `5e1be2c` (v1: initial implementation of the screen-reader
  experience simulator, 2026-07-06T02:01:48+09:00)
- Generating model: Claude Fable 5
- Audit tool: axe-core 4.12.1 / chrome-headless (Chrome 149.0.7827.201,
  ChromeDriver 149.0.7827.155)
- Fix commit: `2d62bd6` (v1-audit, 2026-07-06T02:25:23+09:00)
- How this document was produced: the v1-era file was restored with
  `git show 5e1be2c:index.html`, served again on a separate port from the
  working tree, and axe was actually re-run against it to measure the
  figures below (these are based on a re-run performed while writing this
  document, not a summary recalled from memory or chat logs).

## Conclusion (summary)

| Audit method | Violations found |
|---|---|
| CLI single-pass audit via `npx axe <url>` (initial load only) | **0** |
| Audit of each of the 5 states that reproduce real interaction | **1 rule type × 3 states = 3 instances** |

The simple, one-shot CLI audit reported 0 violations, but this was a false
negative. An element that only enters the DOM once the user navigates into
the simulator screen carried one unresolved landmark violation.

## The 5 states verified, and their results

| # | State | Violations | Rule ID | Impact |
|---|---|---|---|---|
| 1 | Intro screen (initial load) | 0 | — | — |
| 2 | Simulator / BEFORE / blurred | 1 | `region` | moderate |
| 3 | Simulator / BEFORE complete, revealed | 1 | `region` | moderate |
| 4 | Result screen | 0 | — | — |
| 5 | Simulator / BEFORE / English display | 1 | `region` | moderate |

The same rule's violation was detected in 3 of the 5 states verified
(60%). One unique rule type (`region`).

## Violation detail

- **Rule**: `region` — "All page content should be contained by landmarks"
- **Affected element**: `<p class="bar-label">読み上げ字幕</p>` (the label
  on the fixed caption bar at the bottom of the screen; reads "Speech
  captions" in English display)
- **Selector**: `.bar-label`
- **Tags**: `cat.keyboard`, `best-practice`, `RGAAv4`, `RGAA-9.2.1`
- **helpUrl**: https://dequeuniversity.com/rules/axe/4.12/region?application=axeAPI

### Root cause

The fixed caption bar (`#captionbar`) itself had no landmark role, so the
"読み上げ字幕" (Speech captions) text inside it existed in the DOM without
belonging to any landmark (`header`/`main`/`nav`/`footer`/`role="region"`,
etc.).

### Why the CLI's initial-load audit missed it

The caption bar is hidden on the intro screen via the `hidden` attribute.
It only enters the DOM once the user clicks "Start the experience" and
navigates to the simulator screen, at which point JS removes `hidden`.
Because axe excludes hidden elements from evaluation, a CLI audit that
only looks at a single URL's initial load
(`npx axe http://localhost:3000 --exit`) never brought this element into
scope.

## The fix (commit `2d62bd6`)

```diff
-<div class="captionbar" id="captionbar" hidden>
+<div class="captionbar" id="captionbar" role="region" aria-label="読み上げ字幕" data-i18n-arialabel="captionLabel" hidden>
```

In addition, a few lines were added to `renderLang()` to generically
process the `data-i18n-arialabel` attribute, so `aria-label` also follows
along on language switch.

After the fix, the same 5 states were re-audited and **0 violations in
every state** was confirmed.

## What this case suggests

1. **A single CLI audit produces false negatives on SPA-style UI.**
   Elements that only enter the DOM on a state transition are missed by an
   audit that only looks at the initial load. In this case the intro
   screen alone reported 0, so without explicit multi-state verification
   an "axe violations: 0" claim could have been reported incorrectly.
2. **This violation is unrelated to the "five BEFORE-mode defects" the
   demo intentionally reproduces** (focus order, missing aria-label,
   unreachable seek bar, unconveyed state, mechanical alt text). Those
   mock elements carry `aria-hidden="true"` and are never exposed to
   assistive technology or to axe in the first place — they are simulated
   content. What axe caught here was an actual, unintended defect in the
   simulator's own "chassis" (the real-DOM live-region plumbing).
3. **The "axe violations: 0" that CLAUDE.md requires was not met at the
   point of the AI's initial generation (v1).** The defect only became
   visible once multiple states were explicitly exercised, and it was
   resolved in one additional fix commit.

## Reference: raw data (excerpt of axe-core output)

<details>
<summary>Full violations payload for state 2 (BEFORE, blurred, ja)</summary>

```json
{
  "id": "region",
  "impact": "moderate",
  "help": "All page content should be contained by landmarks",
  "helpUrl": "https://dequeuniversity.com/rules/axe/4.12/region?application=axeAPI",
  "tags": ["cat.keyboard", "best-practice", "RGAAv4", "RGAA-9.2.1"],
  "nodes": [
    {
      "target": ".bar-label",
      "html": "<p class=\"bar-label\" data-i18n=\"captionLabel\">読み上げ字幕</p>",
      "failureSummary": "Fix any of the following:\n  Some page content is not contained by landmarks"
    }
  ]
}
```

</details>
