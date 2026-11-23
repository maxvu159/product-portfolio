# Epics and User Stories: Queued Withdrawals

Estimates are story points (Fibonacci). Priority follows the PRD's MoSCoW list.
Sprint slicing at the end.

## Epic 1: Withdraw (user)

### 1.1 Instant withdrawal within the buffer · Must · 3 pts
As a depositor, I want small withdrawals to complete at once, so that I don't wait when the vault has cash.

- **Given** my withdrawal is ≤ the instant threshold, **when** I confirm, **then** assets arrive in one transaction and no request is created.
- **Given** the buffer drops below my amount between quote and confirm, **when** I confirm, **then** the app turns it into a queued request and tells me why before I sign.

### 1.2 Request a queued withdrawal · Must · 5 pts
As a depositor, I want to request a withdrawal larger than the buffer, so that I can exit without contacting the team.

- **Given** my amount is above the threshold, **when** I open the withdraw form, **then** I see "Queued withdrawal" with an ETA range before confirming.
- **Given** I confirm, **then** my shares are locked, stop earning, and the request shows status *Pending* with my queue position.

### 1.3 See request status · Must · 3 pts
As a depositor, I want a status page for my requests, so that I know when I can claim.

- **Given** I have requests, **when** I open Portfolio → Withdrawals, **then** each shows amount, status, ETA range and submitted time.
- **Given** a request has passed its latest ETA by 48 h, **then** a banner explains the delay and links to Support.

### 1.4 Claim fulfilled funds · Must · 3 pts
As a depositor, I want to claim once fulfilled, so that funds reach my wallet.

- **Given** status is *Claimable*, **when** I click Claim, **then** I receive assets valued at the price locked at fulfilment.
- **Given** the vault is paused, **when** I claim an already-fulfilled request, **then** the claim still succeeds.

### 1.5 Cancel a pending request · Should · 2 pts
- **Given** status is *Pending*, **when** I cancel, **then** my shares unlock and earn again.
- **Given** status is not *Pending*, **then** Cancel is disabled with a tooltip explaining why.

### 1.6 Claimable notification · Should · 3 pts
- **Given** I opted in, **when** my request becomes *Claimable*, **then** I get an in-app notice and an email/Telegram message within 5 minutes.

## Epic 2: Operate the queue (operations)

### 2.1 Queue dashboard · Must · 5 pts
As an operator, I want to see all pending requests and daily totals, so that I can plan unwinds.

- **Given** pending requests exist, **then** the dashboard lists them FIFO with amount, age and ETA, plus totals per day.

### 2.2 Propose a fulfilment batch · Must · 5 pts
- **Given** freed liquidity of X, **when** I select "Build batch", **then** the system selects the oldest requests that fit X, never skipping an older one.
- **Then** a multisig transaction is prepared with a human-readable summary (count, total, price).

### 2.3 Execute batch via multisig · Must · 3 pts
- **Given** 4 of 7 signers approve, **when** executed, **then** included requests become *Claimable* at the current price and users are notified.

### 2.4 Seven-day withdrawal forecast · Could · 5 pts
- **Given** queue and historical data, **then** the dashboard shows expected withdrawals for the next 7 days with a confidence band.

## Epic 3: Safety and support

### 3.1 Emergency pause · Must · 3 pts
- **Given** a guardian or the multisig pauses, **then** new requests and fulfilment stop, claims stay open, and the app shows a pause banner.

### 3.2 Support lookup · Should · 2 pts
- **Given** a wallet address, **when** Support searches it, **then** they see every request with status and timeline, read-only.

## Sprint slicing (2-week sprints)

| Sprint | Stories | Points | Outcome |
|---|---|---|---|
| 1 | 1.1, 1.2, 2.1 | 13 | Requests can be created and seen by ops |
| 2 | 1.3, 1.4, 2.2, 2.3 | 14 | Full request → claim loop on testnet |
| 3 | 3.1, 1.5, 1.6, 3.2 | 10 | Safety + quality of life; ready for UAT |
| Later | 2.4 | 5 | After launch KPIs review |
