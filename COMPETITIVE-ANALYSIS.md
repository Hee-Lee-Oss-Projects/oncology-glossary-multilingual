# Competitive & Improvement Analysis — `oncology-glossary-multilingual`

Analyst review of the Hee-Lee Oss cancer-research good-deed project that builds an open, multilingual,
plain-language glossary of oncology and clinical-trial terms for patients and caregivers.
**Risk posture: HIGH, patient-facing.** A mistranslated medical term can cause real harm, so the
spine of any verdict here is the human medical-translation review and clinical sign-off, not the
drafting throughput. All competitor claims below are grounded in cited web sources.

Inputs reviewed: `PLAN.md` (v0.1.0) and `TASKS.md` (v0.1.0) for the project, plus web research.

---

## 1. Correctness & completeness review of PLAN.md

The plan is unusually strong for a v0.1 draft. It already internalizes the hardest truths of this
domain: two blocking human gates (clinical + advocate on the source definition, qualified
medical-translation review + mandatory back-translation on the translation), license-as-data with a
public-domain-first allow-list, "not medical advice" as a CI-enforced field, "no patient data by
construction," a dated reviewer/partner acquisition plan with a hold-at-M0 / pivot / mothball
decision rule, and outcome metrics that explicitly reject vanity counts. Appendix A's 25
self-applied improvements show the author already anticipated most first-order critiques. The
review below is therefore mostly about residual gaps, not foundational errors.

**A. The single biggest conceptual gap: the glossary is word/string-based, not concept-based.**
The data model (`glossary/source/<term-id>.yaml` → `glossary/<lang>/<term-id>.yaml`) keys on a
surface *term string* with free-text `aliases[]`. Professional medical terminology is **concept-based**:
one concept (e.g., "neoplasm") carries many synonyms/lexical variants per language, and the unit of
translation is the *concept*, not the *word*. This matters concretely:
- A stable concept ID should map to external terminology codes — **NCI Thesaurus (NCIt) code, UMLS
  CUI, MeSH descriptor, and (where licensing ever permits) SNOMED CT / ICD-O / ICD-11**. The plan
  names these sources but never proposes *carrying their codes as concept anchors*. Without a CUI/NCIt
  anchor, you cannot deduplicate "secondary cancer" vs "metastasis" vs "metastatic disease," cannot
  detect when two source entries are the same concept, and cannot let a partner cross-walk your
  glossary to their EHR/coding systems. **Recommendation:** add `conceptId` + optional
  `crosswalk: {ncit?, umlsCui?, mesh?, snomed?, icdo?}` to the schema (carrying a *code* is a fact,
  not a derivative work, so it is licensing-safe even for restricted vocabularies — see below).
- This also fixes the false-friend / ambiguity problem structurally: a concept can hold a
  `disambiguationNote` and an explicit `doNotConfuseWith[conceptId]` list ("benign" vs "malignant,"
  "progression" vs "remission," "positive margin" — where "positive" reads as *good* to a layperson
  but is *bad* clinically). The plan flags ambiguity in prose and in `UNCERTAIN:` flags but gives it
  no schema home.

**B. Licensing nuance the plan slightly over-simplifies (mostly conservative, one real trap).**
- The plan's public-domain-first instinct is correct: **NCI Dictionary of Cancer Terms** and its
  Spanish *Diccionario de cáncer del NCI* (9,000+ terms) are U.S.-Government works
  ([cancer.gov English](https://www.cancer.gov/publications/dictionaries/cancer-terms/),
  [español](https://www.cancer.gov/espanol/publicaciones/diccionarios/diccionario-cancer/)).
  Good primary source.
- The plan should explicitly separate **"a code/identifier is a fact (reusable)"** from
  **"the descriptive text of a definition is a copyrightable expression."** SNOMED CT and ICD-O are
  correctly excluded as *content*, but the project can still *reference their codes* as concept
  anchors. The plan currently reads as if even touching SNOMED is forbidden; clarify the distinction
  or the project will needlessly forgo interoperability.
- **MedlinePlus embedded-copyright trap** is well-caught (A.D.A.M. encyclopedia content is not PD).
  But the same trap is *under-stated* for NCI/PDQ: NCI content "may include third-party material" and
  NCI *requests* attribution; the plan says "verify per term," which is right, but the seed task
  `license-000` should require a **per-term** (not per-source) PD confirmation for any term whose NCI
  entry embeds a figure/quote. Add a `thirdPartyEmbedded: bool` field.
- **Translation rights are a distinct grant from reuse rights.** Public-domain status removes
  copyright, so *derivative translations are permitted* — but the plan should state plainly that
  "public domain ⇒ translation allowed" and capture `derivativesAllowed` AND `translationAllowed` as
  separate booleans, because for non-PD sources (WHO/ICD) the two can differ.

**C. Medical-translation QA is named but under-specified.** "Back-translation QA mandatory for
safety-load terms" is the right instinct, but back-translation alone is a weak and sometimes
misleading QA method (a fluent back-translation can mask a localized error). The plan should add the
industry-standard fuller protocol: **forward translation by ≥1 qualified translator → reconciliation
→ back-translation → expert review → cognitive debriefing / harmonization** (the ISPOR / WHO
translation-and-back-translation model used for patient-reported outcomes). At minimum, add: (a)
**two independent forward translations + reconciliation** for safety-load terms, not just one draft +
back-translation; (b) a **terminologist** role distinct from the clinician and the
bilingual reviewer (terminology management is its own competency); (c) explicit handling of
**negation, units/dosing format, and number/date locale** as named QA checklist items (the plan
mentions negation/urgency but not unit and numeral locale, a classic harm vector).

**D. Low-resource-language reality is the project's whole reason to exist — and is the least
de-risked.** Research strongly validates the gap *and* shows it is hardest exactly where the project
aims. A 32-country, 44-language African survey found cancer terms frequently **do not exist** or
carry fear-inducing connotations: "palliation" absent/uncertain for 43% of respondents; "malignant"
rendered as "malevolent/deadly/it will kill" (28%); "radiotherapy" as "burning/fire treatment"
(22%); "prostate" simply absent in some Bantu languages
([PMC11364996](https://pmc.ncbi.nlm.nih.gov/articles/PMC11364996/)). Implications the plan must
absorb:
- For many target languages there is **no settled target term** to translate *to* — the project will
  be doing **terminology creation / neologism coinage and cultural framing**, not just translation.
  That needs native speakers + community validation (cognitive debriefing), not only a credentialed
  translator. Add a `termStatus: {established | coined | descriptive-gloss}` field and a rule that
  *coined* terms require community/native-speaker validation before shipping.
- The FLORES-200 "below usable quality" qualifier is a reasonable trigger but FLORES measures generic
  MT quality, not *medical* terminology coverage; cite it as a *proxy*, and add the
  no-established-target-term signal as a first-class qualifier.

**E. Scope-boundary with sibling projects is asserted but not operationalized.** The plan says it
coordinates term IDs with `ewing-info-translations` / `vital-info-translations` ("shared termbase
only, no scope merge"). But if those projects translate *prose passages* and this one translates
*term definitions*, they will inevitably diverge on the same concept unless there is a **single
shared concept registry / translation memory** all three draw from. Right now the backlog item is
"(medium) Cross-link to trial-term projects" — too weak. This should be elevated: define the shared
concept-ID namespace and TM commons as an M-level dependency, or the portfolio will produce
*conflicting* translations of "informed consent" across three patient-facing products — itself a
safety/trust risk.

**F. Weak / missing metrics.**
- "Critical accuracy/safety defects after delivery = 0" is the right *aspiration* but a **0 target is
  unfalsifiable as a process metric** — with small volumes you will see 0 by luck, not by quality.
  Add a *leading* indicator: defects caught **at** the gate (gate catch-rate) and inter-reviewer
  disagreement rate, which actually measure that the gate is working.
- No metric for **back-translation fidelity** (e.g., semantic-equivalence score on safety-load
  terms), nor for **time-to-withdraw** when a post-delivery defect is found (the withdraw-first
  policy needs an SLA).
- No metric for **coverage of the "must-have safety set"** — define a fixed list of consent/urgency
  terms ("randomized," "placebo," "informed consent," "do not take with," "call your care team,"
  "side effect," "clinical trial phase") and measure % of that set delivered per language, since
  those carry the most harm if missing or wrong.
- "Readability ≤ grade 8" is U.S./English-centric; for many target languages
  Flesch-Kincaid-style scores are invalid. The plan acknowledges "closest culturally-appropriate
  equivalent" but should say the **advocate/native judgment is authoritative and the automated score
  is skipped, not faked,** for languages lacking a validated readability formula.

**G. Smaller gaps.** (1) No **versioning/changelog of a definition over time** in the data model — only
`sourceTermVersion`; a definition that is corrected post-delivery needs its own version history for
the withdraw→correct→re-review→re-publish trail. (2) No **RTL / complex-script** handling note beyond
"Unicode plain text" (Arabic, Urdu, Bengali, Amharic are exactly the under-served set and need
shaping/bidi care). (3) The "not medical advice" notice must itself be **translation-reviewed** in
each language — a machine-translated disclaimer is the worst possible thing to get subtly wrong;
make it a fixed, separately-reviewed, locale-validated string, not a per-entry free field.
(4) **Accessibility/easy-read**: ties to `easy-read-plus` (below) — grade-8 is still too high for
some low-literacy beneficiaries the African study describes.

---

## 2. Competitive landscape (researched, cited)

**Authoritative source glossaries (your inputs / upstream, not competitors):**
- **NCI Dictionary of Cancer Terms** — 9,000+ patient-focused terms, English + full Spanish
  *Diccionario de cáncer*; U.S.-Gov, largely public domain. *Strength:* gold-standard plain-language
  oncology definitions, reusable. *Gap:* essentially **only English + Spanish** for the patient
  dictionary (Japanese exists via a third-party FBRI translation); nothing in low-resource languages.
  [EN](https://www.cancer.gov/publications/dictionaries/cancer-terms/) ·
  [ES](https://www.cancer.gov/espanol/publicaciones/diccionarios/diccionario-cancer/) ·
  [PDQ other languages](https://www.cancer.gov/publications/pdq/other-languages)
- **MedlinePlus (NLM)** — consumer health in English + Spanish with a multilingual collection of
  2,500+ links across 40+ languages and ~250 topics; built-in MeSH thesaurus.
  *Strength:* breadth, trust, federal review standards. *Weakness:* it is a **link directory** of
  third-party docs (variable currency/quality), **not a term-by-term glossary**, and embeds
  copyrighted content (A.D.A.M.) — not cleanly reusable.
  [MedlinePlus languages](https://medlineplus.gov/languages/languages.html) ·
  [2008 launch](https://www.nlm.nih.gov/pubs/techbull/mj08/mj08_medlineplus_languages.html)

**Patient-org glossaries (closest adjacent "products"):**
- **ESMO Patient Guides** — clinician-derived patient guides translated by national oncology
  societies (Greek, French, Spanish, German, Bahasa Indonesia, etc.) with named supportive-care /
  palliative-care glossaries. *Strength:* clinician-backed, genuinely multilingual via society
  partners — a model for *how to get qualified reviewers*. *Weakness:* **guide-level documents, not a
  structured open term database**; translation is by permission (email patient_guides@esmo.org), i.e.
  **not openly licensed/forkable**; coverage skewed to European languages.
  [ESMO patient guides](https://www.esmo.org/for-patients/patient-guides) ·
  [by language](https://patient-guides.esmo.org/)
- **Macmillan Cancer Support** — cancer info translated in **16 languages**, with on-request
  translation in 200+ languages, Braille, large print. *Strength:* strong patient-facing UK reach,
  health-literacy quality. *Weakness:* **copyrighted, all-rights-reserved** (correctly on the plan's
  exclude list); document-level, not a reusable glossary.
  [Macmillan in your language](https://www.macmillan.org.uk/cancer-information-and-support/get-help/in-your-language)
- **Cancer.Net (ASCO)** and **Cancer Research UK / CancerHelp UK** — well-known English cancer term
  glossaries. *Strength:* trusted, plain-language. *Weakness:* **English-centric, copyrighted**
  (excluded for reuse); not concept-coded.
  [CRUK about cancer](https://www.cancerresearchuk.org/about-cancer)

**Terminology infrastructure (upstream / interoperability, not patient-facing):**
- **SNOMED CT International** — concept-based clinical terminology; translations in US/UK/AU English,
  Spanish, Danish, Dutch, Lithuanian, Swedish, Canadian French, plus starter sets in Chinese,
  Japanese, French, German. *Strength:* the gold standard for concept-based, coded, multilingual
  terminology. *Weakness for patients:* **clinician-facing (not plain-language)**, and
  **member-licensed** (free in member countries, but redistribution of content is restricted) —
  hence excluded as content but usable as a *code anchor*.
  [SNOMED licensing](https://docs.snomed.org/snomed-ct-practical-guides/vendor-introduction-to-snomed-ct/7-licensing) ·
  [translations](https://confluence.ihtsdotools.org/display/tran/translations+home)
- **IATE (EU terminology)** — 1.4M+ multilingual entries across 24 EU languages incl. medicine;
  **fully downloadable free**. *Strength:* huge, free, multilingual, authoritative for EU languages.
  *Weakness:* **terminologist/translator-facing**, term-equivalence not patient plain-language; EU
  languages only (not the low-resource set). [IATE](https://iate.europa.eu/) ·
  [download](https://iate.europa.eu/download-iate)
- **NCI Thesaurus (NCIt) / MeSH / UMLS** — open(ish) coded vocabularies; *Strength:* free concept
  anchors. *Weakness:* not plain-language, limited non-English.
- **WHO ICD-11 / ICD-O / IARC** — international disease/oncology classifications, multilingual.
  *Strength:* global authority. *Weakness:* **WHO copyright with reuse terms** (correctly treated as
  restricted by the plan).

**The cautionary "competitor" — generic machine translation:**
- **Google Translate / ChatGPT raw MT** — what patients and clinics actually fall back on today.
  Research is the project's strongest *raison d'être*: UCSF found Google Translate ED discharge
  instructions ~92% accurate Spanish / 81% Chinese, but **2% (Spanish) and 8% (Chinese) of errors
  were potentially clinically harmful**, and far worse for low-resource languages — ChatGPT and
  Google Translate produced **nonsensical phrases and fabricated words** in Amharic, Tigrinya, and
  Somali.
  [UCSF JGIM study](https://pmc.ncbi.nlm.nih.gov/articles/PMC8606479/) ·
  [EthnoMed guidance](https://ethnomed.org/resource/are-machine-translations-safe-to-use-for-patient-discharge-instructions/) ·
  [MT for cancer patient education](https://pmc.ncbi.nlm.nih.gov/articles/PMC11461557/)
- **CLEAR Global / Translators without Borders** — multilingual plain-language health glossary apps
  (COVID-19 glossary, crisis/refugee terminology) built exactly the way this project wants to.
  *This is the closest methodological peer AND the best potential partner* (the plan already names
  them as a translation-reviewer partner). *Strength:* proven multilingual plain-language health
  glossary methodology, field-tested in low-resource languages. *Gap vs. this project:* not
  oncology/clinical-trial-specialized.
  [TWB glossary app](https://clearglobal.org/resources/translators-without-borders-glossary-app/)

**Net read of the landscape:** Authoritative *English/Spanish* oncology glossaries exist and are
reusable (NCI). Authoritative *multilingual* oncology *documents* exist but are mostly copyrighted
(Macmillan, Cancer.Net) or permission-gated and document-level (ESMO). Authoritative *concept-based
multilingual terminology* exists but is clinician-facing and/or licensed (SNOMED, IATE). **Nobody
occupies the intersection: open-licensed + concept-based + plain-language + clinician-and-translator
reviewed + low-resource oncology/clinical-trial terms.** That intersection is the project's lane.

---

## 3. Gaps we can fill

1. **The open, reusable, structured oncology *term database*** (vs. copyrighted documents or
   permission-gated guides) — CC-BY, forkable, machine-readable, that a small nonprofit or community
   clinic can actually embed and redistribute.
2. **Low-resource languages oncology specifically** — the exact set where MT fabricates and where
   *no settled term exists* (African languages, Amharic/Somali/Tigrinya, indigenous and South-Asian
   languages). Nobody serves these with reviewed oncology terms.
3. **Concept-based + code-anchored + plain-language at once** — bridging SNOMED/NCIt/UMLS rigor with
   patient readability, a combination none of the incumbents deliver.
4. **Clinical-trial vocabulary for patients** (informed consent, randomization, placebo, PFS, phase)
   — under-served vs. diagnosis/treatment terms, and the highest-stakes for consent.
5. **Provenance + license-as-data baked into every entry** — a citable, auditable trust layer
   incumbents do not expose.
6. **Disambiguation / false-friend safety layer** ("positive margin," benign/malignant,
   progression/remission, "stable disease") — an explicit, reviewed harm-prevention asset.
7. **A reusable medical-terminology *translation pipeline*** (the methodology + tooling), not just
   the data — reusable across the Hee-Lee Oss cancer/translation portfolio.

---

## 4. Differentiators to win

- **Trust-by-construction.** Every entry ships with source, license, attribution, "not medical
  advice," reviewer sign-offs, and version history — auditable provenance no incumbent glossary
  exposes. This is the durable moat for a *safety-critical* artifact.
- **Two blocking human gates as the product, not a disclaimer.** Clinical + advocate sign-off on the
  source and qualified medical-translation review + back-translation on the target are
  *data-enforced* (CI fails without them). The review *is* the value vs. raw MT.
- **Openly licensed and concept-anchored.** CC-BY content + stable concept IDs cross-walked to
  NCIt/UMLS make it embeddable, forkable, and interoperable — uniquely combinable.
- **Low-resource-first.** Deliberately targeting the languages where MT is dangerous and incumbents
  are absent — outcome-measured by beneficiary adoption, not term counts.
- **Portfolio leverage.** A shared concept registry + translation-memory commons that powers
  `ewios`-sibling projects, multiplying value per reviewed term.

---

## 5. Claude API leverage (and hard limits)

**Where Claude meaningfully helps (always as draft, never as the shipped artifact):**
1. **Drafting plain-language source definitions** grounded in an allow-listed PD source, at a target
   reading level, with the "not medical advice" framing and automatic emission of `UNCERTAIN:` flags
   for ambiguity/advice-creep. Strong fit for NCI-grounded English/Spanish drafting.
2. **Building the concept map / deduplication.** Claude can propose concept clusters, candidate
   `doNotConfuseWith` pairs, alias/abbreviation lists, and suggested NCIt/UMLS/MeSH crosswalk codes
   for *human verification* — accelerating the concept-based restructuring (gap A).
3. **Back-translation & semantic-equivalence QA assistant.** Claude can produce the independent
   back-translation and *flag* divergences (negation flips, dropped urgency, unit/number drift) for
   the human reviewer — surfacing candidates, never clearing them.
4. **Readability + advice-creep self-check** as a first-pass linter (imperative-mood detection,
   undefined-jargon detection, define-in-terms-of-defined-terms checks).
5. **Reviewer tooling**: drafting checklists, summarizing reviewer disagreements, generating the
   structured review artifact, and triaging source-change-watcher diffs.
6. **False-friend / cultural-risk surfacing**: proposing which terms are likely to carry
   fear-inducing or misleading connotations in a given language (per the African-languages finding)
   for native-speaker confirmation.

**Where Claude must NOT decide (hard guardrails — all blocking):**
- **Raw MT/LLM output never ships to a patient** without qualified human medical-translation review;
  no Claude translation is a deliverable on its own. (UCSF/EthnoMed evidence is the justification.)
- **Source-license determinations are human-verified.** Claude may summarize a license; it may not
  set `derivativesAllowed`/`restricted`. A human license reviewer signs.
- **Clinical and translation sign-off are human and blocking.** Claude is never a reviewer of record;
  it cannot resolve a safety-load disagreement.
- **No fabricated terms.** For languages with no established target term, Claude may *propose* a
  descriptive gloss flagged `coined`, but a **native speaker + community validation** decides what
  ships — Claude must not invent and present a neologism as established.
- **Cultural adaptation is by native speakers**, not the model; Claude output is advisory.
- **No advice/dosing/diagnosis authoring** — Claude refuses and flags, per Hee-Lee Oss guardrails.

(If a funded lane is ever used for surge drafting, it stays within a hard per-task escrow cap and
still feeds the same human gates — drafting only.)

---

## 6. Ten concrete optimizations

1. **Make the model concept-based.** Add `conceptId` + `crosswalk{ncit,umlsCui,mesh,snomed,icdo}`
   and migrate term/translation files to hang off the concept (fixes gap A; enables dedup,
   interoperability, partner cross-walk).
2. **Add a disambiguation/false-friend layer.** `disambiguationNote`, `doNotConfuseWith[]`, and a
   reviewed "high-confusion pairs" set as a first-class safety asset.
3. **Upgrade the QA protocol** beyond single-draft+back-translation: two independent forward
   translations + reconciliation for safety-load terms, a distinct **terminologist** role, and named
   QA items for **negation, units/dosing format, and numeral/date locale**.
4. **Define a fixed "must-have safety set"** of consent/urgency terms and measure % delivered per
   language as a core coverage metric (replaces the unfalsifiable "0 defects" as the leading signal).
5. **Add gate-catch-rate and inter-reviewer-disagreement metrics** (leading indicators the gate
   works) alongside the post-delivery defect log.
6. **Separately review and lock the "not medical advice" disclaimer string per language** — fixed,
   native-validated, never a free-text per-entry field.
7. **Add `termStatus {established|coined|descriptive-gloss}` + community-validation requirement** for
   coined terms in low-resource languages.
8. **Split `derivativesAllowed` from `translationAllowed`, add `thirdPartyEmbedded` per term** — to
   handle the NCI/MedlinePlus embedded-copyright trap precisely.
9. **Add per-definition version history + a withdraw SLA** to make the withdraw→correct→re-review→
   re-publish trail real and time-bound.
10. **Elevate the shared concept registry / translation-memory commons** from backlog to an M2-level
    cross-project dependency, so sibling translation projects can't diverge on the same concept.

---

## 7. Parallel & perpendicular spin-offs

- **`translation-memory-commons`** — a shared, openly-licensed concept registry + translation memory
  + termbase that this project, `ewing-info-translations`, and `vital-info-translations` all draw
  from. Highest-leverage spin-off: it prevents divergence and multiplies reuse of every reviewed
  term. (Parallel.)
- **Reusable medical-terminology translation pipeline (toolkit)** — the agent-neutral drafting +
  back-translation-QA + license-gate + review-artifact machinery, packaged so *any* patient-facing
  health-translation good-deed can adopt it. (Perpendicular.)
- **MCP server: "reviewed oncology glossary"** — exposes the concept-coded, reviewed, attributed
  glossary as a read-only MCP tool so other agents/clinic tools can *look up* (never auto-translate)
  a vetted term + its "not medical advice" notice. Distribution channel without a hosting project.
- **`ewing-trial-finder` / trial-eligibility tie-in** — share the clinical-trial term subset (consent,
  randomization, phase) via the concept registry so trial-facing tools reuse vetted plain-language
  definitions (term IDs only, no scope merge).
- **`easy-read-plus`** — an easy-read / sub-grade-8 / pictographic tier for low-literacy beneficiaries
  the African-languages study describes; the oncology safety set is a natural first content set.
- **`open-pronunciation-audio`** — IPA + audio per term (already in backlog) for spoken use by
  interpreters and community health workers.
- **TWB / CLEAR Global partnership** — co-develop the low-resource oncology glossary using their
  proven multilingual plain-language methodology and translator network; the single most credible
  path to both qualified reviewers *and* a distribution partner.

---

## 8. Open questions for the maintainer

1. **Concept-based or string-based?** Will you adopt concept IDs + NCIt/UMLS crosswalk now (cheap at
   v0.1) or pay the migration cost later? (Strong recommendation: now.)
2. **Is "carry a SNOMED/ICD-O *code*" acceptable** under the guardrails, given a code is a fact and
   the *content* stays excluded? This unlocks interoperability without a license breach — needs an
   explicit ruling.
3. **For languages with no established target term**, who is the community-validation authority, and
   is coining terms in scope (it must be, per the evidence) — and how is it governed?
4. **Is back-translation alone sufficient**, or will you adopt the two-forward-translation +
   reconciliation + terminologist protocol for safety-load terms?
5. **Which sibling projects share the concept registry**, and who owns the `translation-memory-commons`
   if it is split out?
6. **First target language**: will you let TWB/CLEAR Global partnership *drive* the choice (toward
   their field languages) rather than waiting for an abstract partner?
7. **Disclaimer localization**: who validates the "not medical advice" string per language, and is it
   locked separately from entry content?

---

*Sources are linked inline above. Strongest evidentiary anchors: NCI Dictionary (EN/ES) for the
reusable source; the African-languages survey (PMC11364996) for the low-resource gap and
fear-connotation harm; the UCSF Google Translate study (PMC8606479) and EthnoMed guidance for the
"raw MT is unsafe, human review required" core thesis; SNOMED/IATE for the concept-based but
licensed/clinician-facing incumbents; ESMO/Macmillan/Cancer.Net for the copyrighted document-level
adjacents.*
