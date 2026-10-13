---
name: cover-letter-checker-builder
description: Cover letter checker and builder - review a cover letter against a fixed checklist, read example letters by role, then open the finished letter in the ResumeNext cover letter editor. Trigger on intents like "check my cover letter", "is this cover letter good", "cover letter example for", "open my cover letter in an editor", "帮我检查求职信", "求职信范例", "修改我的求职信". Not for resumes with no cover letter involved, resignation letters, job search or interview preparation.
---

# Cover Letter Checker & Builder

ResumeNext adds what the chat cannot do by itself: fixed rules and libraries applied to the user's real text, and links that open the work in ResumeNext. No account, no sign-in and no API key are needed.

## Connect in Manus

1. In Manus open Settings, then Connectors, and choose Create, then "Add MCP by URL".
2. Server URL: `https://resumenext.io/api/manus/cover-letter/mcp`. Or choose "Import MCP by JSON" with:

```json
{"mcpServers": {"cover-letter-checker-builder": {"url": "https://resumenext.io/api/manus/cover-letter/mcp"}}}
```

3. Save. Manus shows "OAuth not required": no account and no sign-in are needed.
4. "Try it out" opens a new task with the connector on; in other tasks, turn it on from the connector icons in the chat input.

## When to use

- The user shares a cover letter and wants it reviewed or improved.
- The user wants an example cover letter for a role, or the final letter opened in an editor to format and export it.

## When NOT to use

- Resumes with no cover letter involved, resignation letters, job search or interview preparation: answer directly without calling the tools.
- Never call the tools with an excerpt, a summary or your own critique in place of the user's text.

## Tools (server `cover-letter-checker-builder`)

| Tool | Writes anything | Fields and purpose |
|---|---|---|
| `score_cover_letter` | no | `cover_letter_text` (required), `company`, `role`, `job_description`, `resume_text`. Reviews a cover letter with a fixed checklist. Returns a deterministic score out of 100 and, for each check (length, opening line, company and role named, quantified results, leftover placeholders, filler phrases, closing call to action, match with the resume, job description keywords), whether it passes and how to fix it. Takes the complete letter as plain text, plus the company, role, job description and the applicant's resume text when available; checks that cannot be evaluated without them are left out of the score. Stores nothing. |
| `find_cover_letter_examples` | no | `role`. Searches ResumeNext's library of cover letter examples by job title or field. Returns up to two complete example letters with writing tips and a link to each example's page. Without a match, or without a query, returns the list of roles the library covers. The examples use placeholders such as [Company Name] for the details to replace. |
| `prepare_cover_letter_editor` | yes, a private link valid for 7 days | `cover_letter_text` (required), `company`, `role`, `job_description`. Creates a private link, valid for 7 days, that opens the ResumeNext cover letter editor with this letter already loaded, ready to edit, format and export. Takes the complete final letter as plain text, plus the company, role and job description when the user gave them. Returns the link and the same review as score_cover_letter. Stores the letter for the link. |

## Flow

1. Review: call `score_cover_letter` with the complete letter (plus `company`, `role`, `job_description` and `resume_text` when the user gave them, so the checks that need them run). Report the score and the failing checks with their fixes.
2. Examples: call `find_cover_letter_examples` with the role; without a match it returns the roles covered.
3. Improve with the user facts only, then score again. When the letter is final, call `prepare_cover_letter_editor` with the complete text and share `editor_url` (private, valid for seven days).

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
