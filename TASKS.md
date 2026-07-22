# TASKS — oncology-glossary-multilingual

> Status: Draft · Version: 0.1.0 · Last updated: 2026-06-28 · Owner: J. Carter (acting maintainer) · Lane: donated · Risk tier: **HIGH**

The backlog for the `oncology-glossary-multilingual` good-deed project. Read alongside
[PLAN.md](./PLAN.md). Milestones (M0–M4) match the roadmap there. This project is **HIGH risk**
(patient-facing health content): every patient-facing definition requires **oncologist + patient-
advocate sign-off** before translation, and every translation requires **qualified medical-translation
review** (with back-translation QA for safety-load terms) before it ships.

## How these tasks map to Hee-Lee Oss

Each task below becomes an **Hee-Lee Oss Task JSON** validated against `packages/schema/src/schemas.ts`.
Field mapping:

- **id** — stable slug id, e.g. `oncology-glossary-multilingual-sources-001` (table column `ID`).
- **title** — the task title.
- **project** — always `oncology-glossary-multilingual`.
- **type** — one of `code | research | writing | data | design-spec | maintenance` (table `Type`).
- **lane** — `donated` for all current tasks (no funded/API execution). Funded tasks would need
  `fundedBudgetUsd`; none here.
- **priority** — `high | medium | low`.
- **domain** — array, e.g. `["oncology","translation","patient-education"]`.
- **riskTier** — `low | medium | high`. **Patient-facing definition/translation authoring tasks are
  `high`** (require credentialed expert + advocate / qualified-translation sign-off). Pure tooling,
  allow-list, schema, and process tasks are `low` (table `Risk`).
- **urgent** — boolean (default `false`; no live emergency).
- **deliverable** — `pr | dataset | document | translation` (table `Deliverable`).
- **tokenEstimate** — `small | medium | large` (table `Size`).
- **status** — `open | in-progress | review | delivered | done` (all start `open`).
- **context / objective** — why + what.
- **acceptanceCriteria[]** — checkable bullets (listed below tables for key tasks).
- **resources[]** — links/paths (allow-list, source URLs, checklists, schema).
- **output** — path/description of the produced artifact.
- **requestor** — partner/requestor; `TO BE SECURED` until a partner is confirmed.
- **verifiedNeed** — boolean; **`false`** wherever value depends on an unsecured partner/reviewer.
- **outputLicense** — **MIT** for code/tooling and project metadata (allow-list, schemas); **CC BY 4.0**
  for original plain-language glossary content/translations derived from public-domain sources (with
  source attribution preserved); **source-compatible license** where a source's terms are stricter.

---

## Milestone M0 — Foundation & cold-start (no partner, no reviewers required)

Goal: stand up license/schema/review machinery and prove the source-definition *drafting* path on a
tiny sample — **without publishing any patient-facing definition** (no sign-off available yet).

| ID | Title | Type | Size | Risk | Deliverable | Depends on | Reviewer |
|---|---|---|---|---|---|---|---|
| oncology-glossary-multilingual-sources-001 | Build term-source allow-list with per-source license terms + exclusions (snapshot hash/archive) | data | small | low | dataset | — | Maintainer / License reviewer |
| oncology-glossary-multilingual-license-000 | **BLOCKING:** confirm derivative permission + exact attribution for each seed source | research | small | low | document | sources-001 | License reviewer / Maintainer |
| oncology-glossary-multilingual-schema-001 | Content JSON schemas (allow-list/entry/translation/review) + CI structural check | code | medium | low | pr | sources-001 | Maintainer |
| oncology-glossary-multilingual-review-001 | Clinical review checklist (oncology accuracy) | writing | small | low | document | — | Maintainer |
| oncology-glossary-multilingual-review-002 | Patient-advocate review checklist (plain language, reading level, no advice creep) | writing | small | low | document | — | Maintainer |
| oncology-glossary-multilingual-review-003 | Translation review checklist + back-translation QA procedure | writing | small | low | document | review-001 | Maintainer |
| oncology-glossary-multilingual-template-001 | Reviewer-handoff template + "not medical advice" notice | writing | small | low | document | review-001, review-002 | Maintainer |
| oncology-glossary-multilingual-draft-001 | Draft ≤10 source definitions (held in `review`, not published) | writing | small | high | document | sources-001, license-000, schema-001, template-001 | Maintainer (drafts only; no sign-off yet) |
| oncology-glossary-multilingual-watcher-000 | Minimal source-change re-fetch check (hash compare) [automated in M2] | code | small | low | pr | sources-001, schema-001 | Maintainer |

**Acceptance criteria — key M0 tasks**

`sources-001` (term-source allow-list)
- `sources/term-source-allow-list.yaml` lists ≥ 3 candidate sources including ≥ 1 U.S.-Gov
  public-domain source (e.g., NCI Dictionary of Cancer Terms or ClinicalTrials.gov) and ≥ 1 source
  **explicitly excluded** with reason (e.g., COSMIC/OncoKB non-commercial; SNOMED CT licensed).
- Each entry records: name, canonical URL, version/date, retrieval date, license name + URL, snapshot
  of license text, `derivativesAllowed`, required attribution string, `restricted` flag +
  `exclusionReason` where applicable, `snapshotHash` (SHA-256) and `snapshotArchiveUrl` where available.
- Each entry has `verifiedBy` + `verifiedDate`.
- **No controlled-access or patient-level source appears** (dbGaP/EGA/biobanks are categorically out).
- File validates against the project content schema and passes CI structural checks.

`license-000` (BLOCKING prerequisite of `draft-001`)
- For **each seed source** used by `draft-001`, confirm and record in writing on its allow-list entry:
  (a) terms **permit a derivative plain-language definition**, and (b) the **exact required
  attribution**.
- Any source that cannot be confirmed is **excluded**; `draft-001` uses only confirmed sources.
- `draft-001` **must not start** until this passes.

`schema-001` (content schemas + CI)
- Adds `termSourceAllowListSchema`, `glossaryEntrySchema`, `glossaryTranslationSchema`, `reviewSchema`
  to `packages/schema/src/schemas.ts`, compiled via `validate.ts` (AJV + ajv-formats).
- Structural check (wired into `pnpm test`) **fails CI** if: `notMedicalAdvice` is not `true`,
  `sourceRefs` is empty, a `delivered`/`done` entry lacks the required sign-offs, or a YAML artifact
  is malformed/non-conformant.

`draft-001` (seed drafts — NOT published)
- `license-000` passed before drafting.
- ≤ 10 source definitions drafted, ≤ grade-8 reading level, each grounded in an allow-listed source
  with `sourceRefs`, `notMedicalAdvice: true`, and the "not medical advice" notice.
- Agent **uncertainty flags** (`UNCERTAIN: …` format) captured into the review artifact as
  `agentFlags`; any advice/dosing/diagnostic content is **refused and flagged**, not authored.
- All entries remain in `review` status (**not published**); they carry **no** sign-off because no
  reviewer is secured yet (that is M1). `verifiedNeed: false`.

**M0 Definition of Done:** allow-list (≥ 3 verified sources incl. ≥ 1 excluded, with snapshot
hash/archive) + content schemas + CI structural check + minimal source-change re-fetch check +
clinical/advocate/translation checklists + handoff template + "not medical advice" notice merged;
`license-000` confirmed for seed sources before drafting; ≤ 10 source definitions drafted and **held
in `review` (unpublished)** with uncertainty flags captured. No patient-facing content is published.
All M0 deliverables carry `verifiedNeed: false`.

---

## Milestone M1 — Reviewed source glossary (needs clinical + advocate reviewers)

Goal: secure the expert reviewers and produce the **reviewed** source-language seed glossary.
**Entry gate:** ≥ 1 credentialed oncology reviewer **and** ≥ 1 patient advocate secured — else hold at M0.

| ID | Title | Type | Size | Risk | Deliverable | Depends on | Reviewer |
|---|---|---|---|---|---|---|---|
| oncology-glossary-multilingual-reviewers-001 | Reviewer qualification + independence + disagreement rules (clinical & advocate) | writing | small | low | document | review-001, review-002 | Maintainer |
| oncology-glossary-multilingual-reviewers-002 | Secure ≥1 oncology reviewer + ≥1 patient advocate (COI on file) | research | medium | low | document | reviewers-001 | Maintainer / Steward |
| oncology-glossary-multilingual-glossary-source-001 | Author + clinical/advocate-review ≥25 source terms (seed glossary) | writing | large | high | document | draft-001, reviewers-002 | Oncology reviewer + Patient advocate |
| oncology-glossary-multilingual-license-001 | Per-deliverable license + provenance + "not medical advice" check procedure | research | small | low | document | sources-001, schema-001 | License reviewer |

**Acceptance criteria — key M1 tasks**

`reviewers-001`
- Objective criteria for "credentialed oncology reviewer" (clinician/pharmacist/nurse with relevant
  scope) and "patient advocate" (health-literacy/navigation competence), each with a COI declaration.
- Defines **reviewer independence** (drafting human may not sign off), that **clinical and advocate
  reviewers must be different people**, the **disagreement-resolution ladder** (reconcile → escalate
  → conservative reading wins; recorded in the review artifact), and that **unresolved agent flags
  block sign-off**.

`glossary-source-001` (the core reviewed seed glossary)
- ≥ 25 source terms authored with ≤ grade-8 definitions, `sourceRefs`, `safetyLoad` classification,
  `notMedicalAdvice: true`, and reading-level metadata.
- **Each term has both clinical sign-off and advocate sign-off recorded** in the review artifact; an
  entry missing either remains `blocked` and is not counted.
- All agent uncertainty flags `resolved` or `accepted-as-is` with reasoning before sign-off.
- License/provenance check (`license-001`) green for every entry; output license correct.
- No advice/dosing/diagnostic content (advocate + clinical review confirm; CI `notMedicalAdvice` true).

`license-001`
- A checkable procedure verifying, per deliverable: source allow-listed + permitted, attribution
  present, provenance complete, "not medical advice" present, output license source-compatible.
  Documented as a checklist tooling can later enforce (`license-002`).

**M1 Definition of Done:** ≥ 1 oncology reviewer + ≥ 1 patient advocate secured (COI on file);
qualification/independence/disagreement rules published; **≥ 25 source terms authored and
clinical+advocate-signed-off** with provenance + "not medical advice" + reading-level metadata;
per-deliverable license/provenance check formalized; source-sign-off pass rate reportable once sample
≥ 10. `verifiedNeed: false` (no partner yet).

---

## Milestone M2 — First translated language + translation pipeline (needs translation reviewer)

Goal: translate the reviewed seed glossary into one target language with full translation review.
**Entry gate:** ≥ 1 qualified medical-translation reviewer for the first pair secured.

| ID | Title | Type | Size | Risk | Deliverable | Depends on | Reviewer |
|---|---|---|---|---|---|---|---|
| oncology-glossary-multilingual-translators-001 | Secure ≥1 qualified medical-translation reviewer (first language) | research | medium | low | document | reviewers-002 | Maintainer / Steward |
| oncology-glossary-multilingual-termbase-001 | Language-pair termbase (preserved terms, units, transliteration) | data | small | high | dataset | glossary-source-001, translators-001 | Translation reviewer |
| oncology-glossary-multilingual-translate-001 | Translate reviewed seed glossary into first target language (+ back-translation QA) | translation | large | high | translation | glossary-source-001, termbase-001 | Translation reviewer |
| oncology-glossary-multilingual-license-002 | Automate license/provenance/"not medical advice" enforcement in CI | code | medium | low | pr | license-001, schema-001 | Maintainer |
| oncology-glossary-multilingual-watcher-001 | Automate source-change watcher (hash-diff vs. snapshots) | code | small | low | pr | watcher-000, schema-001 | Maintainer |

**Acceptance criteria — key M2 tasks**

`translate-001` (first translated language)
- Every reviewed source term translated into the target language using `termbase-001`, preserving
  meaning, **negations**, urgency, and preserved terms; UTF-8.
- **Qualified medical-translation reviewer sign-off recorded** per entry; reviewer independent of the
  drafting step (COI declared).
- **Back-translation QA performed for every `safetyLoad != none` term** and compared to the reviewed
  source before sign-off.
- Agent uncertainty flags resolved; translation inherits source attribution + "not medical advice"
  notice; provenance complete; `license-002` green.

`license-002`
- CI validates each deliverable's metadata against its allow-list source and **fails** if attribution,
  provenance, or "not medical advice" is missing or the output license is source-incompatible — the
  **automated** 100% license/provenance hard gate.

**M2 Definition of Done:** ≥ 1 qualified translation reviewer secured; termbase started; reviewed seed
glossary translated into one language with translation review + back-translation QA on all safety-load
terms; license/provenance enforced in CI; automated source-change watcher operating. `verifiedNeed`
still `false` until a partner adopts (M3).

---

## Milestone M3 — First partner delivery (needs partner)

Goal: deliver an adopted glossary set. **All tasks gated on a secured partner** (`verifiedNeed` flips
to `true` only when confirmed). **Steward named** before entering M3.

| ID | Title | Type | Size | Risk | Deliverable | Depends on | Reviewer |
|---|---|---|---|---|---|---|---|
| oncology-glossary-multilingual-partner-001 | Secure first partner; agree language + priority terms | research | medium | low | document | translators-001 | Steward / Maintainer |
| oncology-glossary-multilingual-translate-002 | Translate partner-priority term set into agreed language | translation | large | high | translation | partner-001, translate-001 | Translation reviewer |
| oncology-glossary-multilingual-delivery-001 | Package + deliver glossary set; confirm partner adoption | writing | small | high | document | translate-002, license-002 | Steward |

**Acceptance criteria — key M3 tasks**

`partner-001`
- A named cancer-care/advocacy org confirmed in writing as requestor; agreed target language
  (qualifying per the PLAN target-language rule) and prioritized term list; reviewer coverage for the
  language confirmed. On completion, related tasks update `requestor` and `verifiedNeed: true`.
- **Pause/pivot/mothball decision point** invoked if no partner is secured by ~2027-03-31.

`delivery-001`
- Delivered set includes terms, definitions/translations, provenance, attribution, "not medical
  advice" notice, and all required sign-offs; `license-002` green; **partner confirms adoption for
  distribution in writing** (recorded in the PR/receipt). First true **Definition of Shipped** event.

**M3 Definition of Done:** partner secured (`verifiedNeed=true`); ≥ 1 reviewed, correctly-licensed
translated glossary set **delivered and confirmed adopted** by the partner for distribution.

---

## Milestone M4 — Scale program

Goal: scale languages/terms with sustained quality and tracked outcomes.

| ID | Title | Type | Size | Risk | Deliverable | Depends on | Reviewer |
|---|---|---|---|---|---|---|---|
| oncology-glossary-multilingual-scale-001 | Add ≥1 more language to ≥100 reviewed terms | translation | large | high | translation | delivery-001 | Translation reviewers |
| oncology-glossary-multilingual-rotation-001 | Establish reviewer rotation across all three gates | maintenance | small | low | document | reviewers-002, translators-001 | Maintainer |
| oncology-glossary-multilingual-outcomes-001 | Outcome tracking: post-delivery defect + feedback log | data | small | low | dataset | delivery-001 | Steward |
| oncology-glossary-multilingual-maint-001 | Source re-verification + term-refresh cadence (terminology evolves) | maintenance | small | low | document | sources-001 | Maintainer |

**Acceptance criteria — key M4 tasks**

`outcomes-001`
- A maintained log per delivered language set: adoption status, post-delivery **critical defects
  (target 0)**, readability outcomes, and partner/beneficiary usefulness feedback; feeds PLAN.md
  success metrics.

`maint-001`
- A documented cadence to re-verify allow-list sources (detect upstream/license changes via stored
  snapshots) and refresh entries as oncology/trial terminology evolves; defines the **withdraw →
  correct → re-review → re-publish** procedure for post-delivery inaccuracies and the license-change
  withdrawal procedure.

**M4 Definition of Done:** ≥ 2 languages with ≥ 100 reviewed terms adopted; reviewer rotation across
all three gates operating; outcome tracking live; source/term maintenance cadence in effect; named
sustainability owner.

---

## Backlog / future

Sized but unscheduled:

- **(medium) Audio pronunciation + IPA per term** — preserved-pronunciation aid for spoken use
  (overlaps `open-pronunciation-audio`); needs its own review.
- **(medium) Print/complex-script delivery formats** — partner-driven layout for scripts beyond plain
  Markdown/Unicode.
- **(small) Pivot-language attribution bundle** — bilingual attribution + "not medical advice"
  packaging where partners require target + pivot language.
- **(medium) Cross-link to trial-term projects** — coordinate term IDs with `ewing-info-translations`
  and trial-eligibility projects (no scope merge; shared termbase only).
- **(large, funded — needs escrow) Surge drafting under funded lane** — metered drafting against a
  hard per-task budget for high-demand events; would require `fundedBudgetUsd`; out of scope for v0.1.

---

## Example task JSON

Schema-valid Task JSON for the first M0 task. `verifiedNeed` is **`false`** (no partner/reviewer
secured); `outputLicense` is **MIT** because the allow-list is project metadata, not derived glossary
content. `lane` is `donated`, so `fundedBudgetUsd` is **not** required.

```json
{
  "id": "oncology-glossary-multilingual-sources-001",
  "title": "Build term-source allow-list with per-source license terms and exclusions",
  "project": "oncology-glossary-multilingual",
  "type": "data",
  "lane": "donated",
  "priority": "high",
  "domain": ["oncology", "translation", "patient-education", "licensing"],
  "riskTier": "low",
  "urgent": false,
  "deliverable": "dataset",
  "tokenEstimate": "small",
  "status": "open",
  "context": "oncology-glossary-multilingual builds an open, multilingual, plain-language glossary of oncology and clinical-trial terms for patients and caregivers (education only, not medical advice). Binding cancer guardrails: only open-access/aggregate/de-identified reference sources may be used; controlled-access data (dbGaP, EGA, biobanks) and any identifiable patient data are categorically out of scope; each source's license must be verified before use. Preferred sources are US-Government public-domain references (NCI Dictionary of Cancer Terms, ClinicalTrials.gov, NCI Thesaurus, MeSH); COSMIC/OncoKB (non-commercial), SNOMED CT (licensed), and copyrighted advocacy content are excluded absent permission. Nothing may be defined from a source until it is allow-listed with verified terms.",
  "objective": "Create a structured, per-source allow-list recording each candidate source's license terms, derivative permission, required attribution, restricted/excluded status, and a hashed license snapshot, so the per-deliverable license/provenance gate can be checked consistently and restricted sources are never used.",
  "acceptanceCriteria": [
    "sources/term-source-allow-list.yaml lists >= 3 candidate sources including >= 1 US-Government public-domain source and >= 1 source explicitly excluded with an exclusionReason (e.g., COSMIC/OncoKB non-commercial or SNOMED CT licensed)",
    "Each entry records: name, canonical URL, version/date, retrieval date, license name + URL, snapshot of license text, derivativesAllowed flag, required attribution string, restricted flag with exclusionReason where applicable",
    "Each entry stores snapshotHash (SHA-256 of the captured license/source text) and snapshotArchiveUrl (web-archive copy) where available",
    "No controlled-access or patient-level source appears (dbGaP, EGA, individual-level biobanks are categorically excluded)",
    "Each entry has verifiedBy and verifiedDate; sources with unclear or incompatible terms are marked excluded with a reason",
    "File validates against the project content schema and passes CI structural checks"
  ],
  "resources": [
    "C:/code/hee-lee-oss/planning/projects/oncology-glossary-multilingual/PLAN.md",
    "C:/code/hee-lee-oss/planning/ROADMAP.md",
    "C:/code/hee-lee-oss/docs/good-deed-definition.md",
    "NCI Dictionary of Cancer Terms (verify current license/attribution)",
    "ClinicalTrials.gov Learn About Studies / glossary (US Gov, verify embedded third-party)",
    "MeSH and MedlinePlus content-use notices (verify per item for embedded copyrighted content)"
  ],
  "output": "sources/term-source-allow-list.yaml plus a short README documenting the allow-list schema, the open-access-only guardrail, and the verification process",
  "requestor": "TO BE SECURED",
  "verifiedNeed": false,
  "outputLicense": "MIT"
}
```

---

## Generated task index

Auto-generated by Hee-Lee Oss task-decomposition on 2026-06-29. All 25 tasks validated against the Hee-Lee Oss
task schema. Seed task (`sources-001`) was already present; 24 new task JSON files were created in
`tasks/`. No fan-out was performed (no explicit language names are enumerated in TASKS.md).

All tasks: `lane: donated`, `status: open`, `verifiedNeed: false` (no partner/reviewer secured yet).
Patient-facing content tasks: `riskTier: high`. Pure tooling/process tasks: `riskTier: low`.
Translation tasks: `type: writing`, `deliverable: translation` (schema convention).
Output licenses: MIT for tooling/process; CC BY 4.0 for glossary content/translations.

| Task ID | Milestone | Type | riskTier | Deliverable |
|---|---|---|---|---|
| oncology-glossary-multilingual-sources-001 | M0 | data | low | dataset |
| oncology-glossary-multilingual-license-000 | M0 | research | low | document |
| oncology-glossary-multilingual-schema-001 | M0 | code | low | pr |
| oncology-glossary-multilingual-review-001 | M0 | writing | low | document |
| oncology-glossary-multilingual-review-002 | M0 | writing | low | document |
| oncology-glossary-multilingual-review-003 | M0 | writing | low | document |
| oncology-glossary-multilingual-template-001 | M0 | writing | low | document |
| oncology-glossary-multilingual-draft-001 | M0 | writing | high | document |
| oncology-glossary-multilingual-watcher-000 | M0 | code | low | pr |
| oncology-glossary-multilingual-reviewers-001 | M1 | writing | low | document |
| oncology-glossary-multilingual-reviewers-002 | M1 | research | low | document |
| oncology-glossary-multilingual-glossary-source-001 | M1 | writing | high | document |
| oncology-glossary-multilingual-license-001 | M1 | research | low | document |
| oncology-glossary-multilingual-translators-001 | M2 | research | low | document |
| oncology-glossary-multilingual-termbase-001 | M2 | data | high | dataset |
| oncology-glossary-multilingual-translate-001 | M2 | writing | high | translation |
| oncology-glossary-multilingual-license-002 | M2 | code | low | pr |
| oncology-glossary-multilingual-watcher-001 | M2 | code | low | pr |
| oncology-glossary-multilingual-partner-001 | M3 | research | low | document |
| oncology-glossary-multilingual-translate-002 | M3 | writing | high | translation |
| oncology-glossary-multilingual-delivery-001 | M3 | writing | high | document |
| oncology-glossary-multilingual-scale-001 | M4 | writing | high | translation |
| oncology-glossary-multilingual-rotation-001 | M4 | maintenance | low | document |
| oncology-glossary-multilingual-outcomes-001 | M4 | data | low | dataset |
| oncology-glossary-multilingual-maint-001 | M4 | maintenance | low | document |
