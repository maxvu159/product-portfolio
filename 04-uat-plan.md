# UAT Plan: Queued Withdrawals

## Entry and exit criteria

| | Criteria |
|---|---|
| Entry | Sprints 1–3 merged; contracts on testnet; test wallets funded; dashboard live |
| Exit (to pilot) | All P1 cases pass; no open P1/P2 defects; Support playbook reviewed |
| Exit (to launch) | 5-day mainnet pilot with zero P1 defects and ETA hit rate ≥ 90% |

## Roles

| Role | People | Does |
|---|---|---|
| Test lead | PM | Owns plan, triage, sign-off |
| Testers | 5 (product, ops, support, 2 external users) | Run cases, log defects |
| Engineering | On call | Fix, redeploy, explain |
| Multisig signers | 4 of 7 | Execute fulfilment batches |

## Test cases

| ID | Story | Scenario | Expected | Priority |
|---|---|---|---|---|
| UAT-01 | 1.1 | Withdraw below threshold | Instant, one transaction, no request | P1 |
| UAT-02 | 1.1 | Buffer drops between quote and confirm | Falls back to request with explanation | P1 |
| UAT-03 | 1.2 | Withdraw above threshold | Request created, shares locked, ETA shown | P1 |
| UAT-04 | 1.3 | Status page with 3 requests in different states | Correct status, ETA, timestamps | P1 |
| UAT-05 | 2.2 | Build batch with liquidity for 2 of 3 requests | Picks the 2 oldest, never skips | P1 |
| UAT-06 | 2.3 | Execute batch with 4/7 signatures | Requests become Claimable, users notified | P1 |
| UAT-07 | 1.4 | Claim after price moved since request | Paid at price locked at fulfilment; copy explains | P1 |
| UAT-08 | 1.5 | Cancel pending / cancel claimable | First succeeds; second blocked with tooltip | P2 |
| UAT-09 | 3.1 | Pause, then try request, fulfil, claim | Request and fulfil blocked; claim works | P1 |
| UAT-10 | 1.6 | Notification timing | Arrives within 5 minutes | P2 |
| UAT-11 | 3.2 | Support looks up a wallet | Full timeline, read-only | P3 |

## Mainnet pilot (live money)

- 5 testers, each deposits up to $1K, for 5 days.
- Day 1 deposit · Day 2 instant + queued withdrawals · Day 3 batch fulfilment and claims · Day 4 pause drill · Day 5 wrap-up and sign-off.
- Daily 15-minute check-in; results logged against the case table.

## Defect triage

| Severity | Definition | Response |
|---|---|---|
| P1 | Funds at risk, wrong amount, or flow blocked | Stop pilot, fix before continuing |
| P2 | Wrong status/ETA/copy that misleads a user | Fix before launch |
| P3 | Cosmetic or internal-only | Backlog |
