<!-- status-audit-2026-09-09 -->
> ## Status audit — 2026-09-09
>
> **This repository is still a scaffold:** 5 tracked files, no Hugo shell yet, no `content/` pages, no build output committed.
> The build plan below stands and is not superseded. This box records what the platform
> now requires that the plan predates, and what the first moves are.
>
> **Declared scope (from this plan):** pre-A1 script-onboarding stage plus A1 → B1 proper (core conformance target). B2/C1 are declared as gaps.
>
> ### Platform requirements added since this plan was written
>
> - **`conformance.yml` at the repo root is mandatory** to declare any conformance level.
>   The framework (`curriculum` v1.1.0) defines `core` (A1–B1), `full` (A1–C1) and
>   `complete` (Pre-A1–C2). **No course in the organisation has this file yet** — not even
>   `efl`, which has the descriptor IDs in place. Absence of the manifest, not absence of
>   content, is what currently blocks every conformance claim in the suite.
> - **Descriptor IDs take the form `A1.REC.overall-oral-comprehension.01`.** Every
>   `implements_id` must resolve against `curriculum/levels/`; `conformance_audit.py
>   resolve` is the gate.
> - **Shortcodes, CSS tokens and layout chrome come from the shared `kit` Hugo module.**
>   A course repo holding its own copies is drift.
> - **CI is shared** and lives in `boulingua/.github`: `course-build.yml`,
>   `kit-drift.yml`, `link-check.yml`, `materials-latex.yml`, `org-audit.yml`.
> - **`[taxonomies]` is declared in no course `hugo.toml` today** — including the three
>   mature ones. Declare it here from the start rather than inheriting the omission.
>
> ### First moves, in this order
>
> 1. **Instantiate from `pagegen`.** Copy the template's `hugo.toml`, `go.mod` and CI entry point; do not copy layouts, shortcodes or CSS — those arrive from the shared `kit` module, and a local copy is drift that `kit-drift.yml` will flag.
> 2. **Write `conformance.yml` before the first unit.** Declare `framework: boulingua-curriculum`, `framework_version: 1.1.0`, the target from the scope above, and `conformance_status: in-progress`. Authoring units before the manifest exists is how `daf`, `efl` and `fle` each ended up with descriptor IDs in front matter that no machine can audit.
> 3. **Author unit 01 as a complete vertical slice** — page bundle, exam sibling bundle, slide deck, worksheet, audio and descriptor IDs — and only then scale out. One finished unit settles every convention question the remaining units would otherwise re-open.
>
> ### Where this sits in the suite
>
> Sequencing across all 18 courses, and the reasoning behind the order, live in
> [`boulingua/.github/ROADMAP.md`](https://github.com/boulingua/.github/blob/main/ROADMAP.md).
> The reference implementation to copy from is **`efl`** (180 unit bundles + 180 exam
> bundles, `unitNN-slug` naming, `page_type` and a `curriculum:` block on every unit).

---
# AFL — Arabic course roadmap

Build plan for the boulingua Arabic-as-a-Foreign-Language course (`afl`,
signature accent `#4A23C7` light / `#977EE7` dark, hover `#3B1CA0`, pentagon
mark). This repo is currently a scaffold (LICENSE, README, brand icons) and is
flagged "coming soon" on the world map. This document is the actionable plan to
build the **entire** course — content and website — on the `kit` platform and
the `curriculum` framework, conforming to the boulingua standards.

---

## 1. Overview

**What it is.** A free, openly-licensed (CC BY-SA 4.0 content, MIT code)
reference course in **Modern Standard Arabic (MSA / اللغة العربية الفصحى)** for
German *Gesamtschule* learners and independent self-studiers, published as a
static Hugo site with committed slide decks, worksheets, native-voice audio and
CEFR-traceable units — the same shape as the mature sister sites
[DaF](https://boulingua.github.io/daf/),
[EFL](https://boulingua.github.io/efl/) and
[FLE](https://boulingua.github.io/fle/).

**Realistic CEFR scope.** A pre-A1 **script-onboarding stage** plus **A1 → B1**
proper. That maps to the framework's `core` conformance target (A1–B1). B2/C1
are declared as gaps, not faked — openly-licensed material rarely reaches them,
and Arabic B2+ is a multi-year lift.

**Why this language.** Arabic is the highest-value language on the boulingua map
still missing: large heritage-learner and newcomer population in German schools,
almost no openly-licensed, CEFR-traceable, RTL-correct OER, and a genuine
technical showcase for the platform's RTL capability.

**The 3–5 defining challenges.**
1. **Right-to-left layout + Arabic script** — the biggest technical lift: the
   whole hugo-coder chrome, CSS, materials pipeline and TTS must be RTL- and
   Arabic-script-aware. This is the single largest risk and gates everything.
2. **Diglossia (MSA vs dialect)** — MSA is the written/formal register; nobody
   speaks it natively at home. The course must teach MSA as its reference spine
   while honestly framing the Levantine (Damascus) spoken variety.
3. **Vocalisation (tashkīl / حركات)** — beginner text needs full short-vowel
   diacritics; intermediate text sheds them. Every layer (web font, LaTeX,
   Piper) must render and pronounce vocalised text correctly.
4. **No openly-licensed Arabic voice** — the catalogue's only Arabic speaker
   states no usable licence, so audio is recorded or transcript-only, not
   synthesised (see §2/§6).
5. **Bidi mixing** — Latin scaffolding prose, transliteration, numerals and
   Arabic runs coexist on one page; the bidirectional algorithm must be driven,
   not left to chance.

---

## 2. Language & localisation decisions

Concrete, opinionated calls to make before authoring a single unit.

- **Variant → Modern Standard Arabic (MSA).** *Recommended.* A reference course
  needs one written, pan-Arab, teachable standard with an official CEFR mapping;
  MSA is it. Regional dialect is out of scope as a *taught* system but framed
  explicitly: the world-map pin and cultural thread sit in **Damascus / the
  Levant (بلاد الشام)**, and each unit carries a short "in Damascus you'd hear…"
  Levantine note so learners are not blindsided by the diglossia. Do **not**
  build a dialect track in v1.
- **Script handling → native Arabic script throughout, with scaffolded
  transliteration that fades.** Full **tashkīl (vocalisation)** in the
  script-onboarding stage and A1; reduced diacritics from A2; unvocalised
  running text by B1 (with vocalised glossary entries). A light DIN 31635-style
  transliteration accompanies new vocabulary only up to A2, then is dropped.
- **RTL → first-class, not an afterthought.** *Recommended architecture:* site
  **chrome and scaffolding prose in English** (the platform lingua franca,
  matching EFL, keeps the `en` template chrome and the largest reader base),
  with **every Arabic run wrapped `dir="rtl" lang="ar"`** via a reusable
  `{{< ar >}}` shortcode and a `dir="auto"`-aware content CSS layer. Enable
  hugo-coder's RTL affordances and add RTL-aware `custom.css` (logical
  properties: `margin-inline`, `padding-inline`, `text-align: start`). German
  medium is a live open decision (§9) given the Gesamtschule audience.
- **Level-0 script-onboarding stage → YES, required.** A `pre-alpha` section
  ("The Arabic script / الأبجدية") before A1: 28 letters, the four positional
  forms, sun/moon letters, hamza, tā' marbūṭa, long vs short vowels, and reading
  fully-vocalised words. Modelled as its own content section, not a CEFR level;
  its can-dos map to `preA1` reception/production descriptors where they exist,
  else `no-official-descriptor`.
- **Web font → Noto Naskh Arabic** (SIL OFL) as primary Arabic face, subsetted
  and self-hosted (CSP/offline-safe, no external fetch), with **Amiri** (OFL) as
  an optional display face for headings/Qur'anic-style specimens. Both cover
  full vocalisation marks. Latin text keeps the template's Source Sans 3. These faces are added to the kit's font
  manifest (F3), which builds the subsets and emits the `unicode-range` declarations
  so Arabic glyphs come from Noto Naskh and Latin from Source Sans. Not self-hosted
  here: a course repo carries no `static/fonts/` and no `assets/css/`.
- **TTS → none. Arabic audio is recorded or transcript-only.** *Decided on
  licence, not on quality.* `rhasspy/piper-voices` ships two Arabic voices,
  `ar_JO-kareem-low` and `ar_JO-kareem-medium` — the same speaker at two tiers.
  Both model cards state `License: See URL`, pointing at a training repository
  that carries no licence statement of its own, and "no statement" is not
  permission: neither can go inside a CC BY-SA 4.0 work. An earlier draft of
  this document described the voice as "MIT-compatible OFL data"; that is wrong
  twice over — the dataset states nothing, and OFL is a font licence with no
  application to a speech corpus. **The voice must not be wired.** The fallback
  ladder ends immediately: there is no second Arabic speaker and no other `ar_*`
  locale in the catalogue, and substituting a neighbouring language's voice is
  forbidden org-wide. So A1 core vocabulary and dialogue ship as **recorded
  human audio** where a native reader can be arranged, everything else ships
  **transcript-only**, and audio coverage is declared partial. The voice is
  also Jordanian rather than MSA, which would have been a separate problem.
  Revisit if upstream adds an Arabic voice with a stated licence.
- **Voice IDs are never hand-typed.** Every voice ID, tier and licence position
  in this document is read from `kit/audio/voices.yml`, which is the single
  source of truth and must never be hand-copied into prose; `afl`'s
  row there is `status: none`, with both rejected `ar_JO` voices kept as
  alternatives so the licence position stays auditable. A voice ID typed from
  memory or transliterated into ASCII is how a 404 gets written into a download
  script.

---

## 3. Instantiation from the kit

Stand up the site by building the repo *around* the kit, then changing only the
marked values. Nothing shared is copied.

1. **Create the course repo** (preserve the existing `brand/`, `LICENSE`,
   `README.md`, `ROADMAP.md`). What `afl` holds is: a short `hugo.toml` —
   baseURL, title, languageCode, `[params]`, menus, and the module import

   ```toml
   [module]
     [[module.imports]]
       path = "github.com/boulingua/kit"
   ```

   — a `go.mod`/`go.sum` requiring `github.com/boulingua/kit` at the pinned tag
   (`v1.0.0`), `boulingua.yml` (the per-course config the gate battery reads),
   the twelve-line `.github/workflows/deploy.yml` that calls the org's reusable
   workflow, an empty `content/` skeleton, and the legal pages + `about/`.
   `layouts/`, `assets/`, `i18n/`, `archetypes/` and `scripts/` are **not**
   copied and must never appear in this repo: they are the drift surface, and a
   file that is not here cannot fork. Hugo resolves the first four from the
   module; CI checks the kit out at the pinned tag so `scripts/` and the gate
   battery are on disk before Hugo runs.
2. **Vendor `_materials/`** — run `kit materials sync`, which assembles the
   kit's `latex/`, `fonts/` and `brand/icons/` into the flat `_materials/` tree
   the LaTeX build expects, and commit the result. This is the one vendored
   surface, because XeLaTeX cannot read a Hugo module; every file in it is
   verified against the digests in `kit.lock`, so a hand-edit fails the build
   rather than forking silently. (Earlier drafts said to copy `_materials/` from
   the template — `pagegen` never had one to copy.)
3. **Edit `hugo.toml`** — change only the marked keys:
   - `baseURL = "https://boulingua.github.io/afl/"`
   - `title = "Arabic (MSA) — S. Le Boulanger"`
   - `languageCode = "en"` and `defaultContentLanguage = "en"` (see §2/§9)
   - `[params].navTitle = "Arabic"`, `description`, `keywords`
     (`arabic,MSA,fusha,CEFR,RTL,course,OER`)
   - `params.code = "afl"` — selects the accent from the kit's
     `data/accents.yaml`. Set it: the kit ships the neutral graphite `template`
     accent as its default, so an unconfigured course looks obviously wrong
     rather than looking like DaF.
   - `[[params.social]].url = "https://github.com/boulingua/afl"`
   - `[params.plausible].domain = "boulingua.github.io/afl"` (keep this block
     **last** under `[params]` — the TOML sub-table trap)
   - `[[menu.main]]` sections mirroring `content/`: Script · A1 · A2 · B1 ·
     Materials · About · Legal.
4. **Regenerate brand assets** — the kit's `brand/make_icon.py` renders the
   pentagon + favicons at `#4A23C7` into this repo's `brand/`; confirm the kit's
   `data/accents.yaml` already carries the `afl` row (it does: accent `#4A23C7`,
   hover `#3B1CA0`, dark `#977EE7`) so no CSS is edited.
5. **Fill legal placeholders** — the ⟨…⟩ fields in `impressum` / `datenschutz` /
   `haftungsausschluss`; keep the VG Wort METIS disclosure in `datenschutz`.
6. **First green build** — `hugo server` locally, then push to `main` so
   `deploy.yml` builds with `--panicOnWarning`, runs the gate battery, and
   deploys to GitHub Pages; enable Pages (source: GitHub Actions). Milestone: a
   live, empty-but-styled Arabic-accent site with the RTL font wired.

---

## 4. Curriculum conformance target

- **Declared level → `core` (A1–B1).** Realistic and honest for openly-licensed
  Arabic OER. B2/C1/C2 declared as gaps per `docs/conformance.md` ("declare the
  gap; do not lower the framework to hide it").
- **Scales.** The registry has **79 in-scope** illustrative scales across REC /
  PROD / INT / MED / PLUR / LING / SOC / PRAG. `core` requires implementing
  every in-scope scale that carries an official A1/A2/B1 descriptor. Plan:
  - **Implement fully:** the reception, production, interaction, linguistic,
    sociolinguistic and pragmatic scales with A1–B1 descriptors — the course's
    backbone.
  - **Implement thinly but present:** mediation (MED) and plurilingual (PLUR)
    scales — thin in beginner OER, but every scale with an A1–B1 descriptor
    gets at least one realization so no scale is *silently* missing (a silent
    gap is a conformance failure; a declared `no-official-descriptor` cell is
    not).
  - **Record `no-official-descriptor`** for every (scale, level) the CV leaves
    empty at A1/A2/B1, and `see-reference` where the CV points elsewhere.
- **Mapping units → descriptor IDs.** Each unit's `curriculum` front-matter
  block (`framework: cefr`, `cefr_level`, `cefr_can_do`) references stable
  descriptor IDs `{LEVEL}.{DOMAIN}.{SCALE}.{SEQ}` (e.g.
  `A1.INT.information-exchange.01`). Author Arabic realizations of each ID;
  never reproduce Council of Europe text.
- **Publish the machine-readable scope/coverage manifest** — an `afl`
  `conformance.yml` (mirroring `curriculum/examples/de-a1/`): `framework:
  boulingua-curriculum`, `framework_version`, `language: ar`, `level: A1`
  (then A2, B1), `declared_conformance: core`, and `realizations:` mapping each
  `implements_id` to its Arabic can-do.
- **Gate.** The reusable workflow
  `boulingua/.github/.github/workflows/course-build.yml@v1` runs
  `python .curriculum/scripts/conformance_audit.py resolve --manifest
  conformance.yml --content content` — format, global uniqueness, and every
  `implements:` resolving to a real in-scope (scale, level) **in this repo**.
  `id-audit.sh` audits the framework's *own* level files and **cannot** validate
  this repo. Do not wire it here.

---

## 5. Content creation plan

Phased by stage/level; each unit is a leaf page bundle
(`content/<section>/units/<unitNN-slug>/index.md`, `page_type: unit`) following
the five-step model (Objectives → Input → Practise → Produce → Reflect). Exams
are **first-class sibling bundles** (`…-exam/`, `page_type: exam`, sharing
`unit_nr`). Effort tags: **S** ≈ ½ day, **M** ≈ 1–2 days, **L** ≈ 3–5 days per
unit incl. materials + audio.

- **Recurring cast & theme.** A small ensemble anchored in **Damascus / the
  Levant** — e.g. Layla (لَيْلى) a student, Karīm (كَريم) a café owner, the Old
  City (المدينة القديمة) as the recurring setting — reused across levels so
  vocabulary and story compound. Each unit centres one everyday theme.

| Phase | Section | Units | Focus | Effort |
|---|---|---|---|---|
| **0** | `content/script/` (pre-A1) | 6–8 | Alphabet, positional forms, sun/moon letters, vocalisation, reading vocalised words | M each |
| **1 (MVP)** | `content/level-a1/` | 10–12 | Greetings, self/family, café & food, numbers 1–20, city/directions, daily routine | M–L each |
| **2** | `content/level-a2/` | 10–12 | Past narration, shopping/bargaining, travel, health, work, weather, comparisons | L each |
| **3** | `content/level-b1/` | 10–12 | Opinions, media/news register, subjunctive, formal correspondence, cultural texts | L each |

- **Exams.** One per 3–4 units + an end-of-level exam, each a `…-exam/` bundle
  with `duration_min`, `total_points`, `notenschluessel`, and a committed PDF
  under `static/downloads/<level>/`. Effort **M** each.
- **Appendices** (`content/appendices/<slug>/`, `page_type: appendix`):
  Arabic–English glossary (vocalised headwords), pronunciation & script guide,
  common-errors list (bidi/tashkīl pitfalls), verb-form (wazn) reference,
  numerals & the calendar, assessment rubrics. Effort **M–L**.
- **Acceptance criteria per phase:** every unit has (1) filled `curriculum`
  block with resolvable descriptor IDs, (2) `skills_focus` set, (3) committed
  slide deck + worksheet with thumbnails, (4) recorded audio for vocab &
  dialogue where a reader is available, else a flagged transcript (§2 — no
  licence-clear Arabic voice exists), (5) all Arabic wrapped
  `dir="rtl" lang="ar"`, (6) a registered VG Wort mark (§7), (7) green build
  with all gates. A phase is "done" when its section landing lists every unit +
  exam and `conformance.yml` resolves at that level under the §4 gate.

---

## 6. Website & materials

- **Section landings via shortcodes.** Each `_index.md` (`page_type: section`)
  uses the template shortcodes (`hero`, `lead`, `card-grid`, `card`,
  `downloads`, `callout`, `kicker`, `details`) — never raw HTML. Add an
  `{{< ar >}}` inline shortcode wrapping Arabic in `<span dir="rtl" lang="ar">`
  and a `dir`-aware block callout for worked Arabic examples.
- **RTL/bidi CSS.** Lands in the kit (F4b), not here — logical properties and a
  `[dir="rtl"]` / `:lang(ar)` layer so Arabic runs, tables and lists align
  right while the English chrome stays LTR. It is shared with `pfa` and belongs
  upstream for that reason; self-host Noto Naskh Arabic + Amiri
  WOFF2 with `unicode-range` splits (§2).
- **Materials pipeline (committed).** Decks and worksheets from the kit's
  LaTeX templates (`kit/latex/`) — both already **XeLaTeX + fontspec**, so add an
  Arabic branch using **polyglossia + bidi** (or `\arabicfont` via `fontspec`)
  with Amiri/Noto Naskh, `\setmainlanguage{english}` +
  `\setotherlanguage{arabic}`, RTL environments for Arabic blocks. Build locally
  with `build_materials_latex.py`; commit PDFs under `static/materials/` (+ ODP
  editable decks) and exam PDFs under `static/downloads/`. CI only verifies — no
  TeX Live in the deploy path.
- **Audio — blocked on licence, not on tooling.** No Arabic Piper voice carries
  a usable licence (§2), so `afl` ships **recorded human audio** for A1 core
  vocabulary and dialogue where a native reader can be arranged and
  **transcript-only** elsewhere; audio coverage is declared partial in every
  phase acceptance. Keep the plumbing as specified — OGG/Opus committed under
  `static/materials/audio/`, referenced through the `audio-block` partial, with
  the unit page rendering its `<details>` transcript and no player where there
  is no file — so that a future licence-clear `ar_*` voice drops in without a
  content change. `afl`'s row in `kit/audio/voices.yml` is the one place that
  changes when it does; the ID is never typed here.
- **Thumbnails & downloads.** `render_thumbs.py` for deck/worksheet previews;
  `verify_downloads.py` gate confirms every referenced material exists and is
  attributed. Downloads hub and materials hub carry **no** VG Wort pixel (§7).

---

## 7. VG Wort — pixel assignment for ALL content pages

**Required and non-skippable.** Per `kit/docs/vgwort-standard.md`, every
editorial page ≥ 1800 rendered characters gets exactly **one** VG Wort Zählmarke
on exactly **one** URL. This is the author's statutory remuneration right, not
tracking — emitted static, JS-free, no consent gate, loading eagerly for every
reader.

- **Which pages get a mark:** every **unit**, every **exam**, every
  **appendix**, and long-form **editorial** pages (`/about/`, each section
  landing whose prose reaches 1800 chars). **Never** the home page, the
  materials/downloads hubs, tag/category indexes, paginated continuations, or
  the templated legal pages (Impressum/Datenschutz/Haftungsausschluss).
- **Note on Arabic and Mindestumfang:** the 1800-char threshold counts *rendered
  prose*. Arabic script is compact and many pages mix Latin scaffolding with
  Arabic; check the coverage audit's per-page char count and only register pages
  that genuinely clear 1800 chars of authored text.
- **Process per page:** draw a fresh **public** code (32-hex) from the author's
  **T.O.M.** account → add it to `data/vgwort.yaml` keyed by `url:` (or `path:`)
  with `pixel_url`, `public_id`, `min_chars: 1800`, `author`, `registered_at`
  → the shared resolver (`layouts/_partials/vgwort/url.html`) renders the
  `<head>` preload + eager `<body>` `<img>` → record the code in the private
  usage registry (kept **outside** this repo). Never invent codes; never expose
  the private code; never reuse an assigned code.
- **Gates:** coverage audit (`verify_vgwort_coverage.py`, warns on unregistered
  ≥1800-char pages), render verify (`verify_rendered_pixels.py`, blocking:
  each registered pixel appears on exactly one URL), manifest ↔ data
  (`verify_all_pixels.py`), and the hub guard (no `met.vgwort.de` on the
  materials hub). Exclude `vgwort.de` from lychee.
- **Estimated total marks needed:** ~6–8 script units + ~32 A1–B1 units +
  ~10 exams + ~6 appendices + ~4 editorial pages ≈ **58–62 Zählmarken** for the
  full course. Draw them in batches per phase (script+A1 first: ~22).

---

## 8. Milestones & sequencing

- **M0 — Instantiation (S/M).** §3 complete: repo standing on the kit module,
  `_materials/` vendored, `hugo.toml` set, brand regenerated, legal filled,
  first green Pages deploy. *Depends on:* nothing.
- **M1 — RTL foundation (L).** Arabic web fonts self-hosted, `{{< ar >}}`
  shortcode, RTL/bidi CSS layer, the kit's LaTeX Arabic branch, audio route
  settled per §2. *Depends on:* M0. **This is the gating technical milestone** — no
  content authoring at scale until RTL renders correctly on site and in PDFs.
- **M2 — Script stage live (M).** `content/script/` (6–8 units) + glossary +
  pronunciation appendix, materials, audio, VG Wort marks. *Depends on:* M1.
- **M3 — MVP: A1 live (L).** 10–12 A1 units + exams + `conformance.yml`
  resolving at A1 under the §4 gate. *Depends on:* M2. **First flip-worthy
  state.**
- **M4 — A2 (L).** *Depends on:* M3.
- **M5 — B1 + full appendices (L).** `declared_conformance: core` complete.
  *Depends on:* M4.

**Definition of "done / ready to flip from coming-soon to active":** the map pin
flips at **M3** — script stage + full A1 with exams live, every A1 unit
CEFR-mapped and VG-Wort-marked, materials and audio committed, all CI gates
green, RTL correct on site and in PDFs, `conformance.yml` passing at A1. "Course
complete" is **M5** (A1–B1 `core`).

---

## 9. Open decisions & risks

- **Medium language (en vs de).** Recommended `en` for chrome consistency and
  reach; the Gesamtschule audience argues for German scaffolding. Decide before
  M2 — switching medium after content exists is costly. *(Open.)*
- **Arabic TTS is closed by licence, not open on quality.** Both upstream
  `ar_JO` voices state `License: See URL` against a source that licenses
  nothing, so neither can ship inside a CC BY-SA 4.0 course (§2). *(Decided —
  no Piper audio.)* What stays open is **sourcing a native reader** for A1 core
  audio and deciding how much of the course is transcript-only; settle it in M1
  so the phase acceptance in §5 is not written twice.
- **hugo-coder RTL completeness.** The theme may not fully support `dir=rtl`
  chrome; the English-chrome + wrapped-Arabic approach sidesteps most of this,
  but audit navbar, ToC and footer under mixed bidi. *(Risk.)*
- **LaTeX Arabic shaping.** polyglossia/bidi + Amiri must render tashkīl and
  ligatures correctly in decks/worksheets; budget iteration in M1.
- **Vocalisation policy drift.** Keep the fade-out rule (full → reduced →
  none across pre-A1 → A2 → B1) consistent; encode it in the style guide.
- **Diglossia framing.** MSA-only risks feeling unnatural; the Damascus/Levant
  notes must stay clearly *secondary* to avoid teaching two systems at once.
- **Transliteration scheme.** Pick one (DIN 31635 recommended) and apply it
  uniformly; mixing schemes confuses learners.
- **Numerals.** Decide Western (0–9) vs Eastern-Arabic (٠–٩) presentation per
  level; recommend introducing Eastern-Arabic numerals in the script stage,
  using Western in scaffolding.
