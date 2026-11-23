# How I Work with AI Agents

I use Claude Code as a daily product tool: agents do the legwork, I own the
decisions. This is the workflow behind the artefacts in this repo.

| Stage | What the agents do | What I decide / check |
|---|---|---|
| Discovery | Parallel research agents read competitor docs, public standards (e.g. ERC-7540) and user threads; each returns sourced notes | Which problems matter, which patterns fit our users |
| Spec | Drafting agent turns my outline into PRD sections and business rules from a fixed template | Scope, priorities, every business rule |
| Flow | Agent drafts the BPMN/state diagrams from the business rules | Exceptions and edge cases the draft missed |
| Stories | Agent splits epics into stories with Given/When/Then criteria | Slicing, estimates with engineers, sprint plan |
| Review | A separate review agent, given only the spec (not my reasoning), looks for gaps, contradictions and untestable criteria | Which findings are real; final sign-off |
| Mockup | Agent builds a clickable mockup in the team's codebase as a draft PR | UX decisions; engineers review working screens instead of static images |
| QA / ops | Scheduled agents check data against the source of truth (e.g. reported vs on-chain values) and alert on drift | Thresholds; what counts as a defect |

## Principles

- **Human in the loop for decisions.** Agents propose; anything that changes scope, money or permissions is my call.
- **Independent review.** The reviewer agent never sees the author's reasoning, so it does not just confirm it.
- **Evals over vibes.** Automated outputs are validated against a source of truth before anyone relies on them.
- **Reusable skills.** Repeated processes (reporting, lead tracking, charts) become versioned agent skills, not one-off prompts.

Tools: Claude Code, multi-agent orchestration, custom skills, MCP servers, scheduled agents, Python, GitHub.
