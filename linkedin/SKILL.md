---
name: linkedin-profile-review-resume
description: LinkedIn profile review and resume converter - score the text of a LinkedIn profile section by section and turn it into a resume that opens in the ResumeNext editor. Reads the text the user pastes; it does not connect to LinkedIn. Trigger on intents like "review my LinkedIn profile", "improve my LinkedIn headline", "turn my LinkedIn into a resume", "LinkedIn to CV", "帮我优化领英资料", "把领英资料转成简历". Not for connecting to LinkedIn, posting or messaging on LinkedIn, or job search.
---

# LinkedIn Profile Review & Resume Converter

ResumeNext adds what the chat cannot do by itself: fixed rules and libraries applied to the user's real text, and links that open the work in ResumeNext. No account, no sign-in and no API key are needed.

## Connect in Manus

1. In Manus open Settings, then Connectors, and choose Create, then "Add MCP by URL".
2. Server URL: `https://resumenext.io/api/manus/linkedin/mcp`. Or choose "Import MCP by JSON" with:

```json
{"mcpServers": {"linkedin-profile-review-resume": {"url": "https://resumenext.io/api/manus/linkedin/mcp"}}}
```

3. Save. Manus shows "OAuth not required": no account and no sign-in are needed.
4. "Try it out" opens a new task with the connector on; in other tasks, turn it on from the connector icons in the chat input.

## When to use

- The user pastes the text of a LinkedIn profile (or of its Save to PDF export) and wants a review or a better headline and About section.
- The user wants that profile turned into a resume.

## When NOT to use

- Connecting to LinkedIn, posting or messaging on LinkedIn, or job search: answer directly without calling the tools.
- Never call the tools with an excerpt, a summary or your own critique in place of the user's text.

## Tools (server `linkedin-profile-review-resume`)

| Tool | Writes anything | Fields and purpose |
|---|---|---|
| `review_linkedin_profile` | no | `profile_text` (required), `target_role`. Scores the text of a LinkedIn profile. Returns a deterministic score out of 100, the score and findings for headline, About, experience, keywords and completeness, the strengths and the priorities to fix. Takes the profile as plain text supplied by the user, and the target role when they gave one. Does not connect to LinkedIn. Stores nothing. |
| `convert_linkedin_to_resume` | yes, a private link valid for 7 days | `profile_text` (required), `target_role`, `target_company`, `job_description`. Turns the text of a LinkedIn profile into a resume and creates a private link, valid for 7 days, that opens it in the ResumeNext editor, ready to edit, format with a template and export. Takes the profile as plain text supplied by the user, plus the target role, company and job description when they gave them. Returns the link and the ATS analysis of the resulting resume. Does not connect to LinkedIn. Stores the profile text for the link. |

## Flow

1. Review: call `review_linkedin_profile` with the complete profile text (and `target_role` when given). Report the score and the priorities returned by the tool. If the tool says the text is not a profile, ask the user to paste the profile with its sections.
2. Improve with the user facts only, then review again.
3. Convert: call `convert_linkedin_to_resume` with the complete profile text and share `editor_url` (private, valid for seven days).

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
