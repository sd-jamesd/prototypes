# Scale — Token Mapping (Dev Handoff)

**What this is:** the semantic → primitive mapping for the Scale colour system, so you know which raw ramp each named token resolves to. Values are **not** here — they live in the two export files. This doc is the key that makes those exports buildable.

**Verified:** all anchors below were leaf-checked against the exports on handoff (accent = Cerulean incl. hand-correction, Sand warm in both modes, info = Blue, discovery = Purple).

---

## How the two collections fit together

- **`Color scheme`** (822 vars) — the **primitives**: every Radix ramp (Teal, Sand, Blue…), solids + alphas, **light and dark**. This is where mode values live. Exported as `Light.tokens.json` + `Dark.tokens.json`.
- **`Theme`** (250 vars) — the **semantic aliases**: `accent → Teal`, `error → Tomato`, etc. **Single mode** by design — it's a pointer layer, not a palette. Exported as `Theme A.tokens.json`.

**Resolution:** read the semantic name from `Theme`, resolve it to a primitive, then pull that primitive's value from `Color scheme` **for the active mode**. Light/dark switching happens at the primitive level (`[data-theme]`), exactly as the current product CSS does it (`--neutral → --colors-sand-*` light / `--colors-sand-dark-*` dark).

---

## Foundation

| Semantic | → Primitive | Anchor (step 9) | Notes |
|---|---|---|---|
| `accent` | **Teal** (Cerulean) | `#0C818F` | Step 9 constant in both modes. Light **step 11 = `#006C78`** (hand-corrected — do not take the generator value). |
| `neutral` | **Sand** | `#8E8D86` | Warm re-baseline, corrected in both modes this cycle. |

Both have an `*-alpha` sibling (`Accent Alpha`, `Neutral Alpha`).

---

## Semantic status

| Semantic | → Primitive | Anchor (step 9) | Notes |
|---|---|---|---|
| `error` | **Tomato** | `#E54D2E` | |
| `success` | **Jade** | `#29A383` | |
| `warning` | **Amber** | `#F5A524` | Tailwind-custom amber, **not** Radix Amber (`#FFC53D`). |
| `info` | **Blue** | `#0090FF` | **Changed this cycle — was Sky (`#7CE2FE`).** |
| `discovery` | **Purple** | `#8E4EC6` | **New family this cycle.** Purple `#8E4EC6`, not `#7C3AED`. |

Each has an `*-alpha` sibling (Error Alpha, Success Alpha, Warning Alpha, Info Alpha, Discovery Alpha).

**Product state tokens** map onto these: `active → success`, `cancelled → error`, `pending → warning`.

---

## Category colours

| Token | → Primitive |
|---|---|
| `cat-fee` | Indigo (step 9) |
| `cat-deliverables` | Violet (step 9) |
| `cat-adjustment` | Sky (step 8) |
| `cat-margin` | Plum (step 9) |
| `cat-total` | Cyan (step 9) |

`resource-fee` is its own accent-derived family: `resource-fee → accent-9`, `-soft → accent-3`, `-border → accent-6`, `-text → accent-11`.

---

## Data-viz palette

| Slot | → Primitive |
|---|---|
| `data-1 / 2 / 3` | `resource-fee` (= **accent** / Teal-9) |
| `data-4` | Iris (step 9) |
| `data-5` | Orange (step 9) |
| `data-6` | Tomato (step 9) |
| `data-7` | Blue (step 9) |
| `data-8` | Plum (step 9) |

**AI gradient:** `linear-gradient(90deg, Teal-9, Jade-9, Cyan-9, Iris-9)`.

---

## Deliberate decisions — please don't "fix" these

- **The extra Radix scales are intentional, not bloat.** Indigo, Violet, Sky, Plum, Cyan, Iris, Orange, etc. are load-bearing — they feed the category, data-viz, and AI systems above. **Do not prune them.**
- **P3 is held.** sRGB only, by decision. No P3 layer this cycle.
- **Some alpha `a11` steps are opaque on purpose** (hand-set accessible-text steps). If a value looks fully solid at the top of an alpha ramp, that's intended.

---

## Changed this cycle (diff for review)

- **Sand** re-baselined warm — fixed a cool dark-neutral drift across all 12 dark steps (+ light 1/12).
- **info: Sky → Blue** (`#0090FF`).
- **discovery: new** (Purple `#8E4EC6`).
- Accent confirmed Cerulean incl. the `#006C78` light-11 hand-correction.

---

## One question we need answered before wiring

**Does the build resolve the `Theme → primitive` alias and apply the *dark* primitive, or does it flatten `Theme` to light?**

`Theme` is single-mode; dark lives only in the primitives. If the build reads the **alias reference** (as the product CSS appears to), dark comes through correctly. If it flattens `Theme` to a light hex, **dark never reaches the product** regardless of the primitives being correct. This is the one thing the static exports can't confirm — please verify on the build side.
