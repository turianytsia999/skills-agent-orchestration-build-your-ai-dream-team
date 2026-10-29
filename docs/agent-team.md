# Agent team

I am using GitHub Copilot CLI in a Codespace to orchestrate the custom-agent team building Mona's Project Pulse dashboard.

| Agent | Target model | Responsibility | Definition |
|---|---|---|---|
| Orchestrator | Claude Opus 5.5 (copilot) | Coordinates the team from Copilot CLI: requests the plan, assigns explicit and non-overlapping file scopes, sequences dependent work, and verifies the integrated dashboard. | `.github/agents/orchestrator.agent.md` |
| Planner | Claude Opus 5.5 (copilot) | Researches the repository and relevant documentation, then produces an implementation plan with dependencies, risks, edge cases, validation expectations, and file assignments. | `.github/agents/planner.agent.md` |
| Designer | Gemini 3.7 Flash (copilot) | Owns Project Pulse UI/UX direction and styling, including accessibility, responsive layout, visual hierarchy, project cards, status badges, and priority treatment. | `.github/agents/designer.agent.md` |
| Coder | GPT-5.5 (copilot) | Implements the assigned code with explicit errors and testable behavior; for runnable Project Pulse work, it can also create deterministic launch support so the dashboard opens directly. | `.github/agents/coder.agent.md` |

The Orchestrator obtains a plan first, then delegates design and implementation in parallel only when their assigned files do not overlap; dependent work is sequenced and integrated before completion.
