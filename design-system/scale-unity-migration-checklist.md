# Scale · Unity migration checklist

Derived from the Unity conformance audit register. Organised by **workstream, not by component** — the audit found the component-conformance surface is tiny (the modern `sd-modal` is 5 of ~1,489 modal calls) and the work is overwhelmingly systemic: tenant skins, colour dictionaries, charts, admin. Each workstream below is one owner. Where a stream builds or fixes a component, it is keyed to the rulebook section that specs it.

**Legend:** ☐ open · ◑ in progress · ✅ done · ⚠ verify against current `main` before committing effort — the audit predates the token shipping. Finding IDs (A1, B4…) trace back to the register.

**Phase:** **[UI]** = colour/token conformance in the current codebase, ships with the UI-only pass · **[React]** = build or replace, rides the module's React migration. The systemic streams (§0–§3) are [UI]; most rulebook component work is [React].

---

## How to use this with the two reference docs

This checklist is the **plan** — what to do and in what order. Two docs are the **substance** — how to do each piece. Rule of thumb: **[UI] work → _Scale in Unity_ · [React] work → _Component Rulebook_.**

**`scale-in-unity.html` — the Unity application reference.** For every **[UI]** item below:

- **Part 1 · Corrections** — the fix for each §0–§3 finding as before → after, with the exact token and a grep to confirm it landed. Pick up B7, C3, C1, A4, B5, A1, A2, B4, B8, or C2 and the matching card is the spec for that fix.
- **Part 2 · Contrast oracle** — which token pairs are safe. Check *before* choosing any text or border colour; it is the source of the muted-text floor and the "no neutral border below neutral-9 meets 1.4.11" rule.
- **Part 3 · Contrast recipes** — the correct pattern for tables, badges, forms, nav, and charts, each rendered next to the failing version. Use it whenever you colour a component.

**`scale-component-rulebook.html` — the React component standard.** For the **[React]** items (§4-behaviour, §5, §6) this is normative: what a correct tooltip, popover, drawer, banner, modal, or toast *is*. Build against the rulebook, not this checklist — the checklist only names which rulebook section each build targets (keyed inline as → Rulebook §X).

**In one line:** open _Scale in Unity_ for the colour-and-token work that ships now; open the _Rulebook_ when you build or replace a component in React. This checklist sequences both.

---

## Status since the audit

Two findings are already resolved at the source this session — don't re-run them:

- **A3 · accent-11 non-monotonic — value settled.** Locked at **`#006c78`** (6.15:1; OKLCH L 0.486, sitting monotonically between step 10 at 0.509 and step 12 at 0.334). The register's suggested `#0d5266` (8.7:1) is **superseded** — do not "fix" step 11 back to it. Landing it in Unity depends on regenerating the token export from Figma and committing it; until that ships, Unity still carries the old `#197592`. ⚠ A project doc copy (`scale-design-system.html`) still shows `#197592` ×4 — confirm it isn't a live source before anyone reads it.
- **C4 · doc neutral-12 Stone-era `#261E1B` — resolved.** ✅ Canonical doc now carries `#21201c` and no other Stone anchors.

Everything below is verified only to the audit's snapshot. Re-grep `main` for the ⚠ items first.

---

## §0 · Fix-now — ships in 1.2, small, high value, no dependencies — **[UI]**
*One owner, ~a day.*

- ⚠ **B7 · Register the ECharts theme.** No `echarts.registerTheme` exists; the series palette is a raw 8-colour array duplicated across two files. The Scale AI Data Palette (32 colours, 5 tiers) already exists and nothing consumes it. **Highest visible-improvement-to-effort ratio in the register.**
- ⚠ **C3 · Chatbot step-11-in-step-9 slot.** `sdComponents/sdChatBot/main.css` pins `--accent-9: var(--colors-accent-accent-dark-11)` in a `@scope` block. Repoint to the correct token. This is the exact failure mode A3 rewards — grep for other instances while you're in there.
- ⚠ **C1 · Admin Stone ramp (verify first).** `_cv-admin.scss` declares `--neutral-1..12` as raw Tailwind **Stone** on `:is(html, body)`, scrambled (step 7 lighter than step 6). **Verify live:** `getComputedStyle(document.body).getPropertyValue('--neutral-9')` — Sand `#8d8d86` vs Stone `#978985`. **If Stone, promote to S1** and pull forward.
- ⚠ **A4 (login case) · Verify skinless render.** `--login-form-bg` has 30 references; if login renders without a skin they resolve to nothing (transparent bg, vanished borders). Determines whether A4 is 573 findings or ~514.

## §1 · Tenant skin token migration (B5) — the long pole — **[UI]**
*One owner, sustained, starts now, runs in parallel. Independent of the React work.*
**No design system change reaches any client until this lands.**

> **Strip-back update.** Client branding is being reduced — clients no longer set the primary button or accent. The migration still runs (skins → tokens), but the client-overridable surface per skin shrinks toward logo (and nav, if retained), so the seed set is smaller and the button/accent contrast risk is removed by design, not gated (see _Scale in Unity_, Part 3, the tenant section). **A1/A2 are unaffected** — the warning/success state colours were never client brand and still need the token swap.

- ☐ Migrate one base skin to the token layer (currently 67 token refs across 19 skins against **62,235 raw hex literals**).
- ☐ Re-derive across the **13 byte-identical** skins (3,454 literals each).
- ☐ Reconcile the **6 forks** by diff (2,700–3,000 literals).
- **A1 and A2 close here, not separately.** Warning amber `#fdb302` (1.81:1, 17 skins) and success green `#0da141` (3.39:1, 16 skins) live *in* these skins — fixed by swapping to token values. ⚠ Resolve open question 3 first (fills or text?): dark text `#21201c` gives 9.0:1, or move to a darker amber.

## §2 · Colour dictionary consolidation (B4, B8) — before §1, before React — **[UI]**
*One owner. Do this before the skin migration lands or you migrate the palette three times.*

- ☐ **B4 · Collapse three parallel dictionaries.** `colorable.js` (57 entries, maps *into* the `cv-*` namespace, lives in `sdcore` so every product inherits it — **worst offender**); `previewer.ts:5090+` (~183 colour→rgb hits); `Configuration.ts` (70 chart-array / inline-style hits).
- ☐ **B8 · Close the wrapper bypass.** Two direct `Vue.toasted.show` calls skip `Utils.successToast`/`errorToast`. Route through the wrappers so the chokepoint is real.

## §3 · Admin zone (C1, C2) — its own epic — **[UI]**
*One owner. Distinct zone; C1 and C2 share a lineage.*

- ☐ **C1 · Stone ramp** (see §0 — verify live; may be S1).
- ☐ **C2 · Palette mismatch.** `admin-core.css` is coded against a fuller palette than the export ships — 336 unresolved `--colors-*` (ruby, teal, orange, pink, lime, plum; light + dark + `-contrast`). Export ships accent + sand only. **Decision:** rewrite admin against accent/neutral/status (correct) vs widen the export (faster). ⚠ Depends on open question 5 — is admin in React's first wave?

## §4 · State layer (B3, B2) — **splits [UI] / [React]**
*The seam the UI-only framing turns on. One owner across two phases, or two owners.*

**[UI] · now — colour only:**
- ☐ **B3-colour · Colour the three toast classes.** `sd-success/error/info-toast` are set as classNames but never styled, so toast state colour is currently `vue-toasted`'s vendor default. Add CSS binding each to `--state-*` (surface / border / icon / text). No behaviour change — the existing classes get correct state colour as an interim. → **Scale in Unity** Part 3 for the state-colour recipe · **Rulebook §1.2** for the [UI] conformance rows.

**[React] · later — behaviour + build:**
- ☐ **B3-behaviour · The real toast.** Two live regions (assertive / polite), no auto-dismiss on error/warning, pause-on-hover, the wrapper as chokepoint, `data-state` replacing the three classes. → **Rulebook §1.2**, the [React] rows.
- ☐ **B2 · Overlay state variants.** Don't build state variants onto the legacy modals about to be replaced — throwaway. Build them onto the React overlays. → **Rulebook §2.1 / §2.2**, [React].

## §5 · Build the four missing components (B6) — React scope — **[React]**
*React migration. The only stream where the rulebook per-component specs are the build target.* None of these exist; they're hand-rolled per site.

- ☐ Tooltip → **Rulebook §3.1**
- ☐ Popover → **Rulebook §3.2**
- ☐ Drawer → **Rulebook §2.2**
- ☐ Banner → **Rulebook §1.3**

## §6 · Modal consolidation (B1) — React scope, replace not conform — **[React]**
*React migration. The central finding: `sd-modal` is 0.3% of modal usage — conformance changes almost nothing users see.*

- ☐ `sd.component.messageBox.action` — 940 calls, 146 files → replace.
- ☐ `Utils.openDialog` / `closeDialog` — 544 calls, ~80 files → replace.

## §7 · Hygiene backlog (S4) — verify, don't mass-delete — **[UI] / backlog**
*Backlog. Runtime injection (`createSkin.ts`) hides consumers from static analysis — these are candidate sets to verify, not delete tickets.*

- ☐ 1,701 declared-but-unreferenced tokens (~46%) — verify against runtime, do **not** bulk-delete.
- ☐ Dead code: `sd-menu-tree`, `Utils.removeDialog`, `Utils.viewFormInDialog`, `Utils.infoToast`. (`sd-callout` is dynamically instantiated — **not** dead.)
- ☐ `resourceManager.ts:3028` — multi-KB inline print stylesheet on one line (~13% of script-scope colour).
- ⚠ `smartRM/rmAIAnimate.js` — 482 hex fills, Adobe Animate canvas export. **Mark out of scope in writing** so nobody rediscovers it and panics.
- ☐ Checked-in artefacts: `skin (2).css`, `kpath-old`, `sd-feature-core-608385-1/`, `template/` vs `templates/`.
- ☐ `unity.js` / `unityMobile.js` at 12MB each — page-weight, raise separately (not a design finding).

---

## Blockers to resolve at triage (register open questions)

1. Does login render without a skin? → A4 scope (573 vs ~514).
2. Is admin rendering Stone? → C1 severity (S3 vs S1).
3. Tenant state colours — fills or text? → A1 fix shape.
4. Which tenant directories are live? (`kpath-old` looks dead — auditing dead skins pollutes the register.)
5. Is admin in React's first wave? → C2 approach.

---

## Dependency order (the one thing to get right)

**By phase.** §0–§3 are the UI-only pass — colour and tokens in the current codebase, ship independently of React. §4 splits — B3-colour ships with the UI pass; B3-behaviour and B2 ride React. §5–§6 are React: build (§5), then replace (§6). §5 is the only place the rulebook's per-component specs are consumed.

**By dependency.**
- **§2 before §1** — collapse the dictionaries before migrating skins, or it's three migrations.
- **§1 in parallel from now** — the long pole; gates all client delivery.
- **§0 anytime** — independent, high value.
- **§3 tracked separately** — admin is its own zone.
