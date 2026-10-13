---
name: resume-builder-ats-checker
description: Review and score a resume the way applicant tracking systems read it, tailor it to a job posting, improve the wording with the user facts only, and open the finished resume in the ResumeNext editor through a private link. Trigger on intents like "review my resume", "ATS score", "is my CV ATS-friendly", "tailor my resume to this job", "open my resume in an editor", "帮我看看简历", "简历打分", "优化我的简历", "按这个职位改简历". Not for job search, interview coaching or questions unrelated to a resume.
---

# Resume Builder & ATS Checker

ResumeNext adds two things the chat cannot do by itself: a deterministic ATS score computed on the real document, and a handoff into the ResumeNext editor with the resume already loaded. No account, no sign-in and no API key: both tools work for anyone, and nothing is stored until the user asks for the editor link.

## Connect in Manus

1. In Manus open Settings, then Connectors, and choose Create, then "Add MCP by URL".
2. Server URL: `https://resumenext.io/api/manus/resume/mcp`. Or choose "Import MCP by JSON" with:

```json
{"mcpServers": {"resume-builder-ats-checker": {"url": "https://resumenext.io/api/manus/resume/mcp"}}}
```

3. Save. Manus shows "OAuth not required": no account and no sign-in are needed.
4. "Try it out" opens a new task with the connector on; in other tasks, turn it on from the connector icons in the chat input.

## When to use

- The user shares a resume (pasted text or an attached file you can read) and wants a review, a score, an ATS check, or a comparison with a job posting.
- The user wants the resume improved, re-scored, tailored to a role, or opened in an editor to pick a template, format it or download it.

## When NOT to use

- Job search, salary questions, interview preparation, cover letters with no resume involved, or any request unrelated to a resume: answer directly without calling the tools.
- Never call the tools with an excerpt, a summary or your own critique in place of the resume.

## Tools (server `resume-builder-ats-checker`)

| Tool | Writes anything | Purpose |
|---|---|---|
| `analyze_resume` | no | Scores the resume. Required: `resume_text` (the complete resume as plain text, max 100 KB). Optional: `target_role`, `target_company`, `job_description` (full posting text; enables keyword coverage and job-tailored scoring). Returns `ats_score` (0-100), `grade` (A-D) with `grade_label`, `analysis_mode` (`resume-only` or `job-tailored`), `summary`, `categories` (score by area with `label`, `percent`, `points`, `max`), `issues` (up to 8, with `severity` urgent / important / optional), `keyword_coverage` (`coverage_percent`, `matched`, `missing`) when a job description was given, and a `resume` snapshot (name, headline, counts, sections). |
| `prepare_resume_editor` | yes, a private session | Creates the editor link. Required: `resume_text` (the COMPLETE final resume). Optional: `target_role`, `target_company`, `job_description`, `job_url` (http or https). Returns `editor_url`, `link_valid_days` (7) and the same analysis fields as `analyze_resume`. |

## Flow

1. Review or score: read the full resume text the user provided (attached files included) and call `analyze_resume` with all of it. Report the `ats_score` and `grade` returned by the tool, name the weakest area from `categories`, list the first fixes from `issues`. Never invent a score or a grade.
2. Job tailoring: it needs the posting. If the user asks for a job-specific review without one, ask for the job description first, then call `analyze_resume` with `job_description` (and `target_role`, `target_company` when known). Report `coverage_percent`, the matched keywords and the missing ones, and say where a missing keyword could honestly fit.
3. Improve: rewrite in the conversation using only facts the user supplied; no invented employers, dates, metrics or skills. Then call `analyze_resume` again on the rewritten text so the user sees the new score.
4. Hand off: when the resume is complete, or the user wants to edit, format, pick a template, download or open it, call `prepare_resume_editor` with the complete final text (every role, bullet and contact detail) plus any target role, company, job description or job URL the user gave. Share `editor_url` and say that the link is private and valid for `link_valid_days` days, and that the editor opens with the resume already loaded, with templates and a live ATS score. Do not describe export or download terms: the editor shows them.

## Errors returned by the tools and how to handle them

- "resume_text exceeds the 100 KB limit": ask the user for a shorter version; never cut the content silently.
- "resume_text must be the complete resume as a non-empty string": you passed an empty value or an excerpt; send the whole resume.
- "job_url must be an http or https link to the job posting": fix or drop the URL.
- "Too many requests from this client right now. Wait a minute and try again.": wait about a minute, then retry once.
- "Unexpected argument": only the fields listed above are accepted.

## Rules

- The tools take plain text: read attached files and pass their full text.
- Same input, same score: if the user doubts a number, explain that the score is deterministic and show which `issues` lower it.
- Each `prepare_resume_editor` call creates a separate link; call it once per finished version, not after every small change.
