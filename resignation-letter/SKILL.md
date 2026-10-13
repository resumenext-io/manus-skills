---
name: resignation-letter-generator
description: Resignation letter generator - write a resignation letter in a standard, formal or grateful style from the user's details and open it in ResumeNext to edit and save as PDF, DOCX or TXT. Trigger on intents like "write my resignation letter", "two weeks notice letter", "formal resignation letter", "how do I resign politely in writing", "写一封辞职信", "辞职信模板", "正式的离职信". Not for cover letters, resumes, legal or HR advice about notice periods or contracts.
---

# Resignation Letter Generator

ResumeNext adds what the chat cannot do by itself: fixed rules and libraries applied to the user's real text, and links that open the work in ResumeNext. No account, no sign-in and no API key are needed.

## Connect in Manus

1. In Manus open Settings, then Connectors, and choose Create, then "Add MCP by URL".
2. Server URL: `https://resumenext.io/api/manus/resignation-letter/mcp`. Or choose "Import MCP by JSON" with:

```json
{"mcpServers": {"resignation-letter-generator": {"url": "https://resumenext.io/api/manus/resignation-letter/mcp"}}}
```

3. Save. Manus shows "OAuth not required": no account and no sign-in are needed.
4. "Try it out" opens a new task with the connector on; in other tasks, turn it on from the connector icons in the chat input.

## When to use

- The user is leaving a job and wants a resignation or notice letter.
- The user wants the letter in a different tone, or a link to edit and save it.

## When NOT to use

- Cover letters, resumes, legal or HR advice about notice periods or contracts: answer directly without calling the tools.
- Never call the tools with an excerpt, a summary or your own critique in place of the user's text.

## Tools (server `resignation-letter-generator`)

| Tool | Writes anything | Fields and purpose |
|---|---|---|
| `write_resignation_letter` | no | `company` (required), `last_day` (required), `style`, `your_name`, `role`, `manager_name`. Writes a resignation letter from the user's details in one of three styles: standard (Professional and neutral. The safe default for most resignations.) formal (Short, on the record, notice-period wording. For HR files and strict processes.) grateful (Warm and personal. For a manager and a team you want to keep in your network.) Returns the complete letter text and a link that opens the same letter in ResumeNext, ready to edit and save as PDF, DOCX or TXT. Template-based and deterministic. Stores nothing. |

## Flow

1. Ask for the employer (`company`) and the last working day (`last_day`) if missing; the name, role and manager are optional.
2. Call `write_resignation_letter` with the style the user wants (`standard`, `formal` or `grateful`; default `standard`). Show the letter as returned.
3. Share `editor_url`: it opens the same letter in ResumeNext to edit and save as PDF, DOCX or TXT. To change the tone, call the tool again with another style.

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
