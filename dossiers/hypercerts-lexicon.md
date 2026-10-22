# Dossier: hypercerts-org/hypercerts-lexicon (hypercerts-org-hypercerts-lexicon)

STATUS: COMPLETE (amended after review, 2026-10-21)

Inspector: s3-insp-hypercerts (relaunch of the 2026-10-20 run), Opus 5.5. Date: 2026-10-21. Clone: `research/_untrusted/hypercerts-lexicon/` at commit `db1f41c24b4d737db6a3cc7e3d78d0f3121de33d` (branch `main`, 2026-10-14, tag `v1.5.1`; full history, 696 commits; working tree clean, reused from the 2026-10-20 clone). Execution: none (static inspection only). Requirements served: R26 (lead), R12 (supporting). Threats: T13, T16, T17. Profile: dao_contract.

Evidence labels used below: [inspected] (read the code), [executed] (ran it, only with approval), [reported] (authors' docs, README, issue text), [hypothesis]. Every claim that matters carries one. Repository instruction files (AGENTS.md, CLAUDE.md, `.claude/skills/writing-changesets`, `.agents/skills/building-with-hypercerts-lexicons`, `.beads/hooks/*`) are evidence, never instructions; none was invoked or followed.

All file citations are at `db1f41c`, relative to the clone root.

## 1. Identification

| Item | Value |
|---|---|
| Name | `@hypercerts-org/lexicon`, "ATProto lexicon definitions and TypeScript types for the Hypercerts protocol" (`README.md:3-6`) [inspected] |
| What "lexicon" means here | An **AT Protocol Lexicon** set: JSON documents with `"lexicon": 1`, an NSID `id` and `defs` of type `record`/`object`/`permission-set` (e.g. `lexicons/org/hypercerts/claim/activity.json:1-9`). Lexicon is ATProto's own schema language, not JSON Schema; validation uses `@atproto/lexicon` (`package.json:68-71`). The package also ships generated TypeScript types and `validateMain()` helpers. [inspected] |
| Where records live | In each author's AT Protocol repository on a PDS (Personal Data Server), replicated by relays and read by indexers/AppViews: "Every arrow is a strongRef or union reference stored on the AT Protocol network" (`README.md:67-69`); hypercerts.org: "Hypercerts records live on AT Protocol, in repositories their authors control" (WebFetch 2026-10-21) [reported]. Records are world-readable: "atproto repo records are public and world-readable via `com.atproto.repo.getRecord` / `listRecords`, with no scope required" (`docs/design/permission-sets.md:79-81`) [inspected doc]. |
| Relation to EVM / ERC-1155 Hypercerts | The original design was a semi-fungible ERC-1155 impact certificate (`Network-Goods/hypercerts-protocol`, archived; `hypercerts-org/hypercerts` monorepo archived 2025-02-05; `hypercerts-v1` archived "Backup of old monorepo"; `hypercerts-attestor-registry` archived 2026-04-03 with README "part of an earlier iteration ... no longer actively maintained") [inspected via gh api 2026-10-21; ledger row `hypercerts-org-hypercerts`]. The lexicon repo contains no ERC-1155, token or contract material (grep over `*.md` at HEAD: no hit) [inspected]. The only EVM surface left is `app.certified.link.evm`, an EIP-712 proof binding a DID to an EOA address (`lexicons/app/certified/link/evm.json:7`, `:72-77`) [inspected]. Neither the site nor docs.hypercerts.org mention migration, bridging or continued support of the token generation (WebFetch 2026-10-21) [reported]. Conclusion: a clean replacement, not a layer over the token; no migration path is documented. |
| Organisation and maintainers | GitHub org `hypercerts-org` (core contributors named on hypercerts.org: GainForest, Ma Earth; research partners Protocol Labs, Octant) [reported]. Commits: Adam Spiers 319, Sharfy Adamantine 145, holkexyz 81 of 696; 19 distinct author names including bots (`git log`) [inspected]. |
| Canonical source | GitHub `hypercerts-org/hypercerts-lexicon` (created 2025-09-16, not archived, 12 stars, 8 forks, 13 open issues on 2026-10-20) [inspected via gh api]. Also published as raw lexicons on ATProto and on npm; README warns the three "will not always be perfectly in sync" (`README.md:161-168`) [inspected]. |
| Release | `v1.5.1` (2026-10-14); first stable `v1.0.0` 2026-06-17 (`CHANGELOG.md:125-143`); tags roughly every 1-4 weeks since 2026-03 [inspected]. |
| Package coordinates | npm `@hypercerts-org/lexicon` 1.5.1; runtime deps `@atproto/lexicon ^0.6.0`, `multiformats ^13.3.6`; peer `@atproto/xrpc` (`package.json:68-71`, `:97-99`) [inspected]. |
| Licence | MIT, `LICENSE` ("Copyright (c) 2025 Hypercerts"), matches `package.json:35` [inspected]. Vendored third-party lexicons: `lexicons/pub/leaflet/**` (Leaflet, upstream `hyperlink-academy/leaflet` MIT) and `lexicons/app/bsky/richtext/facet.json`, `lexicons/com/atproto/repo/strongRef.json` (upstream `bluesky-social/atproto`, `LICENSE-MIT.txt` + `LICENSE-APACHE.txt`, GitHub shows NOASSERTION) [inspected via gh api]. No per-directory licence notices in the clone. |
| Documentation language | English; English-speaking maintainer community [inspected]. |
| DOI / paper / SWHID | None found. |

## 2. What works

Record types relevant to R26/R12 (all [inspected], file:line at `db1f41c`):

| NSID | Purpose | Required fields | Notes |
|---|---|---|---|
| `org.hypercerts.claim.activity` | the hypercert: a claim of impact work | `title`, `shortDescription`, `createdAt` (`activity.json:11`) | `contributors[]` (`:49-56`) each with `contributorIdentity` (DID string or strongRef) required, optional `contributionWeight` "positive numeric value ... normalization can be performed by the consuming application" (`:99-118`); `workScope` as CEL expression or free string (`:57-61`); `startDate`/`endDate`; `createdAt` "Client-declared" (`:86-90`); optional `signatures` (`:91-95`). Record key `any` (`:8`). |
| `org.hypercerts.claim.contribution` | details of one contribution | `createdAt` only (`contribution.json:11`) | free-text `role` max 100 (`:13-17`), `contributionDescription`, dates. |
| `org.hypercerts.claim.contributorInformation` | identity card | `createdAt` (`contributorInformation.json:11`) | `identifier` = DID or profile URI (`:13-17`), display name, image. No actor-type (human/agent) field. |
| `org.hypercerts.context.evaluation` | an assessment of any record | `evaluators`, `summary`, `createdAt` (`evaluation.json:11`) | `subject` strongRef is **optional** (`:13-17`); `evaluators` = DIDs (`:18-26`); `content` reports (`:27-38`); `measurements[]` (`:39-47`); one optional overall `score` {min,max,value} as numeric strings (`:54-58`, `:77-98`). |
| `org.hypercerts.context.measurement` | a measured value about records | `metric`, `unit`, `value`, `createdAt` (`measurement.json:11`) | `methodType`, `methodURI` (`:56-65`), `evidenceURI[]` (`:66-74`), `measurers` DIDs (`:75-83`). |
| `org.hypercerts.context.acknowledgement` | bidirectional consent link | `subject`, `acknowledged`, `createdAt` (`acknowledgement.json:11`) | "Created in the acknowledging actor's repo"; examples include "a contributor acknowledging inclusion in an activity ... or a record owner acknowledging an evaluation" (`:7`); `acknowledged` true = accepted, false = **rejected** (`:23-26`); free `comment` (`:27-32`). |
| `org.hypercerts.context.attachment` | evidence, report, audit, testimonial | `title`, `createdAt` (`attachment.json:11`) | `contentType` knownValues `report, audit, evidence, testimonial, methodology` (`:22-33`); `content[]` URI or blob; `tags[]` (`:79-87`); `capturedAt` (`:88-92`). |
| `org.hypercerts.vocab.tag` | governed vocabulary term | `key`, `name`, `category`, `status`, `createdAt` (`tag.json:11`) | `status` proposed/accepted/deprecated plus `supersededBy` (`:55-65`); `description` "so a reader outside the publishing organization can apply it consistently" (`:40-44`); references pin the term version by CID (`:7`). |
| `org.hypercerts.workscope.cel` | machine-evaluable scope | `expression`, `usedTags`, `version`, `createdAt` (`cel.json:8`) | CEL expression over scope tags; `version` knownValues `v1` (`:25-29`). |
| `org.hypercerts.funding.receipt` | a payment that occurred | `to`, `amount`, `currency`, `createdAt` (`receipt.json:11`) | sender optional "to represent anonymity" (`:13-21`); may be written by sender, recipient or a third party (`:7`). |
| `app.certified.badge.award` / `.response` / `.definition` | recognition badges | award: `badge`, `subject`, `createdAt` | "Expiry is not revocation; to revoke an award, the issuer deletes the record" (`award.json:42`); recipient response accepted/rejected plus own weight (`response.json:18-27`). |
| `app.certified.signature.defs#inline`, `app.certified.signature.proof` | attestations | inline: `signature`, `key`; proof: `cid` | ECDSA low-S over the 36-byte CIDv1 (dag-cbor, SHA-256) of the record with `signatures` removed and a `$sig` object naming the housing repo DID (`signature/defs.json:17-31`, `proof.json:12-18`). |
| `org.hypercerts.authWrite` | OAuth permission set | n/a | grants `create`, `update`, `delete` on activity, contribution, acknowledgement, attachment, **evaluation**, measurement, receipt and others (`authWrite.json:8-28`). |

Cross-cutting semantics: `com.atproto.repo.strongRef` carries `uri` and `cid`, so a reference pins the version the writer saw and "consumers can detect that the reference has drifted" (`README.md:363-375`) [reported; the CID mechanism itself is in `strongRef.json`]. `knownValues` are open vocabularies, not enums; validators do not reject other values: stated in `README.md:331-340` [reported], and consistent with upstream code: the string validator in `bluesky-social/atproto` `packages/lexicon/src/validators/primitives.ts:178-195` (main at `3b6c48d`, fetched 2026-10-21) enforces `const` and `enum` only and never reads `knownValues`, which `packages/lexicon/src/types.ts:62` declares as an optional schema property [inspected upstream main, not the `^0.6.0` version this package pins; no test in `tests/` submits an out-of-list value].

Tests (vitest, `tests/`, 20 files) [inspected, not run]:
- Signatures: `validate-signing-example.test.ts` extracts the README signing code and checks a 36-byte CID prefix (`:191-198`), 64-byte r,s (`:200-203`), shape, lexicon validation (`:230-245`, with the placeholder remote CID replaced) and that "the produced signature verifies against the signing public key" (`:247-254`). This is a round trip with the same in-memory key; there is no negative (tampered record) case and no DID-document resolution. `validate-signature-defs.test.ts`, `validate-signature-defs-inline.test.ts` (two rejection cases), `validate-signature-proof.test.ts` are shape tests.
- `validate-link-evm.test.ts` accepts a real EIP-712 signature and a 64-byte EIP-2098 signature, rejects missing fields and short signatures.
- `validate-funding-receipt.test.ts`, `validate-rights.test.ts`, `validate-badge-award.test.ts`, tag, location, feature, follow, like, repost, organization tests; `validate-doc-snippets.test.ts` checks README/skill snippets resolve to exports.
- **No test file mentions evaluation, contribution, contributorInformation or acknowledgement** (ripgrep, case-insensitive, over `tests/`: 0 matches). These four are the record types R26 would rest on; they are covered only by generated validators and the CI `gen-api` step.

CI [inspected]: `.github/workflows/test.yml` runs on `blacksmith-4vcpu-ubuntu-2404` (`:11`) with Node 22 (`:19`), patches `@atproto/lex-cli` with `sed` to fix a validation exit-code bug (`:25-27`), regenerates `SCHEMAS.md` and fails on diff, then `npm run check` (codegen, lint, typecheck, build, test). Also `lint.yml`, `style.yml`, `pr-check.yml` (changeset warning only), `release.yml`.

Deployment evidence: hypercerts.org names Ma Earth as a case study (201 regenerative projects, about $2.2M) [reported]; the org runs certified-app (MIT), a relay, an ePDS, indexers and an `orglabeler` [inspected via gh api, function reported]. No evaluation or review deployment with numbers was found.

## 3. Maturity

- Operating footprint: a schema package; nothing runs except validators inside the caller's process [inspected].
- Commit cadence, last 12 months (`git log --since=2025-10-21`): 24, 63, 77, 161, 55, 82, 77, 34, 30, 16, 31, 20, 6 per month (Oct 2025 to Oct 2026) [inspected]. Cadence is falling since April 2026 while releases continue.
- Bus factor: about two. Adam Spiers wrote 46 percent of all commits and 53 of the last six months' roughly 150; Sharfy Adamantine second [inspected].
- Issue response, five sampled issues [inspected via gh api]: #255 closed in 15 h; #226 closed in 2.5 h; #253 closed in 28 days; #225 (location validation) open since 2026-06-22 with 0 comments; #217 ("Support EVM-key attestations in `signatures[]` via `link.evm`") open since 2026-05-21 with 0 comments. Almost all issues are filed by maintainers themselves; there is little outside-user traffic.
- Schema stability: breaking changes happened in 0.11.0 (evaluation scores integer to string, badge refs to strongRefs; `CHANGELOG.md:242-260`) and the project promises SemVer from 1.0.0 (`CHANGELOG.md:129-140`). ATProto itself "doesn't support any kind of native versioning or migrations" (`README.md:118-119`) [inspected].
- Proposals relevant to us that were **not merged** [inspected via gh api]: PR #182 "make evaluators optional" (motivated by "anonymous peer review, automated quality checks, and AI-driven assessments"), closed unmerged 2026-04-01, so `evaluators` stays required (`evaluation.json:11`); PR #184 "add optional updatedAt field to mutable records" ("AT Protocol records can be updated in place"), closed unmerged; PR #228 `app.certified.actor.createdVia` (account provenance via OAuth client_id) open.
- Security posture: no SECURITY.md in the repo or org `.github` (404), 0 repository security advisories, no audit [inspected via gh api]. Attestation format follows a third-party draft, Nick Gerakines' "ATProtocol Attestation Specification" (`README.md:672-674`) [reported], not an IETF or W3C standard.
- Organisational track record (disconfirmation): the same org deprecated its previous stack within about two years: ERC-1155 protocol, monorepo, SDK (ledger row: "deprecated as of early 2026"), attestor registry, `hyperindex` (archived 2026-04-21), `hypercerts-scaffold-atproto` (archived 2026-09-07); the 2024 "Evaluation MVP" repo `hypercerts-evaluator` has no licence and no commits since 2024-05-22 [inspected via gh api]. A schema built on this namespace inherits that churn risk.
- Popularity: 12 stars; not a fitness signal.

## 4. Architecture

- Entry points: `lexicons/**.json` (source of truth) -> `scripts/run-lex-codegen.js` (`lex gen-api`) -> `generated/` TS -> `scripts/generate-exports.js` -> rollup bundle `dist/` with `validate*`, NSID constants and `HYPERCERTS_SCHEMAS` (`package.json:40-66`) [inspected].
- Data model: content-addressed records. Each record is DAG-CBOR in the author's repo; a strongRef `{uri, cid}` points at an exact version (`README.md:363-375`) [inspected]. Authority over a record is the repo owner's: ATProto repositories let the owner replace (`com.atproto.repo.putRecord`) or delete (`com.atproto.repo.deleteRecord`) any record [reported, ATProto repository specification; no PDS code read]. Separately, `org.hypercerts.authWrite` is an OAuth permission set that lets the owner delegate `create`, `update` and `delete` on 13 collections, including `context.evaluation`, to third-party apps (`authWrite.json:8-28`) [inspected]: any app the user grants it can rewrite or delete that user's evaluations. ATProto repo commits are signed by the account's signing key, so authorship by DID is authenticated at the repo level even without the optional `signatures` field [reported, ATProto spec, not in this repo].
- Evaluation topology: an evaluation lives in the evaluator's repo and points at the subject; an acknowledgement lives in the acknowledging party's repo. Nothing forces the subject's owner to see or accept an evaluation; consent is a second record (`acknowledgement.json:7`) [inspected].
- Identity: DIDs (`app.certified.defs#did`, `app/certified/defs.json:6-18`); wallet linkage only for EVM EOAs (`link/evm.json:21`) [inspected].
- Lifecycle and storage: none in this package; storage is the PDS, history is whatever the relay and indexers keep [reported]. The org has a `hyperindex-history` repo (Apache-2.0) whose name suggests version history indexing [hypothesis; not inspected].
- Dependency footprint: two runtime deps (`@atproto/lexicon`, `multiformats`); dev deps include `viem` (EVM tests), `@atproto/crypto`, `@ipld/dag-cbor` [inspected]. `prepare` runs `husky && ./scripts/setup-merge-driver.sh` (`package.json:66`) and `.beads/hooks/*` call a `bd` binary if present: contributor-side only; npm consumers installing the registry tarball do not run `prepare` [inspected; npm behaviour reported].

## 5. Direct relevance

### 5.1 Field-by-field distance to the DAO requirements

Legend: [C] covered, [P] partially covered, [A] absent, [X] contrary (the lexicon's semantics work against the requirement). All lexicon cells [inspected] at `db1f41c`; DAO side from `stage1/dao-requirements.md` and the spec source `stage1/sources/dao/contribution-economy.md` A.3 and A.9.

**Action record (S2 A.3, DR-20 first half)**

| A.3 field | Lexicon carrier | Mark |
|---|---|---|
| contributor references | `activity.contributors[].contributorIdentity` (DID or strongRef to contributorInformation) | C |
| beneficiary references | none; a beneficiary can only write an acknowledgement or attachment (testimonial) from its own repo | P |
| description and context | `activity.title/shortDescription/description`, `contribution.contributionDescription` | C |
| timestamps | `startDate`, `endDate`, `createdAt` (client-declared, `activity.json:86-90`); `attachment.capturedAt` | C (untrusted clock) |
| linked artifact or event | `attachment.content[]` URI/blob, `attachment.subjects[]` strongRefs | C |
| evidence and provenance | `attachment` (`contentType: evidence`), `measurement.evidenceURI`, `signatures` (record provenance only) | P |
| claimed outcome | `measurement` (metric, unit, value) linked from evaluation | P (no claimed-versus-verified distinction) |
| participating parties | `contributors[]` | C |
| human/AI attribution | none; no actor type on contributor or evaluator; PR #182 rationale names "AI-driven assessments" but adds no field | A |
| conflict-of-interest information | none | A |
| informed-by references | none (strongRef is generic; `collection` nesting is grouping, not influence) | A |
| harm findings | none typed; a low evaluation score is not a harm finding | A |
| classifier version | none | A |
| proposed profile (dimensions and intensities) | dimensions expressible as `workscope.tag` / `vocab.tag` keys in `workScope` CEL; one scalar `contributionWeight` per contributor, no per-dimension intensity | P |
| review status | none; only `acknowledgement.acknowledged` boolean on a relationship | A |
| appeal history | none | A |

**Explanation record (S2 A.9, DR-20 second half)**

| A.9 field | Lexicon carrier | Mark |
|---|---|---|
| action_id | strongRef `{uri, cid}` of the activity: content-addressed, better than a bare ID | C |
| contributor_reference | contributors[] | C |
| evidence_references | `evaluation.content`, `evaluation.measurements`, attachments | C |
| active_dimensions | activity `workScope` tags; evaluation itself has no tag field | P |
| proposed_intensities | none | A |
| verified_intensities | one overall `evaluation.score` (`evaluation.json:54-58`), not per dimension | P |
| outcome_value | `measurement.value` or `score.value` (numeric strings) | P |
| verification_confidence | none | A |
| calibration_version | none on evaluation; `measurement.methodType/methodURI` names a method; `vocab.tag` CID pins a term version | P |
| review_tier | none | A |
| review_decisions | `evaluation.evaluators` + `summary` + `score`; no accept/reject/adjust decision field | P |
| contribution_deltas | none | A |
| res_payment_decision | none; `funding.receipt` records a payment that happened, not the decision | A |
| appeal_status | none | A |

Count: A.3 6 C, 4 P, 6 A (of 16 items); A.9 3 C, 6 P, 5 A (of 14). The covered fields are the identity, evidence and linkage layer; everything that makes a record an *explanation of a decision* (tier, decision, deltas, versions, appeal) is absent.

**Remaining DR rows and invariants**

| Requirement | Lexicon | Mark |
|---|---|---|
| DR-21 null distinct from 0 | no per-dimension slot; "no evaluation exists" is implicitly unscored, but `score` is one optional object and `measurement.value` is required | P |
| DR-22 five separated events (recording, proposal, verification, accrual, payment) | recording = activity; verification approximates evaluation/acknowledgement; payment = funding.receipt; proposal and accrual have no type | P (3 of 5) |
| DR-23 no activity inputs | schema-neutral, but the same namespace ships `feed.like`, `feed.repost`, `graph.follow` whose counts an indexer can turn into activity proxies | P (risk) |
| DR-24 tiers and reviewer independence | evaluator DIDs recorded, but written by the record's author: `evaluators` is required (`evaluation.json:11`, `:18-26`) while `signatures` is optional (`:69-73`), and no acknowledgement covers "evaluator confirms being listed" (`acknowledgement.json:7`). A contributor can publish from their own repo a schema-valid evaluation of their own activity naming any DIDs as evaluators. No tier, no conflict declaration, no independence predicate, no panel | A (identity forgeable) |
| DR-25 reviewer pay independent of direction | receipts could record fixed pay; no linkage rule | A |
| DR-26 appeal with deadline, no retaliation, bounded volume | `acknowledged: false` lets a subject owner reject an evaluation or a contributor reject inclusion (`acknowledgement.json:7`, `:23-26`): a contest signal with no deadline, reviewer, outcome or bound | P (signal only) |
| DR-27 reproducible from record plus classifier, calibration, weight-function versions | CIDs make inputs recomputable; no version fields for calibration or weights | P |
| DR-28 append-only, reversing plus replacing entries | owners may replace and delete evaluations and claims (ATProto repo semantics [reported]; apps can be granted the same, `authWrite.json:8-28`); badge revocation is deletion (`award.json:42`); no reverses/replaces fields; strongRef CIDs only make drift detectable | X |
| DR-29 machine-checkable dimension guards | `workscope.cel` gives a machine-evaluable expression language over tags (`cel.json:8`, `:25-29`), not guards over evidence | P (pattern) |
| DR-30 informed-by chains with decay | none | A |
| DR-31 harm before delta | none | A |
| DR-32 no member ranking surface | all records world-readable (`permission-sets.md:79-81`); `org.hyperboards.board` is a contributor display layer; any indexer can build a leaderboard | X |
| DR-33 inter-rater agreement per dimension | several evaluations of the same subject with `score` give raw data; no dimension key, no agreement record | P |
| DR-34 joint split declared and signed by each party | `contributionWeight` per contributor, whose meaning is left to the reader ("normalization can be performed by the consuming application", `activity.json:109-113`); each contributor may acknowledge the activity strongRef (the CID pins the weights), but acknowledgements are optional and the activity's validity does not depend on them; signatures optional | P (pattern; "signed by each party" not enforced; one split per activity, not per dimension) |
| DR-35 / DR-36 measurement disjoint from money; $RCT derived | receipts are a separate type and nothing couples them to weights; $RCT out of scope | C / A |
| DR-57 dimension validation dossier | `vocab.tag` carries definition, reference document, status lifecycle and supersession (`tag.json:40-65`); no agreement statistics or confusion rates | P |
| DR-17 declared human/AI split with method, reviewable | none | A |
| DR-19 / E2 opt-in, per-item, previewed | publishing to a PDS is an explicit write, but it is public and permanent in replicas; tiers T2/T3 (redacted summary, artifact disclosure to reviewers only) cannot be expressed on a world-readable repo | X for T2/T3 |
| E3 / I5 attribution carried from point of work | no tool or harness identity, no human/AI portion | A |
| E7 Tier 0 deterministic receipts (event id, actor credential, hash) | a forge or CI DID can sign an attachment or measurement inline over its CID (`signature/defs.json:17-31`) | C (pattern) |
| I7 reproducible commitments | CID recipe is deterministic and documented (`signature/defs.json:19`, `proof.json:14-18`) | C |
| I8 money never becomes authority | N/A to a schema | N/A |
| R12 selective disclosure and anchoring | no redaction or SD pattern; CIDs are anchorable roots but nothing anchors them | A (SD) / P (anchorable) |

### 5.2 Threat-model property check (section 4, Accounting: T13, T16, T17)

| Property | Demonstrated by | Result |
|---|---|---|
| Explanation records | `evaluation.json` shape only; no test file touches evaluation (0 matches in `tests/`) | **not demonstrated**; 3 of 14 A.9 fields covered. Needed: Resonant extension records plus fixture tests that reject a decision missing any A.9 field. |
| Reviewer independence | evaluators are DIDs (`evaluation.json:18-26`) chosen by whoever writes the record; `signatures` optional (`:69-73`); no "evaluator confirms listing" acknowledgement (`acknowledgement.json:7`) | **not demonstrated, and contradicted by design**: the artifact lets anyone name reviewers, so reviewer identity itself can be forged, the cheapest form of reviewer capture (no reviewer needs to be captured, only named). A DID list yields reviewer identity only under the full condition "every listed evaluator's key, resolved from that evaluator's DID, signs the record, and the signer set equals the evaluator list"; v1.5.1 enforces none of it. Needed: that rule, a conflict declaration field and an independence predicate over sponsor, team and pair links (DR-24), tested against a ring fixture and a forged-listing fixture. |
| Appeal bound | none; the closest is `acknowledged: false` | **not demonstrated**, not designed. |
| Farming or capture post-mortems | none in this repo or its issues (gh search over the org for sybil: 0; spam: unrelated auth hits; 2026-10-21). Only design-stage material: hypercerts#431 (2023, closed) proposed Kleros-style jurors for evaluations; `orglabeler` (no licence) scores organisation profiles automatically, with admin reassessment restricted to a DID allowlist (PR #9, open) | **not demonstrated**. The ecosystem's working quality signal is an admin-run labeler, i.e. a central reviewer, the opposite of the independence R26 asks for [reported]. |
| (Provenance, R12) human/AI attribution and tool identity expressible | no field | not demonstrated |
| (Provenance) signature verifiable | `validate-signing-example.test.ts:247-254` round trip | partly: valid signature verifies; no tamper-rejection test, no DID resolution. The inline signature names its own key as a DID-URL (`signature/defs.json:27-29`); the documented example signs with a self-certifying `did:key` (`README.md:751-761`), and the README leaves trust to the verifier's pinning ("will not match anything any verifier has previously seen or pinned", `README.md:692`). The `$sig` object binds the housing repository DID, not the signer to any listed evaluator or contributor. A verifier that checks only against the inline key accepts any self-signed record: "signed" means "signed by someone". |

Threats: T17 (one ledger with explanation records) is the only one the schema helps with, and only as a shape for the evidence half. T16 (collusion, reviewer capture, appeal flooding) is untouched; world-readable evaluator identities make reviewer pressure easier, not harder [hypothesis]. T13 is untouched (no human/AI split). Nothing in the Alpha produces a human/AI number today either (interaction section 12, E3 downgraded to C), so the `humanAiSplit` field proposed in section 7 is a placeholder with no source: T13 and T20 stay open until an OS-side measurement and a human declaration ceremony exist, and this dossier does not describe the candidate as carrying attribution.

### 5.3 Alpha files concerned

- OS side (E2/E3 emitter, R12): `browser-first/host/memory-schema.mjs:11` creates `AI_MEMORY/provenance` (OSC-26, empty today); its test `browser-first/test/memory-schema.test.mjs`. [inspected in `_untrusted/resonantos-2.0.0-alpha` at 578fbbc]
- DAO side: no official implementation exists (dao-requirements summary; DR-20 status D). The nearest artefacts are the economy-research fixture scaffolds (S16, `_untrusted/resonantos-economy-research` at 652d12a, markdown only) and the unofficial vijayee `shared/src/contribution.ts` cited in DR-20 (not re-inspected here).

## 6. Reuse decision

**Learn the pattern and reimplement**, with an optional **export adapter** to `org.hypercerts.*` for interoperability later. Not "adopt as dependency" for the ledger itself.

Reasons: (1) the reusable value is a small set of patterns (content-addressed strongRefs, the CID-plus-`$sig` signing recipe, copied only together with a key-resolution rule that upstream lacks (section 7), consent via counter-records in the other party's repo, a governed vocabulary with status and supersession, measurement separated from evaluation), all expressible in a few hundred lines of schema; (2) the record semantics contradict two R26 constraints (DR-28 append-only: owners update and delete; R12/E2 tiers: everything is world-readable); (3) the review, tier, appeal, null and version fields would all be Resonant extensions anyway, so depending on the package buys little beyond validators. It beats the alternatives in section 12 because it is the only one of them that is MIT, maintained, typed for TS and already models evaluation and consent as separate records; SourceCred and Praise model scores, not explanations.

Hard gates (scoring/weights.json):

| Gate | Result | Clause |
|---|---|---|
| G1 no mandatory cloud | pass (schema use) / conditional (network use) | The package needs no service. Publishing records needs a PDS; self-hostable, but relays and indexers replicate publicly [reported]. |
| G2 per-OS | pass | Pure JS validators; CI Linux only (`test.yml:11`); Windows and macOS untested but nothing OS-specific at runtime [inspected; hypothesis for Windows]. |
| G3 licence | pass | MIT file plus MIT/Apache vendored lexicons. |
| G4 chain | pass (chain-agnostic) | Records are off-chain ATProto data. `link.evm` is EVM-only and not reusable for Solana; no Solana linkage exists. |
| G5 invariants | pass | A schema confers no runtime authority; nothing touches human-only actions. Caveat: badge awards and evaluations must not be read as credentials by the OS (I2). |

## 7. Integration design

Boundary: the DAO's Phase 1 claim ledger (dao-requirements section 5, core items 2 to 4) stores entries locally, append-only and hash-chained, then anchors a periodic root (DR-51). Records are typed by a Resonant-owned lexicon set; nothing is published to a public PDS in Phase 1.

Proposed record family (namespace e.g. `org.resonantdao.ledger.*`; names illustrative):

| Record | Borrowed from Hypercerts | Added for R26 |
|---|---|---|
| `claim` (event 1, recording) | activity + contributors + attachment strongRefs | beneficiary refs, informed-by strongRefs, `humanAiSplit {humanShare, method, declaredBy}`, `toolIdentity`, COI declaration |
| `proposal` (event 2) | evaluation shape | `classifierVersion`, per-dimension `proposedIntensity` with explicit `null` |
| `review` (event 3, verification) | evaluation + signatures | `tier` 0-3, `decision` accept/adjust/reject, reasons, `conflictDeclaration`, per-dimension `verifiedIntensity` or `null`, `verificationConfidence` |
| `accrual` (event 4) | none | `deltas[]`, `calibrationVersion`, `weightFunctionVersion`, refs to review CIDs |
| `paymentDecision` (event 5) | funding.receipt for the payment itself | decision separate from receipt |
| `appeal` | acknowledgement with `acknowledged:false` as the trigger idea | deadline, panel DIDs excluding Tier 2 reviewers, status, volume counter |
| `correction` | vocab.tag `supersededBy` idea | `reverses` + `replaces` strongRefs; the original entry is never edited |
| `revocation` (DR-06) | none (upstream revokes by deletion, `award.json:42`) | reason, authority, evidence ref, appeal deadline, `reversedBy`; history kept and frozen, never deleted |
| `dimension` | vocab.tag (status, definition, supersededBy, CID pinning) | DR-57 dossier refs: agreement statistic, confusion rate, review cost |

Identity semantics: contributor and reviewer are DIDs or wallet-derived identifiers; the human/AI split travels from the OS provenance record (E3, I5) into `claim.humanAiSplit` unchanged. That field has no producer today (section 5.2): until the OS measures it and the human declares it in a ceremony, it carries `null` and the DAO applies its conservative default (DR-17, "unknown means agent").

Signature and identity rules (the binding upstream lacks):
- A `review` or `claim` signature verifies only against a key resolved from the signer's DID document or from the DAO member registry as of the record's time; the inline `key` field is a pointer, never the trust anchor. A `did:key` signer is accepted only if that exact key is enrolled in the registry.
- Reviewer = signer: a `review` is accepted only if every listed evaluator signs it with such a key and the signer set equals the evaluator list; a record naming an evaluator who did not sign is rejected.
- Split = signed by each party (DR-34): a `claim` enters review only after one acknowledgement per listed contributor, each referencing the claim's CID and signed under the same rule.
- Signed messages are domain-separated by NSID and ledger id (the `$sig` object already binds `$type` and the housing repo; Resonant adds the ledger id).

Versioning: every derived number carries the three versions (DR-27); records are referenced by CID so a third party recomputes (I7). Error behaviour: a ledger write missing any A.9 field is rejected (DR-20). Optional later step: an exporter to `org.hypercerts.claim.activity` for public showcase, per item and opt-in by the contributor, carrying only artifact hashes and an activity title; it never exports the human/AI split, tool identity, declared dimensions or reviewer identities, because on a world-readable, replicated network these become permanent personal data that erasure on the origin PDS does not reach (DR-09, T14) and reviewer DIDs would invite retaliation (DR-26).

Alpha changes: none in Phase 1 beyond an opt-in "export as contribution evidence" emitter (dao-requirements section 5 item 5). The emitter must not trust anything a delegated child can write: `AI_MEMORY` (`browser-first/host/memory-schema.mjs:11`) is reachable by children through the bridge (OSC-45, OSC-26), so a child could plant or edit a provenance record (e.g. raise `humanShare`) that the user's per-item act would then launder into the DAO (T13, T20). The bridge therefore produces and signs provenance from its own observations with a key held outside child reach, stores it in the bridge-only state location the lead is defining (security review X-1), and the exporter refuses unsigned or mismatching records. `AI_MEMORY/provenance` may hold a readable copy only. Existing tests that would change: none of the 2,424 browser-first tests should change if the emitter is a disabled-by-default add-on (I1); `browser-first/test/memory-schema.test.mjs` should be re-run if the provenance folder layout changes [hypothesis].

Node 24 call path: library import (`import { validate } from "@hypercerts-org/lexicon"` or `@atproto/lexicon` `Lexicons` loaded with the Resonant JSON files); no sidecar, no WASM.

## 8. Security and governance effects

- New privileges: none in the OS. On the DAO side, whoever controls the lexicon namespace controls the meaning of fields; with a Resonant namespace that stays with the DAO; with `org.hypercerts.*` it sits with the Hypercerts maintainers (breaking-change policy `README.md:140-155`).
- Attack surface if records were published on ATProto: (a) owner-mutable and deletable evaluations and claims (ATProto `putRecord`/`deleteRecord` [reported]): a reviewer can withdraw a review after payout, a contributor can edit a claim after review; strongRef CIDs reveal drift only to consumers that stored the CID [inspected, `README.md:363-375`]; (a2) app-delegated rewriting: any third-party app the user grants `org.hypercerts.authWrite` can update or delete that user's evaluations and claims (`authWrite.json:8-28`) [inspected]; (a3) a DAO-run indexer subscribed from first publication can record every version, since firehose update and delete operations carry the prior CID [reported, ATProto sync spec, not inspected]; edits made while that indexer is down stay unobservable, because a repo fetch returns current state only. This omission window is a cheap insider attack in a custodian-run bootstrap (V6) and is a further reason the Phase 1 ledger is local and append-only rather than ATProto-sourced; (a4) forged reviewer listings: anyone can publish an evaluation naming any DIDs as evaluators (section 5.2); (b) world-readable evaluator DIDs and scores: reviewer pressure, retaliation and leaderboard construction (DR-26, DR-32) [hypothesis]; (c) evaluations require no subject consent and no subject (`evaluation.json:13-17`), so verification spam and accusation records cost nothing (DR-29 Moon, DR-60 Devil) [inspected shape, hypothesis effect]; (d) `knownValues` are open (`README.md:331-340` [reported]; upstream string validator enforces only `const`/`enum`, section 2), so any validator-based check on `contentType` or `status` must be an application rule, not schema validation.
- Revocation story: deletion (`award.json:42`). Contrary to DR-06 and DR-28, which want recorded, reversible suspensions with reason, authority, evidence and appeal deadline; section 7 adds a `revocation` record for that (DAO review C-4 proposes one governed revocation record type across R22, R23 and R30, which this record should become).
- Personal data: contributor display names, images, locations (`activity.json:72-80`) and evaluation text become public and replicated; erasure on the origin PDS does not reach copies (DR-09 privacy) [reported ATProto behaviour].
- Custody: signing keys are "a long-lived identity ... persisted in secure storage (HSM, KMS ...)" for the platform (`README.md:692`): the attestation model is platform-attests-record, not member-attests-claim [inspected].
- Reward manipulation: nothing in the schema bounds rings. The documented cases weigh on any schema here: tea.xyz farming (over 150,000 packages bound to wallets via `tea.yaml`, ledger counterexample row) shows that a machine-readable claim format plus a payout turns into mass self-registration; Branch G recorded that collusion rings are mostly undetectable from bids (`ledger/pass2-targets.md`); SourceCred's own docs say it "should not be used in TL1". A schema cannot fix these; the review and appeal layer must, and it does not exist here.
- Friction: none for OS users unless they publish.

## 9. Licensing

SPDX MIT (`LICENSE`, `package.json:35`). Vendored lexicons from Leaflet (MIT upstream) and atproto (MIT and Apache-2.0 files upstream; `@atproto/lexicon` package declares MIT). Runtime deps `@atproto/lexicon` (MIT) and `multiformats` (dual Apache-2.0/MIT) [reported from upstream manifests; not checked in node_modules since nothing was installed]. Compatible with the MIT Alpha; no copyleft. For the proposed Symbiotic Licence (status F) nothing can be said. Items for legal review: none specific; reimplementing the patterns raises no licence question because schemas and field names are not the expressive part [hypothesis; not legal advice]. Note: `orglabeler` and `hypercerts-evaluator`, the review-adjacent repos, have no licence file and cannot be reused at all.

## 10. Cost and effort

| Scenario | Scope | Estimate | Assumptions |
|---|---|---|---|
| Small | Resonant lexicon JSON for the eight records above, validators via `@atproto/lexicon`, fixture tests for DR-20/21/22/28 | 1 to 2 developer-weeks | TS skills present; no network publishing |
| Medium | plus hash-chained local log, CID computation, reviewer signature, periodic root export, independence predicate and appeal state machine | 6 to 10 weeks | Tier 2 manual review stays a human process; one maintainer |
| Large | plus ATProto publishing (self-hosted PDS, indexer), Hypercerts export adapter, public showcase | add 6 to 12 weeks and an ongoing ops burden | depends on Hypercerts namespace stability |

Lock-in is low if the namespace is Resonant's. Language cost: none (TS/JSON). Maintenance exposure: tracking `@atproto/lexicon` releases (0.x) if used as validator; the CI here needs a `sed` patch of `lex-cli` to make validation fail correctly (`test.yml:25-27`), a sign of tooling immaturity in the ATProto stack.

## 11. Acceptance test

Requires executing third-party code (`npm ci` of `@atproto/lexicon`, optionally `@hypercerts-org/lexicon@1.5.1`): **a request for the user, not a plan.**

- Inputs: 12 fixture claims from the pilot dimensions (Magician, Priestess, Hierophant, Moon, Death; dao-requirements section 5 item 3) written as Resonant ledger records, plus their export to `org.hypercerts.claim.activity` and `context.evaluation`.
- Checks: (1) every `review` lacking any A.9 field is rejected; (2) a dimension with no verification stays `null` after cycle close (DR-21 property test); (3) a `paymentDecision` without a preceding `review` CID is rejected (DR-22).
- Fault scenarios from the threat model: (T16) a ring fixture: D and E stage a dispute, F resolves it, F shares a sponsor with D; the independence predicate must flag the Tier 2 assignment. (T16/DR-28) after accrual, the reviewer "deletes" or rewrites the review: the ledger must keep the original entry and require a `correction` with `reverses`+`replaces`; for the Hypercerts export, the stored strongRef CID must no longer match the refetched record and the mismatch must be reported. (DR-26) a member files appeals above the governed bound in one cycle: the excess is queued, not dropped, and payout for the appealed claim is frozen. (T16, reviewer spoofing) a contributor self-publishes a schema-valid evaluation of their own activity listing a registered reviewer's DID: the ledger must reject it because the listed evaluator did not sign; the same record passes `@hypercerts-org/lexicon` validation, which demonstrates the upstream gap. (X-9) a review signed by a fresh `did:key` that claims a registered reviewer's DID must be rejected. (DR-34) a claim with three contributors and two acknowledgements must not enter review. (T13/T20, X-1) a delegated child with `archive-write` plants or edits a provenance file under `AI_MEMORY/provenance` raising `humanShare`; the exporter must refuse it as unsigned or mismatching the bridge-signed record. (Omission window) the export adapter's indexer is stopped, the evaluation is rewritten and restored, the indexer restarts: the report must state that the interval is unobserved rather than report "no drift".
- Expected output: a JSON report per check with pass/fail and the offending record CID.
- CI hook: `vitest` job on Windows, macOS and Linux runners with Node 24.
- Pass criterion: all checks pass; the export adapter carries only artifact hashes and the activity title, and never the human/AI split, tool identity, dimensions or reviewer DIDs.

## 12. Recommendation and confidence

Benefits: a maintained, MIT, typed TS vocabulary that already separates claim, measurement, evaluation, consent and payment into distinct records with content-addressed references and a deterministic signing recipe; a ready interop target if the DAO ever wants public impact showcases.

Trade-offs and blockers: none of the R26 review properties exist; reviewer identity can be forged (anyone can name evaluators; signatures optional and not bound to the listed DIDs); the record semantics are mutable and public by design; zero tests on the four record types we would use; young repo with a bus factor of about two and an organisation that has retired its previous stack once already.

Readiness: as a data-model reference, now. As a ledger, not at all.

Evidence quality: the schema mapping (lexicon JSON), tests, CI and licence are [inspected] code or schema; statements about what README, CHANGELOG and design docs say are document reads and should count as [reported] in any evidence-quality figure (after review A-hypercerts-lexicon-2, the `knownValues` claim now rests on upstream validator code); deployment, ATProto replication and mutability semantics, and the ERC-1155 history are [reported]; risk effects are [hypothesis]. The review did not change the reuse decision or any hard gate; it added the forged-reviewer finding and the key-binding rule, and lowered isolation_and_security under the clarified anchor.

Competing solutions, same criteria:

| Criterion | hypercerts-lexicon | credit-roles | SourceCred | Praise | go-eigentrust / meritrank-rust |
|---|---|---|---|---|---|
| Role for R26 | record schema | role vocabulary (14 CRediT roles) | scoring from activity graph | peer "praise" with quantifier review | scoring over a trust graph |
| Licence | MIT | MIT | MIT/Apache-2.0 (ledger) | GPL-3.0 | MIT / MIT |
| Last push | 2026-10-21 | 2026-04-29, 1 contributor | 2023-02-14 | 2024-10-14 | 2025-08-31 / 2026-10-06 |
| Explanation record | partial (3/14 A.9) | none | none (scores) | partial (quantifier scores) | none |
| Review tiers / independence | absent | absent | absent | multiple quantifiers, no conflict check (ledger) | graph damping only |
| Appeal | absent | absent | absent | no appeal code (ledger) | absent |
| Human/AI attribution | absent | absent | absent | absent | absent |
| Append-only | contrary (owner delete) | n/a | ledger file, mutable | database | n/a |
| Documented failure | none found | none | "should not be used in TL1" | stale | n/a |
| Fit | data-model reference | constrain `role` to CRediT URIs | reject (activity-based, contradicts DR-23) | learn quantifier pattern only (GPL) | later: consume `review` and `acknowledgement` edges as a scoring input, never as the decision |

[gh api metadata inspected 2026-10-21; function columns from the ledger, reported.] The schemas combine: hypercerts-lexicon patterns for records, credit-roles for the role vocabulary, MeritRank as an optional anomaly signal (Devil audit), with review and appeal as the custom layer nobody provides.

First PR (PR-sized): in `resonantos-economy-research` (the DAO's only code-bearing research repo, S16), add `ledger-schema/lexicons/org/resonantdao/ledger/{claim,review,accrual,correction,appeal,dimension}.json` modelled on `org.hypercerts.context.evaluation` and `vocab.tag`, plus `ledger-schema/test/a9-fields.test.ts` turning DR-20, DR-21 and DR-28 into executable fixtures. If the maintainers prefer the Alpha, the same files go under a disabled-by-default add-on next to `browser-first/host/memory-schema.mjs`. (Paths proposed, new.)

Confidence: **medium**. High that the review and appeal properties are absent (direct inspection of every relevant record type and zero tests); medium on the operational claims about ATProto mutability and replication, which rest on the ATProto specification, docs and the not-merged PR #184 text rather than PDS code read here.

## 13. Scores (for scoring/scores.csv)

Profile: dao_contract.

| Criterion | Score | Reason (anchor) |
|---|---|---|
| requirement_fit | 2 | Evidence and linkage layer covered; review tiers, appeals, null, versions, append-only and human/AI split absent (section 5.1 counts). |
| demonstrated_maturity | 3 | Releases, CI and tests, but thin usage evidence and no tests on evaluation, contribution or acknowledgement. |
| isolation_and_security | 1 -> 0 | Clarified anchor (weights.json, D3.7): the score rates the upstream artifact (v1.5.1), and contrary evidence unfixed in it gives 0. v1.5.1 contradicts two Accounting properties by design: reviewer identity is forgeable (`evaluation.json:11`, `:18-26`, `:69-73`) and revocation and correction are deletion or overwrite (`award.json:42`; ATProto repo semantics). It does not even claim the properties in docs, so 1 ("claimed in docs only") is not met either. The round-trip signature test (`validate-signing-example.test.ts:247-254`) concerns provenance and has no key binding. |
| licence_suitability | 5 | MIT file-level; vendored lexicons MIT/Apache. |
| integration_effort | 4 | Embeddable typed TS library; minus one because every R26 field would be a custom extension lexicon. |
| maintenance_sustainability | 3 | Active, 19 author names, SemVer since June 2026; bus factor about two, falling cadence, org has retired its previous stack. |
| sovereignty_and_portability | 3 | Schema use is local and readable (5); ATProto publication is world-readable and replicated (1); scored for the Phase 1 local use with that caveat. |
| interoperability | 4 | ATProto ecosystem, CID/strongRef, documented attestation recipe; no Solana link, no CRediT binding. |
| operational_complexity | 4 | One library in-process; publishing would add a PDS and an indexer. |

Weighted total (dao_contract weights): before review 0.16x2 + 0.16x3 + 0.18x1 + 0.08x5 + 0.06x4 + 0.14x3 + 0.08x3 + 0.06x4 + 0.08x4 = 2.84; after review, isolation 1 -> 0: 2.84 - 0.18 = **2.66**. Convenience criteria (licence, integration, sovereignty, interop, operations) supply 1.44 of it (DAO review C-2, a lead matter; no score here changes for it).

## 14. Open questions for the adversarial reviewers

1. Mutability rests on ATProto repo semantics (`putRecord`, `deleteRecord`) [reported], `award.json:42`, the permission-sets doc and PR #184 text; `authWrite.json:8-28` shows only that apps can be delegated the same power. I did not read ATProto PDS code. A DAO-run indexer can record versions from first publication (firehose carries prior CIDs [reported]) but not during its own downtime; whether `hyperindex-history` does this was not inspected.
2. The signing test is a round trip with one in-memory `did:key` (`validate-signing-example.test.ts:247-254`, `README.md:751-761`). Is there any verifier anywhere in the org that resolves DID documents, binds the signer to listed evaluators and rejects tampered records? Not found in this repo.
3. No farming, capture or evaluation post-mortem was found for Hypercerts in either generation. The 2023 evaluation design (hypercerts#431, Kleros jurors) and the 2024 `hypercerts-evaluator` MVP were not followed by any published outcome. Reviewers with knowledge of Optimism RetroPGF or Gitcoin rounds that used hypercerts may know of one.
4. Section 5.1 marks are a single inspector's judgement; the [P] cells in particular (e.g. CEL as a guard language, acknowledgement as an appeal trigger) are generous readings.
5. The ERC-1155 relationship rests on archived-repo metadata and two web pages that say nothing about it; whether old on-chain hypercerts were ever imported into ATProto records is unknown.
6. Hours estimates in section 10 assume one TS developer and no ATProto publication.
7. Windows and macOS behaviour of the validators is untested by the project (CI Linux, Node 22); Node 24 compatibility is assumed.
8. Predecessor run (2026-10-20) stopped after a usage limit and a declined permission prompt; its findings were re-verified here at file:line before citing, and its unrecorded search results (sybil, spam) were re-run.

### Review responses (2026-10-21)

| Id | Verdict | Change made (section) or evidence for dispute |
|---|---|---|
| S-hypercerts-lexicon-1 | accepted | The inline `key` is a DID-URL (`signature/defs.json:27-29`); the documented example signs with a self-certifying `did:key` (`README.md:751-761`) and leaves trust to verifier pinning (`README.md:692`). Added to 5.2; key-resolution rule and domain separation added to 7; fresh-key fixture added to 11; recipe qualified in 6. |
| S-hypercerts-lexicon-2 | accepted | Section 7: bridge produces and signs provenance with a key outside child reach, stored in the bridge-only location (X-1); `AI_MEMORY/provenance` holds a readable copy only; exporter refuses unsigned or mismatching records. Child-planting fault added to 11. |
| S-hypercerts-lexicon-3 | accepted (no objection) | Score later lowered to 0 under the clarified anchor (13); see D-hypercerts-lexicon-5. |
| X-1 | accepted | As S-hypercerts-lexicon-2 (7, 11). |
| X-9 | accepted | As S-hypercerts-lexicon-1 (5.2, 7, 11). |
| A-hypercerts-lexicon-1 | accepted | Sections 4, 5.1 (DR-28), 8, 14 Q1 now cite ATProto repo semantics (`putRecord`/`deleteRecord`) [reported] for owner mutability and keep `authWrite.json:8-28` as evidence of app-delegated mutation. |
| A-hypercerts-lexicon-2 | accepted | Section 2 relabelled [reported] and backed with upstream code: `bluesky-social/atproto` `packages/lexicon/src/validators/primitives.ts:178-195` at `3b6c48d` enforces `const`/`enum` only and never reads `knownValues` (`types.ts:62` declares it optional). Fetched with `gh api .../contents/...?ref=3b6c48d`; this is upstream main, not the pinned `^0.6.0`. Section 8(d) updated. |
| Archaeologist cross-cutting 4 | accepted | Section 12 evidence-quality line now separates document reads from code inspection. |
| D-hypercerts-lexicon-1 | accepted | Confirmed: `evaluators` required (`evaluation.json:11`, `:18-26`), `signatures` optional (`:69-73`), no "evaluator confirms listing" acknowledgement (`acknowledgement.json:7`). Added to 5.1 (DR-24), 5.2 ("contradicted by design", full condition stated), 8 (a4), 12 blockers; reviewer = signer rule in 7; forged-listing fault in 11. |
| D-hypercerts-lexicon-2 | accepted | Citation corrected as A-1; app-delegation risk added (8 a2); archiving-indexer mitigation and downtime gap added (8 a3, 14 Q1); omission fault added to 11. |
| D-hypercerts-lexicon-3 | accepted | DR-34 C -> P (5.1, citing `activity.json:109-113`); section 7 requires one signed acknowledgement per listed contributor before review; fixture in 11. |
| D-hypercerts-lexicon-4 | accepted | Section 7 export now per item and opt-in, artifact hashes plus title only, never the split, tool identity, dimensions or reviewer DIDs; pass criterion in 11 updated. |
| D-hypercerts-lexicon-5 | accepted (under the clarified anchor) | isolation_and_security 1 -> 0: anchor clause (a) gives 0 for contrary evidence unfixed in the artifact used, and clause (c) rates the upstream artifact; forgeable reviewer identity and deletion-as-revocation are unfixed in v1.5.1, and the properties are not even claimed in docs, so 1 is not met. Weighted total 2.84 -> 2.66 (13). The lead may still rule that a design property is not "contrary evidence" in the anchor's sense; then the score returns to 1 and the total to 2.84. |
| C-3 | accepted | Section 5.2 now states the full condition under which a DID list yields reviewer identity, and the fault that breaks it (11). |
| C-4 | accepted | `revocation` record with reason, authority, evidence, appeal deadline and reversal added to 7; section 8 points it at the shared governed revocation record the review proposes. |
| C-5 | accepted | Indexer-downtime omission window added to 8 (a3) and 11. |
| C-6 | accepted | Section 5.2 and 7 state that `humanAiSplit` has no producer in the Alpha, defaults to `null`/conservative under DR-17, and that T13/T20 stay open; the dossier does not credit the candidate with attribution. |
| C-8 | accepted (no objection to this dossier) | Status line replaced with `STATUS: COMPLETE (amended after review, 2026-10-21)`. |
