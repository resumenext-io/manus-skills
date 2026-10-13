---
name: resume-skills-by-role
description: Resume skills and keywords by role - get the skills and terms that recur for a role from the ResumeNext role guides and see where a resume uses them, listed and shown, listed only, shown only or not mentioned. Trigger on intents like "what skills should my resume have", "skills for a data analyst resume", "check the skills on my resume", "hard skills for", "简历技能", "某职位需要哪些技能", "检查简历里的技能". Not for comparing a resume with a specific job posting (that is the Job Match & Resume Keyword Scanner connector), scoring a whole resume or job search.
---

# Resume Skills & Keywords by Role

ResumeNext adds what the chat cannot do by itself: fixed rules and libraries applied to the user's real text, and links that open the work in ResumeNext. No account, no sign-in and no API key are needed.

## Connect in Manus

1. In Manus open Settings, then Connectors, and choose Create, then "Add MCP by URL".
2. Server URL: `https://resumenext.io/api/manus/resume-skills/mcp`. Or choose "Import MCP by JSON" with:

```json
{"mcpServers": {"resume-skills-by-role": {"url": "https://resumenext.io/api/manus/resume-skills/mcp"}}}
```

3. Save. Manus shows "OAuth not required": no account and no sign-in are needed.
4. "Try it out" opens a new task with the connector on; in other tasks, turn it on from the connector icons in the chat input.

## When to use

- The user asks which skills or keywords a resume for a role should show.
- The user shares a resume and a target role and wants to know which usual skills it lists, shows or leaves out.

## When NOT to use

- Comparing a resume with a specific job posting (that is the Job Match & Resume Keyword Scanner connector), scoring a whole resume or job search: answer directly without calling the tools.
- Never call the tools with an excerpt, a summary or your own critique in place of the user's text.

## Tools (server `resume-skills-by-role`)

| Tool | Writes anything | Fields and purpose |
|---|---|---|
| `find_resume_skills` | no | `role`. Returns the skills and terms that recur for a role in ResumeNext's role guides (tools, methods, certifications and domain terms), with a link to the role's example resume that uses them. Covers the roles of the ResumeNext examples library; without a match, or without a role, returns the list of roles covered. Stores nothing. |
| `check_resume_skills` | no | `resume_text` (required), `role` (required). Reads a resume and sorts the skills vocabulary of the target role by where the resume uses each term: listed in the Skills section (or certifications, languages) and shown in a role or project, listed but not shown anywhere in the work, shown in the work but not listed, or not mentioned. Also returns how many skills the Skills section lists, against the 5 the ResumeNext ATS score asks for. Without a matching role, returns the list of roles covered. Deterministic. Stores nothing. |

## Flow

1. Vocabulary: call `find_resume_skills` with the role. Without a match it returns the 20 roles covered; ask the user to pick the closest.
2. Check: call `check_resume_skills` with the complete resume and the role. Report the four groups as returned and the size of the Skills section.
3. Suggest adding a term only where the user has really used it: a listed skill should be shown in a bullet, a skill shown in the work can be listed. No invented skills.

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
