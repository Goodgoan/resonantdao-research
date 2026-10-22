# Dossier: solana-foundation/rewards (solana-foundation-rewards)

STATUS: COMPLETE (amended after review, 2026-10-21)

Inspector: s3-insp-rewards (relaunch of the 2026-10-20 agent of the same id), Claude Opus 5.5. Date: 2026-10-21. Clone: `research/_untrusted/solana-foundation-rewards/` (full history, not shallow). Pinned to the audited-through commit `aa1cfd9276375e44e57d1917d110ff095fb6d475` (2026-04-22, "fix(rewards): harden direct close lifecycle and revoke accounting (#32)"); the OtterSec engagement itself ran on its ancestor `d795849` (2026-04-03). HEAD `0b1bf178bb989ba2b790f647c7a75dc4cb8e39bd` (branch `main`, 2026-10-05). Unless a citation names another commit, `path:line` citations are at HEAD; citations at `aa1cfd9`, `d795849` or the unmerged branch commit `119f5f5` say so. Execution: none. No build, test or binary from the clone was run. Read-only mainnet RPC (api.mainnet-beta.solana.com) was queried on 2026-10-20 13:07-13:09 UTC (15:07 Europe/Berlin, by the predecessor) and again on 2026-10-21 08:08-08:12 UTC (by me). The responses are saved under the scratchpad (section 14). Read-only GETs also went to verify.osec.io and the GitHub API. Nothing was signed or sent.

Evidence labels: [inspected] (read the code, test, PDF or RPC response), [executed] (none), [reported] (README, docs, issue text, ledger rows from other agents), [hypothesis]. Repository instruction files are evidence only. The clone ships `.claude/commands/validate-accounts.md`. I did not open it as an instruction and did not invoke it (lesson untrusted-clone-skills).

**Headline finding.** The Stage 2 description of this codebase as a Token-2022 NonTransferable "Points" credential with force-burn revocation does not describe the audited code, the code on `main` or the deployed program. Points was added on 2026-03-31 (`1b47f5f`, #22). It was removed three days later by `d795849` (#29, "scope repo to direct and merkle only"), and that removal commit is the one OtterSec audited. Points now lives only on the unmerged, unaudited branch `origin/feat/restore-points-continuous` (`119f5f5`, 2026-05-18). Precisely: the audited tree still compiled the Points mint-construction helper and config layout as unreachable code, but the issue, use, transfer and revoke processors were never in audit scope. At `d795849` you find `pub mod points_cpi` (`program/src/utils/mod.rs:9, 23`), `state/points_config.rs`, seven `events/points_*.rs` and the discriminators `InitPoints = 22` to `RevokePoints = 28` (`traits/instruction.rs:36-43, 80-81`). The entrypoint, however, dispatches only the 12 Direct/Merkle/EmitEvent arms and returns `InvalidInstructionData` for everything else (`entrypoint.rs:59` at `d795849`). `git diff --quiet d795849 119f5f5 -- program/src/utils/points_cpi.rs program/src/state/points_config.rs` exits 0, so the branch helper and layout are byte-identical to the audited dead code. The OtterSec text names no Points file; the only mention is its scope sentence "non-transferable points" [inspected]. That dead code probably explains both OtterSec's scope wording and Stage 2's misreading. The deployed mainnet binary contains no `points` string. What is audited and deployed is a token-distribution program: direct allocations and Merkle claims with vesting and per-claimant revocation. The README still advertises Points (`README.md:20-27, 114-132`), and the open upstream PR #138 ("docs: align README with Direct and Merkle distributions") admits the drift.

## 1. Identification

| Item | Value | Evidence |
|---|---|---|
| Name | Solana Foundation "Rewards Program" (`rewards-program` crate) | `program/Cargo.toml:2`; security.txt `program/src/lib.rs:39-45` [inspected] |
| Canonical source | https://github.com/solana-foundation/rewards (GitHub). It was formerly `solana-program/rewards`, and the deployed binary's security.txt still names that URL (RPC strings, digest of 2026-10-20) | `c34b2b7` "point repo references to solana-foundation org" (2026-06-02) [inspected] |
| Maintainers | Solana Foundation org. Human commits: `jo` 61 plus `Jo D` 17 (same person, dev-jodee), `Anti83` 1, `amilz` 1. `dependabot[bot]` 50 | `git shortlog -sn --all` [inspected] |
| Releases / tags | None. `git tag` is empty and the GitHub releases API returns 0. AUDIT_STATUS's "immutable tags/releases (for example v1.0.0)" do not exist | `audits/AUDIT_STATUS.md:14-18` [inspected]; GitHub API 2026-10-21 [inspected] |
| Packages | crate `rewards` 0.0.1 (Rust client, `clients/rust/Cargo.toml:2-3`). npm `@solana/rewards` (`clients/typescript/package.json:2-3`, source says 0.0.1; `npm view` reports 0.0.0, modified 2026-05-15). Workspace version 0.1.0 (`Cargo.toml:6`) | [inspected] |
| Program ID | `REWArDioXgQJ2fZKkfu9LCLjQfRwYWVVfsvcsR5hoXi` (`program/src/lib.rs:33`), mainnet, program-data `BKRW5hPM4cMjNNr2EKdpScMS5kmK5bTxdELTH4LurZnu` | RPC [inspected] |
| Audit | OtterSec, "Solana Program Rewards Security Assessment", dated 24 April 2026. Engagement 7-14 April 2026, against commit `d795849`. Auditors: Akash Gurugunti, Gabriel Ottoboni | `audits/2026-ottersec-solana-foundation-rewards-audit.pdf` pp.1-3 (text extracted with a stdlib parser) [inspected] |
| Advisory | GHSA-8hgp-gwvv-wrjf, published 2026-05-18, severity medium, no CVE: a third-party disclosure (reporter "Shek Dev") of 3 MEDIUM findings at commit `0ffbc41` (post-audit). The ledger missed it | GitHub security-advisories API [inspected] |
| Licence | MIT, `LICENSE` ("Copyright (c) 2026 Solana Foundation"). Single licence file in the repo. Rust and TS clients declare `license = "MIT"`. The `rewards-program` and workspace manifests carry no `license` field (minor manifest gap, not a conflict) | [inspected] |
| Docs language | English. English-speaking maintainers | [inspected] |
| Runtime | Rust on pinocchio (no Anchor), SBF target. Generated clients via Codama (`idl/`) | `program/Cargo.toml:17-32` [inspected] |

## 2. What works

On `main` (HEAD) and in the audited code, the instruction set is the same 12 instructions [inspected: `program/src/instructions/definition.rs:30-373` at HEAD; the `git show aa1cfd9:` and `d795849:` copies of the same file each list 12 discriminators]. Dispatch is in `program/src/entrypoint.rs:20-63`.

| # | Instruction | What it does | Tests (file: test names, not run) |
|---|---|---|---|
| 0-4 | CreateDirectDistribution, AddDirectRecipient, ClaimDirect, CloseDirectDistribution, CloseDirectRecipient | Per-recipient PDA `[b"direct_recipient", distribution, recipient]` (`state/direct_recipient.rs:28`) holds a vesting allocation funded from the authority's token account | `tests/integration-tests/src/test_create_direct_distribution.rs`, `test_add_direct_recipient.rs`, `test_claim_direct.rs` (incl. `test_claim_direct_rejects_recipient_token_account_wrong_owner` :92), `test_close_direct_*.rs` |
| 5-8 | CreateMerkleDistribution, ClaimMerkle, CloseMerkleClaim, CloseMerkleDistribution | Only a Merkle root is stored on-chain. The leaf is `compute_leaf_hash(claimant, total_amount, schedule)` and the claimant signs and proves inclusion (`instructions/merkle/claim/processor.rs:35-45`) | `test_create_merkle_distribution.rs` (incl. `..._rejects_transfer_fee_underfunding` :120, `..._rejects_transfer_hook_mint` :165), `test_claim_merkle.rs` (incl. `..._rejects_claimant_token_account_wrong_owner` :95), `test_close_merkle_*.rs` |
| 9 | RevokeDirectRecipient | Distribution authority (single signer, `revoke_recipient/accounts.rs:40`) revokes a recipient. Mode NonVested pays out vested-unclaimed and frees the rest. Mode Full frees everything. It closes the recipient PDA (`revoke_recipient/processor.rs:27-32, 53-106`) | `test_revoke_direct_recipient.rs`: `test_revoke_wrong_authority` :136, `test_revoke_not_revocable` :64, `test_revoke_full_at_midpoint` :211, `test_revoke_freed_allocation_allows_new_recipient` :347, revocable-bitmask tests :396-462 |
| 10 | RevokeMerkleClaim | Authority proves the claimant's leaf, then creates a permanent `Revocation` marker PDA `[b"revocation", distribution, claimant]` (`state/revocation.rs:12-24, 73-79`; `revoke_claim/processor.rs:36-55, 140-158`). ClaimMerkle rejects a revoked claimant (`merkle/claim/processor.rs:39-45`) | `test_revoke_merkle_claim.rs`: `test_revoke_merkle_wrong_authority` :84, `test_revoke_merkle_double_revoke` :124, `test_claim_after_revocation_fails` :356, `test_revoke_merkle_closed_distribution_cannot_reactivate_stale_revocation` :371 |
| 228 | EmitEvent | Self-CPI event sink, Anchor-compatible 8-byte discriminators (OtterSec ADV-04 fix, PR #37) | - |

Test volume at HEAD: 219 `#[test]` functions in `tests/integration-tests/src` (LiteSVM, `tests/integration-tests/Cargo.toml:9-24`) and 315 in `program/src` unit modules [inspected, counted with grep]. CI lanes: `.github/workflows/test.yml:31` (`cargo test -p rewards-program`), `:58-64` (build the SBF program, then `cargo test -p tests-rewards-program`), `security.yml:31, 44, 63-64` (`cargo audit`, `pnpm audit --ignore-unfixable`, miri on the program crate), plus format, idl-check, benchmark, coverage, web, publish and release workflows [inspected; not run]. `TEST_COVERAGE.md` is a "semantic coverage estimate ... not instrumented line coverage" and counts 21 instruction handlers, against 12 at HEAD. It is stale from the four-system era [inspected].

**Points (only on unmerged branch `119f5f5`) [inspected at that commit, unaudited, undeployed].** InitPoints creates a Token-2022 mint with NonTransferable, PermanentDelegate (the PointsConfig PDA) and MintCloseAuthority, with `freeze_authority: None` (`program/src/utils/points_cpi.rs:24-85` at `119f5f5`). RevokePoints lets the config authority (single signer, `revoke_points/accounts.rs:33`) burn the user's entire balance through the permanent delegate (`revoke_points/processor.rs:26-50`). TransferPoints needs the authority and the `from_user` to sign (`transfer_points/accounts.rs:39-41`). PointsConfig holds `{bump, transferable, revocable, mint_bump, authority, seed}` with PDA `[b"points_config", authority, seed]` (`state/points_config.rs:30-41`). `PointsMaxSupplyExceeded` is declared in `errors.rs` but referenced nowhere else (git grep). Tests on the branch: `test_points_nontransferable.rs::test_points_direct_spl_transfer_blocked` (:13), `test_revoke_points.rs::test_revoke_points_success` (:47), `::test_revoke_points_wrong_authority` (:107), `::test_revoke_points_not_revocable` (:60), `::test_revoke_points_then_reissue` (:145).

**Deployment [inspected RPC].** Program account (2026-10-21 08:08 UTC, slot 459556847): `{"programData":"BKRW5hPM4cMjNNr2EKdpScMS5kmK5bTxdELTH4LurZnu"}`, owner `BPFLoaderUpgradeab1e11111111111111111111111`, executable true, lamports 200000000, space 36. This is identical to the 2026-10-20 response (slot 459240962). Program-data decoded: last-modified slot 422571636, upgrade authority `DXtFpbPjcn2hxPnw79x1Pfoj35vXh5AsWBkS37YnXMVv`, ELF 179816 bytes (170617 after trimming trailing zeros), SHA-256 of the trimmed ELF `96a2835c4d2c0065882de9845faae7287332e84f4a5f19ef9b199c8c6034d88d` (same both days). The ELF contains 0 occurrences of `points`, `Points`, `continuous` or `NonTransferable`. History from getSignaturesForAddress on program-data (4 entries, unchanged between the two days):

| UTC | Slot | Tx (prefix) | Action |
|---|---|---|---|
| 2026-05-19 16:45:04 | 420808069 | 31mSkgcw | `deployWithMaxDataLen` (maxDataLen 170632), upgrade authority `wB85XAcKnhQsU8BU7njytoU8AcPJuaKfkVacUyHo3Cj`, payer `9LfvVtTCJQ4gEm8qNhcibgXdB9Wgb2MdpAcVRsgu9SUF`; log "Deployed program REWArD..." |
| 2026-05-19 16:55:24 | 420809608 | 5QQAUCNm | `setAuthorityChecked` wB85.. -> 9LfvVt.. (single key) |
| 2026-05-19 19:48:22 | 420835504 | 3XX3qrqy | `setAuthority` 9LfvVt.. -> DXtF.. (log "New authority Some(DXtF..)") |
| 2026-05-27 20:07:42 | 422571636 | 3pf95zpB | `extendProgram` +9184 bytes, payer 9LfvVt.. (no code change) |

A staged upgrade sits ready. Buffer `CdrKPqdpdcbWaub2EsTixWnN1hWsF7foRYLvgoXXMq8x` had its authority set to the vault on 2026-05-27 20:13:51 UTC (tx `31RhNgdE`, signer 9LfvVt..). On 2026-10-21 it still exists (space 179853, authority bytes equal to DXtF..). Its trimmed-ELF SHA-256 is `4e7f4e77cb151f920289fe9bf7a22654c02957da5d0e56ae92ca8409173b0276`. It differs from the deployed ELF and also contains no `points` string.

OtterSec verify API (2026-10-21): `{"is_verified":false,"on_chain_hash":"96a2835c...","executable_hash":"449b6b278cf196d8926073f0118be1a67ab5ddcf656a416a32e3f79ebbcdbe1c","repo_url":"https://github.com/solana-program/rewards/tree/758aa7a5...","commit":"758aa7a5009f856116b963f426bab9c095dae658","last_verified_at":"2026-09-10T09:06:42"}`. The verify PDA claims the deployment was built from `758aa7a` (2026-05-19, a post-audit CI commit), but that commit's build hash does not match the chain. **The source commit of the deployed binary is therefore unknown, and in any case it is post-audit** (the deployment came 27 days after `aa1cfd9` and one day after `4f5a8c6`).

## 3. Maturity

- **Cadence (main, last 12 months)** [inspected]: commits per month: 2026-02: 14, 03: 13, 04: 10, 05: 40, 06: 16, 07: 11, 08: 3, 09: 3, 10: 2. Since June most commits are dependabot bumps and CI hardening (`df7517d` workflow permissions, `7111ccf` deploy secrets). The repository was created 2026-02-03.
- **Bus factor: 1.** dev-jodee wrote 78 of 80 human commits [inspected].
- **Issue response** [inspected, GitHub API 2026-10-21]: only one non-PR issue exists in total (#136 "Codex", 2026-09-23, closed 2026-10-06, 1 comment), so five issues cannot be sampled. PRs are merged within 0-3 days (#126-#139). PR #138 (docs drift) has been open since 2026-10-01.
- **Security posture.** `SECURITY.md` routes reports to GitHub advisories and promises a response "typically within 72 hours". One OtterSec audit exists, plus GHSA-8hgp-gwvv-wrjf (below). CI runs cargo audit, pnpm audit, miri and CodeQL (`ci/scope-codeql-to-production-code` branch). The deployed build is not verified (section 2).
- **Audit content** [inspected, PDF text]. Scope: "The source code was delivered to us in a Git repository at https://github.com/solana-program/rewards. This audit was performed against commit d795849." The scope paragraph describes the module as covering "direct allocations, Merkle claims, continuous balance-based pools, or non-transferable points". The audited commit dispatches no continuous or points instruction. The mint helper and config layout are present only as unreachable code (headline), and every finding concerns Direct or Merkle. Findings: 8 in total, Critical 0, High 1, Medium 2, Low 3, Info 2. All 8 are marked RESOLVED.

| ID | Sev. | Title | Fix |
|---|---|---|---|
| OS-SPR-ADV-00 | High | Absence of recipient token-account ownership check (revocation paths) | PR #33 `bf4077e` |
| ADV-01 | Medium | Recreated distribution PDA revives stale recipients | PR #32 `aa1cfd9` (direct only, see GHSA F2) |
| ADV-02 | Medium | Underfunded allocations due to transfer-fee mints | PR #34 `dc24958` |
| ADV-03 | Low | Parent closure breaks recipient account cleanup | PR #32 |
| ADV-04 | Low | Misleading Anchor-compatible event encoding | PR #37 `2bf46fb` |
| ADV-05 | Low | Recipient-only closure creates incentive mismatch | PR #35 `41c0e2f` |
| SUG-00 | Info | Failure to update Merkle claim state on NonVested revoke | PR #32 |
| SUG-01 | Info | Missing instruction-data validation hooks | PR #36 `31fa899` |

  OtterSec's PDF says "Resolved in PR #NN" but does not say whether OtterSec re-reviewed the fixes. AUDIT_STATUS claims they were "resolved and re-reviewed in PRs #32-#37" (`audits/AUDIT_STATUS.md:12`) [reported].
- **Post-audit advisory GHSA-8hgp-gwvv-wrjf** [inspected]. Reported 2026-05-17 at `0ffbc41`, published 2026-05-18, `patched_versions` empty. Three MEDIUM findings, all present in the audited-through commit `aa1cfd9`: (F1) claim destination token account not pinned to the claimant or recipient, so a phishing dapp could misdirect tokens; (F2) Merkle `close_distribution` deletes the PDA, so stale `MerkleClaim` state carries over on re-creation (the Merkle analogue of OtterSec ADV-01, which was fixed only on the direct side); (F3) Token-2022 TransferHook mints cannot be used on the rewards transfer paths (denial of service). All three are fixed in `4f5a8c6` "fix: audit fixes (#43)" (2026-05-18). Its commit message also lists "reject underfunded merkle distributions" and "remove points and continuous surfaces". **These fixes are unaudited under the repository's own rule** ("Commits after the audited-through SHA are considered unaudited", `audits/AUDIT_STATUS.md:12`). Regression tests exist at HEAD (section 2 rows 0-8) [inspected; not run].
- **Documentation quality.** The README misstates the system. It advertises four distribution types including Points and Continuous, with Continuous instructions 11-21 and Points instructions 22-28 that do not exist on main (`README.md:20-27, 100-132`). It shows a "Solana-Devnet" badge (`README.md:5`) while mainnet is live, and its Deployments table is empty (`README.md:13`). `TEST_COVERAGE.md` is stale. The per-instruction READMEs under `program/src/instructions/{direct,merkle}/README.md` exist (not assessed).
- **Usage.** 13 stars and 6 forks (GitHub API). I found no named production user of the program [not established, section 14]. The Squads vault's other activity involves Token ACL (`TACLkU6CiCdkQN2MjoyDkVg2yAH9zkxiHDsiztQ52TP`, see section 8).

## 4. Architecture

- **Control flow.** `entrypoint!(process_instruction)` (`program/src/entrypoint.rs:20`) splits off a 1-byte discriminator and dispatches to `process_*` (`:22-63`). Each processor parses an `*Accounts` struct whose `TryFrom` runs signer, writable, program-owner, token-program and ATA checks (example: `instructions/direct/revoke_recipient/accounts.rs:28-89`). It then loads state with discriminator and version checks (`state/revocation.rs:38-50`), checks `validate_authority` (`traits/distribution.rs:29-34`: a plain equality against the stored `authority`), performs token CPIs signed by the distribution PDA, and emits an event through self-CPI.
- **Storage.** Program-owned PDAs only. Account discriminators at HEAD: DirectDistributionClosed = 5 and MerkleDistributionClosed = 6, renumbered from the audited layout (section 8). Authority is part of every distribution's PDA seeds: `[b"direct_distribution", mint, authority, seeds]` (`state/direct_distribution.rs:27`) and `[b"merkle_distribution", mint, authority, seeds]` (`state/merkle_distribution.rs:27`). The beneficiary wallet is part of every per-user PDA: `direct_recipient`, `merkle_claim` (`state/merkle_claim.rs:24`) and `revocation` (`state/revocation.rs:18`).
- **Consequence for identity.** No instruction rotates a distribution authority or moves an allocation to another wallet. A grep of `program/src` for `set_authority|update_authority|new_authority|rebind|freeze` returns nothing [inspected]. Both bindings are fixed for the life of a distribution because the addresses derive from them.
- **Lifecycle.** The upgradeable BPF loader governs it. Upgrades go through Squads: CI writes a buffer whose authority it assigns to the vault, then members create the upgrade proposal (`.github/workflows/release.yml:130-171`; "CI keypair role: fee payer and buffer writer only", `:171`) [inspected]. A Doppler-hosted secret supplies `SQUADS_VAULT` (`release.yml:151`).
- **Dependencies.** pinocchio, pinocchio-token / -token-2022 / -associated-token-account / -system / -log, spl-token-2022, codama, const-crypto, thiserror, solana-security-txt (`program/Cargo.toml:17-29`). There are no oracle or off-chain service dependencies. Clients: `@solana/kit ^6.10.0` (`clients/typescript/package.json:30`) and the Rust `rewards` crate.
- **Operational model.** It is a shared public program. Anyone can create distributions in it and becomes the authority of their own distributions. Resonant would not run its own copy unless it forks.

## 5. Direct relevance

| Req. | Resonant target | What this codebase offers | Status |
|---|---|---|---|
| R22 non-transferable credential | DR-01 (transfer must fail), DR-02 (record is truth), DR-04 / DR-10 (one per wallet, caps), DR-06 (revocation with appeal), DR-07 (re-binding), DR-08 (freeze), DR-09 (privacy) | Main: nothing credential-shaped. Branch `119f5f5`: the NonTransferable + PermanentDelegate + force-burn + reissue pattern | D for Resonant use (pattern exists as unaudited source); G for "audited credential" |
| R23 key control and anchored registry | DR-05, DR-50 / 51, Phase 1 core items 1-2 | Merkle distribution: an off-chain list committed by an on-chain root, with signer-gated inclusion-proof claims and per-member revocation markers (`merkle/claim/processor.rs:35-45`; `revoke_claim/processor.rs:43-55`). Nothing for off-chain key-control proof (SIWS) or registry operation | Pattern only (C as code; not a registry) |

**Threat-model property check (threat-model-draft.md section 4, "Credentials (T15)")**

| Property | Demonstrated by | Verdict |
|---|---|---|
| Non-transferable | `test_points_nontransferable.rs::test_points_direct_spl_transfer_blocked` at `119f5f5` (LiteSVM, not run). That test shows only that Token-2022's NonTransferable extension blocks a direct `TransferChecked` | **Not demonstrated, and contradicted on the branch.** The extension alone is not the property; the controlling program's instruction set decides it (review D-1, C-3). On `119f5f5`, `TransferPoints` checks `config.validate_transferable()` (`transfer_points/processor.rs:28`), then burns from `from_user` and mints to `to_user` through the PDA delegate ("Transfer via burn + mint (NonTransferable blocks standard transfers)", `:55-73`), with the authority and `from_user` co-signing (`accounts.rs:39-41`). The class flag is set at InitPoints (`init_points/data.rs:20`). `test_transfer_points.rs::test_transfer_points_success_to_new_user` (:67) exercises the move on a NonTransferable mint (`.transferable(1)` at :255, :318). A holder and issuer who agree can therefore sell a point. Full condition: a mint is non-transferable in the DR-01 sense only if the program holding its PermanentDelegate has no issuer-mediated move, or that move is the governed recovery path. Main and the deployed code have no such asset at all |
| Issuer-revocable | Main: `RevokeMerkleClaim` (discriminator 10), test `test_claim_after_revocation_fails` (`test_revoke_merkle_claim.rs:356`) and `test_revoke_merkle_wrong_authority` (:84). Audited at `d795849` with ADV-00 / SUG-00 fixes, re-fixed post-audit (GHSA F2). Branch: `RevokePoints`, test `test_revoke_points_success` (:47) | **Demonstrated for reward claims, not for credentials.** Revocation is permanent: no un-revoke, no appeal, no recorded reason or evidence, so DR-06 fails (review C-4). It is signed by one authority key per distribution |
| Governed re-binding to a new wallet | None. The beneficiary is a PDA seed, no instruction exists, and TransferPoints on the branch needs the old wallet's signature (`transfer_points/accounts.rs:41` at `119f5f5`), which a lost key cannot give. The closest approximation is issuer revoke-then-reissue (`test_revoke_points_then_reissue` :145, branch), which is unilateral, not governed | **Not supported** |
| Audit report | OtterSec 2026-04-24, scope `d795849`, Direct and Merkle only, 8 findings resolved; GHSA-8hgp-gwvv-wrjf, 3 MEDIUM, fixed post-audit (unaudited) | **Exists, but covers neither the credential pattern nor the deployed code** |

Threats. **T15** is not closed: there is no re-binding and no freeze (the branch sets `freeze_authority: None`, `points_cpi.rs:85` at `119f5f5`), and revocation is not appealable. **T12** is made worse if the program is reused as is: Resonant's distribution authority would be one key fixed in the PDA seeds, and the code's upgrade authority is a third party's 3-of-5 with time_lock 0 (section 8). **T14** is not addressed: DirectRecipient accounts publish the wallet at add time, and a Merkle claim or revocation publishes the claimant-to-distribution link permanently. The Merkle-root design hides the unclaimed list, which is the only privacy-relevant feature [inspected].

**Fitness of purpose.** This program cannot serve as the DAO membership credential.
- On main there is no persistent, wallet-bound object to check. A recipient or claim PDA is an allocation that closes when it is fully claimed or revoked.
- On the Points branch the credential would be a fungible balance. Nothing enforces "at most one active credential per wallet" (DR-04 / DR-10). IssuePoints takes any `quantity` (`issue_points/data.rs:5-6` at `119f5f5`), and no supply cap is enforced.
- Human/AI exclusivity, caps, reason/evidence/appeal records (DR-06) and recovery (DR-07) are absent.

Its value to Resonant is as a **pattern**: (a) Token-2022 NonTransferable with a PDA permanent delegate as the revocation lever. The same lever can also move tokens, so the instruction set around it must be constrained (property table); (b) a Merkle root plus permanent revocation-marker PDA keyed by `(parent, wallet)`; (c) a Squads-buffer release pipeline. Interaction invariants: an on-chain credential read by the OS must remain display-only (I2, I8). Nothing in this program creates OS authority, so G5 is a property of the integration, not of this code.

## 6. Reuse decision

**Decision: learn the pattern and reimplement** (unchanged after review). Phase 1 uses an off-chain signed membership record with no token. A thin Resonant issuer program over Token-2022 is Phase 2, after DR-54 (section 12, review D-4). No hard gate changed in the review. Reject it as a dependency or adapter for R22. Do not fork the Points branch: it is unaudited, single-author, unmerged since 2026-05-18 and was removed from main by its own author. It also lacks the per-wallet uniqueness, rotation, freeze and re-binding that R22 needs, so a fork would rewrite most of it.

| Gate | Result | Clause |
|---|---|---|
| G1 no mandatory cloud | PASS | On-chain program plus any Solana RPC. No telemetry in the program or clients [inspected]. The release pipeline's Doppler secret concerns the maintainers, not users |
| G2 per-OS | PASS | Chain-side code is OS-independent. The TS client (`@solana/kit`) runs in Node on all three OSes [hypothesis, standard]. Building or verifying needs the Solana toolchain or Docker (section 10) |
| G3 licence | PASS | MIT file-level (`LICENSE`), compatible with the MIT Alpha |
| G4 chain | PASS | Solana mainnet |
| G5 invariants | PASS (conditional) | The program grants no OS authority. The Resonant verifier must keep credential state display-only (I2, I8) |

## 7. Integration design

Because the decision is "pattern", the integration is a Resonant-owned issuer program plus a read-only OS verifier. Phase 1 core item 1 (dao-requirements section 5) keeps the registry off-chain and makes the token optional.

- **Phasing (amended after review D-4).** Phase 1 issues no token. Membership is an off-chain signed record in the registry or R30 log (dao-requirements section 5 item 1: the token is "optional"; T18 premature on-chain commitment). Revocation, appeal and re-binding live in that registry. A Token-2022 class is Phase 2, only after DR-54 is met (independent audit, custody policy, devnet test of the exact program). The on-chain design below is a design at status D, not inspected code.
- **On-chain, Phase 2 (proposed Resonant program, not this codebase) [hypothesis/design].**
  - **Uniqueness.** One PDA `[b"member", wallet]` per member, which makes "at most one" structural (DR-04).
  - **Mint.** A Token-2022 mint per credential class with NonTransferable and PermanentDelegate set to a governance PDA, MintCloseAuthority, and metadata pointing to the registry record (DR-02).
  - **No transfer path.** The program has **no** holder-and-issuer move instruction. Its only path for moving a credential between wallets is the governed re-bind below (review D-1).
  - **Revocation.** Revoke burns through the permanent delegate and sets a status bit, or updates the registry root. The reason, evidence reference and appeal deadline stay **off-chain** in the registry, so the chain does not carry a permanent public expulsion record (review D-6, DR-09, T14).
  - **Re-binding (DR-07), with safeguards (review D-5).** k-of-n human attestations plus a timelock: burn on the old wallet, mint on the new, tombstone the old member PDA. Re-binding is a controlled transfer, so it is also a theft and sale channel, and it needs:
    - a veto window in which the old key, if it still exists, can cancel;
    - attester-independence rules: no attester may be related to the new wallet or to the applicant, under the same conflict rules as DR-24 reviewers;
    - a public re-bind log entry with an appeal deadline;
    - a lifetime cap, for example 2 re-binds per member [proposal];
    - C_i history frozen until the appeal deadline passes.
  - **Freeze (DR-08).** Needs a freeze authority held by the governance PDA, which this codebase's pattern lacks (`freeze_authority: None`).
  - **What this codebase contributes.** The shapes of the revoke marker (`state/revocation.rs:12-24`) and the issuer-PDA delegate (`points_cpi.rs:24-85` at `119f5f5`).
- **DR-48 minimums the verifier enforces (amended after review S-3; proposal for the DAO to ratify; DR-48 itself gives no numbers).**
  - Upgrade authority, freeze authority, mint authority and the permanent delegate are each a Squads-class multisig, or a governance PDA, with threshold >= 3 and >= 5 members, and `time_lock >= 604800` s (7 days) for upgrades and authority changes.
  - The multisig's own `config_authority` is not a single key.
  - No staged buffer is accepted without a published upgrade notice.
  - If the upgrade authority and the permanent delegate are the same multisig, that multisig must carry the timelock. A shared multisig with `time_lock 0` fails.
  - The rewards program fails this check (threshold 3 of 5 but time_lock 0; section 8).
- **RPC trust (amended after review S-2).** A display verdict is only as honest as the RPC it came from. The verifier queries two independently operated endpoints, or the user's own node, and shows "unverified" when they disagree. The queries run in the bridge, not the extension, so page-adjacent code never sees which wallet and mint the user checks. Each query still tells the RPC operator what is being checked (T14). That is stated to the user.
- **Off-chain signatures.** Every member-signed record or challenge uses a domain-separated off-chain message envelope (fixed prefix, version, application domain, length). The verifier rejects a record whose bytes parse as a Solana transaction message (review S-transparency-dev-tessera-2, cross-applied).
- **Off-chain registry (R23).** A Merkle root of the member registry anchored periodically, as in `CreateMerkleDistribution`, with inclusion proofs per member. Claims are not needed. Proof of key control comes from a SIWS-style signed challenge (not provided here).
- **OS side (Alpha).** Extend the read-only helpers in capability-matrix row OSC-38 (`ext/src/lib/browser-page-actions.js:589-615` MAIN-world wallet probe; `app-command-handlers.js:687` `/dao` handler) [reported by Stage 1] with a verifier module. Over JSON-RPC (`getAccountInfo`) it checks:
  - the mint's extensions and expected delegate, labelling any PermanentDelegate-bearing mint "issuer-movable" unless its controlling program's pinned hash is known to have no move instruction other than governed re-binding;
  - the class flag, where the program has one (a `transferable=1` class is reported as transferable);
  - the program-data hash and upgrade authority against a pinned allow-list;
  - the DR-48 minimums above;
  - registry inclusion. It returns `{type, valid, revoked, checked_at, evidence}` for display only (I2). Node 24 calls it as a library (`@solana/kit`) inside the bridge, or as plain `fetch` JSON-RPC from the extension. No signing (E8 stays human-only).
- **Tests affected** [reported via OSC-38]: `test/browser-page-actions.test.mjs` and `test/app-command-handlers.test.mjs`. New tests would use recorded RPC fixtures (such as the saved responses in this inspection), so no browser-first test needs network access.
- **Versioning.** Pin the program ID together with a verified-build hash (DR-53). The verifier refuses a mint whose program-data hash or authority changes without a published notice.

## 8. Security and governance effects

**Upgrade authority (T12) [inspected RPC, 2026-10-21].** The vault `DXtF..` belongs to the Squads v4 multisig `4CxQs26DewQ1KaCHfyyjktkYjndNdUqCJvVdygtJFwcJ`, which owns program `SQDS4ep65T869zMMBKyuUq6aD6EgTu8psMjkvj52pCf`. I decoded it from a `VaultTransactionExecute` dated 2026-10-16 16:40:38 UTC (tx `3BiGcF8F...`). Its fields: `threshold 3`, `time_lock 0`, `config_authority 11111111111111111111111111111111` (autonomous), `transaction_index 11`, and 5 members, each with permission mask 7: `9DeYwyFk..`, `9vL7jtVc..`, `EWcevF86..`, `FBR1BPM6..`, `HL87yvhf..`. The DAO-skeptic reviewer settled the link independently (review D-7). Its own getAccountInfo on `4CxQs..` returned the same fields, and an off-line derivation of `[b"multisig", 4CxQs.., b"vault", 0]` under the Squads v4 program gives `DXtF..` (bump 254). `DXtF..` is therefore the index-0 vault of this multisig [reported by reviewer, consistent with my RPC read]. That 2026-10-16 transaction carries the Token ACL program and the verify program `verifycLy8mB96wd9wqq3WDXQwM4oU6r42Th37Db9fC` in its accounts, so the same vault governs at least one other Solana Foundation program. **Any 3 of these 5 keys can replace the code immediately.** A staged buffer already belongs to the vault (section 2). Every distribution that uses this program inherits that power over its escrowed tokens. Before the handover the program sat under single keys for about 3 hours (wB85.., then 9LfvVt..).

**Revocation power.** On main, each distribution's revocation power belongs to one stored `authority` key, checked by equality (`traits/distribution.rs:29-34`). That key cannot be rotated because it is a PDA seed. A lost or stolen authority key means losing the revoke and close powers of that distribution, or having them abused. The Stage 2 row's statement that "the effective revocation power is whoever controls the program authority and the config authority" applies only to the branch design. On the branch, RevokePoints burns the whole balance on one authority signature, with no reason, appeal or delay.

**Diff from the audited commit to HEAD, by area** (`git diff aa1cfd9 main`: 214 files, +14811/-19006; `program/` only: 61 files, +322/-2935) [inspected]:

| Area | Change | Effect on audited guarantees |
|---|---|---|
| Instructions | Same 12 discriminators. `4f5a8c6` (#43): claim paths add `verify_token_account_owner` (`direct/claim/accounts.rs:59`, `merkle/claim/accounts.rs:65`). Merkle close becomes close-in-place with a closed marker (`merkle/close_distribution/processor.rs:70`). Create rejects transfer-hook mints and Merkle underfunding | Security improvements, but unaudited. They fix defects that the audited baseline had (GHSA F1-F3) |
| Account layouts | PointsConfig, RewardPool and UserRewardAccount state and the `utils/points_cpi.rs` mint helper removed in `4f5a8c6` (all unreachable at `aa1cfd9`). Account discriminators renumbered: DirectDistributionClosed 8 -> 5, MerkleDistributionClosed new = 6 (`traits/account.rs` diff). `MerkleDistribution::is_closed` / `close_in_place` added | The ADV-01 fix relies on a discriminator check. Its values changed after review, so any indexer or client built from the audited IDL misreads closed markers. Whether any on-chain account was ever written with the old values depends on the unknown deployed commit [hypothesis] |
| Authority and constraint checks | `validate_authority` unchanged. `5006e14` (#86) replaces `verify_readonly(recipient)` / `verify_readonly(seeds)` with `verify_readonly_or_writable_alias(.., &[payer])` (`utils/account_utils.rs:62-71`) and adds `verify_distinct_accounts` (`:53-59`) in add_recipient, revoke and create. All `*Accounts` move to `&mut AccountView` in the dependency roll-up `618da5d` (#75; pinocchio API migration, alongside solana-program 3 -> 4 in `0f76679`) | One audited invariant (beneficiary account read-only) is relaxed after review. Every account-validation file was touched by a dependency-driven API migration. The audit's "no further issues in scope" cannot be carried to HEAD |
| Outside `program/` | Web app migrated to Vite, CI release via Squads, verify-PDA step, dependency bumps (RUSTSEC-2026-0185 quinn-proto, `859c9db`) | No on-chain effect. Supply-chain surface for the maintainers |

**Guarantees that may no longer hold:** (1) "all findings resolved" refers to `aa1cfd9`, which still carries the three GHSA MEDIUM defects; (2) the audited validation order and constraint set, changed by #86 and the pinocchio migration; (3) the closed-marker discriminator values; (4) any claim about the deployed binary, which matches neither `aa1cfd9` nor the commit its verify PDA names. **Answer to the counterexample rows** (s2-br-f "unaudited delta ... no-timelock multisig" and s2-br-h "77 commits and 214 files"):
- CONFIRMED: 77 commits (`git rev-list --count aa1cfd9..main`), 214 files, no tags or releases, devnet badge, empty Deployments table, 3-of-5 with time_lock 0.
- REFUTED in part: RevokePoints with a PointsConfig permanent delegate is neither on main, nor audited, nor deployed.
- EXTENDED: the audited baseline is itself known-defective (GHSA), and the deployed binary is unverified.

**Personal data (T14).** Every recipient wallet, claim and revocation is a permanent public record linking a wallet to a distribution's authority. A membership credential built this way publishes membership (DR-09 unmet).

## 9. Licensing

MIT (`LICENSE`, Copyright (c) 2026 Solana Foundation), the only licence file in the repo. The Rust and TS clients declare MIT. The program and workspace manifests have no `license` field, a metadata gap with no conflicting licence. Direct dependencies (pinocchio family, spl-token-2022 Apache-2.0, codama) are permissive [reported by their registries; not audited here]. There is no copyleft or source-available clause. Because the decision is "pattern", no code is copied. If snippets are copied from `119f5f5`, keep the MIT notice. Compatibility with the MIT Alpha is high. The Symbiotic Licence is status F (unknown) and cannot be assessed. **Item for legal review:** none specific to this repository. This is technical research, not legal advice.

## 10. Cost and effort

- **Reusing as is (not recommended):** small effort with a TS client, but it gives a non-credential and inherits a third-party no-timelock upgrade authority.
- **Pattern reimplementation (recommended):** a thin Resonant issuer program in Rust (pinocchio or Anchor). Assumptions: 6-9 instructions (init class, issue, revoke with record, freeze / unfreeze, propose / execute re-bind with timelock, close) plus a k-of-n governance PDA. Small: 3-4 engineer-weeks plus tests if Squads v4 is used for governance and the program only checks the PDA signer. Medium: 6-10 weeks with in-program attestation and timelock for re-binding. Large: more than 12 weeks if privacy (compressed or ZK membership) is required. Add an independent audit (external cost; schedule against DR-54) and a verified build.
- **Skills.** Rust/SBF and Solana account-model security, which the Resonant team does not show today (Stage 1 found no program source). This is the main language cost (master prompt 5.5).
- **Per-OS build matrix** [reported plus hypothesis]. Linux: native Solana platform tools and `solana-verify` (Docker). macOS: native, with Apple Silicon caveats documented in `68cba3c`/`6b55255` ("verify-local recipe and Apple Silicon build notes"). Windows: no native Solana platform tools, use WSL2 [hypothesis, standard Solana guidance]. A Windows-only maintainer cannot build or verify natively; that is a visible limitation, not a silent one. Runtime verification from the OS: Node 24 with `@solana/kit` on all three OSes, or plain JSON-RPC over HTTPS.
- **Lock-in:** low for the pattern (Token-2022 is a standard). High if distributions are created inside this shared program, because the authority and beneficiary are fixed in PDAs.

## 11. Acceptance test

Request for the user (it would execute third-party code: LiteSVM tests and Docker builds). Nothing is run until approved.

1. **Deployed-source identification (no fork).** In a disposable container, run `solana-verify build` at `758aa7a`, `5006e14`, `4f5a8c6` and `aa1cfd9`. Compare each hash with `96a2835c...` (deployed) and `4e7f4e77...` (staged buffer). Pass: one commit matches the deployed hash. Fail: none matches, and the dossier's "unverified" finding stands.
2. **Credential-pattern proof (on a scratch fork of the `119f5f5` Points branch, LiteSVM, offline).** Inputs: a governance keypair set, wallets A and B. Steps: InitPoints (revocable) -> IssuePoints(1) to A -> a direct TransferChecked from A to B must fail (T15 transfer) -> simulate key theft of A: the attacker cannot move the point -> RevokePoints(A) -> IssuePoints(1) to B. Expected output: A balance 0, B balance 1, two events. **Fault scenario T15 plus T12:** repeat with the authority key compromised and observe that a single signature revokes any member with no delay. That is the expected FAIL against DR-48 and DR-06, and it documents why a governance PDA plus timelock must wrap the pattern. A second fault: IssuePoints(1) twice to A must be rejected (DR-04). Expected: it is not rejected, which documents the per-wallet gap. A third fault (review D-1): InitPoints with `transferable=1`, then a TransferPoints from A to buyer C co-signed by A and the authority, simulating a sale. Expected: it succeeds, so the verdict on "non-transferable" is FAIL.
   For the Resonant Phase 2 design (review D-5, run against the future Resonant program, not this codebase): three colluding attesters re-bind member A to wallet D while A's key still exists. Pass only if A's veto inside the window cancels the re-bind, and the public log entry plus appeal deadline are created. A second case: A "loses" a key and asks for a re-bind to buyer E a third time. Pass only if the lifetime cap rejects it.
3. **CI hook (Resonant side, no third-party execution).** The verifier test runs against the saved RPC fixtures (`today_pd.json`, `today_multisig.json`). It asserts upgrade authority = `DXtF..`, threshold 3, time_lock 0, and marks the program "not acceptable as credential infrastructure" under DR-48. Pass: the verifier flags it.

## 12. Recommendation and confidence

- **Benefits:** a small, readable pinocchio codebase from the Solana Foundation; MIT; real tests (219 integration, 315 unit); an independent audit, a responsive disclosure process (the advisory was fixed in one day) and a disciplined Squads release pipeline. The Points branch is the clearest Token-2022 "NonTransferable plus PDA permanent delegate plus force-burn plus reissue" reference found.
- **Trade-offs and blockers:** wrong purpose (rewards escrow, not identity); credential code unaudited and undeployed; authority and beneficiary fixed in PDA seeds (no rotation, no re-binding); no freeze; permanent, unappealable revocation; public membership linkage; a third-party 3-of-5 with no timelock can upgrade at any time and has a buffer staged; deployed binary unverified; README and coverage docs wrong.
- **Evidence quality:** about 85 percent [inspected] (code at three commits, the PDF, the advisory, RPC). Competitor facts are [reported] from the Stage 2 ledger.

**Comparison on the same criteria** (competitors not inspected by me; their cells are [reported] from `ledger/codebases.jsonl`):

| Criterion | rewards (main / branch) | solana-attestation-service | mpl-core | Token-2022 + thin issuer program |
|---|---|---|---|---|
| Credential-shaped | No / fungible points | Yes: schema, attestation account, optional NonTransferable token, expiry [reported] | Asset per wallet, plugins [reported] | Yes, by design |
| Non-transferable | No / Token-2022 ext. (branch test) | Tokenised option uses NonTransferable [reported] | Permanent freeze delegate [reported] | Token-2022 NonTransferable (Anza-maintained) |
| Issuer revocation | Claim revocation, single key / force-burn | Close attestation by authorised signer [reported] | Permanent burn delegate; ledger counterexample on burn-delegate abuse [reported] | Burn via permanent delegate = governance PDA |
| Governed re-binding | None | Not built in; revoke plus re-issue [hypothesis] | Permanent transfer delegate possible [reported] | Must be written (timelocked k-of-n) |
| Audit | OtterSec 2026 on code without the credential; GHSA post-audit | None found [reported] | Mad Shield 2024 claimed [reported] | Token-2022: 16 audits reported; the thin program needs its own |
| Upgrade authority | 3-of-5, time_lock 0 (inspected) | 1-of-2 Squads, no timelock; deployed binary predates 2.0.0 source [reported] | Not checked | Resonant's own k-of-n plus timelock |
| Licence | MIT | MIT | Metaplex NFT OSL 1.0, clause 4(c) competitive-product restriction (G3: reference only) [reported] | Apache-2.0 plus Resonant's choice |
| Fit for DR-01/02/04/06/07 | 0 of 5 (even DR-01 fails on the branch: TransferPoints, section 5) | 3 of 5 [hypothesis] | 2-3 of 5, licence-blocked | Design target 5 of 5. **Status D: no code, maturity and audit unknown** |

Amended after review D-4. The fourth column compares a design with inspected code, so it cannot "win" on evidence. It is status D, and its maturity, audit and authority are unknown until it exists. **R22 recommendation, reframed:**
- **Phase 1:** an off-chain signed membership record in the registry or R30 log, with no token. Revocation, appeal and governed re-binding are handled there (dao-requirements section 5 item 1; T18).
- **Phase 2:** a Token-2022 class built on the pattern, only after DR-54 is met (audit, custody policy, devnet test) and with the section 7 safeguards.

SAS is the best ready-made attestation layer, but it is unaudited, its deployment lags its source, and it sits under a 1-of-2 authority. This codebase is a **teaching reference** for the burn-delegate pattern (including its transfer pitfall) and for the Merkle-plus-revocation-marker registry anchor. No Stage 3 candidate supplies an adoptable R22 credential.

**First PR (PR-sized; Resonant Alpha repository, read-only side):** add `host/dao-credential-verifier.mjs`, a bridge-side module (moved from `ext/src/lib/` after review S-2), with `test/dao-credential-verifier.test.mjs`. The extension's `/dao` read path only displays its result. Given RPC JSON for a mint and its program, the verifier checks the Token-2022 extensions (NonTransferable present; PermanentDelegate equal to the configured governance address), the program-data hash and upgrade authority against an allow-list, and a Squads multisig `threshold`/`time_lock` against the numeric DR-48 minimums in section 7 (threshold >= 3 of >= 5, time_lock >= 604800 s, non-single-key config authority). It labels any PermanentDelegate-bearing mint "issuer-movable" unless its program hash is allow-listed as having no move instruction. It compares two RPC endpoints and reports "unverified" on disagreement. The queries run in the bridge. It returns display-only status (I2). Wire it into the `/dao` read path (`app-command-handlers.js:687`) behind the existing optional DAO helper. The tests use recorded fixtures from this inspection (rewards program: expected verdict "fails DR-48: time_lock 0"). No signing, no network in tests.

**Confidence: high** that this codebase should not be the R22 credential (code, chain and audit agree). **Medium** on the pattern-effort estimates.

## 13. Scores (for scoring/scores.csv)

Profile: **dao_contract**.

| Criterion | Score | Reason (anchor) |
|---|---|---|
| requirement_fit | 1 | Main and the deployed program offer only revocable reward claims. The credential pattern exists only on an unmerged branch, with no re-binding, uniqueness, freeze or privacy |
| demonstrated_maturity | 2 | Tests, CI, mainnet deployment and an audit, but pre-release (0.0.1, no tags), bus factor 1, about 8.5 months old, no named production users. Between anchor 1 (pre-release) and 3 |
| isolation_and_security | 1 -> 0 | Amended after review S-1/D-3/W-3, under the anchor clarified on 2026-10-21 (state.md D3.7). Clause (a): the artifact that would be used is the deployed binary, and its source is unverified, so the GHSA-8hgp-gwvv-wrjf fixes cannot be shown to be in it. The fixes exist only in later, unaudited source. Contrary evidence also includes the confirmed counterexample rows and the branch TransferPoints move against non-transferability (D-1) |
| licence_suitability | 5 | MIT, file-level |
| integration_effort | 1 | A TS client exists for reading, but serving R22 needs a new or forked Rust/SBF program and toolchain the team does not have (anchor 1). Reuse as is would need a fork (anchor 0) |
| maintenance_sustainability | 3 | Solana Foundation org, dependency and security hygiene active, quick disclosure fix; but one human author, feature work stopped after May, docs drift unaddressed |
| sovereignty_and_portability | 3 | No account or service: any RPC or own node works; data user-readable but permanently public; shared program under a third-party authority |
| interoperability | 4 | Token-2022 standard, Codama IDL, Anchor-compatible events, `@solana/kit` client |
| operational_complexity | 3 | One on-chain program plus RPC; for Resonant's own variant, add a multisig and release pipeline (one "sidecar"-class dependency) |

Weighted (dao_contract): 0.16x1 + 0.16x2 + 0.18x0 + 0.08x5 + 0.06x1 + 0.14x3 + 0.08x3 + 0.06x4 + 0.08x3 = **2.08 / 5** (was 2.26 before the amendment). It fails no hard gate, so it is scored for information. The decision is "learn the pattern".

## 14. Open questions for the adversarial reviewers

1. **Deployed source unknown.** The deployed ELF hash `96a2835c...` matches neither the OtterSec-verify PDA's commit `758aa7a` (build hash `449b6b27...`) nor anything I could check statically. I did not build any commit (that requires execution; acceptance test step 1). The staged buffer `4e7f4e77...` is also unattributed.
2. **Audit re-review.** OtterSec's PDF says "Resolved in PR #NN" for each finding but does not state that OtterSec re-reviewed PRs #32-#37. AUDIT_STATUS says they were re-reviewed [reported]. Was GHSA-8hgp-gwvv-wrjf's fix (#43) ever reviewed by OtterSec? Nothing in the repository says so.
3. **Score contest (resolved).** Two reviewers argued isolation_and_security = 0, and the lead clarified the anchor (D3.7). The score is now 0 (section 13).
4. **Old discriminator values on-chain.** If any account was written with the audited discriminator numbering (DirectDistributionClosed = 8), the renumbering could make a closed marker unrecognised after an upgrade. It depends on the unknown deployed commit [hypothesis]. A `getProgramAccounts` scan of the program would settle it. I did not run it, to keep the RPC footprint at what the launch message allowed plus the multisig and buffer lookups.
5. **Multisig identity (settled).** I decoded the multisig with my own parser (`rpc_ms.py`). The DAO-skeptic reviewer's independent getAccountInfo and off-line derivation show `DXtF..` is the index-0 vault of `4CxQs..` (bump 254), with threshold 3, time_lock 0 and 5 members (review D-7). Ledger s2-br-f agrees.
6. **PDF text.** It was extracted with a stdlib-only parser (no poppler or pypdf on this machine), and some words lost their spacing. The severity table and the finding list are unambiguous, but code identifiers inside the PDF were partly lost.
7. **Production users.** I did not establish any user beyond the maintainers. A `getProgramAccounts` count of distributions would show usage.
8. **Competitor cells** in section 12 are [reported] from the Stage 2 ledger and need their own dossiers.
9. **Evidence files** (scratchpad `C:\Users\<local-user>\AppData\Local\Temp\claude\c--My-Agents-75-ResonantDAO-and-OS\0f2652ff-3bb9-401c-b3a1-df5f9a82067c\scratchpad\s3-insp-rewards\`): `prog.json`, `pd.json`, `sigs_pd.json`, `tx_*.json` (2026-10-20 13:07 UTC, copied from the predecessor); `today_prog.json`, `today_pd.json`, `today_sigs_pd.json`, `today_sigs_vault.json`, `today_vault_tx*.json`, `today_multisig.json`, `today_buffer*.json`, `today_osec_status_all.json`, `advisories.json`, `audit.txt` (2026-10-21).
10. **Failed approaches (for the next session):** the Read tool cannot render this PDF (no pdftoppm); pypdf and PyMuPDF are not installed, so a stdlib zlib/ToUnicode parser was used. The predecessor's 2026-10-20 run stopped before writing the dossier: an account usage limit, then a permission prompt declined while the user was away.

### Review responses (2026-10-21)

| Id | Verdict | Change made (section) or evidence for dispute |
|---|---|---|
| S-solana-foundation-rewards-1 | accepted | 13: isolation_and_security 1 -> 0 under the clarified anchor clause (a) (deployed binary unverified; GHSA fixes only in unaudited later source). Total 2.26 -> 2.08. 14 Q3 resolved |
| S-solana-foundation-rewards-2 | accepted | 7: RPC trust assumption stated, two-endpoint comparison with an "unverified" state, queries in the bridge. 12: first-PR module moved to `host/dao-credential-verifier.mjs` |
| S-solana-foundation-rewards-3 | accepted | 7: numeric DR-48 minimums (threshold >= 3 of >= 5, time_lock >= 604800 s, non-single-key config authority, shared multisig must carry the timelock), marked as a proposal for the DAO to ratify. 12: first PR enforces them |
| S-transparency-dev-tessera-2 (cross-applied) | accepted | 7: domain-separated off-chain message envelope for member-signed records; the verifier rejects bytes that parse as a transaction message |
| X-10 | accepted | Same change as S-1 (13) |
| W-3 | accepted | 13: 1 -> 0, total 2.08 as W-3 computed |
| D-solana-foundation-rewards-1 | accepted | 5: the non-transferable row now reads "contradicted on the branch". Verified at `119f5f5`: `transfer_points/processor.rs:28` (validate_transferable), `:55-73` (burn plus mint), `accounts.rs:39-41`, `init_points/data.rs:20`, `test_transfer_points.rs:67, 255, 318`. 7, 12: verifier labels PermanentDelegate mints "issuer-movable" and reads the class flag. 11: sale fault added. 12: DR fit 1 -> 0 of 5 |
| D-solana-foundation-rewards-2 | accepted | Headline and 3 reworded. Verified: `utils/mod.rs:9, 23`, `traits/instruction.rs:36-43, 80-81`, `entrypoint.rs:59` at `d795849`; `git diff --quiet d795849 119f5f5 -- points_cpi.rs points_config.rs` exit 0. The OtterSec text names no Points file (grep of the extracted text: one hit, the scope sentence). 8: helper removal noted |
| D-solana-foundation-rewards-3 | accepted | As S-1 |
| D-solana-foundation-rewards-4 | accepted | 12: the thin-issuer column is status D with maturity and audit unknown. R22 reframed: Phase 1 off-chain signed record with no token, Phase 2 Token-2022 after DR-54. 6, 7 updated |
| D-solana-foundation-rewards-5 | accepted | 7: re-bind safeguards (old-key veto window, attester conflict rules, public log plus appeal deadline, lifetime cap, frozen C_i). 11: collusion and repeated "lost key" faults for the Resonant design |
| D-solana-foundation-rewards-6 | accepted | 7: revocation reason, evidence and appeal kept off-chain; on-chain only a status bit or registry root |
| D-solana-foundation-rewards-7 | accepted (confirmation) | 8 and 14 Q5: vault PDA derivation recorded as settled |
| C-3 (DAO skeptic) | accepted | 5: the property row names the full condition (program instruction set, not the extension) and 11 adds the breaking fault |
| C-4 (DAO skeptic) | accepted | 5: DR-06 failure stated explicitly (no reason, evidence, appeal or reversal) |
| C-2, TM-1, TM-2, TM-9 (DAO skeptic); TM-9 (security) | noted (lead's) | Threat-model and weights items. They change no section 13 score here. D-5/D-1 content they cite is already addressed in 5 and 7 |
| Archaeologist (13 citation checks, counts) | none | All PASS; no objection raised, nothing to fix |
