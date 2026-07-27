# DAM design contracts

Companion to `dam-design-contracts.html`. The HTML **shows** the surfaces; this **states** them — exact values, class names, and tokens, so the build can be checked rather than eyeballed.

**Spec (visual):** https://sd-jamesd.github.io/prototypes/artifacts/DAM/dam-design-contracts.html
**Tracking:** SCR-189 (parent) → SCR-190 … SCR-196 · SCR-84 (Implement Scale design system) → SCR-207 … SCR-215

**Precedence when sources disagree**

1. **Scale** wins on executional detail — tokens, component specs, accessibility thresholds.
2. **Design principles** win on posture and prioritisation — when to be dense, when to deviate.
3. **Competitor patterns** are reference only. They inform, they never decide.

Every rule below carries its rationale in one line. That's deliberate: an undocumented rule gets re-litigated, and re-litigation is where drift comes from.

---

## 0. Build vocabulary — verified against the running app

Read directly from the DAM's compiled stylesheet (`index-*.css`, 1,050 rules · Vite + React + Radix Themes) in July 2026. **These are the real names — use them.** Where this section and the prose below disagree, this section wins: it is what exists.

### 0.1 Type scale — the ladder is already tokenised

| Token | Size | Line | Role |
|---|---|---|---|
| `--type-0-size` / `--type-0-line` | 11px | 16px | Counts, badges, micro |
| `--type-1-size` / `--type-1-line` | 12px | 16px | Labels, values, options, chips, eyebrows |
| `--type-2-size` / `--type-2-line` | 14px | 20px | Body, section headers, buttons, find field |
| `--type-3-size` / `--type-3-line` | 16px | 24px | Panel header |
| `--type-4` … `--type-6` | 18 / 24 / 32px | — | **Not for in-product surfaces** |

Weights: `--weight-regular` 400 · `--weight-medium` 500 · `--weight-semibold` 600 · `--weight-bold` 700.

So the ladder rule is **"type-0 through type-3 only"** — not "delete 18 and 24px". Those steps legitimately exist for other contexts; they just don't belong on a dense in-product surface. Stating it as a token range makes it enforceable.

**Line-height was missing from this contract entirely.** It is tokenised — always pair a size with its `--type-N-line`.

### 0.2 Spacing and radius

`--space-1` 4px · `--space-2` 8px · `--space-3` 12px · `--space-4` 16px · `--space-5` 24px · `--space-6` 32px · `--space-7` 40px · `--space-8` 48px · `--space-9` 64px (each × `--scaling`). Radius via `--radius-1…6`.

### 0.3 Semantic colour — Scale expressed over Radix

| Semantic token | Resolves to |
|---|---|
| `--primary-default` | `--accent-9` |
| `--primary-hover` | `--accent-10` |
| `--primary-text` | `--accent-11` |
| `--primary-subtle` | `--accent-3` |
| `--primary-subtle-hover` | `--accent-4` |
| `--primary-border` | `--accent-7` |
| `--text-default` | `--neutral-12` |
| `--text-subtle` | `--neutral-11` |
| `--text-disabled` | `--gray-a9` |
| `--border-subtle` / `-default` / `-emphasis` / `-strong` | `--gray-a5` / `a6` / `a7` / `a8` |
| `--surface-subtle` / `--surface-muted` | `--neutral-2` / `--neutral-3` |
| `--surface-hover` / `--surface-active` | `--gray-a3` / `--gray-a4` |

`--neutral-1…12` alias `--gray-1…12`. **Never reach past the semantic layer** to a raw Radix step unless no semantic token exists.

### 0.4 Component classes

| Element | Real class |
|---|---|
| Rail container | `.toolbar` + density modifier `.toolbar--icons` / `.toolbar--stacked` |
| Rail item | `[role="tab"].icon-nav-item` (+ `.active` / `[aria-selected]`) |
| Lightbox rail wrapper | `.lightboxSidebar` |
| Read-only field row | `.kit-field-row` (inside `.kit-field-list`) |
| Disclosure glyph | `.kit-disclosure` — **canonical**. Two rival patterns still exist: `.lucide-chevron-down` inside `.accordionItem__trigger`, and `.tagpickerDisclosure` (SCR-212) |
| Filter group | `.filterPanelGroup` + `__header` `__group` `__group-icon` `__count` |
| Filter group label | `.group-label` |
| Panel find field | `.panelSearch` |
| Generic accordion | `.accordionItem` + `__trigger` |
| File metadata section | `.meta-panel--files` |
| Rights | `.rightsPanel` · `.rightsSectionLabel` |
| Tags | `.sdtag-row` · `.sdtag-section` · `.sdtag-search` · `.sdtag-count` |
| Similar | `.similarity-chip` · `.sim-badge` |
| Bulk selection | `.selection-menu-panel` · `.selection-menu-count` |
| Radix Themes primitives | `.rt-IconButton` · `.rt-BaseButton` · `.rt-Badge` · `.rt-TextFieldInput` |

### 0.5 The rail already ships both densities

```css
.toolbar--icons   [role="tab"].icon-nav-item { height:44px; padding:0; }
.toolbar--stacked [role="tab"].icon-nav-item { padding:var(--space-2) var(--space-1);
                                               font-size:var(--type-1-size); }
```

Both modifiers exist today. **Lightbox = `.toolbar--icons`. Edit = `.toolbar--stacked`.** The stacked padding and 12px label already match this contract — the "one component, two densities" rule is implemented, not net-new.

States use **dedicated nav tokens**, themeable per surface:
`--icon-nav-fg` · `--icon-nav-bg-hover` · `--icon-nav-fg-hover` · `--icon-nav-active-bg` · `--icon-nav-active-fg` · `--icon-nav-active-bar` (2px bar, `border-radius:1px`).

This **supersedes §2.4's shims** — `--primary-tint-dark`, `--cerulean-dark` and `--surface-hover-dark` are retracted. The build already solved it properly.

---

## 1. Foundations — the shared kit

Every surface inherits this section. Land it first (SCR-190); the surface work depends on it.

### 1.1 Type ladder

Four sizes. Nothing else ships.

| Size | Used for | Weight |
|---|---|---|
| **16px** | Panel header | 600 |
| **14px** | Body, section header, search/find field, button label | 400 body · 600 headers/buttons |
| **12px** | Field labels, values, options, chips, eyebrows | 400 labels/options · 500 values/chips · 700 eyebrows |
| **11px** | Counts, badges, micro text | 600 |

**Express these as `--type-0-size` … `--type-3-size` with their matching `--type-N-line`, never raw px** (see §0.1). `--type-4` and above exist in the scale but are out of scope on in-product surfaces.
*Why:* hierarchy comes from weight and colour. Extra sizes add noise without adding levels.

**Typeface:** Inter, sole. No alternatives.

### 1.2 Read-only field row — one atom, every surface

Real class: **`.kit-field-row`**, inside `.kit-field-list`. As built:

```css
.kit-field-row { display:grid; grid-template-columns:108px 1fr;
                 gap:var(--space-1) var(--space-2);
                 align-items:baseline; }
```

Label `--text-subtle` / `--weight-regular`; value `--text-default` / `--weight-medium`; `word-break:break-word`.

The 108px column matches this contract. Two corrections **in the build's favour** — adopt these: the gap is `--space-1 --space-2`, not a flat 12px, and alignment is `baseline`, not `start`. Baseline is correct for label/value text pairs.

- Value is **left-aligned in column 2**. Not inline right-aligned.
- `word-break:break-word` is required — long filenames must wrap, not overflow.
- Dark twin swaps three tokens only: `--ed-line` / `--ed-label` / `--ed-value`.

*Why:* right-aligned inline values break on long content, and long content is the norm here (asset filenames).

### 1.3 Editable field

```css
.flab { font-size:12px; font-weight:500; color:var(--ed-label); margin-bottom:6px; }
.inp  { font-size:14px; padding:9px 11px; border-radius:var(--radius-2);
        background:var(--ed-input-bg); border:1px solid var(--ed-input-bd);
        color:var(--ed-value); }
```

Editable Title stays a **14px input**. The input chrome — border + fill — is the editable signal; read-only rows have none.

**Layout is the affordance signal:**

| Pattern | Means |
|---|---|
| Label above + bordered input | Editable |
| Label beside + bare text (`.frow`) | Read-only |

*Why:* the two patterns are self-describing at a glance. No colour coding needed, and no second label style.

> **Proposed, not yet ratified:** *a bordered box means editable, always — no exceptions.* This generalises the rule above. Raised because the live build styles the File-metadata disclosure control with input chrome (see §7). Confirm before treating as law.

### 1.4 Find / search fields — icon discipline

```css
.search { display:flex; align-items:center; gap:9px; font-size:14px;
          background:var(--stone-100); border:1px solid transparent;
          border-radius:var(--radius-2); padding:9px 11px; color:var(--subtle); }
/* dark twin: background rgba(255,255,255,.045); color var(--ed-faint) */
```

Two tiers, deliberately unalike:

| Tier | Field | Icon | Verb | Chrome |
|---|---|---|---|---|
| **Primary** | Global asset search | **Magnifier** | "Search" | Bordered, white, elevated, wide, top |
| **Secondary** | Any list-narrowing field | **List-filter** | "Find" | Borderless, faint fill, recessed |

List-filter icon path: `M2 5h20` · `M6 12h12` · `M9 19h6`

Applies to: filter rail ("Find attributes or tags"), edit ("Find attributes"), collections ("Find collections").

**The magnifier is reserved exclusively for global asset search.** One magnifier in the whole product — it doubles as a conformance canary.
*Why:* a magnifier on a filter field is indistinguishable from global search, so users type their query into the wrong field. This regressed twice; do not revert.

### 1.5 Disclosure vs navigation glyph

| Glyph | Means | Where |
|---|---|---|
| **Filled triangle** (SVG, 7px) | Expand/collapse **in place**. Rotates: right when closed, down when open. | Filter facets, edit attributes, collection group headers, File-metadata section |
| **Stroke chevron** (SVG, 14px) | **Navigate** — drill into another view. Static, points the way you go. | Collection rows (open the collection) |

```css
.chev svg        { width:7px; height:7px; transition:transform .12s; }
.chev.closed svg { transform:rotate(-90deg); }
```

Triangle geometry: `viewBox="0 0 96.154 96.154"`, filled, `currentColor`. Real class: **`.kit-disclosure`** (`.kit-disclosure--open` for the rotated state).

⚠️ **Three disclosure treatments currently coexist in the build** (SCR-212): `.kit-disclosure` in `.filterPanelGroup` (correct), `.lucide-chevron-down` in `.accordionItem__trigger`, and `.tagpickerDisclosure` in the tag picker. `.kit-disclosure` is canonical; the other two converge onto it.

- **Never use Unicode text glyphs (▾ ▴ ▸) for icons.** They inherit surrounding font-weight and fall back to a system font, so they rendered at three inconsistent sizes. That was a live bug, not a style preference.
- A panel may use both glyphs — a group header opens with a triangle, its rows navigate with a chevron. That reads clearly *because* the two never swap jobs.

*Precedent users already know:* filled triangle = macOS/Finder disclosure; chevron = iOS row indicator.

### 1.6 Accordion — flat, hairline-separated

```css
.acc-item { position:relative; padding:13px 0 13px 11px;
            border-bottom:1px solid var(--border); }
.acc-head { display:flex; align-items:center; gap:8px;
            font-size:14px; font-weight:600; color:var(--text); }
.acc-body { padding:10px 0 2px; display:flex; flex-direction:column; gap:9px; }
```

No boxes. No border between head and body. Groups separated by **one hairline**.
Identical in filter and edit — byte-identical modulo three theme tokens (`--border`/`--ed-line`, `--text`/`--ed-value`, `--primary`/`--c-primary-300`).

Real classes: **`.filterPanelGroup`** (+ `__header`, `__group`) is the conformant implementation, used in `.filters`, `.rightsPanel` and `.upload-modal`. **`.accordionItem`** (+ `__trigger`) is the second implementation, scoped to `.dam-layout` and `.glassTheme`.

⚠️ **`glassTheme` overrides this rule.** `.glassTheme .accordionItem__trigger` applies `border: 1px solid var(--glass-border)`, `background-color: var(--glass-bg)` and `backdrop-filter: var(--glass-blur)` — reinstating exactly the box this section forbids. Any surface running glassTheme will show a bordered accordion header regardless of what this contract says. See §7.

*Why:* enterprise users tolerate density when grouping is clear. Boxes-per-group is chrome that adds no information. If you can remove it and meaning still lands, remove it.

### 1.7 Active-group indicator — and the nesting rail

**Two different vertical lines. They do different jobs and must not be conflated** — reading them as one thing is what makes this section look self-contradictory.

**Selection indicator — accent, 2px, header only**

```css
.acc-item.sel .acc-head::before {
  content:''; position:absolute; left:-11px; top:1px; bottom:1px;
  width:2px; background:var(--primary); border-radius:1px; }
.count { font-size:11px; font-weight:600; color:var(--accent-9); }
```

- 2px **accent** edge on the **group header**. Works collapsed and expanded.
- **Never** an accent border running the full height of expanded children — rejected as noise on long lists (SCR-118).
- Count format is **"n of total"** — e.g. `2 of 3`.

**Nesting rail — neutral, structural**

- Nested groups indent under a **neutral** vertical hairline showing the parent → child relationship.
- Build tokens: `--group-indent`, `--group-hairline`, `--group-option-inset`.
- This is **structure, not state**: present for nested groups whether or not anything is selected, and never the accent colour.

*The distinction:* accent says **"this group has active selections."** The neutral rail says **"these options belong to that parent."** Extending selection down to sub-group level (SCR-207) is about the first; drawing hierarchy is the second. Neither reinstates the rejected full-height accent border.

⚠️ Implementations differ: `.accordionItem.has-selections > __trigger::before` vs `.has-selected-filters > .filterPanelGroup__header::after` — same job, opposite pseudo-element (SCR-212).

### 1.8 Button tiers

Classify by **what the action does to system state**, never by how important it looks.

```css
.fbtn { font-size:14px; font-weight:600; height:32px; padding:0 14px;
        border-radius:var(--radius-2); border:1px solid transparent; }
```

| Tier | Use for | Style |
|---|---|---|
| **Primary** | Creates data / advances workflow. One per context. | `background:var(--primary); color:#fff` |
| **Secondary** | Modifies view or context. No data created or destroyed. | transparent, `--primary` text + border |
| **Tertiary** | Peripheral action | transparent, `--primary` text, no border |
| **Ghost** | Inline on a row or item | transparent, `--subtle`, 16px, padding `0 11px` |
| **Destructive** | Irreversible only | `background:var(--red); color:#fff` |

Reversible actions (archive, hide) stay **Secondary** — not Destructive.

### 1.9 Selectable list item

Checkbox · thumbnail · title (14/600) · spec (12/muted) · optional red status.
Hairline-separated, **not** boxed. Selected = cerulean tint + 2px edge + checked box.

**Disable-don't-hide:** unavailable items stay visible with a red reason (e.g. "⊘ Image too small"). Dark error token `--ed-error:#ec7a6e`.
*Why:* hiding an option removes the user's ability to understand why it's unavailable.

**Show-don't-hide (companion rule):** structural and navigational controls stay visible-disabled. Permission- or rights-locked *leaf actions* hide, unless a live recovery path exists.

### 1.10 Section headers

| Element | Style | Use |
|---|---|---|
| **Icon section header** | 14px / 600, sentence case | A major section (Focal point, Saved templates, Attributes, Tags) |
| **Uppercase eyebrow** | 12px / 700, `letter-spacing:.08em`, uppercase, `--subtle` | A sub-label *inside* a section (e.g. Time-based rights); also left-nav group labels |

Two sections in one tab = two icon section headers stacked with spacing. No box, no divider between them.

---

## 2. Navigation rail — one component, two densities

The rail is **one component shown at two densities**, never two rails. Density follows **context**, not mode-as-a-flag: a viewer recedes so the asset is the hero; an editor scaffolds because it's a work surface.

### 2.1 Destination sets

| Set | Items |
|---|---|
| **Shared spine** (identical in both) | Details · Attributes · Rights |
| **Lightbox adds** | **Similar** (after Attributes) · **Download** |
| **Edit adds** | Adjustments · Template · Versions · Access |

- **AI is not a rail destination.** It lives inside Tags, under Attributes (SCR-150).
- **Download in edit is the top-bar action**, not a rail stop.
- **Similar is lightbox-only** — discovery has no authoring role in edit.
- **File metadata is not a rail destination** — it folds into Details as a section (SCR-174).

A view-only or edit-only destination is legal. An **undocumented** one is the breach.

### 2.2 Densities

```css
/* Lightbox — icon-only */
.rail       { width:54px; display:flex; flex-direction:column;
              align-items:center; gap:2px; padding:12px 0;
              border-right:1px solid var(--border); }
.ri         { width:100%; padding:13px 0; display:flex;
              align-items:center; justify-content:center; }

/* Edit — stacked icon over label */
.rail.dark      { width:88px; align-items:stretch; }
.rail.dark .ri  { flex-direction:column; gap:var(--spacing-1);
                  padding:var(--spacing-2) var(--spacing-1);
                  font-size:var(--font-size-1); font-weight:500;
                  text-align:center; border-radius:var(--radius-2);
                  border-left:2px solid transparent; }
.ri svg         { width:20px; height:20px; }
```

Spacing follows Scale's `.icon-nav` rhythm: **8px container padding, 2px item gap, `--radius-2`, 4px icon→label, 12px label**. Stacked item lands ≈52px.
*Current build runs ≈88px per item — that's the drift to fix.*

**Not inline** (icon-left/label-right). Stacked in edit, icon-only in lightbox.

### 2.3 Edit rail grouping

Three bands, separated by a **hairline, no group labels**:

```
describe    Details · Attributes
─────────
transform   Adjustments · Template
─────────
manage      Versions · Rights · Access
```

*Why this order:* a **describe → transform → manage** gradient, roughly descending frequency. Identity and catalog first (opened most — Attributes now also carries Tags and AI). Editing tools walled in the centre, so edit reads as a workspace, not a menu. Lifecycle and governance last — consequential but occasional. Versions groups with Rights and Access because all three concern the asset *over time*, not its content or its pixels.

**Separator rule:** a separator marks a **category boundary** — one per group, never per-item. It requires a real multi-item band on both sides.

**The lightbox gets no separators.** Map the same logic onto its five items and you get one pair plus three singletons; fencing a single item is per-item decoration, which the rule forbids. Its order is already correct: Details · Attributes · Similar · Rights · Download.

```css
.rail-div { height:1px; background:rgba(255,255,255,.09); margin:7px 15px; }
```

### 2.4 States

| State | Light | Dark |
|---|---|---|
| **Active** | `background:var(--primary-subtle)` · `color:var(--primary-default)` · 3px left edge | `background:var(--primary-tint-dark)` · `color:var(--cerulean-dark)` |
| **Hover** | `background:var(--surface-muted)` · `color:var(--text)` | `background:var(--surface-hover-dark)` · `color:rgba(255,255,255,.85)` |

Hover must be scoped `:not(.on)` so it never overrides active.

This is Scale's `.icon-nav-item` / `.icon-nav-item.active` / `.icon-nav-item:hover`. **Do not hardcode the highlight.**

**Resolved — no shim needed.** The build defines dedicated nav-state tokens (`--icon-nav-fg`, `--icon-nav-bg-hover`, `--icon-nav-fg-hover`, `--icon-nav-active-bg`, `--icon-nav-active-fg`, `--icon-nav-active-bar`) which theme per surface. Use those: the light/dark table above is the *intent*, the `--icon-nav-*` tokens are the *mechanism*. Active bar is **2px** (`border-radius:1px`) — matching the accordion active edge (§1.7).

### 2.5 Labels and tooltips

- **`aria-label` is always present**, both densities. Non-negotiable, accessibility.
- **A visible tooltip appears only where the name isn't already shown** — icon-only lightbox **yes**, labelled edit rail **no**.
- Sole exception: a labelled item earns a tooltip if its label truncates.

*Why:* a tooltip repeating a visible label is pure redundancy — hover delay, zero new information.

---

## 3. Edit panel

Dark theme. Inherits the full kit; only the unique tool canvas is bespoke.

- **Rail:** stacked icon+label, three bands (§2).
- **Attributes:** flat accordion (§1.6) at parity with library filters. Active-group edge + `n of total` count (§1.7).
- **Find field:** list-filter icon + "Find attributes". Never a magnifier.
- **Read-only rows:** the `.frow` atom, dark twin (§1.2).
- **File metadata:** collapsed section at the foot of **Details** — not a rail tab (SCR-174).
- **Date fields** (e.g. Embargo) carry an in-field calendar affordance: Lucide calendar SVG inside the input via `.inp-wrap` / `.inp-ico`. Hover resolves to `--ed-value`, not cerulean.
- **Disclosure marks:** SVG triangle, 7px (§1.5).

**Tool-tab scope:** Adjustments / Template / AI inherit the **full shared chrome kit** — section headers, button tiers, list-filter find field, hairline list separation, disable-don't-hide. Only the unique tool canvas or render is bespoke.

**File-metadata section spec:**

```css
.msec      { margin-top:16px; border-top:1px solid var(--border); padding-top:14px; }
.msec-head { display:flex; align-items:center; gap:8px; padding:4px 0;
             font-size:14px; font-weight:600; color:var(--text); cursor:pointer; }
.msec.open .msec-head { border-left:2px solid var(--primary);
                        padding-left:10px; margin-left:-12px; }
```

**Borderless.** Hairline above, 14/600 text, trailing triangle, no box and no fill. It is a disclosure control, not an input.

---

## 4. Library filters

- **Find field:** list-filter icon, "Find attributes or tags". The magnifier belongs to global search only (§1.4).
- **Facet accordion:** flat, single hairline between groups, no boxes, 14/600 header (§1.6).
- **Active-group indicator:** 2px edge on the header only, plus `n of total` pill. Works collapsed *and* expanded. Never a full-height border (§1.7).
- **Disclosure:** SVG triangle, 7px. Replace all Unicode.
- **Date facet** is net-new (relates SCR-8). Dimensions and duration use **preset buckets**. Lifecycle is a single "Include archived" toggle.

---

## 5. Lightbox

- **Rail:** icon-only — Details · Attributes · Similar · Rights · Download. No separators (§2.3).
- ⚠️ **Pending change (SCR-210):** the lightbox tab menu moves to the left and behaves as a **drawer** (pull-out), not a fixed rail. This supersedes the rail model on this surface only; edit is unaffected. **This section needs rewriting once the drawer design is settled** — do not build the lightbox rail from §2 in the meantime.
- **Read-only panes:** the `.frow` atom (§1.2).
- **File metadata:** a section in Details, not a rail tab.
- **Glyph rule** applied: triangle for in-place disclosure, chevron for drill-in where present.
- Read-only is **a state of the kit**, not a separate build. The lightbox adds no new components.

---

## 6. Collections

```css
.row    { display:flex; align-items:center; gap:var(--spacing-2); height:36px;
          padding:0 var(--spacing-3); border-radius:var(--radius-2);
          border-left:2px solid transparent; }
.count  { font-size:11px; font-weight:600; color:var(--text-muted);
          background:var(--surface-emphasis); border-radius:var(--radius-full);
          padding:1px 8px; min-width:24px; line-height:16px; }
.search input { height:34px; background:var(--surface-muted);
                border:1px solid transparent; border-radius:var(--radius-2);
                padding:0 12px 0 32px; font-size:var(--font-size-2); }
```

- **Find field:** borderless list-filter, "Find collections".
- **Ownership grouping** (My Collections / Shared with me) as uppercase 12/700 eyebrow group labels.
- **Three visibility states** intact. Public/open state deferred to a separate roadmap item.
- **Group headers:** filled disclosure triangle (7px). **Row drill-in:** stroke navigation chevron.
- **Counts:** 11/600.

⚠️ **Known debt:** collections runs a **parallel implementation** of row / search / count / chev, and a parallel token vocabulary (`--color-stone-*` vs `--stone-*`). It is not literally sharing the kit. Converging it is part of SCR-196.

---

## 7. Known gaps, open questions, and live drift

### Scale gaps

~~No dark active-nav or hover token.~~ **Closed.** The build already ships `--icon-nav-*` state tokens that theme per surface (§0.5). The shims previously proposed here are withdrawn, and the upstream Scale ask is not needed for the rail.

Still worth confirming with whoever owns Scale: whether the `--icon-nav-*` pattern should be promoted into Scale proper, so other dark surfaces (nav bar, bulk action bar) get the same treatment rather than each inventing their own.

### Open design calls

- **Glyph legibility pass.** In an icon-only rail some glyphs don't carry alone. Candidates: **Rights → ©** (the one mark everyone reads as licensing), **Attributes → key+value rows**, **Similar → fix**. The Metadata glyph retired with its tab. Not conformance — a design decision, tracked separately.
- **Similar scope.** Implemented lightbox-only on first principles (discovery, not authoring). Wants confirmation from the similarity-search release owner.
- **Bordered box = editable** as a general law (§1.3). Proposed, not ratified.
- **`glassTheme` provenance and scope.** A full theme variant exists in the build (`--glass-bg`, `--glass-border`, `--glass-blur`) applied via `.glassTheme` to accordions, filter groups and `.lightboxSidebar .rightsPanel`. It is **not part of Scale as far as this contract can tell**, and it actively overrides contract rules — the bordered Attributes header is a live example (§1.6). Two questions: is it sanctioned, and does a translucent/blurred/bordered treatment belong on dense in-product surfaces at all, given the *hierarchy from contrast, not borders* principle? A design call, not a bug.

### Drift observed in the live build

| Observed | Should be |
|---|---|
| File-metadata bar has input chrome (border + fill + radius) | Borderless `.msec-head` — hairline above, 14/600, trailing triangle (§3) |
| Rail items ≈88px tall in the screenshot | `.toolbar--stacked` already specs `--space-2 --space-1` padding + `--type-1-size`. If it renders tall, something upstream is overriding the modifier — check the modifier is actually applied |
| Rail active / hover | Already tokenised via `--icon-nav-*`. **No work needed** — verify it's wired, don't rebuild |
| `.accordionItem__trigger` still uses `.lucide-chevron-down`, while `.filterPanelGroup` uses `.kit-disclosure` | Both should use `.kit-disclosure` — the triangle-vs-chevron drift, precisely located (§1.5) |
| `.panelSearch` styles both `.lucide-search` and `.lucide-list-filter` | The component supports both by design; the icon is chosen per instance. Conformance = every *narrowing* field passes list-filter (§1.4) |
| Attributes section header renders with a **border + fill** (edit) | Flat: bare 14/600 header, single hairline, no box (§1.6). Likely source is `.glassTheme .accordionItem__trigger` — confirm in DevTools |
| Active-group edge uses `::before` on `.accordionItem`, `::after` on `.filterPanelGroup__header` | One pseudo-element, one implementation (§1.7, SCR-212) |
| Three disclosure glyphs coexist | Converge on `.kit-disclosure` (§1.5, SCR-212) |
| Attribute find field uses magnifier + "Search for attributes" | List-filter + "Find attributes" (§1.4) |
| Unicode ▾/▴ rendering at three sizes | Single 7px SVG triangle (§1.5) |
| Original filename shows double extension (`…_potx.jpg.jpg`) | Data bug — needs its own ticket |
| Long filename wraps mid-word across 3 lines | `word-break` is correct per atom, but check whether the 108px label column should shrink on narrow panels |

---

## 8. Conformance checklist

Mapped to the Linear subs under **SCR-189**.

**SCR-190 — Foundations / kit** *(lands first, everything inherits it)*
- [ ] Type ladder is 11/12/14/16 only; 24/18/15/13/12.5 removed
- [ ] Weights 400 / 500 / 600 / 700 per §1.1
- [ ] `.frow` two-column atom (`108px 1fr`) on every read-only surface, value left-aligned, wrapping
- [ ] Editable Title remains a 14px input
- [ ] Every Unicode disclosure glyph replaced with the 7px SVG triangle
- [ ] Chevron reserved for navigation only
- [ ] Five button tiers classified by state effect
- [ ] Selectable list item: hairline-separated, disable-don't-hide
- [ ] Active + hover use `.icon-nav-item` tokens; dark twins tokenised
- [ ] Inter only

**SCR-191 — Navigation rail** (+ **SCR-192** approach spike)
- [ ] One component, two densities — icon-only lightbox, stacked edit
- [ ] Shared spine Details · Attributes · Rights identical in both
- [ ] Viewer adds Similar + Download; edit adds Adjustments · Template · Versions · Access
- [ ] AI absent from rail; Download is edit's top-bar action
- [ ] Edit grouped into three bands with hairline separators, no group labels
- [ ] Lightbox has **no** separators
- [ ] Scale `.icon-nav` spacing rhythm; stacked item ≈52px
- [ ] Active + hover tokenised, hover scoped `:not(.on)`
- [ ] `aria-label` in both; visible tooltip only in the icon-only rail

**SCR-193 — Edit panel**
- [ ] Stacked labelled rail
- [ ] Flat accordion; active edge on header only + `n of total`
- [ ] List-filter + "Find attributes"
- [ ] Read-only rows on `.frow` (dark twin)
- [ ] File metadata is a **borderless** collapsed section in Details
- [ ] Embargo/date fields carry the in-field calendar affordance
- [ ] 7px SVG triangles

**SCR-194 — Library filters**
- [ ] List-filter + "Find attributes or tags"; no magnifier
- [ ] Flat facet accordion, single hairline, 14/600 header
- [ ] Active edge header-only + `n of total`, working collapsed and expanded
- [ ] 7px SVG triangles
- [ ] Date facet, preset buckets, single "Include archived" toggle

**SCR-195 — Lightbox**
- [ ] Icon-only rail, correct five, no separators
- [ ] Read-only panes on `.frow`
- [ ] File metadata is a section in Details
- [ ] Triangle/chevron rule applied

**SCR-196 — Collections**
- [ ] Borderless list-filter "Find collections"
- [ ] Ownership grouping as 12/700 eyebrow labels
- [ ] Three visibility states intact
- [ ] Triangle on group headers, chevron on row drill-in
- [ ] Counts 11/600
- [ ] Parallel row/search/count/chev implementation converged onto the shared kit

---

## 9. The consistency laws

The short version. If a decision isn't covered above, these decide it.

1. **One glyph, one meaning.** Triangle discloses, chevron navigates, magnifier searches globally. A glyph never does two jobs.
2. **Layout is the affordance.** Label-above + border = editable. Label-beside + bare text = read-only.
3. **Disable, don't hide.** Unavailable stays visible with a reason. Structural controls stay visible-disabled; only rights-locked leaf actions hide.
4. **Density when it earns it.** Enterprise users tolerate density when grouping is clear. Never apply marketing-page whitespace to an in-product surface.
5. **Meaning through restraint.** Default to neutrals for structure. If you can remove it and the meaning still lands, remove it.
6. **Compose before you invent.** A net-new pattern requires written rationale. Composing from existing Scale components is the default.
7. **Same job, same component.** If two surfaces do the same thing, they use the same atom — not a parallel implementation that merely resembles it.
8. **Fix the glyph, don't re-add the label.** If an icon can't carry its meaning alone in an icon-only context, the icon is wrong.
