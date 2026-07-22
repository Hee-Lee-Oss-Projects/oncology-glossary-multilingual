# PLAN — oncology-glossary-multilingual

> Status: Draft · Version: 0.1.0 · Last updated: 2026-06-28 · Owner: J. Carter (acting maintainer) · Lane: donated · Risk tier: **HIGH** (patient-facing health content)

## Executive summary

`oncology-glossary-multilingual` is a Hee-Lee Oss good-deed project that builds an **open, multilingual,
plain-language glossary of oncology and clinical-trial terminology for patients and caregivers** —
the words a newly-diagnosed person, a family caregiver, or a trial participant runs into ("adjuvant
therapy," "randomized," "metastatic," "neutropenia," "informed consent," "progression-free
survival") and cannot find clearly explained in their language. The deliverable is **educational
reference content**, not medical advice: each entry is a term, a short plain-language definition, the
authoritative source it was derived from, and a standing **"not medical advice — talk to your care
team"** notice, translated into many languages while preserving clinical meaning.

This is a **HIGH risk-tier** project because the output is **patient-facing health content**. Under
the Hee-Lee Oss good-deed definition, HIGH risk requires **credentialed expert sign-off before merge**.
This project therefore makes a **two-stage, blocking human-review gate** the spine of the pipeline,
not a footer:

1. **Source-definition gate (clinical + advocate).** Every canonical source-language entry must be
   signed off by a **credentialed oncology clinician** (oncologist, oncology pharmacist, or oncology
   nurse with the relevant scope) **and** a **patient advocate** (for plain-language clarity,
   reading level, and emotional appropriateness) **before any translation begins**.
2. **Translation gate (qualified medical translator + native review).** Each translated entry must
   be signed off by a **qualified medical-translation reviewer** for that language pair, with
   **back-translation QA mandatory** for terms that carry safety-load (dosing, negation, urgency,
   "call your doctor" triggers), before that language ships.

An entry that fails either gate is not shipped. **Unreviewed definitions are never translated**, and
**untranslated-but-unreviewed text is never published.**

The work runs in the **donated lane**: a human runs their own coding agent interactively to draft
candidate entries and translations and to open PRs; the Hee-Lee Oss CLI only prepares workspaces and opens
PRs — it never invokes an agent or runs headless. The funded lane is out of scope for v0.1.

**Binding cancer-domain guardrails (see Data, licensing & compliance — this is the lead section).**
This project uses **only open-access / aggregate / de-identified reference sources**. It ingests
**no patient data of any kind**: controlled-access repositories (dbGaP, EGA, individual-level
biobanks) and any identifiable patient data are **out of scope**. A glossary does not need
patient-level data and will never touch it. Term definitions are derived **only from license-verified
sources** — preferring U.S.-Government public-domain references (NCI Dictionary of Cancer Terms,
ClinicalTrials.gov, NCI Thesaurus, MeSH) and explicitly **excluding** non-commercial or restricted
sources (COSMIC, OncoKB) and all-rights-reserved copyrighted advocacy content unless written
permission is on file. **Every assertion carries provenance.**

Honesty note: **no partner cancer-care organization, advocacy group, or clinic is yet secured**, and
**no credentialed oncology reviewer or qualified medical translator is yet engaged.** Both are hard
external dependencies. Until they are secured, `verifiedNeed` is recorded as **`false`** on all
delivery-dependent tasks, and the project's Definition of Shipped **cannot** be met. M0/M1 build
partner-independent foundations (allow-list, schema, review machinery, a reviewed source-language
seed set); translation and delivery are gated behind securing the reviewers and a partner.

**Reviewer- and partner-acquisition plan (dated) with a build-vs-hold decision rule.** Outreach is
time-boxed against the build, not open-ended:
- By **2026-08-31**: ≥ 3 candidate clinical reviewers and ≥ 2 candidate patient advocates in active
  conversation (target types in *Governance*); pilot source-language term set scoped.
- By **2026-10-31**: **at least one credentialed oncology reviewer and one patient advocate secured**
  (the M1 entry gate).
- By **2026-12-31**: a qualified medical-translation reviewer for the **first** target language
  secured (the M2 entry gate); ≥ 1 partner org in active conversation.
- **Decision rule:** if **no credentialed clinical + advocate reviewer pair is secured by the M1
  entry date**, source-definition work **does not proceed to sign-off** and the project **holds at
  M0** (allow-list, schema, templates only) — it will **not** publish patient-facing definitions
  without expert review. If **no partner/steward is secured by ~2027-03-31**, the project does not
  ship blindly: it either (a) **pivots** the last-mile beneficiary (hand the reviewed, openly-licensed
  glossary to an existing patient-education platform or cancer-org library) or (b) **mothballs** to
  maintenance-only, recorded in governance. The reviewed source glossary remains a useful open
  artifact in either case.

## Problem & beneficiaries

**Who is helped (directly).** Patients newly diagnosed with cancer, family caregivers, and people
considering or enrolled in clinical trials — especially those whose primary language is **not** the
language their clinic, consent form, or trial documentation is written in. Secondary direct users:
patient navigators, community health workers, interpreters, and small cancer-support nonprofits who
need a trustworthy, citable reference they can hand to families.

**Who is helped (ultimately).** People making **high-stakes, frightening decisions** under time
pressure who currently rely on a language they read poorly, on inaccurate machine translation of
clinical jargon, or on nothing. Clearer understanding of terms supports **informed consent**,
**adherence**, and the ability to **ask the right questions** of a care team — without ever
substituting for that care team.

**The need.** Oncology and clinical-trial vocabulary is dense, Latinate, and unforgiving:
mistranslating "benign" vs. "malignant," "progression" vs. "remission," "do **not** take with food,"
or "randomized" can mislead a frightened reader. Authoritative plain-language glossaries (e.g., the
NCI Dictionary of Cancer Terms) exist mainly in English and a small number of pivot languages;
high-quality, **reviewed, attributed** versions in lower-resource languages are scarce or absent.
Generic machine translation of medical terms is unsafe and carries no provenance or review. The gap
is **accurate, plain-language, expert-reviewed, attributed** term definitions a patient can actually
trust and a nonprofit can actually distribute.

**Verified need: TO BE SECURED.** The *category* need is well-evidenced (large multilingual cancer
populations; documented health-literacy and language barriers in oncology care and trial
enrollment). But a **specific requesting organization, target language set, and priority term list
are not yet secured.** We record this honestly: foundation tasks proceed without a partner; all
delivery/adoption tasks are marked `verifiedNeed: false` and **blocked** until a partner, a
credentialed clinical reviewer, a patient advocate, and a qualified translator are confirmed.

**Partner / requestor: TO BE SECURED.** Candidate partner types: cancer patient-advocacy
organizations and their multilingual programs, community oncology clinics and cancer centers serving
multilingual populations, clinical-trial patient-engagement / health-literacy groups, interpreter/
medical-translation NGOs (e.g., Translators without Borders / CLEAR Global), and public cancer-info
services. None is committed as of this draft.

## Goals and non-goals

**Goals**
- Build a **reviewed, openly-licensed, plain-language source-language glossary** of high-frequency
  oncology and clinical-trial terms, each with a definition, provenance, and a standing "not medical
  advice" notice.
- Establish a **two-stage blocking review pipeline**: clinical + patient-advocate sign-off on every
  source definition, then qualified medical-translation review (with back-translation QA for
  safety-load terms) on every translation.
- Maintain a **per-source allow-list** recording each term source's exact license, reuse/derivative
  permission, and required attribution — preferring public-domain sources and **excluding**
  restricted ones (COSMIC/OncoKB non-commercial; copyrighted advocacy content).
- Translate the reviewed glossary into **target languages** that are under-served by trustworthy
  oncology materials, preserving clinical meaning and reading level.
- Deliver glossary sets that a **partner organization actually adopts and distributes** to
  beneficiaries, with outcomes tracked.

**Non-goals**
- **Not** medical advice, diagnosis, prognosis, dosing guidance, or treatment recommendations. The
  glossary defines terms; it never tells a person what to do about their cancer. (Authoring such
  guidance is out of scope even under expert review for v0.1.)
- **Not** a symptom checker, trial-eligibility tool, trial finder, or decision aid. (Those are
  separate, higher-surface projects — e.g. `ewing-trial-finder`.)
- **Not** a general machine-translation service or on-demand "translate my report" tool; we never
  translate a patient's own documents or any individual-level text.
- **Not** built on patient data: **no controlled-access data (dbGaP, EGA, biobanks), no identifiable
  patient data, no individual-level records** — ever.
- **Not** relicensing or "freeing" copyrighted or restricted source material (SNOMED CT, COSMIC,
  OncoKB, copyrighted advocacy content); we honor every source's terms.
- **Not** publishing any term in a language for which no qualified medical-translation reviewer is
  available, and **not** publishing any source definition without clinical + advocate sign-off.
- **Not** a hosting/website/app project (delivery formats are open data + documents a partner can
  embed); no self-serve portal in v0.1.

## Success metrics (outcomes)

Outcome-based and beneficiary-centric. Baselines are ~0 (new project). **Outcome** targets are for
the first ~6 months after a partner **and** reviewers are secured; **interim foundation metrics** are
tracked from M0/M1 so progress is visible *before* a partner exists. We explicitly **do not** count
"PRs merged," "terms drafted," or "words translated" as success — only **reviewed, correctly-licensed
terms adopted by a beneficiary**.

**Outcome metrics (post-partner + reviewers)**

| Metric | Baseline | Target | How measured |
|---|---|---|---|
| Reviewed glossary set **adopted by a partner** for distribution | 0 | ≥ 1 language set adopted by ≥ 1 org | Partner written confirmation in PR/receipt |
| Target **languages covered** end-to-end (reviewed source → translated → translation-reviewed → delivered) | 0 | ≥ 2 | Project registry |
| Reviewed **terms** delivered per adopted language | 0 | ≥ 100 core terms | Glossary entry count with full sign-off |
| Source definitions passing **clinical + advocate sign-off** (first or second pass) | n/a | ≥ 90% (**effective from M1**, min sample 10) | Review records |
| Translations passing **qualified medical-translation review** (first or second pass) | n/a | ≥ 90% (**effective from M2**, min sample 10) | Review records |
| **Critical accuracy/safety defects** found *after* delivery (meaning-changing term error, negation/urgency error, advice creep) | n/a | **0** | Post-delivery defect log |
| **License/provenance compliance** of delivered entries (source cited + permitted + "not medical advice" present) | n/a | **100% (hard gate)** | License/provenance CI check per deliverable |
| **Readability** of source definitions meeting target reading level | n/a | ≥ 90% at/below target grade (see Scope) | Readability check + advocate review |
| Partner-/beneficiary-reported **usefulness** (qualitative) | n/a | Positive from ≥ 1 partner | Partner/beneficiary feedback |

**Interim foundation metrics (M0/M1, partner-independent)**

| Metric | Baseline | Target | How measured |
|---|---|---|---|
| Term sources **verified** and recorded on the allow-list | 0 | ≥ 3 by end of M0 (≥ 1 explicitly excluded with reason) | Allow-list entries with `verifiedBy`/`verifiedDate` |
| Source-language terms **authored + clinical/advocate-reviewed** (seed set) | 0 | ≥ 25 by end of M1 | Reviewed source entries |
| Credentialed clinical reviewers + patient advocates **secured** | 0 | ≥ 1 each by end of M1 | Reviewer roster + COI declarations |
| Qualified **medical-translation reviewers** secured | 0 | ≥ 1 (first language) by end of M2 | Reviewer roster |
| **Cycle time** per term (draft → clinical/advocate sign-off → license/provenance clear) | n/a | Tracked; trend down | Timestamps on PR/review artifacts |

**Sample rule for the "≥ 90% pass" rates.** The denominator is **every entry that reaches the
relevant review gate** (counted once per entry, not per pass); an entry "passes" if it earns sign-off
on its **first or second** pass. Each rate is reported **only once its denominator is ≥ 10**; below
that we report the raw count (e.g., "8/9 passed") to avoid small-sample noise.

## Scope

**Term-selection rule (objective).** A term qualifies for the glossary if it is (a) **high-frequency
in patient-facing oncology / clinical-trial contexts** (consent forms, trial summaries, treatment
explanations, common diagnoses/procedures), and (b) **defined in at least one license-verified,
allow-listed source**. Priority order: (1) a secured partner's stated priority terms; (2)
**safety-relevant or consent-relevant** terms (informed consent, randomization, placebo, side-effect/
toxicity terms, "call your care team" triggers); (3) terms most likely to be **misunderstood or
mistranslated**; (4) terms already covered by a public-domain source (lower license risk). Ties break
toward terms that strengthen an already-started semantic cluster (e.g., a coherent "clinical-trial
basics" set).

**Target-language selection rule (objective).** A target language qualifies if it meets **≥ 2** of:
(a) it is **absent or below a usable quality threshold** for medical content on a named MT benchmark
(reference: **FLORES-200**); (b) it has a **large cancer-affected speaker population** relative to
available trustworthy oncology materials; (c) **professionally-reviewed oncology glossaries are
scarce or absent** for it. **A qualified medical-translation reviewer for the pair is a hard
precondition, not a qualifier** — we never publish a language we cannot review. Among qualifying
languages, prioritize: secured-partner language → largest under-served population → reviewer coverage
available → source terms already reviewed.

**Reading-level target (objective).** Source definitions target **≤ US grade 8** plain-language
readability (or the closest culturally-appropriate equivalent per language), checked by an automated
readability score **and** patient-advocate judgment (the score is a floor, advocate review is the
authority). Definitions avoid undefined jargon, define-in-terms-of-defined-terms where possible, and
carry the standing "not medical advice" notice.

**In scope**
- Source **term-source allow-list** with per-source license terms, derivative permission, attribution.
- A **reviewed source-language glossary**: term, plain-language definition, provenance, "not medical
  advice" notice, reading-level metadata, optional aliases/abbreviations.
- **Translations** of reviewed entries into named target languages with preserved clinical meaning.
- **Clinical + advocate review** of source definitions; **qualified medical-translation review** +
  back-translation QA of translations; all sign-offs recorded in the PR.
- **License + provenance verification** per source and per deliverable; content schemas + CI checks.
- Delivery packaging: term, definition/translation, provenance, attribution, "not medical advice"
  notice, reviewer sign-offs.

**Out of scope**
- Any medical advice, diagnosis, prognosis, dosing, or treatment/triage guidance; symptom checking;
  trial eligibility/matching; decision aids.
- Translating sources **not** on the allow-list or whose license is unverified/incompatible; any
  restricted source (SNOMED CT, COSMIC, OncoKB) or copyrighted advocacy content without written
  permission.
- **Any** patient-level, identifiable, or controlled-access data (dbGaP/EGA/biobanks); ingesting or
  translating a user's own documents.
- Publishing definitions without clinical + advocate sign-off, or languages without qualified
  translation review.
- Audio/video, OCR, complex-script DTP beyond Unicode plain text / simple formats unless a partner
  requires it (then scoped as future work); hosting/portal.

## Solution approach & architecture

This is primarily a **content/data pipeline** project (deliverables are reviewed glossary entries +
their translations + provenance), with light agent-neutral tooling. It rides on existing Hee-Lee Oss
donated-lane mechanics (CLI prepares workspace; human runs their agent; PR opened; human/expert
review gates "done").

**Pipeline (per term, then per term × language)**

*Stage A — source definition (must complete before any translation):*
1. **Select & verify source** — confirm the term's authoritative source is on the allow-list;
   re-verify current license + required attribution; record provenance (URL, version/date, retrieval
   date, license snapshot hash).
2. **Draft plain-language definition** — agent drafts a ≤ grade-8 definition grounded in the
   allow-listed source(s), preserving meaning, attaching the "not medical advice" notice, and
   emitting **uncertainty flags** (see Quality gates) for anything ambiguous or advice-adjacent.
3. **Readability + safety self-check** — agent runs the readability check and an **advice-creep
   self-check** (no instruction to act; defines, does not advise) as a first pass.
4. **Clinical review** — a credentialed oncology clinician verifies accuracy and that the definition
   is correct and not misleading; records sign-off.
5. **Advocate review** — a patient advocate verifies plain-language clarity, reading level, emotional
   appropriateness, and absence of advice creep; records sign-off.
   *(4 and 5 are both required; an entry with either missing is `blocked`.)*
6. **License + provenance check** — confirm source allow-listed, attribution present, provenance
   complete, "not medical advice" notice present.

*Stage B — translation (only for Stage-A-approved entries):*
7. **Draft translation** — agent translates the reviewed source definition using the language-pair
   glossary/termbase, preserving meaning, negations, urgency, and listed preserved terms; emits
   uncertainty flags.
8. **Qualified medical-translation review** — a qualified reviewer for the pair verifies accuracy,
   safety, and cultural appropriateness; records sign-off.
9. **Back-translation QA (mandatory for safety-load terms)** — an independent back-translation is
   compared to the reviewed source before sign-off for terms carrying dosing/negation/urgency/"call
   your care team" load.
10. **License + provenance check** (translation inherits source attribution + notice) → **package &
    deliver** to partner → record adoption.

**Artifacts / data model**
- `sources/term-source-allow-list.yaml` — entries: `{ id, name, url, licenseName, licenseUrl,
  reuseTerms, derivativesAllowed, requiresAttribution, attributionTemplate, restricted (bool),
  exclusionReason, snapshotHash, snapshotArchiveUrl, verifiedBy, verifiedDate, notes }`
  (`snapshotHash` = SHA-256 of captured license/source text).
- `glossary/source/<term-id>.yaml` — `{ termId, term, aliases[], definition, readingLevelScore,
  notMedicalAdvice (bool, must be true), sourceRefs[], domainTags[], safetyLoad (none|dosing|
  negation|urgency|consent), reviewStatus, clinicalSignoff, advocateSignoff, agentFlags[] }`.
- `glossary/<lang>/<term-id>.yaml` — `{ termId, lang, translation, sourceTermVersion, reviewStatus,
  translationSignoff, backTranslation?, agentFlags[] }`.
- `templates/` — `clinical-review-checklist.md`, `advocate-review-checklist.md`,
  `translation-review-checklist.md`, `reviewer-handoff.md`, `not-medical-advice-notice.md`.

**Formats.** UTF-8 throughout; YAML for structured entries + metadata; Markdown for delivered/human
views; canonical term IDs stable across languages so a term and its translations are linkable.

**Content schemas & CI validation.** The Task JSON schema lives in `packages/schema/src/schemas.ts`
(AJV / JSON Schema **draft-07**). The project's content artifacts get their **own** JSON Schemas in
the same place — `termSourceAllowListSchema`, `glossaryEntrySchema`, `glossaryTranslationSchema`,
`reviewSchema` — compiled and exposed via `validate.ts` exactly like `taskSchema`/`registrySchema`
(AJV with `ajv-formats`). YAML artifacts are parsed to JSON and validated by a structural-check
script (`tooling-001`) wired into `pnpm test`, so **CI fails on any malformed or non-conformant
content file** (e.g., `notMedicalAdvice` not `true`, missing `sourceRefs`, missing sign-off on a
`delivered` entry). The license/provenance gate (`license-001` → automated in `license-002`) builds
on this by cross-checking each deliverable against its allow-list source. Validation stays
**agent-neutral**, in the core schema package, not in adapters.

**Key decisions**
- **Two blocking review stages, expert-gated.** HIGH risk ⇒ clinical + advocate sign-off on every
  source definition **before** translation, and qualified medical-translation review (+ back-translation
  for safety-load terms) **before** any language ships. No exceptions; gates are data-checked.
- **Allow-list first, license-as-data.** Licensing is structured, checkable data; the per-deliverable
  gate is verified consistently. Restricted sources are recorded as **excluded** with a reason.
- **Public-domain-first sourcing.** Prefer U.S.-Government public-domain references; exclude
  non-commercial/restricted (COSMIC/OncoKB) and copyrighted advocacy content absent written permission.
- **"Not medical advice" is structural, not cosmetic.** `notMedicalAdvice: true` is a required,
  CI-enforced field; advice-creep is an explicit reviewer and self-check item.
- **No patient data, by construction.** A glossary needs none; the schema has no field for it and the
  pipeline never ingests individual-level or controlled-access data.

## Data, licensing & compliance

**THIS IS THE PROJECT'S MOST IMPORTANT SECTION. The binding cancer-domain guardrails below lead it.
Be conservative; when terms are unclear or a source is restricted, do not use it.**

### Binding cancer-domain guardrails (non-negotiable)

1. **Open-access / aggregate / de-identified ONLY.** Term definitions are derived solely from
   open-access, published reference sources. The project ingests **no patient-level data**.
2. **Controlled-access data is OUT OF SCOPE.** **dbGaP, EGA, individual-level biobanks**, and **any
   identifiable patient data** are categorically excluded — they require authorized access + IRB and
   are unnecessary for a glossary. There is **no** code path, schema field, or task that ingests them.
3. **Per-source license verification is mandatory.** No term is defined from a source until that
   source is **allow-listed with verified terms.** Open research repositories (TCGA, GEO, PRIDE) are
   open but **irrelevant** to this glossary (no patient-level data needed); **COSMIC and OncoKB are
   non-commercial / restricted** and are **excluded**. SNOMED CT is licensed and **excluded** absent
   a confirmed member license. Copyrighted advocacy content (e.g., ASCO Cancer.Net, ACS) is
   all-rights-reserved and **excluded** absent written permission.
4. **No medical advice.** Output is **education only**; every entry carries a "not medical advice —
   talk to your care team" notice and is **oncologist + patient-advocate reviewed** (riskTier HIGH).
5. **Provenance on every assertion.** Every definition cites the allow-listed source(s) it derives
   from; provenance is non-optional and part of the license gate.

### Sources & licenses (per-source allow-list)

Only allow-listed, terms-verified sources may be used. Preferred (verify each per term):

- **NCI Dictionary of Cancer Terms** (US National Cancer Institute) — U.S.-Government work, generally
  **public domain**; NCI requests attribution and some materials may carry restrictions or embed
  third-party content. **Verify per term; record attribution.** Primary patient-facing source.
- **ClinicalTrials.gov** "Learn About Studies" / glossary (US NIH/NLM) — U.S.-Government work,
  generally **public domain**; verify embedded third-party material. Primary for trial terminology.
- **NCI Thesaurus (NCIt)** — open terminology under NCI's open terms; verify current license.
- **MeSH (Medical Subject Headings)** (NLM) — U.S.-Government, generally **public domain**; verify.
- **MedlinePlus** (NLM) — much is public domain **but it embeds copyrighted third-party content
  (e.g., A.D.A.M. encyclopedia)**; **do not assume reusable — verify per item.**

**Excluded unless explicitly permitted (recorded as `restricted` with `exclusionReason`):**
- **COSMIC, OncoKB** — non-commercial / restricted (per Hee-Lee Oss cancer guardrails). Excluded.
- **SNOMED CT** — member-licensed. Excluded absent a confirmed license.
- **ICD-O / ICD-11** — WHO copyright with its own reuse terms; treat as restricted, verify before any
  use, include required WHO attribution if ever permitted.
- **Copyrighted patient-advocacy content** (ASCO Cancer.Net, ACS, etc.) — all-rights-reserved;
  excluded absent written permission.

For **each** source we record: canonical URL, version/date, retrieval date, license name + URL, a
**snapshot of the license text**, derivatives/translation permission, the required attribution
string, a `restricted` flag with `exclusionReason` where applicable, and `verifiedBy` + `verifiedDate`.

**Snapshot integrity (hash/archive-based).** The license snapshot is stored as a committed archive
copy plus a recorded **SHA-256 content hash** and, where possible, a web-archive (Wayback) URL. A
**source-change watcher** (minimal manual/scripted re-fetch in M0; automated in M2) periodically
re-fetches each allow-listed source, recomputes the hash, and **flags any drift** in the source or
its license for re-verification before further use or delivery — important here because oncology
definitions and trial terminology **change over time** (new therapies, updated standards).

**BLOCKING prerequisite for the first source-definition set.** Before `glossary-source-001` (seed
authoring) may begin, the maintainer/license reviewer must have **confirmed and recorded on each
chosen source's allow-list entry** that the source's terms **permit derivative works** (a
plain-language definition derived from it) and the **exact required attribution**. If a candidate
source cannot be confirmed, it is **excluded** and a different allow-listed source is used. This is a
**hard gate, not an open question.**

**Provenance model.** Every deliverable ships provenance linking it to its source entry, version,
retrieval date, glossary/termbase version, drafter (agent + human), and reviewer sign-offs
(clinical, advocate, translation). Provenance is part of the license gate.

**Output licensing.** Project **tooling/code is MIT**. **Original glossary content** authored by this
project (the plain-language definitions and translations the project creates) is released under
**CC BY 4.0** *to the extent the underlying source permits derivatives and a CC-BY relicense* —
because most preferred sources are **U.S.-Government public domain**, an original CC-BY plain-language
definition derived from them is appropriate, **with attribution to the source preserved**. Where a
source's terms are more restrictive (e.g., WHO/ICD material, if ever permitted), the derived content
**inherits the source-compatible license** and **is not** relicensed more permissively. Where a
source forbids derivatives, it is **not** used. The "not medical advice" notice travels with every
entry in every language.

**Privacy / PII.** Sources are public reference material; the project ingests **no personal or
patient data** and collects **no end-reader data.** PII surface is essentially nil by design. Partner
and reviewer contact details are handled out-of-band and never committed.

**Attribution requirements.** Every deliverable includes the source attribution string(s) and the
"not medical advice" notice verbatim, in the target language (and a pivot language where the partner
requires it).

## Quality, review & risk gates

**Risk tier: HIGH.** Per the good-deed definition, HIGH (health/safety) **requires credentialed
expert sign-off before merge.** Here that means **two credentialed/qualified human gates**, both
blocking, recorded in the PR, and **data-enforced** (an entry without the required sign-off cannot
reach `delivered`/`done`).

**Required review before a deed is "done"**
1. **Agent self-check** — readability check + **advice-creep self-check** + emitted `UNCERTAIN:`
   flags (first pass; never sufficient alone).
2. **Source-definition gate (both required):**
   - **Credentialed oncology clinician** sign-off (accuracy, not misleading, scope-appropriate).
   - **Patient-advocate** sign-off (plain language, reading level, emotional appropriateness, no
     advice creep).
3. **Translation gate (per language):** **qualified medical-translation reviewer** sign-off, with
   **back-translation QA mandatory** for safety-load terms (dosing/negation/urgency/consent).
4. **License + provenance verification** (source allow-listed + permitted, attribution present,
   provenance complete, "not medical advice" present, output license correct).
5. **CI green** for code/tooling and structural checks on content files.
6. **Maintainer approval** of the PR.

**Reviewer independence, COI, and disagreement resolution.** Reviewers must be **independent of the
drafting step** — the human who ran the drafting agent may **not** be a sign-off reviewer for that
entry — and each records a **conflict-of-interest declaration**. The **clinical and advocate
reviewers must be different people** (clinical accuracy and plain-language clarity are distinct
competencies). If reviewers disagree, the entry **cannot** be signed off until resolved: (1) reconcile
against the source/termbase, recording rationale; (2) escalate unresolved safety-relevant
disagreements to a **second clinician / the maintainer**, decision recorded; (3) **when in doubt, the
more conservative reading wins** and the disputed entry is held back. Recorded in the review artifact.

**Agent uncertainty self-check (operationalized).** As part of step 1 the drafting agent must **emit
explicit uncertainty flags** — one per flag of the form
`UNCERTAIN: <termId/location> | <type: term|negation|advice-creep|cultural|ambiguous-source> | <note>` —
for anything it is unsure of, **and must refuse outright** to author advice/dosing/diagnostic content
(flagging it instead per Hee-Lee Oss refusal guardrails). Flags are copied into the review artifact as
`agentFlags`. **No sign-off may be recorded while any flag is unresolved**: each must be `resolved`
(reviewer adjudication) or `accepted-as-is` (with reasoning). Unresolved flags **block** "done".

**Definition of Shipped (project-specific).** A glossary entry/set is *shipped* only when: acceptance
criteria met **and** clinical + advocate sign-off recorded (source) **and**, for translations,
qualified medical-translation sign-off (+ back-translation QA for safety-load terms) recorded **and**
license/attribution/"not medical advice"/provenance verified **and** CI green **and** the set is
**delivered to and adopted by a partner organization** for distribution to beneficiaries.
Merged-but-not-adopted is **not** shipped.

## Roadmap & milestones

**M0 — Foundation & cold-start (no partner, no reviewers required).**
Goal: stand up the license/schema/review machinery and prove the *source-definition* drafting path on
a tiny sample — **without** publishing any patient-facing definition (no sign-off yet).
Exit criteria: term-source allow-list with **≥ 3 verified sources** (≥ 1 explicitly **excluded** with
reason; hash/archived snapshots; minimal source-change re-fetch check); content JSON schemas + CI
structural check merged (incl. `notMedicalAdvice` required-true and sign-off-presence rules);
clinical, advocate, and translation **review checklists** + handoff template + "not medical advice"
notice merged; the **BLOCKING license prerequisite confirmed/recorded** for the seed sources;
**≤ 10 draft** source definitions produced **and held in `review` (not published)** with agent
uncertainty flags captured. `verifiedNeed: false` throughout. Metrics 100%/≥90% are **effective from
M1/M2**; M0 enforces the structural + license-presence checks.

**M1 — Reviewed source glossary (needs clinical + advocate reviewers).**
Goal: secure the expert reviewers and produce the **reviewed** source-language seed glossary.
Exit criteria: **≥ 1 credentialed oncology clinician and ≥ 1 patient advocate secured** (COI on file;
M1 **entry gate** — if unmet, hold at M0); reviewer-qualification + independence + disagreement rules
documented; **≥ 25 source terms authored and clinical+advocate-signed-off**, each with provenance,
"not medical advice", and reading-level metadata; license/provenance check formalized; ≥ 90%
source-sign-off rate reportable (min sample). Dependency: clinical + advocate reviewer sourcing.

**M2 — First translated language + translation pipeline (needs translation reviewer).**
Goal: translate the reviewed seed glossary into one target language with full translation review.
Exit criteria: **≥ 1 qualified medical-translation reviewer for the first pair secured** (M2 **entry
gate**); language-pair termbase started; the reviewed source set translated into one language with
**translation review + back-translation QA on all safety-load terms**; **automated source-change
watcher operating**; license/provenance enforcement automated in CI (`license-002`). Dependency:
translation-reviewer sourcing.

**M3 — First partner delivery (needs partner).**
Goal: deliver an adopted glossary set. **All tasks gated on a secured partner** (`verifiedNeed`
flips to `true` only when confirmed).
Exit criteria: partner organization secured; target language + priority term list agreed; **≥ 1
reviewed, correctly-licensed translated glossary set delivered and confirmed adopted** for
distribution. First true Definition-of-Shipped event. **Steward named** (prerequisite to enter M3).

**M4 — Scale program.**
Goal: scale languages/terms with sustained quality and tracked outcomes.
Exit criteria: ≥ 2 languages with ≥ 100 reviewed terms adopted; reviewer rotation established;
outcome tracking (post-delivery defect log, partner/beneficiary feedback) operating; source/term
maintenance cadence (re-verify sources; refresh as oncology terminology evolves) in effect; named
sustainability owner.

## Work breakdown

The itemized, sized backlog lives in **[TASKS.md](./TASKS.md)**, organized by the milestones above
(M0–M4) plus a Backlog/future section. Each task maps to a Hee-Lee Oss Task JSON (schema in
`packages/schema/src/schemas.ts`) with id, type, lane, risk tier, deliverable, acceptance criteria,
and license fields. M0/M1 tasks are partner-independent foundations; M1 gates on clinical+advocate
reviewers, M2 on a translation reviewer, M3+ on a secured partner — all marked accordingly
(`verifiedNeed: false` until then).

## Governance, roles & stakeholders

- **Maintainer (Owner): J. Carter (acting)** — assigned now; accepts/sequences tasks, approves PRs,
  owns the allow-list integrity and the license/provenance gate; acts as interim license/compliance
  reviewer until a dedicated one is named. **Does not** substitute for the clinical or advocate gate.
- **Credentialed oncology reviewer(s): TO BE SECURED** — oncologist / oncology pharmacist / oncology
  nurse with relevant scope; signs off every source definition for clinical accuracy. **Required
  before any source definition is published** (M1 entry gate). Candidate sources: academic cancer
  centers, oncology professional societies, volunteer clinician networks.
- **Patient-advocate reviewer(s): TO BE SECURED** — cancer patient advocates / health-literacy
  specialists / patient navigators; signs off plain-language clarity, reading level, and emotional
  appropriateness. Must be a **different person** from the clinician.
- **Qualified medical-translation reviewer(s) (per language pair): TO BE SECURED** — native/
  near-native, medical-translation competent; signs off each translation; back-translation QA for
  safety-load terms. Candidate partner: Translators without Borders / CLEAR Global.
- **License/compliance reviewer** — may be the maintainer initially; verifies per-source terms and
  per-deliverable attribution/provenance/"not medical advice"; escalates ambiguous/restricted sources.
- **Steward (last-mile owner): TBD — named by end of M2** (acting maintainer holds interim). Owns the
  partner relationship and confirms **adoption**; naming a steward is a **prerequisite to enter M3**.
- **Partner / requestor: TO BE SECURED** — cancer-care/advocacy org defining language + term
  priorities and confirming adoption.
- **Reviewer rotation** defined in M4 to avoid single-person dependence on any gate.

## Dependencies & integrations

- **Hee-Lee Oss donated lane**: `packages/cli` (workspace prep + PR), `packages/core`, `packages/schema`
  (Task JSON + new content schemas). No funded-lane / API-key execution in v0.1.
- **Public reference sources** (read-only retrieval, license-verified): NCI Dictionary of Cancer
  Terms, ClinicalTrials.gov, NCI Thesaurus, MeSH, MedlinePlus (per-item verification).
- **Credentialed oncology reviewer(s)** — external dependency, **not yet secured** (M1 entry gate).
- **Patient-advocate reviewer(s)** — external dependency, **not yet secured** (M1 entry gate).
- **Qualified medical-translation reviewer(s)** (e.g., Translators without Borders / CLEAR Global) —
  external dependency, **not yet secured** (M2 entry gate).
- **Partner cancer-care/advocacy organization** for requirements + adoption — **not yet secured**
  (M3 gate).
- **Terminology references** (e.g., medical termbases) per language, subject to their own licenses.

## Risks & mitigations

| Risk | Likelihood | Impact | Mitigation | Owner |
|---|---|---|---|---|
| Inaccurate/misleading definition reaches a frightened patient (meaning error, false reassurance) | Medium | Critical | Two blocking gates (clinical + advocate); "not medical advice" notice; conservative-reading rule; post-delivery defect log (target 0) | Clinical reviewer / Maintainer |
| Advice creep — glossary drifts from defining terms to telling people what to do | Medium | Critical | Explicit non-goal; agent advice-creep self-check + refusal; advocate review item; CI `notMedicalAdvice` field | Advocate / Maintainer |
| Mistranslation of safety-load term (negation, urgency, "call your care team") | Medium | Critical | Termbase-driven drafting; qualified translation review; **mandatory back-translation QA** for safety-load terms | Translation reviewer / Steward |
| Use of restricted/non-commercial source (COSMIC/OncoKB/SNOMED) or copyrighted advocacy content | Medium | High | Allow-list-first; restricted sources recorded as **excluded**; per-deliverable license gate; "if unclear, don't use" | License reviewer / Maintainer |
| **No credentialed clinical/advocate reviewer secured** → cannot publish definitions | High | High | M1 **entry gate** + dated outreach + **hold-at-M0 decision rule**; never publish without expert sign-off | Acting maintainer → Steward |
| No qualified translation reviewer for a language | High | High | Reviewer availability is a hard precondition; partner with translation NGO; don't ship unreviewed languages | Steward / Maintainer |
| No partner secured → nothing reaches "shipped" | High | High | M0–M2 build partner-independent value; dated outreach; pivot/mothball decision rule (~2027-03-31); `verifiedNeed=false` until secured | Acting maintainer → Steward |
| Accidental ingestion of patient/controlled-access data | Low | Critical | **No** schema field/task/code path for it; explicit non-goal; allow-list contains only open reference sources | Maintainer |
| Oncology terminology/source changes after publication | Medium | Medium | Version + retrieval date in provenance; source-change watcher (hash-diff); maintenance cadence; note original source is authoritative | Maintainer |
| Agent overconfidence / unflagged uncertainty | Medium | High | Operationalized `UNCERTAIN:` flags into review artifact; unresolved flags **block** sign-off; output treated as draft only | Reviewers |
| Reading-level too high / culturally inappropriate | Medium | Medium | ≤ grade-8 target + readability check; advocate is the authority; cultural-appropriateness review item | Advocate |

## Security & privacy

- **Threat surface** is small: public reference ingestion + text artifacts in a public repo. Main
  risks are **integrity** (wrong/tampered source; inaccurate definition; mistranslation), **advice
  creep**, and **license/compliance** — not data exfiltration.
- **No patient data, by construction.** No controlled-access (dbGaP/EGA/biobank) or identifiable
  patient data is ever ingested; there is no schema field or code path for it. End-readers are not
  tracked; no analytics on patients.
- **No secrets** in the normal flow (donated lane uses the human's own agent; no API keys/tokens/
  escrow). Per CLAUDE.md, never write secrets/tokens into logs, receipts, or committed files.
- **PII**: none ingested; partner/reviewer contacts kept out-of-band and uncommitted.
- **Abuse/misuse prevention**: refusal guardrails apply — refuse to author medical advice/dosing/
  diagnosis, refuse misinformation, refuse restricted-source/copyright violations, refuse anything
  targeting/deceiving people. Source-integrity verification (correct, current, authoritative source)
  is part of every authoring task.
- **Supply-chain**: pin source URLs + version/date; **hash/archive the license + source snapshot**;
  run a source-change watcher (hash-diff) to detect later changes (M0 minimal, M2 automated).

## Sustainability & maintenance

- **After delivery**, maintainer + steward keep the allow-list, source glossary, and translations
  current, re-verifying sources and refreshing entries as oncology terminology and trial standards
  evolve. Reviewer rotation (M4) avoids single-person dependence on any gate.
- **Outcome tracking** continues post-delivery: a per-language defect/feedback log (critical defects
  target 0), and periodic partner/beneficiary check-ins to confirm continued use and capture errors.
  Outcomes (adoption, defects, usefulness, readability) — not merge counts — are the maintained metrics.
- **Decommissioning**: if a source's license changes to forbid reuse, affected entries are flagged
  and, if required, withdrawn; provenance makes impact assessment possible. If a definition is found
  inaccurate post-delivery, it is **withdrawn first, corrected, re-reviewed, then re-published.**

## Open questions

1. **Clinical + advocate reviewers** (blocks M1 and any publication): which credentialed oncology
   reviewer(s) and patient advocate(s) sign off the source definitions? Outreach owner: acting
   maintainer; target ≥ 3 clinical + ≥ 2 advocate conversations by 2026-08-31; ≥ 1 each secured by
   2026-10-31; **hold at M0 if unmet.**
2. **Translation reviewers** (blocks M2 per language): recruit individuals or partner with a
   translation NGO (Translators without Borders / CLEAR Global)? Formal qualification criteria?
3. **Partner / requestor** (blocks M3 and `verifiedNeed=true`): which cancer-care/advocacy org is the
   first requestor, and what are their target language(s) and priority terms? Pivot/mothball decision
   point ~2027-03-31 if none.
4. **First target language(s)**: chosen by the target-language rule once a partner/translation
   reviewer is known.
5. **Output license per source family**: confirm CC-BY relicense is correct for public-domain-derived
   definitions vs. inheriting source-compatible terms for any WHO/ICD material (if ever permitted).
6. **MedlinePlus per-item verification**: workflow to reliably distinguish public-domain NLM content
   from embedded copyrighted third-party content (A.D.A.M., etc.).
7. **Funded lane?** Proposal says donated; metered drafting under escrow for surge demand is out of
   scope for v0.1.

## References

- `C:\code\hee-lee-oss\CLAUDE.md` — Hee-Lee Oss work rules, lanes, quality bar, refusal & cancer guardrails.
- `C:\code\hee-lee-oss\docs\good-deed-definition.md` — good-deed criteria and risk tiers (HIGH ⇒ expert sign-off).
- `C:\code\hee-lee-oss\packages\schema\src\schemas.ts` — Task JSON schema (+ new content schemas to add).
- `C:\code\hee-lee-oss\planning\ROADMAP.md` — portfolio + Track 8 cancer-research guardrails.
- NCI Dictionary of Cancer Terms; NCI Thesaurus (verify current open terms).
- ClinicalTrials.gov "Learn About Studies" / glossary (US Gov, verify embedded third-party).
- MeSH (NLM) and MedlinePlus content-use notices (verify per item for embedded copyrighted content).
- Translators without Borders / CLEAR Global (candidate translation-reviewer partner).

---

## Appendix A — Improvements applied

The following 25 specific improvements were identified during drafting and are **already applied** in
the body of this PLAN (and the companion TASKS.md). Each lists the concrete change.

1. **Two-stage blocking review gate made the pipeline spine** (clinical + advocate on source, then
   translation review) rather than a single review — reflecting HIGH risk tier. *(Exec summary;
   Quality gates; Architecture Stage A/B.)*
2. **"Not medical advice" turned into a CI-enforced data field** (`notMedicalAdvice: true` required;
   structural check fails otherwise) instead of a prose disclaimer. *(Architecture; Quality gates.)*
3. **Cancer guardrails promoted to lead the Data/licensing section** as an explicit numbered,
   non-negotiable block (open-access only; controlled-access out of scope; per-source verification;
   no advice; provenance). *(Data, licensing & compliance.)*
4. **Restricted sources named and pre-excluded** (COSMIC/OncoKB non-commercial; SNOMED CT licensed;
   copyrighted advocacy content) with an `exclusionReason` field, not left to chance. *(Data section;
   data model.)*
5. **"No patient data by construction"** — stated that no schema field, task, or code path ingests
   controlled-access/identifiable data, making it structurally impossible, not just policy. *(Scope;
   Architecture; Security.)*
6. **Dated reviewer- and partner-acquisition plan** with explicit milestone entry gates (M1 needs
   clinical+advocate; M2 needs translation reviewer; M3 needs partner). *(Exec summary; Roadmap.)*
7. **Hold-at-M0 / pivot / mothball decision rule** added so the project never publishes patient-facing
   content without experts and never "ships to nobody." *(Exec summary; Risks; Open questions.)*
8. **Advice-creep treated as a top-tier (Critical) risk** with its own agent self-check, refusal
   behavior, advocate review item, and CI field — distinct from accuracy error. *(Quality gates; Risks.)*
9. **Mandatory back-translation QA for safety-load terms** (dosing/negation/urgency/consent), with a
   `safetyLoad` enum field driving when it triggers. *(Architecture; Quality gates; data model.)*
10. **Objective term-selection rule** (frequency + allow-listed source + safety/consent priority)
    rather than vague "important terms." *(Scope.)*
11. **Objective target-language rule** (FLORES-200 reference + under-served population + reviewer
    availability as a hard precondition). *(Scope.)*
12. **Objective reading-level target (≤ grade 8)** with an automated score as a floor and advocate
    judgment as the authority. *(Scope; success metrics.)*
13. **Clinical and advocate reviewers required to be different people** (distinct competencies) and
    both independent of drafting, each with a COI declaration. *(Quality gates; Governance.)*
14. **Operationalized agent uncertainty flags** in a defined `UNCERTAIN:` format copied into the
    review artifact; unresolved flags **block** sign-off. *(Quality gates.)*
15. **Disagreement/conflict-resolution ladder** (reconcile → escalate → conservative reading wins),
    recorded in the review artifact. *(Quality gates.)*
16. **License snapshot integrity via SHA-256 hash + web-archive URL** and a source-change watcher
    (minimal M0, automated M2) — because oncology terminology changes over time. *(Data; Security.)*
17. **BLOCKING license prerequisite for the seed authoring task** (derivative permission + exact
    attribution recorded per source) — a hard gate, not an open question. *(Data; TASKS license-000.)*
18. **Content JSON Schemas in the core schema package** (allow-list/entry/translation/review) wired
    into `pnpm test`, keeping validation agent-neutral per CLAUDE.md. *(Architecture.)*
19. **Outcome metrics separated into post-partner vs. partner-independent interim metrics**, so
    progress is visible before a partner exists; vanity metrics explicitly rejected. *(Success metrics.)*
20. **Min-sample rule (≥ 10) for the ≥ 90% pass rates** to avoid small-sample noise; effective-from
    milestones stated. *(Success metrics.)*
21. **Output-license logic made source-aware** (CC-BY for public-domain-derived definitions; inherit
    source-compatible terms otherwise; never relicense restrictively-sourced content more permissively).
    *(Data, licensing.)*
22. **MedlinePlus embedded-copyright trap called out explicitly** (A.D.A.M. etc.) with per-item
    verification required and an open question to define the workflow. *(Data; Open questions.)*
23. **Withdraw-first correction policy** for post-delivery inaccuracies (withdraw → correct →
    re-review → re-publish), leveraging provenance for impact assessment. *(Sustainability.)*
24. **Explicit non-goals against scope creep** into trial-finding/eligibility/decision-aids/symptom-
    checking (which are separate higher-surface projects), keeping this project bounded. *(Goals/non-goals.)*
25. **Steward-before-M3 governance prerequisite** and reviewer-rotation in M4 to remove single-person
    dependence on any blocking gate. *(Governance; Roadmap.)*

---

## Review sign-off

**Reviewer:** Acting maintainer (self-review against PLAN_SPEC.md, CLAUDE.md, the good-deed
definition, the Track 8 cancer guardrails, and the Task JSON schema). **Date:** 2026-06-28.

**Completeness check.** All 17 required H2 sections are present and in order. Data/licensing &
compliance leads with the binding cancer guardrails. The HIGH-risk patient-facing gate (oncologist +
patient-advocate sign-off, then qualified translation review) is **blocking** and data-enforced.
TASKS.md exists with schema-mapped tasks, per-milestone tables + acceptance criteria, a backlog, and
a schema-valid example Task JSON with `verifiedNeed: false`.

**Correctness fixes applied during review.**
- Confirmed the example Task JSON in TASKS.md includes **every** schema-`required` field
  (`id, title, project, type, lane, priority, domain, riskTier, urgent, deliverable, tokenEstimate,
  status, context, objective, acceptanceCriteria, output, verifiedNeed`) and uses only enum-valid
  values; `acceptanceCriteria` has ≥ 1 item; `output` is non-empty. No `fundedBudgetUsd` because the
  task is `lane: donated` (the schema only requires it for funded tasks).
- Verified **no task** ingests patient-level or controlled-access data; the seed-authoring task draws
  only from allow-listed open reference sources.
- Aligned milestone **entry gates** between PLAN Roadmap and TASKS (M1 = clinical+advocate, M2 =
  translation reviewer, M3 = partner) and ensured `verifiedNeed: false` on all delivery-dependent tasks.
- Ensured the riskTier on patient-facing source/translation authoring tasks is **high**, while pure
  tooling/allow-list/process tasks are **low** — consistent with the good-deed risk-tier definition.
- Reconciled the "not medical advice" notice as both a required content field and a CI structural check.

**Residual items requiring a human decision** (carried in Open questions): securing credentialed
clinical + advocate reviewers (blocks M1/publication), a qualified translation reviewer (blocks M2),
and a partner/steward (blocks M3 and `verifiedNeed=true`); plus the MedlinePlus per-item license
verification workflow. **Sign-off status: APPROVED as a Draft (v0.1.0)** for foundation work (M0–M1
authoring machinery); **publication of any patient-facing definition is blocked** until the M1
reviewer gate is satisfied.
