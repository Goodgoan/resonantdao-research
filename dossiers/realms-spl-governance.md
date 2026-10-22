# Dossier: Realms / spl-governance (mythic-project-spl-governance-v3-1-3)

STATUS: COMPLETE (amended after review, 2026-10-21)

Inspector: s3-insp-realms, Claude Opus 5.5. Date: 2026-10-21. Profile: dao_contract. Requirement: R24 (P1); threat T12 (high).

Clones (read-only, `--depth 200`, nothing built or run):
- `research/_untrusted/mythic-spl-governance/` = Mythic-Project/spl-governance-v3.1.3 at `00fa982b1555abc80f7ce1a22dbd14c4d3d0fe8d` (branch `main`, 2026-03-10). Scoring codebase_id `mythic-project-spl-governance-v3-1-3`. Unless a citation names another tree, `path:line` refers to this clone.
- `research/_untrusted/mythic-governance-program-library/` = Mythic-Project/governance-program-library at `1abc9987c2f24c24e241eae9a3b5d99fddf38a06` (branch `master`, 2026-03-23). Cited as `gpl:path:line`.
- `research/_untrusted/squads-v4/` = Squads-Protocol/v4 at `af94153ff77a28b6effe46b9c94baaa93742b48c` (branch `main`, 2026-08-20). The competitor. Cited as `sq:path:line`.

Execution: none. Read-only mainnet JSON-RPC (api.mainnet-beta.solana.com; getAccountInfo, getSignaturesForAddress, getTransaction, getProgramAccounts, getBlockTime) ran on 2026-10-21 10:29-10:40 UTC. My scripts ran with `python -I` from my scratchpad: `rpc.py` (method allow-list, no signing code), `gov.py` (Borsh decoder and PDA derivation), `tx.py`, `progauth.py`, `hist.py`, and after review `versions.py` (2026-10-21, about 11:00-11:30 UTC). The request/response pairs (102 at first publication, more after review) are saved verbatim in `<scratchpad>\s3-insp-realms\rpc-log.jsonl`. Read-only GETs also went to verify.osec.io, the npm registry, raw.githubusercontent.com (the OtterSec PDF) and the GitHub API. Nothing was signed or sent. The clones ship no `.claude`, `AGENTS.md` or `CLAUDE.md`.

Evidence labels: [inspected] (code, test, PDF or RPC response read), [executed] (none), [reported] (README, docs, ledger rows, third-party press), [hypothesis].

**Headline findings.**
1. **The source tree does not match the deployed program.** The shared mainnet program `GovER5Lthms3bLBqWub97yVrMmEogzX7xNjdXpPPCVZw` logs `VERSION:"3.1.7"` (tx `5qkibmpj..`, 2026-10-21 08:50 UTC). The newest public source is 3.1.3 (`governance/program/Cargo.toml:3`, commit `430daad`). The program prints that string from `env!("CARGO_PKG_VERSION")` (`governance/program/src/processor/mod.rs:70`). OtterSec's verifier holds two verification records. `GET https://verify.osec.io/status-all/GovER5..` returns:
   - a record signed by the upgrade authority `HF7gCq..` for this repository at `00fa982`, with executable hash `b1d75931..`, last verified 2026-04-30T17:42:43;
   - a record signed by `Et8dPTTh..` (the buffer-spill key of the 2026-10-05 upgrade) for `spl-governance-v3.1.2` at `46bf640`, with executable hash `642d1371..`, last verified 2026-04-30T17:38:49.

   Both records show the on-chain hash `0ad7fdd4..`, and both say `is_verified: false`. The plain `/status` endpoint returns `executable_hash: ""`. I found no public source for 3.1.4 to 3.1.7. The canonical source of the **deployed** artifact is therefore unknown, and this tree is its closest public ancestor [inspected].

   Version timeline, from the `VERSION` log of the last successful GovER5 transaction before each council upgrade (`versions.py`, amended after review D-1):

   | Period | Version |
   |---|---|
   | until the 2026-03-10 upgrade | 3.1.2 |
   | 2026-03-10 to 2026-04-30 | 3.1.3 |
   | 2026-04-30 to 2026-07-07 | 3.1.4 |
   | after 2026-07-08 | 3.1.6 |
   | from 2026-10-05 | 3.1.7 |

   So the public tree ran on mainnet for seven weeks only. The drift began with the 2026-04-30 upgrade at 17:37:28 UTC, five minutes before the `00fa982` verification record was refreshed. The 3.1.5 build of 2026-07-07 is inferred; no transaction was read for it.
2. **The upgrade authority is a 5-of-7 council with zero hold-up.** The authority `HF7gCq7BMB6cFfhharTGGm31sqDdpmvLTgF4WEoW8y9n` is the DAO wallet (native treasury) of a governance in the realm "Realms Security council 8" (bump 250, derived off-line). That governance is hosted on a second program, `GoVERLM..` (v3.1.1), which the same wallet also upgrades. The council has 7 single-key members. Approval needs 60 percent yes, which rounds up to 5 of 7. `min_transaction_hold_up_time` is 0, council vote tipping is `Early`, and both community voting and **community veto are `Disabled`**. The community mint `EtHceUV7..` has supply 0, mint authority `HF7gCq..` and no freeze authority. So no veto route of any kind exists over this authority (amended after review D-2). The 2026-10-05 upgrade ran 2 h 34 min from proposal creation to execution. The same 5-of-7 also holds the upgrade authority of the deployed plugins: nft-voter, token-voter, token-haver, core-voter and VSR. Stage 2's "DAO-governed PDA" is confirmed, but that DAO is a small council with no timelock [inspected].
3. **The hold-up is a delay, not a veto window.** Veto is possible only while a proposal is in `Voting` (`state/proposal.rs:324-351`). Cancel is possible only before `Succeeded` (`:794-818`), and only by the proposer or its delegate (`processor/process_cancel_proposal.rs:45-46`). Early or strict tipping ends voting as soon as the threshold is met (`state/proposal.rs:661-689, 742-757`). The tipping that applies is the **voting** population's (`process_cast_vote.rs:164-166`). A veto by the other population can therefore be pre-empted unless the *proposing* population's tipping is `Disabled`. The upstream test `test_cast_yes_and_veto_votes_with_yes_as_winning_vote` (`tests/use_veto_vote.rs:723-789`) already shows a partial veto stranded when one yes vote tips the proposal to `Succeeded` [inspected].
4. **The electorate is itself an authority** (added after review D-5, D-6, D-13). These authorities can each undo a timelock or veto elsewhere:
   - the realm authority, which can be any key under `SetUnchecked`, swaps weight plugins and quorum denominators with one signature;
   - the voting-mint authority mints voters, or revokes them mid-vote;
   - built-in delegation lets any key cast a member's binding vote.

   Each is a key class in section 12.

## 1. Identification

| Item | Value |
|---|---|
| Name | SPL Governance ("Realms" is the UI and brand), Rust native Solana program, v3 account model |
| Canonical source (history) | solana-labs/solana-program-library `governance/`, archived 2025-03-11. That repo's README says it "no longer contains the SPL program implementations" [reported, ledger row s2-br-h] |
| Continued source | Mythic-Project/spl-governance-v3.1.3 (created 2025-09-27; description "v3.1.3 of the spl-governance program"). It is a copy of the SPL monorepo with the governance changes from Mythic-Project/solana-program-library branches `governance-v3.1.2` and `gov-no-requires-signature`. That fork's `master` has been stale since 2025-01-08. Sibling Mythic-Project/spl-governance-v3.1.1 is the source of the 3.1.1 build [inspected via GitHub API] |
| Deployed artifact | `GovER5..` (shared "default" instance, `governance/README.md:41-47`; `Anchor.toml:23`). ProgramData `BZYjZ2Zb..`, 1,319,901 bytes, last deployed at slot 453601996 (2026-10-05 14:09:33 UTC). Runs **3.1.7**, with no public source (headline 1). Test instance `GTesTBiE..`: last deployed 2022-12-13, single ordinary key `G1DTKKGX..` |
| Maintainers | Mythic-Project organisation (the Realms team). Governance-path commits since 2025-01-01: crypt0miester 29, Utkarsh/Utkarsh Shukla 3 (`git log --since=2025-01-01 -- governance/`) [inspected] |
| Packages | Crate `spl-governance-v3` 3.1.3, with Cargo `repository` still pointing to `spl-governance-v3.1.2` (`governance/program/Cargo.toml:2-7`). npm `@realms-today/spl-governance` 0.3.33 (2025-10-03, maintainers `0xshuk`, `mes_`; registry licence MIT; repository Mythic-Project/oyster) [inspected registry] |
| Plugins | Mythic-Project/governance-program-library: bonk-plugin, core-attribute-voter, core-voter, nft-voter, quadratic, realm-voter, token-haver, token-voter (`gpl:programs/`). VSR lives in Mythic-Project/voter-stake-registry (GPL-3.0, verified build at `c5f91c5`, per verify.osec.io) |
| Licence | Apache-2.0: the root `LICENSE` is the full Apache 2.0 text, and the crate says `license = "Apache-2.0"` (`governance/program/Cargo.toml:7`). This resolves the ledger's "licence unknown". Plugins: `gpl:LICENSE` is only a 12-line Apache notice ("Copyright 2020 Solana Foundation"; GitHub NOASSERTION), and every plugin crate declares Apache-2.0. npm client: registry MIT versus repository Apache-2.0 (section 9) |
| Audits | OtterSec "SPL Governance v3", an intermediate report delivered 2022-09-12 at an unnamed commit. Accretion A25REA2 (2025-09-10), on core-voter PR #2 at `1a81ca8` only. Section 3 |
| Docs | English. docs.realms.today source in Mythic-Project/realms-doc. Its `pages/spl-governance.mdx` still describes v3.1.0 (2022) [inspected] |

## 2. What works

Account model [inspected, `governance/program/src/state/`]:
- **Realm** (`realm.rs:132-162`): community mint, optional council mint, `authority`, `name` (PDA seed).
- **RealmConfigAccount** (`realm_config.rs:91-106`): per-population `GoverningTokenConfig` = {`voter_weight_addin`, `max_voter_weight_addin`, `token_type` Liquid/Membership/Dormant} (`:74-86`).
- **GovernanceV2** (`governance.rs:86-131`): `GovernanceConfig` (`:29-75`) holds `min_transaction_hold_up_time`, `voting_base_time`, `voting_cool_off_time`, community and council vote thresholds, both veto thresholds, tipping per population and `deposit_exempt_proposal_count`. Each governance has a system-owned native treasury PDA (`native_treasury.rs`).
- **ProposalV2** (`proposal.rs`) with **ProposalTransactionV2**, each carrying its own `hold_up_time`.
- **VoteRecordV2** (`vote_record.rs:96-`).
- **TokenOwnerRecordV2** (`token_owner_record.rs:30-90`): deposit, `unrelinquished_votes_count`, `outstanding_proposal_count`, delegate.

Mechanics with their code and tests [inspected; tests read, not run]:

| Mechanism | Code | Tests |
|---|---|---|
| Hold-up between vote end and execution | `proposal.rs:841-881` (legacy tx: per-transaction `hold_up_time`); `:886-918` (versioned tx: the governance minimum **read at execution time**, so a later config-lowering proposal shortens the remaining hold-up of versioned proposals that have already `Succeeded`; amended after review D-4); insert rejects `hold_up_time < min_transaction_hold_up_time` (`process_insert_transaction.rs:57`) | Active: `test_execute_transaction_with_create_proposal_and_execute_in_single_slot_error` (`tests/process_execute_transaction.rs:703`), `test_insert_transaction_with_invalid_hold_up_time_error` (`tests/process_insert_transaction.rs:197`). **Ignored**: `test_execute_proposal_transaction_with_invalid_state_errors` (`:357-359`, holds the main `CannotExecuteTransactionWithinHoldUpTime` assertion at `:485`) and `test_execute_upgrade_program_transaction` (`:231-233`, ignored upstream since `8e9dc13`, 2023-12-15, "until program-test manages fork graphs correctly") |
| Voting window, cool-off (only Deny/Veto in cool-off) | `proposal.rs:324-351, 389-405` | `test_assert_cannot_vote_approve_within_voting_cool_off_time` and siblings (`proposal.rs:2517-2600`, unit) |
| Tipping (Strict / Early / Disabled) | `proposal.rs:661-775` | in `tests/process_cast_vote.rs` |
| Veto by the other population | `process_cast_vote.rs` (Veto arm), `proposal.rs:777-792`; threshold per population (`governance.rs:347-372`) | `tests/use_veto_vote.rs`: 16 tests (`grep -c "async fn test_"`; amended after review D-16), incl. `test_cast_yes_and_veto_votes_with_yes_as_winning_vote` (`:723`), `test_cast_council_veto_vote` (`:20`), `test_cast_community_veto_vote` (`:100`), `test_cast_veto_vote_without_tipping` (`:320`), `test_cast_council_veto_vote_within_cool_off_time` (`:964`) |
| Membership tokens, revoke | `process_revoke_governing_tokens.rs:25-75`: the owner may revoke itself; otherwise the **mint authority alone** may burn any deposited `Membership` tokens (`assert_spl_token_mint_authority_is_signer`). No reason, evidence or appeal deadline is recorded. Votes already cast stay standing (OtterSec SUG-01, still open). The burn lowers supply, and supply is the default max voter weight, so the absolute threshold of every proposal still in `Voting` falls and a revocation can tip a live vote [inspected]. **Revoke exists; governed revocation (DR-06) does not** (amended after review D-6) | `tests/process_revoke_governing_tokens.rs` |
| Delegation (cannot be disabled by config) | `process_set_governance_delegate.rs:26-28`: the owner **or its current delegate** sets any key as delegate, so a delegate can re-delegate. Votes, proposals and cancels accept owner or delegate (`state/token_owner_record.rs:124-141`). `VoteRecordV2` stores `governing_token_owner`, not the signer (`state/vote_record.rs:96-112`) [inspected; added after review D-13] | `tests/process_set_governance_delegate.rs` |
| Council removal (one way) | `process_set_realm_config.rs:52-75` | `test_set_realm_config_with_remove_council` (`:124`), `test_set_realm_config_with_council_restore_error` (`:177`) |
| Realm authority transfer / none | `process_set_realm_authority.rs:26-52`: `SetChecked` (the new authority must be a governance of the realm), `SetUnchecked` (**any key**) or `Remove`. With one signature, at any time, the realm authority can replace either population's voter-weight and max-voter-weight add-ins, change the max-voter-weight source and change the governance-creation minimum (`process_set_realm_config.rs:26-140`). The code's own comment, `:43-47`, says "changing voter-weight or max-voter-weight addin could accidentally make proposals to succeed which would otherwise be defeated" [inspected; added after review D-5] | `test_set_realm_authority_to_none` (`tests/process_set_realm_authority.rs:72`), `test_set_realm_authority_unchecked` (`:93`) |
| Voter-weight and max-voter-weight add-ins | `addins/voter_weight.rs:15-77` (expiry, action, action target; record owner = configured add-in; realm, mint and owner match) | `setup_realm_with_voter_weight_addin.rs`, `use_realm_with_voter_weight_addin.rs`, `use_realm_with_max_voter_weight_addin.rs`, `use_realm_with_all_addins.rs` |
| Execute arbitrary instructions signed by governance or treasury PDA | `process_execute_transaction.rs` | `test_execute_mint_transaction` (`:20`), `test_execute_transfer_transaction` (`:126`) |

**CI** [inspected via GitHub API]: the workflow "Governance Pull Request" (`.github/workflows/pull-request-governance.yml`) runs `./ci/cargo-test-sbf.sh governance`. Its `push` trigger names branch `master`, but the default branch is `main` (`:10-11`). Every one of its 31 recorded runs (2025-09-27 to 2026-07-21) failed. That includes run 22866738472 on PR `valid-mint-fix`, the last code change merged to `main`, which failed after 1 min 36 s with zero steps reported. No green CI run demonstrates the test suite at any commit of this repository.

**Deployment evidence** [inspected RPC]: GovER5 is in real but moderate use. The reviewer's `getSignaturesForAddress` (limit 1000) spans 2026-10-05 15:49 to 2026-10-21 08:50 UTC. That is about 60 transactions a day (daily range 7-145), 23 of them failed [reported by reviewer D-15, from `s3-rev-dao-r24\rpc-log.jsonl`]. How many realms run on the shared instance versus their own was not counted. One example is a VSR deposit plus `CreateTokenOwnerRecord` on 2026-10-21 08:50 UTC. Treasury `HF7gCq..` shows council proposals that upgraded GovER5 on 2025-10-01, 2026-03-10, 2026-04-30, 2026-07-07, 2026-07-08 and 2026-10-05. It upgraded token-voter, nft-voter and `EoKpGErC..` on 2026-09-01, the chat program `gCHAtYK..` on 2026-07-26 and `SRLMkVQ..` on 2025-11-18. It revoked one council membership (`RevokeGoverningTokens { amount: 1 }`, 2026-05-29). The set is six GovER5 upgrades in twelve months, four of them after the last public source commit.

## 3. Maturity

- **Footprint:** an on-chain program, so there is no local process. Clients read it over any Solana RPC.
- **Maintenance:** the governance path had 2 commits in the last 12 months (`532ca96` 2026-02-04 "add package config", `430daad` 2026-03-09 "fix valid_mint_length"). It had 32 commits since 2025-01, 29 of them by one developer. Bus factor is about 1-2. Plugins had 8 commits in 12 months (crypt0miester, Cryptomiester, Utkarsh). Version upgrades on chain (3.1.4 to 3.1.7) run ahead of public source. CHANGELOG stops at 3.1.2 (`governance/CHANGELOG.md:3-8`). There are no tags or GitHub releases. Repository hygiene: commit `532ca96` added a stray gitlink, `.5724fadf-2e95-4571-8738-1612b6816970/spl-governance-v3.1.2` (mode 160000 to `46bf640`), with no `.gitmodules` [inspected].
- **Issue response:** not sampled. The repository lists 12 open items, all Dependabot PRs. `security-advisories` returns 0 for both Mythic repositories [inspected API].
- **Security process:** `SECURITY.md` is still the Solana Labs text and points at the **archived** solana-labs repository's advisory form plus security@solana.com (`SECURITY.md:9-25`). The fork has no disclosure channel of its own [inspected].
- **Audits** [inspected PDFs]:

| Report | Firm, date | Scope | Findings | Fix status | Relation to the live artifact |
|---|---|---|---|---|---|
| SPL Governance v3 | OtterSec (R. Chen, K. Chow), intermediate report 2022-09-12 ("ongoing assessment") | spl-governance v3, commit not named | OS-GOV-ADV-00 medium: voter weight manipulation by burning after vote. OS-GOV-SUG-00: mint/burn of membership tokens shifts max_voter_weight. OS-GOV-SUG-01: revoke membership should check and revoke votes | ADV-00 resolved (#3210). The fix is present: `CannotRelinquishInFinalizingState` (`process_relinquish_vote.rs:124`). SUG-00: queuing "intended" (not built). SUG-01: **still open**, since `process_revoke_governing_tokens.rs` has no `unrelinquished_votes_count` check (grep) | Predates 3.1.0 (2022-12-13), every later feature and the 3.1.4-3.1.7 binaries. No final report found |
| A25REA2 | Accretion (R. Reith), 2025-09-10 | core-voter plugin, PR #2 at `1a81ca8` | ACC-C1 critical: missing owner check on Core assets (fake NFT votes). ACC-H1 high: votes on not-yet-existing proposals. ACC-M1 medium: third party zeroes voter weight | All fixed (`35d06d8`, `97997fa`, `1a81ca8`). The report says "Status: unverified" for deployment | core-voter `cNFTHB..` was deployed 2025-09-15 and is unverified on verify.osec.io. core-attribute-voter is **not** covered. Its commit `5eb76c8` "fix audit issues" (2026-03-20) cites no published report |

- **Documented failures:** Solend SLND1/SLND2, 2022-06 (ledger counterexample; press reports only) [reported]. Section 8 answers it.

## 4. Architecture

- **Control flow:** client tx -> `processor/mod.rs` dispatch (it logs VERSION) -> instruction processor -> state checks -> for `ExecuteTransaction`, `invoke_signed` with the governance or native-treasury seeds -> target program, for example the BPF loader `Upgrade`.
- **Authority handover:** a governance becomes the authority of another program or mint outside spl-governance. The current authority calls the BPF loader `SetAuthority` (or SPL Token `SetAuthority`) and names the DAO wallet. The docs recommend the DAO wallet over the governance account (`governance/NOTES.md:11-23`; realms-doc `pages/spl-governance.mdx`). The legacy `ProgramGovernance`, `MintGovernance` and `TokenGovernance` types, which did the transfer in-program, are deprecated (`NOTES.md:5-9`). The program-governance creation path and its tests are gone in v3.1.2+ (`b12dec3`).
- **What is left to a small group:** whoever passes a proposal in that governance can execute any instruction with the DAO wallet's signature. That includes upgrading the governed program, minting, changing the governance's own config (`process_set_governance_config.rs` requires the governance PDA signer) and changing the realm (the realm authority is the governance). In a council-only realm, that group is the council's threshold.
- **Plugin trust:** the core program accepts a `VoterWeightRecord` if its owner is the configured add-in program and the realm, mint, owner, expiry and action match (`addins/voter_weight.rs:15-77`). It cannot check how the weight was computed. Whoever can upgrade the add-in program can mint arbitrary voting weight. Plugin chaining exists (`gpl:programs/quadratic/src/lib.rs:18-32`, `use_previous_voter_weight_plugin`), and each link adds a trust root.
- **Dependencies:** solana-program, borsh, spl-token, spl-token-2022 (`governance/program/Cargo.toml`). The client brings `@solana/web3.js ^1.22`, borsh 0.3, axios and bs58 (npm metadata).

## 5. Direct relevance

R24 (P1) against DR-43, DR-46, DR-47, DR-48, DR-49 and I-11 [inspected unless marked]:

| Row | What spl-governance gives | Gap |
|---|---|---|
| DR-43 weight = base + min(cap, f(human-attributed C_i)), per-member cap | core-attribute-voter reads `u64` from a Metaplex Core attribute key, verifies the attribute plugin authority and caps **per NFT**: `weight.min(collection_config.max_weight)` (`gpl:programs/core-attribute-voter/src/state/registrar.rs:128-191`). Test `test_cast_asset_vote_with_weight_capped_to_max` (`gpl:.../tests/cast_nft_vote.rs:1124`). An off-chain ledger could publish each member's computed weight as an attribute | The cap is per NFT, not per member: a holder of two NFTs sums them (README example "175"). A member cap therefore needs one non-transferable asset per member, which brings back R22. No test exercises `AttributeAuthorityMismatch` (grep: no test file). The program is **not deployed** on mainnet (`HmdGe7..` absent). It is unaudited. It depends on mpl-core (licence: section 9). `total_weight` (quorum denominator) is set by hand by the realm authority, and the README itself shows how a wrong value lets a minority pass (example 2). Replayable explanation records (DR-43 "published replay") are out of scope |
| DR-46 custodian transition with triggers | A primitive only. Removing the council mint is one-way (`process_set_realm_config.rs:52-75`, test `:177`), but removing the custodians' **capture** is not (amended after review D-7). After the council is removed, power stays with whoever holds three things. The realm authority can install a community voter-weight add-in that weights only custodian-issued assets (token-haver, nft-voter, or core-attribute-voter with a custodian `expected_attribute_authority`). The community-mint authority can mint or revoke. The plugin upgrade key can rewrite weights. Removal also strands council deposits (`:72-73`: "makes it impossible to withdraw council tokens") | No trigger, timer or sunset exists on chain. Every phase change is a proposal that the current holders must pass. **Transition completion must be a checkable state, not one instruction.** The realm authority, both voting-mint authorities, plugin configuration and plugin upgrade authority must all be the community governance, or none, and the section 7 verifier must report no custodian-held key over any of them. Measurable triggers stay off-chain, in DR-46 text |
| DR-47 emergency powers bounded, time-limited, reviewed | Nothing is specific to emergencies. Any passed proposal executes. Time limits and lapse do not exist | Needs a separate design: a dedicated, audited pause/freeze program whose actions lapse, or an off-chain rule in Phase 1 (section 12 checklist). Squads spending limits are **not** an emergency mechanism. A listed member transfers up to an amount per period with no vote and no time lock (`sq:.../instructions/spending_limit_use.rs:90-100, 148-168`). They do not lapse, and they cannot pause, freeze or delist (amended after review D-11a) |
| DR-45 delegation human-to-human, revocable, never via an AI account | Built-in delegation cannot be switched off by config. A delegate can re-delegate, and the vote record does not keep the signer (section 2) | DR-45 and the human-only invariant (I4) **cannot be enforced by the core program**. An agent's hot key set as delegate casts binding votes indistinguishable on chain from the member's. A binding-vote design would need a voter-weight plugin that returns 0 when the signer is not the owner (designed, not built), or an off-chain rule with detection (added after review D-13) |
| T13 human-only governance | none | Same as DR-45. R25 (personhood) stays uninspected (D4.0 (2)) |
| DR-48 k-of-n plus timelock on upgrade, mint and freeze | Council or community thresholds plus `min_transaction_hold_up_time`; DAO wallet as authority | No floor on hold-up or voting time (`governance.rs:638-679` validates thresholds only). The live default instance itself runs with hold-up 0. No veto inside the hold-up |
| DR-49 thresholds per class published and versioned | Per-governance config. Several governances per realm can represent proposal classes | Versioning and publication are off-chain |
| I-11 two governance systems | An option, not a mapping (amended after review D-20). One realm can carry a community population and a council population, for example the L0-L4 developer NFTs via nft-voter or core-voter. But that makes the developer holders a veto body over member votes, and member votes a veto over them (`governance.rs:347-372`). In a council-only bootstrap they would also hold the realm authority (T12, I-17). Alternatives: two realms, or two governances with disjoint powers | The boundary between the two systems is a constitutional choice and must still be written in the spec |

Threats: **T12** is closed only in part. The mechanisms exist, but the live inherited authority defeats the property (section 8). **T18:** on-chain voting in Phase 1 would contradict dao-requirements section 5, item 6 (advisory votes off-chain).

Property check (threat model v0.2, Governance row):

| Property | Verdict | Evidence |
|---|---|---|
| Multisig on every authority | Expressible: a council threshold is a k-of-n | `get_min_vote_threshold_weight` rounds up (`proposal.rs:1116-1140`). Live: 5-of-7 single keys [inspected RPC] |
| Timelock on every authority | Designed. **Partly tested**: the main hold-up assertion sits in an ignored test, so only the single-slot test is active. **Contrary evidence on the live artifact**: hold-up 0 on the upgrade authority of GovER5, GoVERLM and five plugins | Section 2; RPC decode of `6rFUnQik..` |
| Including inherited authorities | **Not demonstrated, contrary evidence**: a DAO on the shared instance inherits a 5-of-7 / 0-second upgrade authority over its own governance program and plugins. Mitigation: a DAO-owned instance (`governance/README.md:30-46`) | Section 8 |
| Including off-chain trust roots | Out of scope for the program. Section 12 gives the off-chain design | none |
| Including authorities that set voter weights, the electorate or the quorum denominator (review TM-R24-1, X-3) | **Not demonstrated.** These authorities have no timelock of their own: the realm authority (one signature, any key under `SetUnchecked`), the voting-mint authority (mint, revoke), the plugin configuration (`max_weight`, `total_weight`, `expected_attribute_authority`, set by the realm authority alone, `gpl:programs/core-attribute-voter/src/instructions/configure_collection.rs:33, 51-54, 66`) and delegation | Section 2 table |
| A veto or cancel reachable by a party other than the approving quorum (review TM-R24-2) | Expressible (a veto by the other population) only if the proposing population's tipping is `Disabled` **and** the veto threshold is reachable. The veto threshold is a share of the vetoing population's **max** voter weight, not of turnout (`process_cast_vote.rs:146-162`, `proposal.rs:1116-1140`). At 60 percent it is unreachable in a large, low-turnout DAO. Live authority: no veto at all | Section 8 |
| Voter-weight plugin (dimension-weighted or credential-gated) | Interface demonstrated by tests. Dimension weights: core-attribute-voter (tested, undeployed, unaudited). Credential gate: token-haver (deployed, under the council). The Civic gateway plugin was dropped ("deprecated support due to original program being not upgraded", `gpl:Anchor.toml:8`). The plugin's own upgrade authority is the same 5-of-7 / 0 s council (quadratic: a single key `2ajKbdL2..`; gateway `GgathUhd..`: single key `48V9nmW9..`) | RPC `progauth.py` |
| Documented failures | Solend 2022 [reported]. OtterSec SUG-01 still open [inspected]. Accretion C1/H1 fixed [inspected] | Section 3 |

## 6. Reuse decision

**Reference only for Phase 1. Investigate further for Phase 2, as a DAO-owned instance built reproducibly from published source, never the shared instance.** Phase 1 needs no on-chain governance: advisory votes are off-chain (dao-requirements section 5, item 6; T18). The deployed shared instance fails T12 by inheritance (headline 2). The published source does not match it (headline 1). The two features that would carry R24 most directly have the weakest evidence: per-member dimension weights (core-attribute-voter, undeployed and unaudited) and a credential gate (Civic plugin dropped). The account model, the population split, veto and one-way council removal are the best Solana reference for DR-46 and I-11 that this study found. They are primitives, not the transition itself (D-7). Squads v4 (section 12) is the better holder for a later program-upgrade or mint authority. Two further findings from the review weaken the Phase 2 binding-vote option, and no gate changes. Built-in delegation makes DR-45 and human-only voting unenforceable in the core program (D-13). And the electorate-defining authorities carry no timelock of their own (D-5, D-6). Before Realms is recommended for binding votes, a custom weight plugin must zero non-owner signers, and every electorate authority must sit under the protected governance.

Hard gates:
- **G1 pass.** No service is required. Any RPC or own node works. The hosted UI (app.realms.today) is optional.
- **G2 pass with matrix.** It is an on-chain program, the same on every OS. Clients are Node or TS. The SBF build toolchain is Linux or macOS. Windows builds need WSL [hypothesis, not run]. Section 7.
- **G3 pass.** Apache-2.0 file-level. The npm registry's MIT declaration conflicts with the repository; flag it.
- **G4 pass.** Solana.
- **G5 pass, conditional.** Nothing in the program touches the OS. The integration must not turn a vote, a token owner record or a plugin weight into runtime authority (I2). The section 7 verifier is display-only.

## 7. Integration design

- **Seam:** dao-requirements section 5, Phase 1 core item 6 (advisory votes) and DR-48 checks. Phase 1 needs no Realms deployment. The Alpha integration is a **read-only authority verifier**, which also serves Squads and plain keys.
- **Process boundary:** bridge-side Node 24 module. It makes read-only RPC calls (fetch) against two independent endpoints and compares them (V9). There is no SDK dependency: the decoders are about 150 lines and follow `state/governance.rs:29-131`, `realm.rs:103-162` and `sq:programs/squads_multisig_program/src/state/multisig.rs`, as I did in `gov.py`. The verifier never signs.
- **Data:** a published `authorities.json` (DR-48: "listed in the docs and checkable on-chain"), signed under the meta-authority row of section 12 and versioned in the R30 log. The verifier refuses a version that is not in the log (amended after review D-11b). For each key class the file records:
  - the expected holder type (`squads-v4`, `spl-governance-native-treasury`, `none`) and the address;
  - the minimum k-of-n, and the published affiliation of every key (insider or outside);
  - the minimum timelock;
  - the veto requirements, where a veto must be guaranteed;
  - the allowed program-data hash.
- **Checks** (amended after review D-4, D-5, D-6, D-8, D-10):
  - (1) **Insider quorum.** The number of insider keys among keys with approval power is at most k-1. In Squads, only members with the `Vote` permission count (`sq:state/multisig.rs:62`).
  - (2) **Veto window.** `veto_window_guaranteed` is true only when all of these hold:
    - the **proposing** population's tipping is `Disabled`;
    - the vetoing population's veto threshold is not `Disabled` and is at most a stated reachable share of its max weight (for example 10-20 percent);
    - base voting time plus cool-off is at least a stated floor;
    - check (3) passes.
  - (3) **Realm authority** is the protected governance itself (set with `SetChecked`) or `none`, never a key or a less-protected governance.
  - (4) **Voting-mint authorities** of both populations: the protected governance or none.
  - (5) **Plugin configuration and plugin upgrade authority** meet the same minimums.
  - (6) **Pending changes.** The verifier flags any `Voting` or `Succeeded` proposal that contains `SetGovernanceConfig`, `SetRealmConfig`, `SetRealmAuthority`, `RevokeGoverningTokens` or a mint to a voting mint on a governance it checks. A snapshot cannot see pending changes, and versioned hold-ups can shrink retroactively (D-4).
  - (7) **Squads `config_authority`** is null.
- **Output:** `{class, observed_holder, k, n, insiders, timelock_s, veto_window_guaranteed, pending_changes, binary_verified, verdict}`. Verdict is one of `meets`, `fails: <clause>` or `unverified`. It is shown in `/dao audit` and never used as authority (I2). **Status: designed, not yet tested** (acceptance test 11A).
- **Identity semantics:** no principal is created. The DAO's state is described, not acted on.
- **Error behaviour:** if the two RPCs disagree, or an account is missing, the verdict is `unverified`, never `meets`.
- **Alpha files** (at 578fbbc) [inspected existence]: new `browser-first/host/dao-authority-verifier.mjs` and `browser-first/test/dao-authority-verifier.test.mjs`. `browser-first/resonantos-side-panel-extension/src/lib/wallet-dao-audit-markdown.js:39` (`walletDaoAuditMarkdown`) gains an "Authorities" block. `app-command-handlers.js:444` already advertises `/dao audit`. The rewards dossier proposes `host/dao-credential-verifier.mjs` for mints, and the two should share the RPC-compare helper.
- **Tests affected among the 2,424:** `browser-first/test/wallet-dao-audit-markdown.test.mjs`. Its test "wallet DAO audit markdown preserves read-only human boundary" (`:55`) must still pass, so the new block adds no action affordance. Bridge route tests are unaffected if the verifier is called inside the existing DAO helper path and adds no route.
- **Per-OS matrix:** the verifier is pure Node, so it runs on Windows, macOS and Linux and is identical on each. Building spl-governance or a plugin from source runs on Linux and macOS, and on Windows only under WSL [hypothesis]. Nothing degrades silently. RPC failure is reported as `unverified`.

## 8. Security and governance effects

**Inherited authority (V10) [inspected RPC, 2026-10-21]:**

| Program | Role | Upgrade authority | Last deploy |
|---|---|---|---|
| GovER5Lthms3.. | shared spl-governance, v3.1.7 | HF7gCq.. (Realms Security council 8 DAO wallet) | 2026-10-05 |
| GoVERLMGbGF8.. | hosts that council's realm, v3.1.1 | HF7gCq.. (self-governed) | 2025-09-09 |
| GnftV5kLjd67.. nft-voter; HA99cuBQCCzZ.. token-voter; 7gobfUih.. token-haver; cNFTHBQu.. core-voter; vsr2nfGV.. VSR | voter-weight plugins | HF7gCq.. | 2026-09-01; 2026-09-01; 2024-06-19; 2025-09-15; 2026-09-29 |
| quadCSapU8nT.. quadratic | plugin | 2ajKbdL2.. single key | 2024-02-22 |
| GgathUhdrCWR.. gateway (Civic) | plugin, unsupported in the library | 48V9nmW9.. single key | 2022-08-30 |
| GTesTBiEWE32.. | test instance | G1DTKKGX.. single key | 2022-12-13 |
| HmdGe7Sp.. core-attribute-voter; GRmVtfLq.. realm-voter; 7yJT49aj.. bonk-plugin | at their `declare_id` | not deployed on mainnet | none |

Council governance `6rFUnQik..`: community vote `Disabled`; council `YesVotePercentage(60)` of max weight 7, so 5 approvals; council vote tipping `Early`; council veto `YesVotePercentage(60)`, which is moot because the community cannot propose; **community veto `Disabled`**, so no one can veto a council proposal (`governance.rs:367-369` returns `GoverningTokenMintNotAllowedToVote`; test `test_cast_community_veto_vote_with_community_veto_disabled_error`, `tests/use_veto_vote.rs:175`); voting 259,200 s base plus 86,400 s cool-off, both pre-empted by tipping; **hold-up 0**. The council mint `ACVqan3W..` has supply 7, mint authority HF7gCq and no freeze authority, so the council admits and expels its own members by its own vote. One revocation happened on 2026-05-29. The community mint `EtHceUV7..` has supply 0 and the same mint authority (amended after review D-2). Realm authority is the governance itself. All 7 voters are ordinary on-curve keys, not multisigs. The 2026-10-05 sequence: `CreateProposal "Reg Update"` 11:35:43 UTC, `InsertInstruction hold_up_time: 0`, `SignOffProposal`, then five `CastVote Approve` between 11:35:52 and 14:02:18, then `ExecuteTransaction` with inner `Upgraded program GovER5..` at 14:09:33. **Any 5 of these 7 keys can replace the code of the program, and of the plugins, that every DAO on the shared instance relies on, with no delay.** This is the same class of finding as the rewards dossier's 3-of-5 / time_lock 0. The ratio is higher, but the time lock is still zero.

**Veto and council mechanics** [inspected]: veto is cast by the population other than the proposal's voting population (`governance.rs:347-372`). A council can veto community proposals, and the community can veto council proposals, each with its own threshold or `Disabled`. Veto counts only while `Voting` (`proposal.rs:324-351`). Tipping is independent of the veto configuration. The tipping used is the **voting** mint's (`process_cast_vote.rs:164-166`, `get_vote_tipping`; `proposal.rs:661-775`), so `Early` or `Strict` tipping by the proposing population can end a proposal before the other population has vetoed. The veto threshold is a share of the vetoing population's **max** voter weight (`process_cast_vote.rs:146-162`). Only the proposer or its delegate can cancel, and only before `Succeeded` (`process_cancel_proposal.rs:45-46`, `proposal.rs:794-818`). After `Succeeded` nothing on-chain can stop execution.

**Rule for any Resonant configuration** (amended after review D-10; the earlier text named the wrong field). Wherever a veto is meant to protect, all of these must hold:
- the **proposing** population's tipping is `Disabled` (for a community proposal, that is `community_vote_tipping`);
- the vetoing population's veto threshold is reachable by that population in practice;
- the hold-up is long enough for a public alarm.

A council veto over community decisions is a custodian power over members, not an appeal route for them. Even when all three hold, the hold-up only buys time for people to act off-chain, for example by withdrawing funds.

**Emergency powers:** none are built in. Whatever a passed proposal can do is the emergency power. It is bounded only by which assets the DAO wallet holds.

**Counterexample rows answered:**

| Row | Answer |
|---|---|
| s2-br-f against spl-governance-v3.1.3: archived upstream, single 2022 audit, no maintainer redundancy | **Confirmed and extended.** The upstream is archived. The only governance audit is an intermediate 2022 report at an unnamed commit. About 1-2 active developers. **New:** the deployed 3.1.7 has no public source, and governance CI failed in 31 of 31 runs |
| s2-br-f, Solend SLND1/SLND2 (2022-06), short window, low turnout, emergency powers | **Confirmed as a configuration failure the program still permits.** `assert_is_valid_governance_config` puts no floor on `voting_base_time` or hold-up (`governance.rs:638-662`). In v3 the yes threshold is a percentage of **max voter weight**, not of turnout (`proposal.rs:1116-1140`), which addresses the "1 percent of turnout made quorum" element. That holds only while max voter weight is honest: a plugin `total_weight` set too low reopens it (`gpl:.../core-attribute-voter/README.md`, example 2). Whether Solend ran v2 or v3 was not checked |
| s2-br-h against solana-labs SPL (R22 row): no governance commits seen in 2025-2026 | **Refuted in part.** The Mythic SPL fork's `master` is stale, but continued work went to spl-governance-v3.1.3 (32 governance commits since 2025-01). Since 2026-03 the work continues only as unpublished binaries |
| s2-br-h against governance-program-library: maintained fork of an archived upstream | **Confirmed.** 8 commits in 12 months. "Lint and Test", "Cargo Audit" and "Sec3 Pro Audit" all failed on `master` at HEAD (2026-03-23 runs). On-chain upgrade authority is now resolved: section 8 table |
| s2-br-h against oyster: MIT in npm versus Apache-2.0 in repo; no commit for 12 months | **Confirmed.** npm 0.3.33 says MIT, the repository says Apache-2.0, and the last publish was 2025-10-03 |

**Personal data:** every vote, token owner record and delegation is public and permanent, and linked to the wallet (T14). On-chain binding votes would publish each member's choice and weight. DR-09 and DR-32 pressure points.

**Capture risks for ResonantDAO:**
- If a credential issuer or ledger operator writes core-attribute-voter weights, that operator is the vote. The attribute-authority check makes the plugin trust exactly one key.
- In a council-only bootstrap, the custodians control the realm authority, the plugin configuration (`total_weight`, attribute authority) and every threshold (V6, I-17).
- **Realm authority** (D-5). With one signature, mid-vote, it can do any of the following, and so make a 7-day hold-up and a tipping rule elsewhere cosmetic:
  - raise the vetoing population's max weight, making its veto unreachable;
  - lower the proposer's max weight, making a vote tip;
  - swap in an add-in that weights only its own assets.

  Under `SetUnchecked` it can be any key (`process_set_realm_authority.rs:37-45`). For core-attribute-voter it alone sets `max_weight`, `total_weight` and `expected_attribute_authority`. The max-voter-weight record it writes has no expiry (`gpl:.../configure_collection.rs:113-114`).
- **Voting-mint authority** (D-6). Minting admits voters at will. Revoking burns a member's deposit with no DR-06 record and can tip a live vote (section 2).
- **Delegation** (D-13). Any key a member, or its delegate, names can cast the member's vote, with no trace of the signer.

## 9. Licensing

Technical research, not legal advice.
- **spl-governance:** Apache-2.0. The root `LICENSE` is the full text, and the crate declaration matches. Compatible with the MIT Alpha, with attribution and NOTICE handling. Confidence high.
- **governance-program-library:** Apache-2.0 by crate declarations. The `LICENSE` file is a notice header, not the licence text (GitHub NOASSERTION). Low risk.
- **@realms-today/spl-governance:** registry MIT, repository Apache-2.0. Legal item: which licence governs the published tarball. Either is MIT-compatible.
- **voter-stake-registry:** GPL-3.0. Reference only for code. Calling it on-chain does not copy it [hypothesis].
- **Metaplex Core (mpl-core):** a dependency of core-voter and core-attribute-voter (`gpl:programs/core-attribute-voter/Cargo.toml:25`). The rewards dossier and the ledger report the Metaplex NFT OSL 1.0 with a competitive-product clause 4(c) [reported there; not re-read here]. Legal item: whether building a voter-weight plugin linked to the mpl-core crate is affected. The program depends on the crate, not only on the deployed program.
- **Squads v4** (the competitor; section 12). The repository has one licence file, the AGPL-3.0 root `LICENSE` (`sq:LICENSE`). The program crate uses it via `license-file`. `sq:sdk/multisig/package.json:17` says MIT without MIT text, and the Rust SDK and CLI say MIT OR Apache-2.0 [reported by the ledger correction row; root and package.json inspected]. Items for legal review:
  - **Deploying** an unmodified copy: the bytecode becomes public. AGPL-3.0 section 13 (remote network interaction) applies to *modified* versions. Whether a Solana program is "interacted with over a network" in that sense is open.
  - **Calling** the deployed immutable `SQDS4ep..` from MIT code: no Squads code is copied, so no AGPL obligation attaches [hypothesis].
  - **Forking** the program: the derivative must stay AGPL-3.0, with its source offered to network users.
  - **Embedding client code** in the MIT Alpha: the TypeScript SDK's MIT declaration is not backed by licence text under an AGPL root. Prefer writing a small decoder of your own from the account layout (section 7), so that no SDK code is bundled.
- **MetaDAO:** BUSL-1.1, production use only "approved by MetaDAO LLC" (ledger). Not open source, so reference for pattern only.

## 10. Cost and effort

- **Phase 1:** small. The verifier and `authorities.json` take 2-4 days of JS plus fixtures, and no Solana deployment is needed.
- **Phase 2, own instance:** medium. It needs a reproducible build from a pinned public commit (`solana-verify`), deployment, upgrade-authority handover, a governance configuration review, a fresh audit of the deployed commit (the 2022 report does not cover it) and plugin deployment if weights are on-chain. Rust/SBF skills the team does not have (master prompt 5.5).
- **Phase 2, shared instance:** a smaller build cost, but it inherits the 5-of-7 / 0 s authority (section 8). Rejected.
- **Lock-in:** the Realms account model is the de facto standard (UI and indexers). Leaving means migrating treasury authorities, which is possible because they are ordinary authorities.
- **Operations:** council or community key management, RPC monitoring and the verifier.

## 11. Acceptance test

A. **Read-only verifier (no third-party code executed).**
- Inputs: the RPC fixtures recorded in this inspection (`rpc-log.jsonl`): governance `6rFUnQik..`, realm `2r7VSe..`, realm config `3CMWxi..`, council mint `ACVqan..`, GovER5 ProgramData header, and the Squads multisig `4CxQs26D..` from the rewards dossier.
- Expected:
  - GovER5 gives `fails: DR-48 timelock 0 < 604800; no veto route (community veto Disabled, community supply 0); insider quorum (7 of 7 voters are council keys with no published affiliation); binary unverified (on-chain 0ad7fdd4.. != verified b1d75931..)` (amended after review D-2).
  - The rewards vault gives `fails: time_lock 0`.
  - The section 12 design fixture (vault V 2-of-2 over custodian multisig C and outside multisig O, time_lock 604800, config_authority null; O with time_lock 604800) gives `meets`.
- Fault scenarios (threat model T12, V9, V10; ids used in the section 12 status column):
  - F1: the authority is a single on-curve key: `fails: single key`.
  - F2: two RPC fixtures disagree on the ProgramData authority: `unverified`.
  - F3: a governance with hold-up 604800 but `council_vote_tipping Early` and a community veto: `fails: veto window not guaranteed`.
  - F3b: council tipping `Disabled` but `community_vote_tipping Early` on a community proposal with a council veto: `fails: proposing population tips` (D-10).
  - F3c: veto threshold 60 percent of max weight: `fails: veto threshold above reachable share` (D-10).
  - F4: a Squads multisig with `config_authority` set: `fails: config authority can reset time_lock without the time lock` (`sq:.../instructions/multisig_config.rs:165-178`).
  - F5: the realm authority is a single key, or a governance weaker than the protected one: `fails: realm authority can change weights without timelock` (D-5).
  - F6: a `Voting` proposal plus a pending `RevokeGoverningTokens` or a mint to the voting mint: `fails: electorate change during open vote` (D-6).
  - F7: 3 custodian-affiliated keys can approve an upgrade (3-of-5 with 2 outside): `fails: insider quorum` (D-8).
  - F8: in the V fixture, the custodian multisig C alone, and separately the community DAO wallet alone, tries to replace a member of V: both fail, because a config transaction needs 2-of-2 plus the time lock (D-9).
  - F9: a token owner record with a non-null `governance_delegate` in a realm used for binding votes: `fails: delegation not zeroed` (D-13).
  - F10: a status-list version whose changed indices have no inclusion proof of a logged revocation record: the list is `unverified` and the previous state is kept (D-12).
  - F11: an `authorities.json` version not found in the R30 log: refused (D-11b).
  - F12: a Squads spending limit on V: `flagged: spending limit is not an emergency power` (D-11a).
- CI hook: `node --test browser-first/test/dao-authority-verifier.test.mjs`. Pass: all verdicts equal the expected strings, and no network access occurs in tests.

B. **Program-level checks of the hold-up, veto, revoke and delegation claims (requires executing third-party code: a request for the user, not a plan).** In a sandbox, run `cargo test-sbf` on `governance/program` at `00fa982` with the two ignored tests un-ignored. The pre-emption itself is already shown upstream by `test_cast_yes_and_veto_votes_with_yes_as_winning_vote` (`tests/use_veto_vote.rs:723-789`). Add three assertions of mine (amended after review D-3):
- (i) after that tip, a further veto returns `InvalidStateCannotVote`;
- (ii) `RevokeGoverningTokens` by the mint authority during `Voting` lowers the mint supply, and with it the max voter weight, so that the next vote or `FinalizeVote` passes a proposal that would otherwise have failed;
- (iii) a delegate-cast vote is recorded under the owner.

Pass: the hold-up assertions hold, and (i)-(iii) behave as section 2 states.

## 12. Recommendation and confidence

- **Benefits:** the most complete Solana governance model inspected here: two populations, veto, hold-up, a plugin weight interface, one-way council removal, a DAO wallet as a generic authority, and real tests. It is Apache-2.0, and its production use is real but moderate (about 60 transactions a day on GovER5; section 2).
- **Additional blockers for binding votes** (after review):
  - built-in delegation, which cannot be disabled and leaves no trace of the signer, so DR-45 and I4 cannot be enforced by the core program (D-13);
  - the electorate-defining authorities (realm authority, voting-mint mint and revoke, plugin configuration) have no timelock of their own (D-5, D-6);
  - council removal does not end custodian control unless every one of those authorities moves too (D-7).
- **Trade-offs and blockers:**
  - the deployed artifact has no public source;
  - its authority has no timelock and covers the plugins too;
  - the hold-up is not a veto window;
  - there is no floor on time parameters;
  - the only audit is 2022 and intermediate;
  - CI never green in the recorded history;
  - dimension-weight plugin undeployed and unaudited, with a per-NFT cap rather than a per-member cap;
  - the Civic credential plugin was dropped;
  - effectively two maintainers.
- **Evidence quality:** about 85 percent [inspected] (code, tests, two audit PDFs, 102 RPC calls, the verify API). Solend and the Metaplex licence are [reported].

**Competitor on the same criteria** (Squads v4 inspected at `af94153`; deployed facts from RPC by the ledger and the rewards dossier, plus my verify.osec.io GET):

| Criterion | spl-governance (as inspected) | Squads v4 |
|---|---|---|
| k-of-n | Council or community threshold; a percentage of max weight, rounded up | `threshold` of members with permission masks (`sq:state/multisig.rs`) |
| Timelock | `hold_up_time`, no floor; live default 0 | `time_lock` ≤ `MAX_TIME_LOCK` = 3 months (`sq:state/multisig.rs:9, 191`); enforced in execute (`vault_transaction_execute.rs:71`, `config_transaction_execute.rs:83`, `batch_execute_transaction.rs:89`). No public test asserts `TimeLockNotReleased` (grep) |
| Cancel inside the delay | None after `Succeeded`. Before that, only the proposer or its delegate can cancel (`process_cancel_proposal.rs:45-46`) | `proposal_cancel` on `Approved`, by threshold cancel votes (`sq:instructions/proposal_vote.rs:116-130`; `sq:state/proposal.rs:89-102`). Same k as approval, so it is not a minority veto. `reject` while Active needs `cutoff` = n - k + 1 (`sq:state/proposal.rs:67-80`) |
| Config bypass | The governance can lower its own hold-up by proposal, under the current hold-up. For versioned transactions the lower value also applies retroactively to proposals that have already `Succeeded` (`proposal.rs:886-918`; D-4) | A *controlled* multisig's `config_authority` sets `time_lock` and adds or removes members directly (`multisig_config.rs:71-86, 165-178`), so use autonomous multisigs only. In an autonomous multisig only the members' own config transactions, under threshold and time lock (`config_transaction_execute.rs:83`), change the key set |
| Deployed artifact | Upgradeable by 5-of-7 / 0 s; v3.1.7 unverified | Immutable (upgrade authority null; ledger RPC row). verify.osec.io records a verified build by signer `sqdcVVo..` at `2a47b4c` (2025-08-27). `git diff 64af733 2a47b4c -- programs/` is empty, so the deployed code equals the README's "final and fully audited" commit `64af733` (`sq:README.md:177`) [inspected diff] |
| Audits | OtterSec 2022 (intermediate); Accretion 2025 (one plugin) | 10 PDFs: OtterSec, Neodyme, Certora (formal verification) and Trail of Bits (`sq:audits/`; listed, not read) |
| Voter weights, populations, veto | Yes (plugins, two populations, veto) | No. Members are equal keys; there is no token voting |
| Foot-guns | Tipping pre-empts veto; hold-up 0 allowed | Permanent-time-lock warning (`sq:README.md:38`), bounded at 3 months; immutable means unpatchable |
| Licence | Apache-2.0 | AGPL-3.0 program; SDK manifest MIT without text (section 9) |
| Maintenance | About 2 developers; source behind binary | HEAD 2026-08-20; npm SDK 2.1.4 lags (2025-06-16), which matters little because the program has been frozen since 2024-11-20 |

The **Squads counterexamples answered:**
- permanent time-lock foot-gun: **confirmed**, but bounded by an on-chain maximum and invariant (`multisig.rs:188-192`);
- npm client lagging the program: **confirmed, low impact**, because the program is immutable;
- licence: **confirmed**, legal items in section 9.

EVM patterns (from ledger rows only; reference, G4): Aragon OSx (AGPL-3.0, permissioned plugins), Hats Protocol (AGPL-3.0, role trees, stale since 2024-05), Safe plus Zodiac Delay and Roles modifiers (LGPL-3.0; the Delay modifier adds a cancellable delay) and Snapshot (snapshot-strategies archived; score-api without a licence file; off-chain signed voting). MetaDAO futarchy: BUSL-1.1, so not usable. **Not inspected** (coverage limit, amended after review D-19): Tribeca (named in R24; its status was not checked in this inspection) and DAO DAO (CosmWasm, so G4 makes it reference only). The article should state this limit, as it does for R25 and R27.

**Which stack per authority class** (T12 key classes; threat model v0.2 A11, V6, V10):
- **Phase 1** (off-chain first; dao-requirements section 5): no on-chain governance stack. Create no program and no mint (T18). Retire the devnet single-key artifacts (DR-48 row: program HMthR7.., wallets vbYQ7r.. and HtZyHf..), either by setting authorities to none or by publishing them as superseded. Advisory votes are off-chain and replayable, and their tallies are committed in the R30 log (tessera dossier section 7). Custodian powers are enumerated and every use is logged (DR-46, DR-47).
- **Later** (Phase 2, after DR-54 conditions; one design, amended after review D-8, D-9):
  - A Squads v4 vault **V** holds the program-upgrade and mint authorities. V is autonomous (`config_authority` null), with `time_lock` ≥ 604,800 s. Its two voting members are:
    - **C**, a Squads multisig of the custodians (2-of-3);
    - **O**, a Squads multisig of outside organisations (2-of-3, its own `time_lock` ≥ 604,800 s).
  - V's threshold is 2-of-2. No upgrade, mint or membership change of V succeeds without O. In V, the insiders' approval keys number 1, which is at most k-1 = 1. A member objection goes to O, whose outside members can refuse, or cancel inside O's own time lock by O's outside-only quorum. That route needs no custodian.
  - Squads was chosen because it is immutable, verified against the audited commit and its time lock is capped.
  - Phase 2 leaves the key set without on-chain community control: changing V needs C and O. That residual gap (DR-46 open on chain) is stated, not hidden.
  - **Phase 3** (only if binding member votes are adopted): C is replaced in V by the DAO wallet of a **DAO-owned Realms instance**, built reproducibly from a published, audited commit. V remains 2-of-2 (community plus O). Replacing C is itself a V config transaction, so it needs C and O plus the time lock, and the custodians must consent to their own exit. DR-46 triggers decide when they are bound to do so. The Realms instance follows every Realms row below.
  - Assumption: a Squads member can be another multisig's vault PDA approving by CPI from a vault transaction [hypothesis; not tested, acceptance F8].
  - Deadlock risk: if O stops cooperating, nothing upgrades. For an upgrade authority that fails safe.

**Insider rule (review X-4) for every row meant to resist custodian capture:**
- insider keys with approval power ≤ k-1;
- the appeal or veto route never needs the quorum that approved;
- every key has a published affiliation.

Insider means a custodian, or anyone paid by or affiliated with the founding team.

Governance checklist (proposed; not scored, per D4.0 (c)). **Every row is designed, not yet tested.** The status column names the acceptance fault in 11A that would change that.

| Key class | Proposed holder | k-of-n (insiders) | Timelock | Appeal or veto route (not the approving quorum) | On/off-chain | Status |
|---|---|---|---|---|---|---|
| Program upgrade (any Resonant Solana program) | Phase 1: **none exists**; devnet HMthR7 authority set to none. Later: vault V | Later: V 2-of-2 over C (custodians 2-of-3) and O (outside 2-of-3); insiders 1 ≤ k-1 | V `time_lock` ≥ 7 days; O `time_lock` ≥ 7 days; `config_authority` null | O refuses, or O's outside members cancel inside O's time lock. Every proposal and its verifiable-build hash is posted to the R30 log on creation | Off-chain in Phase 1 (nothing to govern); on-chain later | designed, not yet tested (F1, F4, F7, F8) |
| Squads V configuration (members, threshold, time lock) | V's own config transactions | 2-of-2 as above | V `time_lock` (`config_transaction_execute.rs:83`) | as above; Phase 3 replaces C by the community DAO wallet | On-chain later | designed, not yet tested (F4, F8) |
| Mint and freeze ($RCT, $RES, credential classes) | Phase 1: **no mint** (T18, DR-36); devnet mints revoked or marked superseded. Later: V. Freeze authority none unless DR-07 requires it | as program upgrade | as program upgrade | as program upgrade | Off-chain / on-chain later | designed, not yet tested (F1, F7) |
| Realm authority (Phase 3 Realms only) | The protected community governance itself (`SetChecked`), or `Remove`; never a key or a weaker governance | that governance's thresholds | its hold-up, ≥ 7 days | veto by the other population (row conditions below) | On-chain | designed, not yet tested (F5) |
| Voting-mint authorities, community and council (Phase 3) | The protected governance | its thresholds | ≥ 7 days. Never revoke or mint while any proposal is in `Voting` | A revocation is allowed only after a logged DR-06 record (reason, authority, evidence, appeal deadline) in the R30 log and after the DR-26 appeal deadline has passed, decided by the appeal panel | On-chain plus R30 log | designed, not yet tested (F6) |
| Veto configuration (Phase 3) | Governance config | proposing population's tipping `Disabled`; vetoing population's veto threshold ≤ 10-20 percent of its max weight; base plus cool-off ≥ 7 days | as hold-up | the other population's veto | On-chain | designed, not yet tested (F3, F3b, F3c) |
| Voter-weight plugin configuration and upgrade (if on-chain weights) | Upgrade: V. Config (`max_weight`, `total_weight`, `expected_attribute_authority`): realm authority = protected governance. Attribute authority: a weight-publisher key whose outputs are committed in the R30 log | publisher outputs signed off 2-of-3 with at most 1 insider | upgrades ≥ 7 days; weights published one epoch before any vote | members contest weights under DR-26 before the vote opens, decided by the appeal panel | On-chain later | designed, not yet tested (F5) |
| Delegation policy (Phase 3) | Custom weight plugin that returns 0 when the signer is not the owner; until it exists, delegation is forbidden by rule and flagged | none | none | verifier flags any non-null delegate | On-chain | designed, not yet tested (F9) |
| Status-list signer (R22/R23 revocation) | Issuer key on a hardware token. A change is valid only with a revocation record carrying the DR-06 fields and reason class, co-signed 2-of-3 by co-signers of whom at most 1 is a custodian, and logged in R30 **before** the list is re-signed. **Member verifiers require an inclusion proof per changed index; otherwise the list is `unverified` and the previous state is kept** (D-12) | 2-of-3 co-sign, insiders ≤ 1; 1 signing key | notice period before effect (for example 72 h); emergency suspension under the emergency row | DR-26 appeal to the appeal panel, excluding the co-signers; reversal is a logged record; no revocation reason on chain (T14) | Off-chain | designed, not yet tested (F10) |
| Emergency powers (DR-47): pause payouts, suspend a credential, delist an item | Emergency committee | 2-of-3, at most 1 insider | none at action; **lapses after 72 h** unless confirmed by the normal process; mandatory post-action review within 14 days | review by the appeal panel; every action logged in R30 with its reason. On chain: no emergency program in Phase 2 (Squads spending limits excluded, D-11a) | Off-chain | designed, not yet tested (F12) |
| Appeal panel (DR-26) | Three people | 2-of-3, at least 2 outside; disjoint from the quorum that decided the matter | decision deadline published | escalation to an advisory member vote | Off-chain | designed, not yet tested |
| Custodian set (who adds or removes custodians) | Signed record in the R30 log | custodian 2-of-3 **and** 2 outside signers (O members or the appeal panel), so insiders ≤ k-1 | announced ≥ 14 days ahead | members object to the appeal panel; DR-46 triggers published | Off-chain | designed, not yet tested |
| Meta-authority: signer of `authorities.json` and per-class thresholds (DR-49) | as custodian set | as custodian set | ≥ 14 days | as custodian set; the verifier refuses versions not in the R30 log | Off-chain | designed, not yet tested (F11) |
| TUF root | Offline keys held by different people | 3-of-5, at most 2 insiders | rotation announced in R30 ≥ 14 days before signing; expiry ≤ 1 year | clients keep the previous root until the announced date; members object to the appeal panel. Per D4.0 (3)(a) (amended after review D-17): a **security** revocation suspends at once, with user notice and a logged, warned override; a **policy** delisting never disables; every revocation record carries its reason class in the R30 log | Off-chain | designed, not yet tested |
| TUF targets | Release maintainers (insiders by role; not meant to resist custodian capture, since root bounds it) | 2-of-3 | short expiry; delegations logged | root holders, with an outside majority, revoke delegations | Off-chain | designed, not yet tested |
| Log key (R30) and receipt key | Log operator (custodian), separate log and receipt keys (tessera section 7) | 1 log key, bounded by the witness quorum | rotation only by a governed log record announced ahead | members hold receipts; a split view or omission is provable; outside witnesses refuse to cosign | Off-chain | designed, not yet tested |
| Witness keys | Three outside organisations, one key each; the operator runs none | `outside 2` of 3 (meets X-4) | not applicable | witness withdrawal published; policy change only by a governed record | Off-chain | designed, not yet tested |
| Anchoring fee-payer key (weekly SPL Memo, D-transparency-dev-tessera-6) | Log operator hot key | 1-of-1. Acceptable because a memo is trusted only when it equals a witnessed checkpoint | not applicable | a wrong memo is detectable against witnessed checkpoints; rotate on suspicion | On-chain memo | designed, not yet tested |
| Verifier RPC endpoints (V9) | At least two providers run by different operators, listed in `authorities.json`, plus an optional own node | 2 must agree | not applicable | disagreement gives `unverified`, never `meets` | Client | designed, not yet tested (F2) |
| Provenance key (R12/T13) | Per-member human key in the bridge-only store, unreachable by agents (R03, R15) | 1-of-1 per member | not applicable | revocation through the status list | Off-chain | designed, not yet tested |
| Third-party inherited authorities (V10) | Listed in `authorities.json` and checked by the verifier. Resonant does not build on any program whose authority fails the same minimums (GovER5 fails; the rewards program fails) | as observed | as observed | none: inherited authority cannot be appealed, only avoided | Both | verdicts observed (11A expected values) |

**First PR (Resonant Alpha repository, read-only side):** add `browser-first/host/dao-authority-verifier.mjs` with `browser-first/test/dao-authority-verifier.test.mjs`, plus a spec file `docs/dao/authorities.example.json`. It decodes a spl-governance `GovernanceV2`, `RealmV2` and `RealmConfigAccount`, a Squads v4 `Multisig` and a BPF-upgradeable ProgramData header from recorded RPC JSON. It checks each against the checklist minimums and emits the section 11A verdicts. It adds an "Authorities" block to `walletDaoAuditMarkdown` (`wallet-dao-audit-markdown.js:39`). It also implements the checks in section 7: insider quorum, veto window, realm authority, voting-mint authorities, pending changes and Squads `config_authority`. The fixtures are the GovER5 and council accounts from this inspection, so the expected verdict is "fails: DR-48 timelock 0; no veto route; insider quorum". It makes no network calls in tests and holds no signing code.

**Confidence: high** that Phase 1 should use no on-chain governance stack and that the shared Realms instance fails T12 by inheritance (code, RPC and the verify API agree). **Medium** on Squads v4 as the later authority holder: its audits were not read, and AGPL questions are open. **Low** on Realms as a later binding-vote layer, until a public, audited source matching a deployable binary exists.

## 13. Scores (for scoring/scores.csv)

Profile: **dao_contract**. Rates the upstream artifact as inspected (D3.7 (c)), not the proposed checklist.

| Criterion | Score | Reason (anchor) |
|---|---|---|
| requirement_fit | 3 | Populations, veto, hold-up, a plugin interface, one-way council removal and a DAO wallet as authority cover most of R24's mechanics. Missing: a per-member cap, on-chain transition triggers, bounded emergency powers and a veto inside the hold-up. Phase 1 does not need it on-chain |
| demonstrated_maturity | 3 -> 2 | Amended after review D-14. The rated tree `00fa982` has no release, no tag and no published crate, and governance CI failed in 31 of 31 runs. Its own production use is limited to the seven weeks it ran as 3.1.3 on GovER5 (2026-03-10 to 2026-04-30; version timeline, headline 1). Current usage belongs to 3.1.7, whose source is unknown, and cannot be credited to this tree (the logic of anchor (a)). Tests exist. Between anchor 1 (pre-release) and 3 (tests and releases) |
| isolation_and_security | 2 | The Governance-row properties are designed. They are partly tested: the single-slot hold-up test is active, the main hold-up and upgrade tests are ignored, and the 16 veto tests and the plugin tests exist, but none was shown to pass in CI. After review: the electorate-defining authorities (realm authority, voting-mint revoke and mint, plugin configuration) and delegation have no timelock of their own. Under the configuration this dossier proposes, they sit under the protected governance, so per (d) this lowers nothing further. Score unchanged (review D-21: no objection). Per (a), the artifact a DAO would actually use, the shared GovER5 binary, is unverified against any public source and sits under a 5-of-7 / 0 s authority, which is contrary evidence for the inherited-authority property. I do not propose that use, so per (d) I do not score 0. The own-instance path rests on a 2022 intermediate audit and code with open SUG-01. Between 1 and 3 |
| licence_suitability | 5 | Apache-2.0 file-level (root LICENSE plus crate). The npm MIT/Apache mismatch is minor |
| integration_effort | 2 | A typed TS client exists for reads, and the read-only verifier is small. Any on-chain use needs a Rust/SBF build, deployment and audit capacity the team does not have (anchor 1). Read-only reuse is near anchor 3 |
| maintenance_sustainability | 1 | One organisation and about two developers. The governance path had 2 commits in 12 months, and on-chain versions 3.1.4-3.1.7 have no published source or changelog. CI is broken, and SECURITY.md points to an archived repository. On-chain operation is active, which keeps it above 0 |
| sovereignty_and_portability | 3 | Any RPC, no account, own instance possible. All governance data is public and permanent, and the shared instance sits under a third party's authority |
| interoperability | 4 | The de facto Solana DAO standard (Realms UI, SDK, plugin interface, many DAOs). The account layouts are stable and decodable without the SDK |
| operational_complexity | 3 | One on-chain program plus RPC for reads. An own instance adds a deployment, an upgrade-authority holder and plugin deployments |

Weighted (dao_contract), amended after review D-14: 0.16x3 + 0.16x2 + 0.18x2 + 0.08x5 + 0.06x2 + 0.14x1 + 0.08x3 + 0.06x4 + 0.08x3 = 0.48 + 0.32 + 0.36 + 0.40 + 0.12 + 0.14 + 0.24 + 0.24 + 0.24 = **2.54 / 5** (was 2.70). It fails no hard gate (G5 conditional). Decision unchanged: reference only (Phase 1); investigate further (Phase 2, own instance), with the binding-vote blockers of section 12.

## 14. Open questions for the adversarial reviewers

- **Source of 3.1.7.** I found no public repository, branch or tag for 3.1.4 to 3.1.7. Searches: GitHub code search for `"spl-governance-v3" "3.1.7"` and `org:Mythic-Project "3.1.7" governance` (0 hits); branch lists of both Mythic SPL repositories; the Mythic organisation repository list. A private repository, or an unpushed branch, is likely [hypothesis]. Someone should ask the Realms team, and whether the 2026-10-05 change ("Reg Update") had any review.
- **Council identities.** The 7 council keys (`7pPJt2xo..`, `EZLvwGdG..`, `EUisgNmi..`, `3PKhzE9w..`, `KfxsYDfi..`, `3LPh9LN8..`, `Fywb7YDC..`) were not attributed to people or organisations. Three more token owner records hold 0 tokens, one of them revoked on 2026-05-29. Whether "council 8" means earlier councils were replaced was not established.
- **Tests not run.** No hold-up, veto or plugin test was executed. The CI failure cause (0 steps in 96 s) suggests a workflow or runner problem rather than failing tests [hypothesis]. Run B in section 11 would settle it.
- **Audits not read:** Squads v4's 10 audit PDFs; any OtterSec final report for spl-governance v3 (none found in anza-xyz/security-audits); any audit behind core-attribute-voter's "fix audit issues" commit.
- **Solend specifics:** which program version and configuration Solend used in 2022 was not checked.
- **Metaplex licence:** the effect of the Metaplex licence on a plugin linked to the `mpl-core` crate is from other dossiers [reported], not re-read here.
- **Shared-instance prevalence:** how many DAOs run on GovER5 versus their own instances was not counted (getProgramAccounts on realms was not run, to limit RPC load).
- **Possibly optimistic text:** the governance README still says "This way only the DAO can ever change and upgrade the most important program it uses" (`governance/README.md:36`). That is true for a DAO-owned instance, but the shared instance, which the same README offers to "anybody", is governed by the Realms council.
- **Rate limits:** the public RPC answered HTTP 429 twice. The last part of the `hist.py` history listing (after 2026-09-01) is therefore incomplete. The 2026-09-29 VSR and 2026-10-05 GovER5 upgrades come from `progauth.py` and `tx.py` instead.

### Review responses (2026-10-21)

Source: `reviews/dao-skeptic-r24.md` (s3-rev-dao-r24). Each claim was rechecked at the recorded SHAs and by RPC (`versions.py`; rpc-log.jsonl).

| Id | Verdict | Change made (section) or evidence for dispute |
|---|---|---|
| D-realms-spl-governance-1 | partly accepted | Headline 1: both `/status-all` records named, and the endpoint named. The reviewer's hypothesis is refuted by RPC. The last successful GovER5 tx before the 2026-04-30 upgrade logs 3.1.3 (`bS8BAK3X..`, 17:33 UTC); before 07-07 it logs 3.1.4 (`21rDwZ6a..`); before 10-05, 3.1.6 (`3p57QzCH..`); before 03-10, 3.1.2 (`tfeg6EiN..`). So 04-30 deployed 3.1.4, and "four upgrades after the last public source commit" with four version bumps (3.1.4-3.1.7) stands. A version timeline was added |
| D-realms-spl-governance-2 | accepted | Headline 2 and section 8: community veto `Disabled`, community supply 0 (my RPC: `EtHceUV7..` supply 0, mint authority `HF7gCq..`); `governance.rs:367-369`; test `:175`. The 11A expected verdict now reads "no veto route" |
| D-realms-spl-governance-3 | accepted | Headline 3, section 8 and the 12 competitor table: cancel is proposer- or delegate-only (`process_cancel_proposal.rs:45-46`). 11B cites `test_cast_yes_and_veto_votes_with_yes_as_winning_vote` (`tests/use_veto_vote.rs:723-789`) and keeps only the missing assertion |
| D-realms-spl-governance-4 | accepted | Section 2 hold-up row and the 12 "Config bypass" row: versioned hold-up is read at execution (`proposal.rs:886-918`), so a lower value applies retroactively. Section 7 check (6) flags pending config changes |
| D-realms-spl-governance-5 | accepted | Section 2 realm-authority row (`process_set_realm_authority.rs:26-52` allows any key under `SetUnchecked`; `process_set_realm_config.rs:43-47` comment), section 8 capture, section 7 check (3), checklist row "Realm authority", fault F5 |
| D-realms-spl-governance-6 | accepted | Section 2 revoke row (`process_revoke_governing_tokens.rs:25-75`, mint-authority branch): "revoke exists; governed revocation (DR-06) does not". Checklist row "Voting-mint authorities", fault F6, 11B (ii) |
| D-realms-spl-governance-7 | accepted | Section 5 DR-46 row: council removal is a primitive. Transition completion is defined as a verified state (no custodian key over realm authority, mints, plugin configuration or plugin upgrade). The stranded-deposit comment is cited (`process_set_realm_config.rs:72-73`) |
| D-realms-spl-governance-8 | accepted | Section 12: insider rule (insiders ≤ k-1; the appeal route never needs the approving quorum; published affiliations). Program upgrade, mint and TUF root rows redesigned. The Squads cancel is no longer listed as the appeal route, since it needs `threshold` (`sq:state/proposal.rs:89-102`); the route is now O's refusal or cancel by an outside-only quorum. Status-list co-signers hold at most 1 insider. Fault F7 |
| D-realms-spl-governance-9 | accepted | Section 12 "Later": one design. V is autonomous, 2-of-2 over C and O; Phase 3 replaces C by the Realms DAO wallet through a V config transaction. In an autonomous multisig, direct member changes need `config_authority` (`sq:.../multisig_config.rs:71-86`), confirmed. The Phase 2 lack of on-chain community control over the key set is stated. Fault F8; the PDA-member assumption is labelled [hypothesis] |
| D-realms-spl-governance-10 | accepted | Section 8 rule and section 7 check (2): proposing population's tipping (`process_cast_vote.rs:164-166`); veto threshold a share of the vetoing population's max weight (`:146-162`); reachable-share and voting-time floors. Checklist row "Veto configuration"; faults F3b, F3c. Council veto noted as a custodian power, not a member appeal |
| D-realms-spl-governance-11 | accepted | Checklist rows added: emergency powers (2-of-3, ≤1 insider, 72 h lapse, 14-day review), meta-authority (`authorities.json` signed and logged; F11), custodian set, anchoring fee-payer key, verifier RPC endpoints, appeal panel. Squads spending limits removed as a DR-47 tool (section 5; `spending_limit_use.rs:90-100, 148-168` confirmed; F12) |
| D-realms-spl-governance-12 | accepted | Status-list row: the verifier requires an inclusion proof per changed index, otherwise `unverified` with the previous state kept; at most 1 custodian co-signer. Fault F10 |
| D-realms-spl-governance-13 | accepted | Section 2 delegation row (`process_set_governance_delegate.rs:26-28`, `token_owner_record.rs:124-141`, `vote_record.rs:96-112`, all confirmed). Section 5 DR-45 and T13 rows; section 6 and section 12 binding-vote blockers; checklist row "Delegation policy"; fault F9; 11B (iii) |
| D-realms-spl-governance-14 | accepted | Section 13: demonstrated_maturity 3 -> 2. The rated tree ran on mainnet only 2026-03-10 to 2026-04-30 (new RPC timeline), with no release or tag and no green CI. Weighted total 2.70 -> 2.54 |
| D-realms-spl-governance-15 | accepted | Section 2: "heavy" replaced by the reviewer's figure (about 60 tx/day, range 7-145, 23 failed in the window), labelled [reported]. Realm count stays an open question |
| D-realms-spl-governance-16 | accepted | Section 2: 16 tests (`grep -c "async fn test_" tests/use_veto_vote.rs` = 16), also in section 13 |
| D-realms-spl-governance-17 | accepted | TUF root row aligned with D4.0 (3)(a): security revocation suspends with notice and override; policy delisting never disables; reason class logged |
| D-realms-spl-governance-18 | accepted | Checklist status column: every row "designed, not yet tested" with the 11A fault id. Section 7 verifier marked likewise |
| D-realms-spl-governance-19 | accepted | Section 12: Tribeca and DAO DAO not inspected (Tribeca status unchecked; DAO DAO is CosmWasm, so G4); the article to state the limit |
| D-realms-spl-governance-20 | accepted | Section 5 I-11 row is now an option with its veto and capture consequences; two realms or disjoint governances as alternatives |
| D-realms-spl-governance-21 | accepted ([OK] row) | No change; it confirms section 8, the Squads diff and isolation_and_security 2 |
| X-1 | accepted | Coverage limit recorded (D-19). R25 and R27 stay uninspected, per D4.0 (2) |
| X-2 | accepted | Each primitive named in X-2 (hold-up, council removal, outside keys, Squads cancel, 2-of-3 co-sign, council tipping) now carries its full condition and a breaking fault (sections 5, 7, 8, 12; F3-F10) |
| X-3 | accepted | The checklist now enumerates on-chain authorities (upgrade, mint, freeze, realm authority, voting-mint, plugin configuration and upgrade, delegation policy, Squads configuration), off-chain trust roots (A11) and meta-authorities (custodian set, `authorities.json` signer) |
| X-4 | accepted | Insider rule stated above the checklist and applied per row. TUF targets and the log key are declared exceptions with the reason (bounded by root or by witnesses) |
| TM-R24-1 | lead's; reflected | Not edited in the threat model. The section 5 property table gains the row "Including authorities that set voter weights, the electorate or the quorum denominator: not demonstrated" |
| TM-R24-2 | lead's; reflected | Section 5 row "A veto or cancel reachable by a party other than the approving quorum"; insider rule in section 12 |
| TM-R24-3 | lead's; reflected | Section 5 T13 and DR-45 rows (delegation). No score change |

Score changes: demonstrated_maturity 3 -> 2. Weighted total 2.70 -> 2.54 (dao_contract weights from `scoring/weights.json`). Hard gates unchanged. Reuse decision unchanged (reference only for Phase 1; investigate further for Phase 2). Binding votes on Realms now carry explicit blockers (section 12).
