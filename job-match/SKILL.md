---
name: job-match-resume-keyword-scanner
description: Job match and resume keyword scanner - compare a resume with a job posting, see the posting's keywords the resume covers and misses with a job-tailored ATS score, then open the tailored resume in the ResumeNext editor. Trigger on intents like "match my resume to this job", "which keywords am I missing", "tailor my resume to this posting", "resume keyword scanner", "简历和职位匹配", "我的简历缺哪些关键词", "按职位描述改简历". Not for job search, salary questions, interview preparation, or a resume review with no job posting (that is the Resume Builder & ATS Checker connector).
---

# Job Match & Resume Keyword Scanner

ResumeNext adds what the chat cannot do by itself: fixed rules and libraries applied to the user's real text, and links that open the work in ResumeNext. No account, no sign-in and no API key are needed.

## Connect in Manus

1. In Manus open Settings, then Connectors, and choose Create, then "Add MCP by URL".
2. Server URL: `https://resumenext.io/api/manus/job-match/mcp`. Or choose "Import MCP by JSON" with:

```json
{"mcpServers": {"job-match-resume-keyword-scanner": {"url": "https://resumenext.io/api/manus/job-match/mcp"}}}
```

3. Save. Manus shows "OAuth not required": no account and no sign-in are needed.
4. "Try it out" opens a new task with the connector on; in other tasks, turn it on from the connector icons in the chat input.

## When to use

- The user shares a resume and a job posting and asks how well they match, which keywords are missing or how to tailor the resume.
- The user wants the tailored resume opened in an editor with the job attached.

## When NOT to use

- Job search, salary questions, interview preparation, or a resume review with no job posting (that is the Resume Builder & ATS Checker connector): answer directly without calling the tools.
- Never call the tools with an excerpt, a summary or your own critique in place of the user's text.

## Tools (server `job-match-resume-keyword-scanner`)

| Tool | Writes anything | Fields and purpose |
|---|---|---|
| `match_resume_to_job` | no | `resume_text` (required), `job_description` (required), `target_role`, `target_company`. Compares a resume with a job posting. Returns the posting's keywords that appear in the resume and the ones that are missing, how often each occurs on both sides, the coverage percentage, and a deterministic ATS score for this job with the issues to fix first. Takes the complete resume and the full posting as plain text. Stores nothing. |
| `prepare_tailored_resume` | yes, a private link valid for 7 days | `resume_text` (required), `job_description` (required), `target_role`, `target_company`, `job_url`. Creates a private link, valid for 7 days, that opens the ResumeNext editor with this resume loaded and the job attached as its target, so the editor scores it against that posting. Takes the complete resume and the full posting as plain text, plus the role, company and job link when the user gave them. Returns the link, the job-tailored ATS score and the keyword coverage. Stores the resume and the posting for the link. |

## Flow

1. Compare: call `match_resume_to_job` with the complete resume and the full posting (and `target_role`, `target_company` when known). Report the coverage and the ATS score returned by the tool, the matched keywords and the missing ones. Never invent a figure.
2. Tailor: rewrite using only facts the user supplied, saying where a missing keyword could honestly fit; then call `match_resume_to_job` again on the new text.
3. Hand off: when the tailored resume is final, call `prepare_tailored_resume` with the complete text, the posting and any `job_url`. Share `editor_url` and say the link is private and valid for seven days.

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
