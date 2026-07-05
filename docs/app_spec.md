<!-- Japanese version: app_spec.ja.md -->

# Fictional Video Streaming Service — UI Specification

This document defines the UI specification for a fictional video streaming
service, used in the accessibility-remediation case study demo (`index.html`
in this repository).

**Top-priority requirement**: this service must not be confused with any
real video streaming service (Netflix, Hulu, Amazon Prime Video, Disney+,
U-NEXT, ABEMA, dアニメストア, etc.). Nothing — name, logo, color palette,
title, or advertiser — may evoke anything real.

---

## Step 1: Deciding the service name

> **Revision history (important)**: the first draft picked "Yorumado"
> based solely on a self-check of phonetic/spelling patterns. An actual web
> search then revealed that `yorumado.com` — a real "night job" listing
> site called "YORUMADO" — already exists. The combination of "yoru"
> (night) + "mado" (window, i.e. a service counter) happened to match the
> naming logic of a real job-listing service.
> **Lesson learned**: self-checking phonetic patterns alone is not
> enough — a meaningful Japanese compound can coincide with a real
> business even if it is a wholly invented word. Below, candidate names are
> re-selected only after actually web-searching them and confirming no
> real match exists.

### Candidates (existence verified via web search)

| Option | Service name | Origin | Existence check via web search |
|---|---|---|---|
| D | **Quorzana** | A purely phonetic coinage with no meaning (cannot be decomposed into any existing Japanese or English word) | Searched `"Quorzana"` alone and combined with "streaming/video/app/動画" — no exactly-matching company, service, or product was found, nor any close neighbor. |
| E | **Vindrelo** | A purely phonetic coinage with no meaning | No exact match, but a real **live video streaming platform** called `Vindral` is spelled and pronounced very close to it, and is in the same industry — judged too risky to adopt. |
| F | **Zaphileo** | A purely phonetic coinage with no meaning | No exact match, but several real video/streaming brands starting with "Zap-" exist (Zappiti, Zap.stream, Zappr, Zattoo) — the initial phonetic cluster is concentrated in the competing genre, judged risky. |

### Selected: Option D, "Quorzana"

**Rationale**:
1. Neither a plain search nor a search combined with streaming-industry context turned up any exactly-matching real company, service, or product (Options E and F were excluded because close real video/streaming brands were found).
2. It is a phonetic coinage that cannot be decomposed into any existing Japanese or English word, structurally avoiding the exact risk flagged in the Step 1 revision history — a meaningful compound accidentally matching a real business.
3. "Quorzana" is not a combination of common nouns, so it does not get swept up in real-service suggestions on search engines either.

From here on, this specification uses **Quorzana** as the service name.

**Caveat**: this verification is practical due diligence via web search; it
is not an exhaustive check of every trademark registry, domain, or social
account worldwide. Before any real-world publication, a dedicated
trademark-database and domain-availability check would be advisable.

---

## Step 2: Visual identity

### Logo

- **Mark**: an abstract geometric mark made of rotated, overlapping regular
  hexagons (simple shapes like circles, shields, or triangles are avoided,
  since they are too easily confused with major services' logo shapes).
  Two hexagons offset by 30° and overlapped abstractly represent a "mado"
  (window).
- **Wordmark**: "QUORZANA" set in all caps with generous letter-spacing, in
  a system font. No italics, no enclosing shapes, no ribbon decoration —
  none of the decorative devices seen in existing logotypes.
- **Prohibited**: a red rightward arrow/triangle (evokes existing Service
  A), a red rounded rectangle with white text (evokes existing Service B),
  or a circle with a single initial (evokes existing Service C) — none of
  these compositions may be used in any form.

### Color palette candidates

Self-check method: contrast ratios were computed and checked against the
WCAG 2.2 AA thresholds (≥ 4.5:1 for normal text, ≥ 3:1 for large
text/UI components).

#### Option A: the "Yoru-Murasaki" (Night Purple) palette

| Use | Color | Background | Contrast ratio | Verdict |
|---|---|---|---|---|
| Dark bg × body text | `#F2ECFA` on `#1B1023` | Dark | 15.86:1 | AA/AAA pass |
| Dark bg × accent (gold) | `#E0A63A` on `#1B1023` | Dark | 8.46:1 | AA/AAA pass |
| Light bg × body text | `#241533` on `#F7F3FB` | Light | 15.58:1 | AA/AAA pass |

Avoids the signature colors of major streaming services (red-on-black, a
solid green, a solid blue); built on a deep purple (`#1B1023`) with a warm
gold accent (`#E0A63A`).

#### Option B: the "Yoru-Midori" (Night Green) palette

| Use | Color | Background | Contrast ratio | Verdict |
|---|---|---|---|---|
| Dark bg × body text | `#EFF6F2` on `#10221E` | Dark | 15.07:1 | AA/AAA pass |
| Dark bg × accent (orange) | `#E8895A` on `#10221E` | Dark | 6.42:1 | AA pass |
| Light bg × body text | `#14231F` on `#F1F7F4` | Light | 15.00:1 | AA/AAA pass |

The deep green (`#10221E`) carries a risk of association with an existing
service's "solid green" brand, and is a factor in rejecting this option.

### Selected: Option A, the "Night Purple" palette

**Rationale**: Option B's deep-green family is in the same hue family as an
existing solid-green brand, and the "green = that service" association
risk remains even with different lightness/saturation. Option A's purple
family is a different hue family from every major service's signature
color (red-on-black / green / blue), and achieves a contrast ratio of 8:1
or higher across every pairing — judged superior on both visual
distinctiveness and accessibility.

```
--color-bg-dark: #1B1023;
--color-text-dark: #F2ECFA;
--color-accent: #E0A63A;
--color-bg-light: #F7F3FB;
--color-text-light: #241533;
```

These tokens switch according to `prefers-color-scheme: dark` / `light`.

### Typography

No branded typeface licensed to any real service is used; only the
standard OS system font stack is used.

```css
font-family: -apple-system, BlinkMacSystemFont, "Hiragino Sans",
  "Hiragino Kaku Gothic ProN", "Segoe UI", Roboto, "Noto Sans JP",
  Meiryo, sans-serif;
```

---

## Step 3: Screen layout specification (video player screen)

This follows industry-standard streaming-service UI patterns, while every
element is built from dummy content specific to Quorzana.

### 3-1. Header

| Element | Role |
|---|---|
| Logo (hexagon mark + "QUORZANA" wordmark) | Link to home; navigates to the top page on click/tap. |
| Search icon + input field | Trigger for searching dummy titles on the site. Expanding it moves focus into the text field. |
| Account icon | Trigger that opens a dummy user menu (switch profile, settings, log out). |
| Language switch (JA/EN) | Dynamically switches the `<html lang>` attribute and the UI copy's bilingual display. |

### 3-2. Video player body

- Thumbnails/backgrounds are all dummy visuals generated from **CSS
  gradients + geometric patterns** (hexagons, stripes, dot patterns,
  etc.). No photographs, live-action assets, or anything evoking a real
  title or real actor (e.g. a person silhouette) is used.
- The player area is either a dummy `<video>` element implementation or a
  mock playback UI generated with CSS/JS; no actual video file needs to be
  embedded.

### 3-3. Playback controls

| Control | Role |
|---|---|
| Play/Pause button | Toggles playback state. Conveys state via icon change plus a text label / `aria-pressed`. |
| Seek bar | Displays and changes playback position (`role="slider"`, keyboard-operable). |
| Volume slider | Displays and changes volume; paired with a mute toggle button. |
| Subtitles toggle | Toggles subtitles on/off and opens a language-selection menu. State is conveyed with both text and icon. |
| Quality settings button | Opens a resolution-selection menu (dummy options: Auto/High/Medium/Low). |
| Next-episode button | Trigger that navigates to a dummy next episode (e.g. "Chapter 02"). |

### 3-4. Recommendation carousel

Made of 3–5 dummy cards. Titles use generic words that do not hint at any
real title:

- "Chapter 01"
- "Untitled Series"
- "Sample Feature"
- "Placeholder Story"
- "Draft Episode"

Each card is composed of a CSS-gradient + geometric-pattern dummy
thumbnail, a title, and a dummy duration (e.g. 24 min).

### 3-5. Banner ad slot

Implemented as a generic placeholder that does not evoke any real
advertiser or real brand (e.g. text reading "Sample ad placeholder" over a
solid color / gradient background only). No real logo, product name, or
company name is used in any form.

---

## Step 4: Anonymized specification of "how it breaks"

The following describes common defect patterns frequently seen in
real-world accessibility remediation work, phrased with **generic nouns
that do not evoke any specific product or company**. These generalize and
anonymize examples actually encountered in practice, and do not indicate
any specific employer or product name.

1. **Inconsistent focus order**
   The visual layout order (top-left to bottom-right) does not match the
   DOM focus-traversal order, so keyboard/screen-reader users move between
   elements in an unintended order.

2. **Decorative elements focused before real controls**
   Decorative images or thumbnail elements (which are not actually
   interactive) receive Tab focus before the buttons that are actually
   operable, making it hard to find where to start.

3. **Icon-only buttons with no label**
   Buttons composed only of a visual icon (play/pause, volume, subtitles
   toggle, etc.) have no accessible name for screen readers (e.g. missing
   `aria-label`), so assistive-technology users cannot tell what the
   button does.

4. **Toggle state not conveyed**
   Controls with an on/off or selected/unselected state (subtitles toggle,
   favoriting, etc.) express that state only through a visual color
   change, with no programmatic state notification such as
   `aria-pressed` or `aria-checked` — so assistive-technology users cannot
   tell the current state.

5. **Mechanical alt text**
   An image's `alt` attribute is left as a mechanical file name such as
   `IMG_00231.jpg` or `thumbnail_v2_final.png` instead of text describing
   its content, so screen-reader users hear meaningless information.

These are described as industry-wide anti-patterns observed repeatedly
across the field. Quorzana's v1 implementation intentionally reproduces
these defects, and they are resolved step by step in v2 and later fixes
(each stage committed per the Git conventions).

---

## Why this specification should not be confused with a real service

1. The service name "Quorzana" is a phonetic coinage with no meaning, and
   web search has confirmed that no exactly-matching real company,
   service, or product exists. It also does not overlap with any major
   video streaming service's name in phonetics, spelling, or coinage
   pattern.
2. The logo (an abstract hexagon mark) and color palette (Night Purple,
   AA/AAA-compliant at 8:1+ contrast) are designed independently, in a
   different family from any major service's signature color or logo
   shape, so no visual association arises.
3. All on-screen content (titles, thumbnails, ad slots) is built from
   dummy generic words and CSS-generated geometric patterns, containing no
   proper noun or live-action asset that would evoke a real title, real
   actor, or real advertiser.
