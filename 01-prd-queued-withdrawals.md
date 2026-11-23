# PRD: Queued Withdrawals

| | |
|---|---|
| Product | Northwind Vault (fictional stablecoin yield vault) |
| Owner | Product Manager |
| Status | Approved for build · v1.0 |
| Stakeholders | Engineering (contracts, backend, frontend), Operations, Risk, Support, Marketing |

> All numbers in this document are illustrative.

## 1. Problem

Northwind Vault deploys ~85% of deposits into yield strategies and keeps ~15%
as an idle buffer. When a withdrawal is larger than the buffer, the current
instant-withdraw call fails.

- 6.2% of withdrawal attempts failed last quarter; 41% of support tickets were about withdrawals.
- Users who saw a failed withdrawal were 3x more likely to fully exit within 30 days.
- Operations frees funds manually, with no queue or priority, so large exits cause
  ad-hoc unwinds at bad prices.

## 2. Goal and success metrics

Make every withdrawal predictable, without forcing the vault to hold more idle cash.

| Metric | Baseline | Target (90 days after launch) |
|---|---|---|
| Failed withdrawal attempts | 6.2% | < 0.5% |
| Withdrawal-related support tickets | 41% of tickets | < 15% |
| Median time from request to claimable | n/a (manual) | ≤ 24 h |
| Requests fulfilled within the shown ETA | n/a | ≥ 95% |
| Idle buffer needed | 15% | ≤ 10% (higher yield for everyone) |

Guardrail: 30-day net deposit retention must not drop versus the previous quarter.

## 3. Personas

| Persona | Need | Pain today |
|---|---|---|
| **Retail depositor** ($500–$20K) | Know when money arrives | Random failures, no ETA |
| **Whale / fund** ($500K+) | Exit size without moving the price | Has to DM the team |
| **Vault operator** (Operations) | See demand early, unwind in order | Reacts to surprises |
| **Support agent** | Answer "where is my money?" in one look | No status to point to |

## 4. Scope

**In scope (v1)**
- Request withdrawal → pending → claimable → claimed, per ERC-7540
- ETA shown at request time and on a status page
- Instant path kept for requests that fit within the idle buffer
- Operator dashboard: queue, totals by day, fulfil in batches
- Notifications (in-app + email/Telegram opt-in) when claimable
- Cancel a pending request

**Out of scope (v1)**
- Partial fulfilment of a single request
- Priority fees / paying to jump the queue
- Secondary market for queued positions

## 5. Requirements (MoSCoW)

| ID | Requirement | Priority |
|---|---|---|
| R1 | A withdrawal that fits the idle buffer completes instantly in one transaction | Must |
| R2 | A larger withdrawal creates a request; shares are locked and stop earning from that moment | Must |
| R3 | The app shows an ETA before the user confirms, and the queue position after | Must |
| R4 | Operators fulfil requests first-in-first-out, in batches, through a multisig | Must |
| R5 | Users claim fulfilled requests at the price locked at fulfilment | Must |
| R6 | Users can cancel a request while it is still pending | Should |
| R7 | Notification when a request becomes claimable | Should |
| R8 | Operator dashboard forecasts the next 7 days of withdrawals | Could |
| R9 | Auto-claim to the user's wallet | Won't (v1) |

## 6. Business rules

1. **Instant threshold** = idle buffer − 2% safety margin, recalculated every block.
2. **Price lock.** A request is valued at the share price at fulfilment, not at request time. This stops users from gaming a falling price.
3. **FIFO.** Requests are fulfilled strictly in order; a batch may not skip an older request.
4. **ETA** = time to unwind the queued amount ahead of the user plus their own, using each strategy's published unwind time; shown as a range (e.g. "12–24 h").
5. **SLA breach.** If a request is not claimable 48 h after the latest ETA, Support is alerted and the user sees a banner with the reason.
6. **Cancel** returns the locked shares; allowed only while status = Pending.
7. **Emergency pause** stops new requests and fulfilment; claims of already-fulfilled requests stay open.

## 7. Permission matrix

| Action | User | Operator | Multisig (4/7) | Guardian |
|---|---|---|---|---|
| Request / cancel own withdrawal | ✓ | | | |
| Claim own fulfilled request | ✓ | | | |
| Propose a fulfilment batch | | ✓ | | |
| Execute a fulfilment batch | | | ✓ | |
| Change instant threshold / margin | | | ✓ (24 h timelock) | |
| Emergency pause | | | ✓ | ✓ |
| Unpause | | | ✓ | |

## 8. Dependencies and risks

| Risk | Impact | Mitigation |
|---|---|---|
| Strategy unwind slower than its published time | ETA missed, trust drop | ETA shown as a range; SLA alert (rule 5); weekly ETA accuracy review |
| Bank-run style mass exit | Queue grows faster than unwinds | Emergency pause; pre-agreed unwind order per strategy; comms template |
| Users misunderstand price lock | Complaints after a price drop | Plain-language copy + tooltip; tested in UAT (case UAT-07) |
| Multisig signers unavailable | Fulfilment delayed | 4/7 threshold, signer rota, 4 h response target |

## 9. Release plan

| Phase | Scope | Gate to next phase |
|---|---|---|
| 1. Internal testnet | All Must items | All UAT P1 cases pass |
| 2. Mainnet pilot | 5 internal testers, real funds, capped at $1K each | 5 days, zero P1 defects, ETA hit rate ≥ 90% |
| 3. Public launch | Everyone; instant path + queue | Metrics dashboard live; Support playbook signed off |
| 4. Iterate | Should/Could items | Review KPIs at day 30 and day 90 |

## 10. Open questions

- Q1. Should whales above a size get a dedicated unwind window? (Owner: PM + Risk, due before Phase 3)
- Q2. Do we show queue position publicly or only to the owner? (Owner: PM + Design)
