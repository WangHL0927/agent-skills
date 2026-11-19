---
name: stakeholder-review
description: Multi-stakeholder review. Dispatch one independent agent per real stakeholder of a decision to investigate and propose (or review an existing proposal); an independent arbiter agent consolidates, runs further rounds if needed, and decides; the main session only coordinates. Use when the user explicitly asks for multiple parties / agents / models to work independently from different stakeholder positions (e.g. "get the product team's take", "each stakeholder proposes a plan", "multi-agent discussion", "council"). Not for single-party plan or code review, purely technical checks with no differing stakeholder interests, or questions with a clear answer. If the user hasn't asked but the decision clearly involves trade-offs between stakeholders, ask first.
---

# Stakeholder Review

Three roles:

- **Main session = coordinator**: scopes the decision, writes the brief, dispatches seats and the arbiter, relays and hands over. Its conversation context is noisy and biased, so it does not arbitrate, rewrite, or veto the arbiter's requests. Any disagreement it has goes in a separate, labeled "coordinator's note" after the report.
- **Seats**: each investigates from its own stakeholder position and produces a proposal (or review).
- **Arbiter agent**: fresh context, strong model. Receives only the decision to be made and the minimum starting information, and fills in the rest by verifying the current state itself. Consolidates, decides whether another round is needed, and makes the call.

The value comes from independent investigation and real evidence, not role-play.

**Handoffs** (all steps): pass simple content with the current harness's built-in dispatch / messaging / resume tools; write complex content (proposals, consolidation drafts, reports) to files. When the tools can't resume an agent or a handoff fails, fall back to files.

- **Run directory**: the coordinator creates one directory per review, in the project or another location every participating agent can read and write (not a session-private temp directory), and passes its absolute path.
- **Ownership**: every file is named by owner and round — `brief.md` (coordinator), `arbiter-brief-check.md`, `<seat>-r<N>.md`, `arbiter-consolidation-r<N>.md`, `arbiter-decision.md` — and starts with a line `owner: <name> · round: <N>`. Each agent writes only its own files. Seats never read other seats' files (not at all in round 1; later only what the arbiter passes them). The arbiter may read everything.
- **If a write is refused**: the agent returns the full content inline; the coordinator saves it verbatim under that agent's filename, with the first line noting "saved by coordinator, unchanged".

Communicate with the user, and write the final report, in the user's language.

## 0. Scope

- State in one sentence what is being decided, what is not, and who makes the final call (anything that is the user's call goes back to the user).
- Pick the mode: **propose** (from scratch) or **review an existing proposal**. In review mode, the proposal text is given as the object under review; in propose mode, no draft is given. In both modes, the coordinator's opinion of any proposal is withheld.
- List where to verify: where the current implementation lives (code, contracts, config), and where to check usage frequency and issue volume (time-bounded audit logs, tickets, monitoring). Mark anything unavailable as "unknown". The coordinator provides pointers and raw data only — no interpretation, no conclusions on behalf of seats or the arbiter.

## 1. Seats

- Seats come from the decision's **real stakeholders**: for product decisions, the actual user groups; for architecture, maintainers, callers, operations, security, whoever bears the cost, etc. Any role the user names must be included.
- At least one independent agent per role. Merge roles only when both their interests and their acceptance criteria are identical. No dedicated devil's-advocate seat.
- Models: when possible, prefer different vendors and different agents; otherwise use different tiers of the same agent and mark them "same-source" in the report.

## 2. Brief (one shared brief for all seats and the arbiter)

Give facts, not conclusions:

- Include: the question to decide, the mode, the user's words verbatim (a coordinator draft the user quoted back doesn't count), fixed constraints, where to verify, known data and gaps.
- Exclude: the coordinator's drafts, leanings, and evaluations of prior work. Prior work is described only as what was done and where.
- Role descriptions say only "who this seat represents and what they do day to day", never what it should conclude.
- Before dispatching, reread the brief and remove every "should / recommend / proposal"-type phrasing other than the user's own words.
- Write the brief to a file; each seat's prompt adds only its own role paragraph.

**Brief check before dispatch**: the coordinator first dispatches the arbiter agent (fresh context) with only the brief, the seat list with role paragraphs, and the user's words. The arbiter checks for leading language, unstated assumptions, missing facts it can verify itself, and missing stakeholders, and returns concrete edits in `arbiter-brief-check.md`. The coordinator applies them as written (no vetoes) and records them at the end of the brief. The same arbiter is used for step 4; if it can't be resumed, the new arbiter reads `arbiter-brief-check.md` first.

## 3. Round 1: isolated and parallel

Dispatch all seats at once in fresh contexts (not inheriting the coordinator's context, so no draft leaks in); seats can't see each other until all are in. Each seat: read-only except for its own output file; investigate before speaking; length as the task requires. Return short output directly; write long output to its own file and return a summary plus the path. A seat may dispatch its own sub-agents for research; they work only for that seat and follow the same file rules, and anything they find counts as verified only after the seat has checked the original source. The output must contain:

1. The real scenarios and pain points from this position
2. The proposal or review (what to do / change, and why)
3. What to do first (ranked) and what explicitly not to do
4. **Acceptance criteria**: what the plan must look like for this seat to accept it
5. Every conclusion tagged **[verified]** (with file / command / source) or **[inferred]**; distinguish implementation evidence from usage-frequency evidence. Claims about the current state without a source are void.

## 4. Hand off to the arbiter

The coordinator passes every seat's original output (full text or path) to the arbiter from the brief check. The arbiter reads the full outputs, not just summaries, and:

- Re-checks the final brief for anything leading that slipped through; flags it, and disregards whatever was led. Treats data supplied by the coordinator as leads only and re-verifies anything decisive (it may use sub-agents for this, under the same source-check rule).
- Writes a consolidation draft: consensus, conflicts, blind spots no seat raised, missing evidence — facts and disagreements only, no ruling.
- Returns to the coordinator: the consolidation draft, plus either "decide" or a "next-round request" (which seats, which points, which facts to verify, and the drafted prompts).

## 5. Further rounds

- The arbiter requests another round only when disagreements, blind spots, or unverified facts could change the ruling; otherwise it decides directly. No rounds for the sake of process.
- The coordinator sends the arbiter's drafted prompts to the named seats verbatim (no cuts, rewrites, or vetoes) and returns the replies verbatim to the **same** arbiter agent. If it can't be resumed, a new arbiter first reads the previous consolidation draft file.
- Next-round input contains only the relevant parts of the consolidation draft (each side's position, evidence, shared facts) — **never a preliminary ruling**. When a disagreement hinges on facts, dispatch a verification task instead of another debate.
- Seats must: name the new evidence or reasoning behind any change of position; when holding a position, answer the other side's strongest argument; and check whether the consolidation draft misreads or omits their side's interests.
- Stop when the arbiter judges there is no new evidence or reasoning that could change the ruling.

## 6. Decision and report

The arbiter writes the report (as `arbiter-decision.md` when large). When handing over, the coordinator's summary is taken verbatim from the report's conclusion section, unchanged. The report contains:

- The decision (what, for whom, what first, what explicitly not)
- Conflicts and the reasoning behind each ruling; places where the majority was overruled are listed separately
- What evidence would overturn the decision; missing evidence and how to obtain it
- Items that need the user's decision
- What the review contributed: new evidence and viewpoints and their effect on the ruling, what is only inferred; how many rounds ran and which models were used (same-source marked)
