# Design system

The full reference — tokens, components, patterns, and the audit that produced
them — is the page at **[/design-system](https://rafaelborges.design/design-system/)**,
which lives in this repo at `design-system/index.html`.

**That page is the source of truth.** This file deliberately does not restate it.
It holds the handful of things you need in plain text while editing CSS, and
nothing that is already written down somewhere else.

---

## Where things live

| File | Holds |
|---|---|
| `styles/style.css` | Every token (`:root`, commented by group), the shared components, the homepage |
| `styles/case.css` | Case-study-only layout and components. Loads *after* style.css |
| `index.html` | Homepage. Inline scripts: hash landing, scroll-spy, smooth scroll, the "more cases" modal |
| `cases/<slug>.html` | One page per case study |
| `design-system/index.html` | The reference page. Self-contained — do not edit by hand, see below |

**Tokens are documented in the CSS itself.** `:root` in `styles/style.css` is
grouped and commented — read it there rather than trusting a copy.

---

## Conventions that aren't obvious from any single rule

**Adding a value to a scale.** If it repeats three or more times across
components, add a token. Otherwise leave it literal. The spacing scale governs
*component rhythm* only — section-scale numbers (56–208px) stay literal on
purpose, because each appears once or twice somewhere unrelated and tokenising
them would invent a rhythm that doesn't exist.

**Naming the type scale.** Steps are named by role (`--text-body-lg`), not size,
because the same step is reused at different breakpoints and a size-based name
goes stale the first time one moves.

**Line-heights stay literal**, except `--leading-display`. Px line-heights are
tied to the font-size beside them and don't generalise. Don't "finish the job".

**Measuring `line-height: normal`.** It resolves to 20px at 16px *for Sora* —
but only where Sora actually loads. Measure it on the live site, never in a
sandbox without network, or you'll get the fallback face's metric (~18px).

**Utilities earn `!important`.** `.u-hide-below-*` exists to override the
component rules it is layered over, and has to beat `#hero-banner ul` at (1,1,0).
That is the one place it's the right tool rather than a smell.

**Starting a new case study.** Copy the section structure from
`cases/comprehensive-insurance.html`. The two ID-scoped sections must keep their
ids (`intro`, `overview`) *and* their classes (`case-intro`, `case-overview`) —
the breadcrumbs anchor to the ids, the CSS targets the classes. Mockups use the
`.case-figure` component; its markup is documented on the reference page.

---

## Rules that will bite you

These are also commented at the point of use in the CSS. Repeated here because
they're the ones that fail *silently*.

- **A component class on an `<a>` may silently lose its styling.** The reset's
  `a:link` is (0,1,1) and beats any bare class at (0,1,0) — that's how
  `.btn-link` lost its underline in the footer. `a.btn-link` fixes that one;
  check any other class you put on an anchor.
- **No `background` on `.btn`.** It ties with `.dark`/`.light` on specificity and
  would win by source order, blanking every themed button.
- **Don't shorten `.case-content .case-intro .container .content`.** The generic
  `.case-content section .container .content` is (0,3,1); a bare
  `.case-intro .container .content` is (0,3,0) and loses.
- **`.case-figure` layout belongs on the `<figure>`**, not the button or the
  image. Moving it back makes the two variants occupy different boxes.
- **The hero cue stays scoped to `#hero-banner`.** A bare class loses to
  `#hero-banner ul`.
- **`picture { display: contents }` needs `picture source { display: none }`.**
  Without it every `<source>` becomes a grid/flex item and silently adds a row.
- **Percent-encode spaces in `srcset`.** A raw space is a descriptor separator,
  so the WebP is dropped with no error.
- **Never `loading="lazy"` on a parallax image** — the transform needs a known
  `offsetHeight`.
- **The two orientation queries sit last in `style.css` on purpose.** Anything
  appended after them silently outranks them.
- **Focus is never animated.** Transitions cover `opacity` and
  `background-color` only.

---

## Deliberately not "fixed"

These look like inconsistencies and are decisions. Leave them alone.

- **`font-display: block` on Tanker** — the hero wordmark is sized to Tanker's
  metrics, so a fallback would overflow the banner mid-load.
- **No JS text fitter.** The wordmark is sized with container queries
  (`19.5cqw`); the fitter was removed on purpose.
- **`case.css` overrides `scroll-behavior`** intentionally.
- **1.5px borders**, everywhere except the About portrait (6px) and the
  image-modal close button (1px).
- **No `--color-positive`.** Good news already reads in the brand yellow.
- **Breakpoints aren't tokenised** — custom properties don't work inside
  `@media`, so that isn't a gap, it's a CSS limitation.

---

## Editing the reference page

`design-system/index.html` is **generated, not hand-written.** It is built from a
single authored source together with the published Claude artifact, so the two
copies can't diverge. Editing the deployed file directly is what would reintroduce
the drift this setup exists to prevent — regenerate instead.
