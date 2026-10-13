---
name: resume-bullet-point-checker
description: Resume bullet point checker and action verbs - check resume bullet points line by line for action verbs, numbers, pronouns and phrases to delete, and get action verbs by type of work from the ResumeNext guide. Trigger on intents like "check my resume bullets", "make my bullet points stronger", "action verbs for", "what to write instead of responsible for", "检查我的简历要点", "简历动词", "优化工作经历描述". Not for scoring a whole resume (that is the Resume Builder & ATS Checker connector), cover letters or job search.
---

# Resume Bullet Point Checker & Action Verbs

ResumeNext adds what the chat cannot do by itself: fixed rules and libraries applied to the user's real text, and links that open the work in ResumeNext. No account, no sign-in and no API key are needed.

## Connect in Manus

1. In Manus open Settings, then Connectors, and choose Create, then "Add MCP by URL".
2. Server URL: `https://resumenext.io/api/manus/bullet-points/mcp`. Or choose "Import MCP by JSON" with:

```json
{"mcpServers": {"resume-bullet-point-checker": {"url": "https://resumenext.io/api/manus/bullet-points/mcp"}}}
```

3. Save. Manus shows "OAuth not required": no account and no sign-in are needed.
4. "Try it out" opens a new task with the connector on; in other tasks, turn it on from the connector icons in the chat input.

## When to use

- The user shares resume bullet points and wants them checked or made stronger.
- The user asks which action verbs to use for a type of work, or what to write instead of a weak phrase.

## When NOT to use

- Scoring a whole resume (that is the Resume Builder & ATS Checker connector), cover letters or job search: answer directly without calling the tools.
- Never call the tools with an excerpt, a summary or your own critique in place of the user's text.

## Tools (server `resume-bullet-point-checker`)

| Tool | Writes anything | Fields and purpose |
|---|---|---|
| `check_resume_bullets` | no | `bullets` (required). Checks resume bullet points one line at a time against ResumeNext's action verb guide and the line checks of its ATS score. For every line it returns the opening word, whether that word is an action verb, whether the line opens with a gerund or with a phrase to delete (Responsible for, Worked on, Helped with, Assisted with, Duties included), whether it carries a number, a percentage or an amount, whether it uses the first person, its word count and what to change, most important first. Also returns the totals, whether at least a third of the lines carry a number, and the lines to rewrite first. Deterministic. Stores nothing. |
| `find_action_verbs` | no | `work_type`. Returns the action verbs of ResumeNext's guide for one type of work (leadership: leadership and ownership; building: building, engineering, and shipping; process-improvement: process improvement and efficiency; analysis: analysis and research; finance: budget, finance, and cost control; communication: communication, writing, and persuasion; sales: sales, accounts, and customer work; teaching: teaching, training, and coaching; care: care, clinical, and health services), each group with its usage note, plus the phrases to delete from a resume and the rules for picking a verb without inflating it. Without a type of work, returns every group. Stores nothing. |

## Flow

1. Check: call `check_resume_bullets` with the bullets, one per line, as the user wrote them (up to 60 lines). Report the totals and the lines to rewrite first with their fixes.
2. Verbs: call `find_action_verbs` with the `work_type` that fits the role (or without it for every group).
3. Rewrite the weak lines with the user facts only: no invented numbers. Then call `check_resume_bullets` again on the new lines.

## Errors returned by the tools and how to handle them

- "<field> is required and must not be empty": ask the user for it, then call again.
- "<field> exceeds ...": ask the user for a shorter version; never cut the content silently.
- "<field> must be one of: ...": use one of the listed values.
- "Too many requests from this client right now. Wait a minute and try again.": wait about a minute, then retry once.
- "Unexpected argument": only the fields listed above are accepted.

## Rules

- The tools take plain text: read attached files and pass their full text.
- Same input, same result: the tools apply fixed rules, so report what they return and never invent a score, a count or a term.
- Use only facts the user supplied when rewriting: no invented employers, dates, metrics or skills.
