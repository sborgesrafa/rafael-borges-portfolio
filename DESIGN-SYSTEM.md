# Design System — rafaelborges.design

Documented from source on 2 September 2026, from `styles/style.css`,
`styles/case.css`, `index.html` and `cases/comprehensive-insurance.html`.

**Status:** the six findings marked ✅ below were fixed on 2 September 2026.
Score moved 52 → 60; the remaining gap is token adoption.

A live version with rendered swatches, type specimens and component previews is
published as a Claude artifact.

**Snapshot:** 26 tokens declared in `:root` · 11 components · 15.42:1 core text
contrast · system health **60 / 100**.

---

## 1. Foundations

Everything derives from two HSL values. There is no third brand colour, no
neutral ramp and no elevation scale — depth comes from borders and surface
inversion instead of shadow.

### 1.1 Colour

| Token | Value | Role |
|---|---|---|
| `--color-primary` | `hsla(64, 96%, 81%, 1)` → `#F7FDA0` | Text and borders on dark; the background on light surfaces. The only accent. |
| `--color-secondary` | `hsla(95, 50%, 9%, 1)` → `#15220B` | The page ground; text and borders on light surfaces. |
| `--transparency-primary` | `hsla(64, 96%, 81%, 0.3)` | Active nav pill; hairline above the case outro. |
| `--transparency-secondary` | `hsla(95, 50%, 9%, 0.3)` | **Unused.** |
| `--nav-scrim` | `hsla(95, 50%, 9%, 0.85)` | Sticky nav and case breadcrumbs. 0.85 holds labels at 9.8:1 even where the lime hero passes underneath. |

Aliases:

| Token | Resolves to | Uses |
|---|---|---|
| `--color-bg-dark` | `--color-secondary` | 3 |
| `--color-text-on-dark` | `--color-primary` | 3 |
| `--color-bg-light` | `--color-primary` | 2 |
| `--color-text-on-light` | `--color-secondary` | 2 |
| `--focus-ring` | `--color-primary`, flipped by `.light` | 5 |

**Contrast**

| Pair | Ratio | AA | AAA |
|---|---|---|---|
| `#F7FDA0` on `#15220B` | 15.42:1 | Pass | Pass |
| `#15220B` on `#F7FDA0` | 15.42:1 | Pass | Pass |
| `#FD6B6B` on `#15220B` (one-off negative-result red) | 5.92:1 | Pass | Fail |

The palette is the system's strongest asset: a 15.4:1 ratio in both directions
means no component can accidentally fail contrast as long as it takes its
colours from the surface — which is exactly how the surface classes are built.

### 1.2 Surfaces

`.dark` and `.light` are the system's central mechanism. They are not themes in
the usual sense — both appear on the same screen. A component doesn't choose its
colours; it inherits them from whichever surface class wraps it.

- `.dark` — bg `--color-bg-dark`, ink `--color-text-on-dark`. Applied to `body`,
  all case containers except the intro, and every icon button.
- `.light` — bg `--color-bg-light`, ink `--color-text-on-light`. Hero banner,
  contact banner, case intro, modal panel. Also flips `--focus-ring` to the dark
  green so the ring stays visible.

> **The specificity trap.** `.btn` deliberately sets no `background`. `.btn` and
> `.dark` have the same specificity, so a background declared on `.btn` would win
> by source order and blank out every themed button. Keep backgrounds on the
> surface classes only.

### 1.3 Typography

Two faces. **Tanker** (self-hosted, single 400 weight) carries every display
moment; **Sora** (variable, Google Fonts) does everything else across six
weights.

Tanker loads with `font-display: block` rather than `swap` — the hero wordmark is
sized to Tanker's metrics, so painting a fallback at that size would overflow the
banner for a few frames.

| Role | Face | Size | Line height |
|---|---|---|---|
| `h2` display | Tanker 400 | 120px (72px ≤768) | 100% |
| `.casetitle` | Tanker 400 | 60px (40px ≤425) | 100% |
| `p.special` | Tanker 400 | 32px | normal |
| `h3` section | Sora 800 | 32px | 100% |
| `.casesubtitle` | Sora 600 | 24px | 100% |
| `p.case` lead | Sora 300 | 20px (16px ≤425) | 28px |
| body | Sora 200 | 16px | 28px (24px ≤425) |
| `.btn` | Sora 400 | 16px | 24px |
| `.btn-menu.small` | Sora 300 | 14px | normal |

**Size scale, as used:** 14 · 16 · *18* · 20 · 24 · *28* · 32 · 40 · 60 · 72 ·
120. Italic steps appear only inside media queries. 29 `font-size` declarations,
none tokenised.

**Weights used:** 200 (5×), 300 (3×), 400 (9×), 600 (5×), 700 (1×), 800 (3×).
The font request asks for the full `wght@100..800` range; 100 never appears.
Narrowing to `200;300;400;600;700;800` trims the download with no visual change.

### 1.4 Spacing

Nine tokens on a 4px grid: `--space-2` (8) · `-3` (12) · `-4` (16) · `-6` (24) ·
`-8` (32) · `-9` (36) · `-10` (40) · `-11` (44) · `-12` (48).

**None of them is referenced anywhere in either stylesheet.** All 113 spacing
values in the codebase are literals. The scale also skips 4px, 20px and 28px, and
doesn't reach the 64/80/100/120/208px values the layouts actually use. 36px
appears nowhere; 44px is the control height rather than a grid step.

### 1.5 Radius and border

| Token | Value | Used for |
|---|---|---|
| `--radius-s` | 28px | Hero ≤600px |
| `--radius-m` | 32px | Hero ≤768px |
| `--radius-l` | 36px | **Unused** |
| `--radius-xl` | 40px | Hero, case container |

Fifteen further radii are hardcoded: `100px` for every pill control (×5), `24px`
for cards (×3), `50%` for modal close buttons (×2), plus `40px`, `16px`, `8px`,
`4px` and `40px 40px 0 0`. The pill and card radii are real, recurring decisions
and belong in the scale.

**Border weight:** `1.5px` is the system weight — buttons, icon buttons, cards,
insight cards and the outro rule. Two deliberate exceptions: the About portrait
frame at `6px`, and the image-modal close button at `1px`. Sub-pixel borders round
inconsistently across browsers at some zoom levels, so 1.5px is a choice to keep.

### 1.6 Motion

| Token | Value | Uses |
|---|---|---|
| `--ease` | `cubic-bezier(0.2, 0.7, 0.2, 1)` | 0 |
| `--t-fast` | 150ms | 0 |
| `--t-med` | 300ms | 0 |

All three are declared and never used. There is exactly one `transition` in the
codebase — `background 0.2s ease` on the image-modal close button — and it does
not use them. Every hover is an instant jump to `opacity: 0.8`.

The one substantial animation is the case page's scroll-driven parallax: each
`.parallax-section` translates its content by
`(scrollY − sectionTop) × (1 − speed)`, speed 0.5 desktop / 0.8 below 1024px.
The script returns early under `prefers-reduced-motion: reduce` and binds no
listeners; `case.css` also clears any applied transform, so a preference change
mid-session settles the page rather than freezing it mid-parallax.

---

## 2. Components

### 2.1 Button — `.btn`

A 44px pill, 1.5px border, optional trailing arrow icon. Colour comes entirely
from the surface class.

| Variant | Use when | Notes |
|---|---|---|
| `.btn.dark` | Any action on the green ground | The default. Used 5×. |
| `.btn.light` | Actions on the lime hero or banner | Now usable — not yet used in markup. |
| `.btn.secondary` | Lower-priority action beside a primary | Drops the surface background only; the border comes from `.btn`. |

**States:** default · hover (`opacity: .8`) · focus-visible (2px `--focus-ring`,
2px offset). **`:active` and disabled are undefined** — for every component in the
system. Survivable with no form on the site; the first contact form will need both.

> **Why the border is `currentColor`.** It used to be `var(--color-primary)`,
> which meant a `.btn` on a lime surface drew a lime-on-lime border and read as a
> floating text label — the reason `.btn.light` was declared but never usable.
> `currentColor` takes the ink the surface class already set, so the border
> follows the surface like everything else. Rendering on dark surfaces is
> unchanged (`rgb(247, 253, 160)` either way).

### 2.2 Icon button — `.btn-icon`

44×44 circle holding a single SVG. Two sizes: 44px everywhere, 56px in the
contact banner (back to 44px below 600px). Always carries an `aria-label`; the
icon inside is `alt=""`.

### 2.3 Menu link — `.btn-menu`

Navigation only. `.active` is applied by the scroll-spy script and pairs with
`aria-current="true"`. `.small` is the case-page breadcrumb (14px, weight 300,
4px/8px padding).

### 2.4 Text button — `.btn-link`

An inline `<button>` styled as a link, used once — the "more on the way" trigger
in the work note. Underlined at 4px offset, transparent, inherits its font.

### 2.5 Card — `.card` / `.card-light`

An icon-plus-label block for impact figures. The two classes are byte-for-byte
identical except for `border-color`: `.card` takes the dark green for lime
surfaces, `.card-light` the yellow for green ones.

> **The naming is inverted.** `.card-light` is the variant for *dark* surfaces.
> Everywhere else `.light` means "this surface is lime". Two classes differing by
> one property are also one class and a token: `--card-border`, flipped by
> `.light` exactly as `--focus-ring` already is.

### 2.6 Insight card — `.insight-card`

The case study's signature block — a behavioural-design principle, tagged, named
and explained. The only component with a gradient fill: 45° from transparent to
`rgba(247,253,160,0.15)`.

> The subtitle sets `font-size: 16px` against `line-height: 16px`. At a 1.0 ratio
> a two-line subtitle — which all three are — has its ascenders and descenders
> nearly touching. 20–22px is the minimum comfortable value.

### 2.7 Modals — `.case-modal` and `.image-modal`

|  | `.case-modal` | `.image-modal` |
|---|---|---|
| `role` / `aria-modal` | Yes | Yes |
| Focus trap | Yes | Yes |
| Focus returned on close | Yes | Yes |
| Escape closes | Yes | Yes |
| Backdrop closes | Yes | Yes |
| Body scroll lock | `.case-modal-open` | `.modal-open` |
| Opened by | button | `.zoom-btn` button |

The lightbox sets its image from `img.currentSrc` rather than `img.src`, so it
enlarges the WebP the `<source>` actually served rather than re-fetching the PNG,
and copies the source image's `alt` across.

The case modal returns focus to its trigger, deliberately skipped when it closes
on its way to `#contact` so restoring focus doesn't fight the smooth scroll.

The two are now behaviourally identical but still implemented twice, with two
different body-lock classes (`.case-modal-open`, `.modal-open`). One modal
component with a variant would collapse this table into a single row — worth
doing when a third modal appears, not before.

---

## 3. Patterns

### 3.1 Responsive images

Every photograph ships as WebP with a PNG fallback via `<picture>`.

| Rule | Why |
|---|---|
| `picture { display: contents }` | Keeps the `<img>` as the flex/grid item, so every existing layout rule still targets it. |
| `picture source { display: none }` | `<source>` computes to `display:block`; `contents` promotes it to a grid item, silently adding a row to every container holding an image. |
| `%20` in `srcset` | A raw space in a `srcset` is a descriptor separator. Filenames with spaces must be percent-encoded or the WebP is silently dropped. |

Above the fold: `fetchpriority="high"`, no lazy loading. Below: `loading="lazy"` —
but never on a parallax element, whose transform depends on a known
`offsetHeight`.

### 3.2 Section heights

Homepage sections **cover** the viewport rather than being fixed to it:
`min-height: calc(100svh − navHeight)`, with the `100vh` line first as fallback.
Nav height is 100px above 1024px and 60px below; `scroll-margin-top` matches so
anchor landings sit just under the bar.

### 3.3 Scroll spy

Both the homepage nav and the case breadcrumbs highlight the last section whose
top has crossed a line 40% down the viewport, with the final item forced active at
the bottom of the page.

> **Why not IntersectionObserver.** The original used a 0.5 threshold, which can
> never fire on a section taller than the viewport — the highlight stuck on the
> wrong item. The scroll-position approach has no such floor.

### 3.4 Hero wordmark sizing

CSS-sized, not measured by JavaScript. `#hero-banner` is a container
(`container-type: inline-size`); the title is `clamp(28px, 19.5cqw, 320px)`.
"Rafael Borges" in Tanker at `-0.03em` advances ≈4.92× its font-size, so 20.3cqw
would fill the line exactly; 19.5cqw is the largest value that never overflows
once letter-spacing rounds to whole pixels — worst-case fill 98.0% across
320–2000px.

**Do:** let container queries size display type · keep Tanker on
`font-display: block` · break the wordmark to two lines only on tall phones
(≤600px, ≤3/5 aspect, ≥700px tall).

**Don't:** reintroduce a JS text fitter · two-line the wordmark on tablets (it
only reaches ~65% of the line and halves the portrait) · switch to `swap`.

### 3.5 Focus

`--focus-ring` defaults to the yellow and is flipped to the dark green by
`.light` and `.case-modal__content`, so the ring is always visible against
whatever it sits on. Rings are 2px with a 2px offset (4px on `.btn-link`, 6px on
the email link).

All five focus rules use the token, plus `.zoom-btn`. (`.btn-link` and the
contact email link used to hardcode `--color-primary` and `--color-secondary`,
bypassing the mechanism built for exactly this.)

### 3.6 Alt text

The convention is `alt=""` for decorative icons, with meaning carried by an
adjacent `aria-label` or visible text. Both pages now follow it throughout — 21
decorative images on the case page, 8 on the index — and only the five content
mockups carry descriptive alt. The case page previously broke it in 12 places,
where icons sitting beside their own visible label were announced twice.

---

## 4. Responsive

| Query | File | What changes |
|---|---|---|
| ≤1440px | both | Outer padding 48→32px; case section padding; nav max-height 80px |
| ≤1224px | case only | Case container becomes a column; mockups to 70% and centred |
| ≤1024px | style only | Hero to 3/1.65 and horizontal 50/50; nav 100→60px |
| ≤768px | style only | h2 120→72px; hero to 3/2; About stacks |
| ≤600px | both | Hero to 3/4 portrait; work section stacks; logo → "RB" |
| ≤425px | both | Hero to 3/5; body line-height 28→24px; contact menu stacks |
| 601–1024px portrait | style only | Portrait tablets stack fully; photo gets full width |
| ≤600px, ≤3/5, ≥700px tall | style only | Two-line wordmark at `clamp(28px, min(38cqw, 18svh), 320px)` |

> **The two orientation queries sit last in the file on purpose** — they have to
> win over the width-only blocks above them. Anything appended to `style.css`
> after them will silently outrank them.

`case.css`'s 1224px breakpoint is the odd one out: it matches neither 1024 nor
1440, and there is no shared breakpoint list for a third stylesheet to follow.

---

## 5. Audit

**Score: 60 / 100** (was 52 before the 2 Sep fixes), weighted across six
categories.

| Category | Score | Change |
|---|---|---|
| Token definition | 8/10 | — |
| Token adoption | 3.5/10 | +0.5 — focus ring now fully tokenised |
| Component states | 5/10 | — |
| Naming consistency | 5/10 | — |
| Accessibility | 9/10 | **+3** — keyboard, dialog and reduced-motion fixed |
| Documentation | 8/10 | +2 — this file |

### Token coverage

| Category | Defined | Referenced | Hardcoded in CSS |
|---|---|---|---|
| Colour | 10 | 9 | 6 |
| Spacing | 9 | **0** | **113** |
| Radius | 4 | 3 | 15 |
| Typography | **0** | — | **29** |
| Motion | 3 | **0** | 1 |
| Breakpoints | 0 | — | 11 |

### Findings

**✅ Bug — Unclosed paragraph in the case intro** · `cases/comprehensive-insurance.html:48`
A `<p class="case">` is closed with `</h1>`. Browsers recover, but the parse tree
isn't what the markup says, and it will confuse a screen reader's heading list and
anything that scrapes the page. → changed the closing tag to `</p>`. **Done.**

**✅ A11y — Zoomable case images are keyboard-inaccessible** · `.js-zoom-image` ×4
The four mockups open a lightbox on click but are plain `<img>` elements — not
focusable, not announced as interactive, no keyboard path in. The lightbox has no
`role="dialog"`, no focus trap and didn't return focus on close. → each is now a
`.zoom-btn` `<button>` carrying the grid placement the bare image had, and
`.image-modal` has the dialog treatment `.case-modal` already had. **Done.**

**✅ A11y — No `prefers-reduced-motion` guard on the parallax** · `case.css`
Every case section transforms its content on scroll, at every viewport, with no
opt-out. Scroll-coupled parallax is a known vestibular trigger. → the script now
returns early on `matchMedia("(prefers-reduced-motion: reduce)")` and `case.css`
clears any applied transform if the preference flips. **Done.**

**Tokens — Spacing scale defined, never used** · `:root`, 9 tokens
Zero references; 113 spacing literals. The scale skips 4/20/28px and doesn't reach
the 64/80/100/120px values the layouts use. → extend the scale to the layout
sizes, then convert the ~40 component-level values first.

**Tokens — No typography tokens at all** · 29 declarations
Sizes, families and line-heights are literals everywhere, including inside media
queries. The scale that emerges is coherent — 14/16/20/24/32/40/60/72/120 — it
just isn't written down. → add `--text-*` and `--font-display` / `--font-body`.

**Naming — `.card` and `.card-light` are duplicates**
Eight identical declarations each; only `border-color` differs, and the name is
inverted. → one `.card` using `--card-border`, flipped by `.light`.

**✅ A11y — Decorative icons carry descriptive alt text** · case page, 12 images
Icons beside their own visible label are announced twice — "Idea Icon" then
"Insight". Two are misspelled "Shiedl User Icon". `index.html` gets this right in
all 8 cases, so the two pages disagree. → `alt=""` on all 12. **Done.**

**✅ Tokens — Focus ring token bypassed** · `.btn-link`, `#contact .email-link`
`.btn-link` hardcodes `--color-primary`, so on a lime surface its ring would be
invisible. The email link hardcodes `--color-secondary` — correct today only
because it happened to sit on the light banner. → both now `var(--focus-ring)`; all five focus rules use the token. **Done.**

**✅ Consistency — `.btn` hardcoded a yellow border, so `.btn.light` was unusable**
The border is `var(--color-primary)` rather than `currentColor`, so `.btn.light`
on the lime hero rendered lime-on-lime with no visible edge. → now `currentColor`.
Verified identical rendering on dark surfaces (`rgb(247, 253, 160)` either way).
**Done.**

**Consistency — Three inline styles bypass the system** · case page :337, :381, :417
`color: #fd6b6b` is the only semantic colour in the project and it lives in an HTML
attribute. `font-size: 24px` appears twice as an ad-hoc sub-heading. → promote the
red to `--color-negative` (it clears AA at 5.92:1), give the two "Results" lines a
real class.

**Polish — Motion tokens unused; no transitions on hover**
Every hover snaps instantly to `opacity: 0.8`. → `transition: opacity var(--t-fast)
var(--ease)` on the four interactive components.

**Polish — Insight-card subtitle line-height equals its font-size**
16px on 16px, and all three wrap to two lines. → 20–22px.

**Naming — Four visibility utilities with four different meanings**
`.hide` only works inside `#hero-banner` below 1024px. `.hide-m` lives in case.css
and fires at 1224px. `.hide-mobile` / `.hide-desktop` are hero-scoped and swap at
600px — and again in the portrait-tablet block. → rename to the breakpoint, e.g.
`.u-hide-below-1024`, and lift them out of their parent selectors.

**Housekeeping — Bilingual comments and duplicate rule blocks**
Comments switch between Portuguese and English mid-file. `.case-modal` declares
`display: none` twice; `.case-outro__actions` and `#work .case-cover .content` are
each split across two adjacent blocks. `--radius-l` and `--transparency-secondary`
are declared and never used.

### What's working

Worth stating plainly, because an audit reads as a list of faults: contrast is
excellent in both directions, the surface-inversion model is a genuinely good idea
cleanly executed, the responsive work handles cases most sites ignore (portrait
tablets, short phones, container-relative display type), and the inline comments
explain *why* — the letter-spacing rounding sweep, the `display:contents` trap,
the IntersectionObserver threshold, the `font-display` choice. That commentary is
better than most production codebases carry, and it is what made this document
possible to write from the source alone.

---

## 6. Priority actions

~~1. **Fix the four one-line correctness bugs**~~ — ✅ done 2 Sep 2026. The
   unclosed `<p>`, 12 alt attributes, two focus rings, `.btn` to `currentColor`.

~~2. **Make the case images keyboard-operable and guard the parallax**~~ — ✅ done
   2 Sep 2026. The four mockups are `.zoom-btn` buttons, `.image-modal` has focus
   trap + focus restore + `role="dialog"`, and the parallax bails out under
   `prefers-reduced-motion`.

3. **Add typography tokens before the next case study** — the scale is already
   coherent; writing it down costs one `:root` block. Doing it now means the
   second case study inherits it instead of adding a third set of literals.
   *~1 h, highest leverage on the next page.*

4. **Collapse `.card` / `.card-light` into one component** — introduces the
   `--card-border` pattern, the same surface-aware mechanism `--focus-ring`
   already proves. *~30 min.*

5. **Adopt the spacing scale, component-level first** — convert padding and gap
   inside components and leave section-level layout numbers literal until the
   scale reaches them. Roughly 40 of the 113 values, and the 40 that repeat.
   *~2 h, do last.*

### Next up

Items 3–5 above, and two things the audit didn't cover but that matter more once
a second case study exists:

- **The case template has a hidden contract.** `case.css` styles most sections
  generically, but `#intro` and `#overview` are ID-scoped — a new case must reuse
  those two IDs or silently lose its layout. Convert to `.case-intro` /
  `.case-overview` so the contract is explicit.
- **Missing components for case #2.** The before/after mockup comparison appears
  four times and isn't a declared component (it's four `.mockup` images plus a
  handler). And there is one semantic colour, `#fd6b6b`, living in an HTML
  attribute — case studies report numbers that go both ways, so
  positive/negative/neutral should be tokens before the next case is written.

### Deliberately not on this list

The JS text fitter (removed on purpose), `font-display: swap` (breaks the hero
mid-load), aligning `scroll-behavior` (case.css overrides it intentionally), and
the 1.5px borders. These look like inconsistencies and are decisions.
