# Process Flow: Queued Withdrawals

BPMN-style swimlane flow. In the specs I deliver, the same flow is drawn in a
BPMN tool; here it is in Mermaid so GitHub renders it.

## Swimlanes: request to claim

```mermaid
flowchart LR
    subgraph U[User]
        U1([Start: wants to withdraw]) --> U2[Enter amount]
        U4[Review ETA and confirm]
        U7[Receive 'claimable' notice] --> U8[Claim funds]
        U8 --> U9([End: funds in wallet])
    end

    subgraph A[App / Backend]
        A1{Amount ≤ instant threshold?}
        A2[Show ETA range + queue position]
        A6[Send notification]
    end

    subgraph C[Smart contract]
        C1[Instant redeem]
        C2[Create request, lock shares]
        C5[Mark batch claimable at locked price]
        C6[Transfer assets]
    end

    subgraph O[Operations]
        O1[Review queue daily]
        O2[Unwind strategies FIFO]
        O3[Propose batch → multisig executes]
    end

    U2 --> A1
    A1 -- Yes --> C1 --> U9
    A1 -- No --> A2 --> U4 --> C2 --> O1 --> O2 --> O3 --> C5 --> A6 --> U7
    U8 --> C6
```

## State machine of a request

```mermaid
stateDiagram-v2
    [*] --> Pending: request created, shares locked
    Pending --> Cancelled: user cancels
    Pending --> Claimable: batch fulfilled (price locked)
    Pending --> Paused: emergency pause
    Paused --> Pending: unpause
    Claimable --> Claimed: user claims
    Cancelled --> [*]
    Claimed --> [*]
```

## Exceptions handled

| Exception | Where | Handling |
|---|---|---|
| Buffer drops between quote and confirm | App → Contract | Contract re-checks; falls back to a request, user sees why |
| ETA exceeded by 48 h | Operations | Support alert + in-app banner (PRD rule 5) |
| Emergency pause | Contract | New requests and fulfilment stop; fulfilled claims stay open |
| User cancels after batch proposed | Contract | Cancel rejected if status ≠ Pending; app explains |
