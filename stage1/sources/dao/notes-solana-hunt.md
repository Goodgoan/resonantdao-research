# Notes: Solana reality check (s1-dao)

Fetched/queried: 2026-10-20 (UTC evening of 2026-10-19 per GitHub API timestamps). Evidence type: inspected (public RPC reads, static file reads). No code executed except my own read-only query scripts (python -I, scratchpad).

## Public RPC endpoints used
https://api.devnet.solana.com, https://api.testnet.solana.com, https://api.mainnet-beta.solana.com (getBalance, getAccountInfo jsonParsed, getSignaturesForAddress limit 1000, getTransaction sampled). Public devnet rate-limited (HTTP 429 once; retried slowly).

## Addresses found and results

| Address | Where quoted | Cluster result (2026-10-20) |
|---|---|---|
| AaJvB1mpVixTxipTCf6kJoj3uX5hmDTyaYSsyAJhtc2F | resonant-hub backend/dao_mint.py L18; manolo-register-patch/README.md ("DAO Authority Wallet") | devnet/testnet/mainnet: balance 0, 0 signatures. Never used on any cluster in retained history. |
| 2z2GEVqhTVUc6Pb3pzmVTTyBh2BeMHqSw1Xrej8KVUKG ($RCT) | Sanmarcsoft/resonantos-alpha dashboard/server_v2.py L64 | devnet: Token-2022 mint, extension nonTransferable, decimals 9, supply 1001139000001000 raw (~1,001,139 RCT), mintAuthority HtZyHfnJHFEYwstNhSd6ekPX9SL6DynEDix57ViULzat, freezeAuthority none; 118 signatures 2026-02-17..2026-04-17. Sampled txs invoke only Token-2022, ATA, System, ComputeBudget. Not found on testnet or mainnet. |
| DiZuWvmQ6DEwsfz7jyFqXCsMfnJiMVahCj3J5MxkdV5N ($RES) | same file L65 | devnet: SPL Token (legacy) mint, decimals 6, supply 1000008000000000 raw (= 1,000,008,000 RES), mintAuthority HtZyHf..., freezeAuthority vbYQ7r...; 22 signatures 2026-02-17..2026-02-28. Not on testnet/mainnet. |
| 7Zxr6W..., zwwrrG..., 7sybHS..., 8HQF2j..., 9V4oLe... ($REX-GOV/FIN/COM/CRE/TEC) | same file L73-78 | devnet: Token-2022 mints, nonTransferable, decimals 0, supply 0, mintAuthority vbYQ7r...; 1 signature each, 2026-02-17. |
| J77vwN... (manifesto), CiDQqQ... (identity), 7Zi9vx... (alpha) | Sanmarcsoft/resonantos-alpha data/nft_registry.json | devnet: Token-2022, nonTransferable, decimals 0, supply 1, mintAuthority vbYQ7r...; 3 sigs each, 2026-02-17/18. |
| vbYQ7rZu19Rjtro9obQxFeHq5UPNF5RQXA8jP8qywfF | derived from mint/freeze authority fields | devnet: System-owned wallet, ~25.04 SOL, >=1000 signatures (window 2026-02-19..2026-03-18). Not on mainnet. Upgrade authority of program below. |
| HtZyHfnJHFEYwstNhSd6ekPX9SL6DynEDix57ViULzat | mint authority of RCT and RES | devnet: no account (signing keypair or PDA with no data; not determined). |
| HMthR7AStR3YKJ4m8GMveWx5dqY3D2g2cfnji7VdcVoG | found in a sampled transaction of vbYQ7r... | devnet: upgradeable BPF program (owner BPFLoaderUpgradeable), programData 52g38vMCN5CG4zjFuEcyxSgibtfDD8V9ZZKqo1hd3BQG, upgrade authority vbYQ7r...; 38 signatures 2026-02-18..2026-03-20. Not on mainnet. Source code, IDL and verifiable build NOT found. Attribution to "Symbiotic" Anchor program in server_v2.py (seeds [b"symbiotic", human, 0], discriminator sha256("global:transfer_out")) is a hypothesis (shared authority wallet), confidence medium. Anchor IDL account not checked. |

## Attribution of the legacy artifacts
- Sanmarcsoft/resonantos-alpha (not a GitHub fork; description "building the DAO of AI"; pushed 2026-04-04; last commits 2026-02-19) README instructs `git clone https://github.com/ManoloRemiddi/resonantos-alpha.git`. `gh api repos/ManoloRemiddi/resonantos-alpha` -> 404 on 2026-10-20 (deleted, renamed or private). Legacy OpenClaw-era ResonantOS alpha: "Symbiotic Wallet | Three-wallet Solana architecture (Human + AI + Symbiotic PDA)"; "Token Economy | $RCT (soulbound governance) + $RES (transferable currency) + REX sub-tokens". Solana toolkit imported from `~/resonantos-augmentor/solana-toolkit` (nft_minter, token_manager, wallet) is NOT in the repo.
- Confidence that the devnet mints are ResonantOS artifacts: medium (addresses appear only in that legacy code; mint metadata names not read).

## GitHub searches (gh api search/code unless noted), 2026-10-20
| Query | Total | Relevant result |
|---|---|---|
| ResonantDAO | 161 | websites, economy-research docs, community repos (simongonzalezdc/resonant-authority, resonant-gifts, vonstegen/resonant-vcs). No program code. |
| "Resonant Contribution Token" | 1 | simongonzalezdc/resonant-gifts README (glossary only) |
| ResonantDAO declare_id | 0 | - |
| ResonantDAO anchor | 18 | economy-research prose only |
| "Human Community Member" | 99 | unrelated projects |
| "Augmentatism Manifesto" NFT | 3 | Sanmarcsoft/resonantos-alpha (server_v2.py, wallet.html, README) |
| AaJvB1mpVixTxipTCf6kJoj3uX5hmDTyaYSsyAJhtc2F | 0 | (code search index does not cover the hub file) |
| ResonantDAO language:Rust | 0 | - |
| ResonantDAO language:TypeScript solana | 0 | - |
| "Resonant Pioneer" | 3 | unrelated |
| MemberRecord Manifesto | 54 | unrelated |
| search/repositories resonantdao | 3 | ManoloRemiddi/ResonantDAO.com (website), vijayee/resonantdao-example, simongonzalezdc/resonant-authority |
| search/repositories "resonant dao solana" | 0 | - |
- `gh api orgs/ResonantDAO/repos` -> [] (public_repos 0, org created 2026-06-04).
- `gh api repos/ManoloRemiddi/ResonantDAO` -> 404. The whitepaper section 20 links three docs in this repo (dao-nft-membership.md, dao-governance-mechanics.md, smart-contract-roadmap.md): inaccessible.
- ManoloRemiddi/kora is a fork of solana-foundation/kora (Solana relayer), untouched since 2026-02-02: no ResonantDAO code.
- Rate limit hit once (search/code 10/min); queries resumed after reset.

## Pages checked for program IDs / mints
whitepaper, contribution-economy, start, build, home, dao, community, learn, join/* : no program ID, mint address, cluster address or explorer link quoted (grep of saved snapshots).

## Result
No mainnet or testnet deployment found. Legacy devnet artifacts (Feb-Apr 2026) exist for a superseded token model (RCT as minted soulbound token, REX sector tokens, RES SPL mint) and one unverified upgradeable devnet program with single-key upgrade authority. Nothing found for the v0.6 design (Human/AI member exclusivity, sponsor link, Symbiotic Licence gating, Pioneer cap, 22 balances).
