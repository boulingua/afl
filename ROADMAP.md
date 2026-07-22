# AFL — Arabic course roadmap

Build plan for the boulingua Arabic-as-a-Foreign-Language course (`afl`,
signature accent `#4A23C7` light / `#977EE7` dark, hover `#3B1CA0`, pentagon
mark). This repo is currently a scaffold (LICENSE, README, brand icons) and is
flagged "coming soon" on the world map. This document is the actionable plan to
build the **entire** course — content and website — from the `pagegen` template
and the `curriculum` framework, conforming to the boulingua standards.

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
4. **Native-voice TTS** — verifying an openly-licensed Arabic Piper voice that
   pronounces vocalised MSA acceptably (see §2/§6).
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
  full vocalisation marks. Latin text keeps the template's Source Sans 3. Self-
  host WOFF2 under `static/fonts/`; declare `unicode-range` so Arabic glyphs
  come from Noto Naskh and Latin from Source Sans.
- **TTS → Piper `ar_JO-kareem` (Jordanian Arabic, MIT-compatible OFL data).**
  *Recommended, pending an authoring smoke-test.* It is the openly-licensed
  Arabic voice in `rhasspy/piper-voices`; Jordanian phonology sits close to the
  Levantine framing. **Verify before committing audio:** (a) it reads
  fully-vocalised MSA acceptably, (b) hamza/tā' marbūṭa/shadda are honoured. If
  quality on vocalised MSA is poor, fall back to recorded human audio for
  A1 core vocabulary and mark audiogen coverage as partial. Add the voice to
  `audiogen`'s voice table + `get_voices.sh`.

---

## 3. Instantiation from pagegen

Stand up the site by copying the template verbatim, then changing only the
marked values.

1. **Copy the template** into this repo (preserve the existing `brand/`,
   `LICENSE`, `README.md`, `ROADMAP.md`): bring over `hugo.toml`, `go.mod`,
   `archetypes/`, `content/`, `layouts/`, `assets/`, `data/`, `i18n/`,
   `static/`, `scripts/`, `.github/`, `lychee.toml`, `.gitignore`,
   `.gitattributes`, `.nojekyll`, `LICENSE-CONTENT.md`, `CITATION.cff`.
2. **Edit `hugo.toml`** — change only the marked keys:
   - `baseURL = "https://boulingua.github.io/afl/"`
   - `title = "Arabic (MSA) — S. Le Boulanger"`
   - `languageCode = "en"` and `defaultContentLanguage = "en"` (see §2/§9)
   - `[params].navTitle = "Arabic"`, `description`, `keywords`
     (`arabic,MSA,fusha,CEFR,RTL,course,OER`)
   - `params.code = "afl"` (selects the accent from `data/accents.yaml`)
   - `[[params.social]].url = "https://github.com/boulingua/afl"`
   - `[params.plausible].domain = "boulingua.github.io/afl"` (keep this block
     **last** under `[params]` — the TOML sub-table trap)
   - `[[menu.main]]` sections mirroring `content/`: Script · A1 · A2 · B1 ·
     Materials · About · Legal.
3. **Regenerate brand assets** — `python brand/make_icon.py` to render the
   pentagon + favicons at `#4A23C7`; confirm `data/accents.yaml` already carries
   the `afl` row (it does: accent `#4A23C7`, hover `#3B1CA0`, dark `#977EE7`) so
   no CSS is edited.
4. **Fill legal placeholders** — the ⟨…⟩ fields in `impressum` / `datenschutz` /
   `haftungsausschluss`; keep the VG Wort METIS disclosure in `datenschutz`.
5. **First green build** — `hugo server` locally, then push to `main` so
   `build-deploy.yml` builds, runs the gate battery, and deploys to GitHub Pages;
   enable Pages (source: GitHub Actions). Milestone: a live, empty-but-styled
   Arabic-accent site with the RTL font wired.

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
  `implements_id` to its Arabic can-do. It must pass
  `curriculum/scripts/id-audit.sh` (format, global uniqueness, every
  `implements:` resolves to a real in-scope (scale, level)). Wire this as an
  opt-in CI gate.

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
  slide deck + worksheet with thumbnails, (4) native-voice audio for vocab &
  dialogue (or a recorded-audio fallback flagged), (5) all Arabic wrapped
  `dir="rtl" lang="ar"`, (6) a registered VG Wort mark (§7), (7) green build
  with all gates. A phase is "done" when its section landing lists every unit +
  exam and `conformance.yml` passes `id-audit.sh` at that level.

---

## 6. Website & materials

- **Section landings via shortcodes.** Each `_index.md` (`page_type: section`)
  uses the template shortcodes (`hero`, `lead`, `card-grid`, `card`,
  `downloads`, `callout`, `kicker`, `details`) — never raw HTML. Add an
  `{{< ar >}}` inline shortcode wrapping Arabic in `<span dir="rtl" lang="ar">`
  and a `dir`-aware block callout for worked Arabic examples.
- **RTL/bidi CSS.** Extend `assets/css/custom.css` with logical properties and a
  `[dir="rtl"]` / `:lang(ar)` layer so Arabic runs, tables and lists align
  right while the English chrome stays LTR; self-host Noto Naskh Arabic + Amiri
  WOFF2 with `unicode-range` splits (§2).
- **Materials pipeline (committed).** Decks from **slidegen** (Beamer) and
  worksheets from **sheetgen** — both already **XeLaTeX + fontspec**, so add an
  Arabic branch using **polyglossia + bidi** (or `\arabicfont` via `fontspec`)
  with Amiri/Noto Naskh, `\setmainlanguage{english}` +
  `\setotherlanguage{arabic}`, RTL environments for Arabic blocks. Build locally
  with `build_materials_latex.py`; commit PDFs under `static/materials/` (+ ODP
  editable decks) and exam PDFs under `static/downloads/`. CI only verifies — no
  TeX Live in the deploy path.
- **Native-voice audio.** `audiogen` / Piper `ar_JO-kareem` → OGG/Opus committed
  under `static/materials/audio/` and referenced via the `audio-block` partial.
  **Decision/availability note:** the Arabic Piper voice must pass the vocalised-
  MSA smoke-test in §2 before bulk generation; if it fails, ship recorded human
  audio for A1 core and mark audiogen coverage partial in the phase acceptance.
- **Thumbnails & downloads.** `render_thumbs.py` for deck/worksheet previews;
  `verify_downloads.py` gate confirms every referenced material exists and is
  attributed. Downloads hub and materials hub carry **no** VG Wort pixel (§7).

---

## 7. VG Wort — pixel assignment for ALL content pages

**Required and non-skippable.** Per `pagegen/docs/vgwort-standard.md`, every
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

- **M0 — Instantiation (S/M).** §3 complete: site copied, `hugo.toml` set, brand
  regenerated, legal filled, first green Pages deploy. *Depends on:* nothing.
- **M1 — RTL foundation (L).** Arabic web fonts self-hosted, `{{< ar >}}`
  shortcode, RTL/bidi CSS layer, slidegen/sheetgen Arabic branch, Piper voice
  verified. *Depends on:* M0. **This is the gating technical milestone** — no
  content authoring at scale until RTL renders correctly on site and in PDFs.
- **M2 — Script stage live (M).** `content/script/` (6–8 units) + glossary +
  pronunciation appendix, materials, audio, VG Wort marks. *Depends on:* M1.
- **M3 — MVP: A1 live (L).** 10–12 A1 units + exams + `conformance.yml` passing
  `id-audit.sh` at A1. *Depends on:* M2. **First flip-worthy state.**
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
- **Piper Arabic quality on vocalised MSA.** Highest content risk. If the
  smoke-test fails, audio scope narrows to recorded A1 core. Mitigate early in
  M1.
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
