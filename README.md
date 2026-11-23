# Product Portfolio · Hai Minh Vu

Sample product work for Product Owner / Product Manager / Business Analyst roles.

The case below is a **fictional product** ("Northwind Vault", a stablecoin yield
vault). It uses only public standards and made-up numbers, and contains no
information from any employer. It shows how I take one feature from problem to
release: the same artefacts I write in my day job.

## Case: Queued withdrawals for a yield vault

**Problem.** Funds in a yield vault are deployed into strategies, so the vault
cannot always pay every withdrawal instantly. Users who hit "Withdraw" and get
an error or an unexplained wait open support tickets and leave.

**Feature.** An asynchronous withdrawal queue (based on the public
[ERC-7540](https://eips.ethereum.org/EIPS/eip-7540) standard): users request a
withdrawal, see a clear estimated time, and claim once operations has freed the
funds.

| # | Artefact | What it shows |
|---|---|---|
| 1 | [PRD](01-prd-queued-withdrawals.md) | Problem, personas, goals and KPIs, scope, MoSCoW requirements, business rules, permission matrix, risks, release plan |
| 2 | [Process flow (BPMN-style)](02-process-flow.md) | Swimlane flow across User, App, Smart contract and Operations; state machine of a request |
| 3 | [Epics and user stories](03-user-stories.md) | 3 epics, 12 stories with Given/When/Then acceptance criteria, priority and estimate |
| 4 | [UAT plan](04-uat-plan.md) | Test cases, roles, entry/exit criteria, live-money pilot, defect triage |
| 5 | [How I work with AI agents](05-ai-assisted-workflow.md) | Where agents speed up discovery, specs and QA, and where a human stays in the loop |

## Skills this demonstrates

- Product discovery: problem framing, personas, competitor patterns, success metrics
- Requirements: PRD, functional specs, business rules, permission matrix, acceptance criteria
- Process modeling: BPMN-style swimlanes, state machines
- Agile delivery: epics, user stories, prioritisation (MoSCoW), estimates, sprint slicing
- Quality: UAT planning, risk-based testing, release gates
- AI-assisted product work: agentic workflows with Claude Code, review agents, human-in-the-loop checks

Contact: [t.me/Max_zooted](https://t.me/Max_zooted)
