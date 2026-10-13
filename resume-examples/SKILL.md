---
name: resume-examples-templates
description: Resume examples and templates - browse complete resume examples by role and ResumeNext's resume templates, each with a link that opens it in the ResumeNext editor as a starting point. Trigger on intents like "show me a resume example for", "sample resume for a nurse", "which resume template should I use", "ATS-friendly resume template", "简历范例", "某职位的简历样本", "简历模板推荐". Not for reviewing or scoring the user's own resume, cover letters or job search.
---

# Resume Examples & Templates

ResumeNext adds what the chat cannot do by itself: fixed rules and libraries applied to the user's real text, and links that open the work in ResumeNext. No account, no sign-in and no API key are needed.

## Connect in Manus

1. In Manus open Settings, then Connectors, and choose Create, then "Add MCP by URL".
2. Server URL: `https://resumenext.io/api/manus/resume-examples/mcp`. Or choose "Import MCP by JSON" with:

```json
{"mcpServers": {"resume-examples-templates": {"url": "https://resumenext.io/api/manus/resume-examples/mcp"}}}
```

3. Save. Manus shows "OAuth not required": no account and no sign-in are needed.
4. "Try it out" opens a new task with the connector on; in other tasks, turn it on from the connector icons in the chat input.

## When to use

- The user wants to see how a resume for a role is written.
- The user wants a template, or to start a new resume from an example or a template.

## When NOT to use

- Reviewing or scoring the user's own resume, cover letters or job search: answer directly without calling the tools.
- Never call the tools with an excerpt, a summary or your own critique in place of the user's text.

## Tools (server `resume-examples-templates`)

| Tool | Writes anything | Fields and purpose |
|---|---|---|
| `find_resume_examples` | no | `role`. Searches ResumeNext's library of resume examples by job title or field. Returns up to three matching examples with their headline, summary, experience bullets and skills, a link to the example's page, and a link that opens the example in the ResumeNext editor as a starting point. Without a match, or without a query, returns the list of roles the library covers. The people and companies in the examples are fictional. |
| `list_resume_templates` | no | `audience`. Lists ResumeNext's resume templates with their name, whether the layout is ATS-safe or visual, a one-line description and who it suits. Each comes with a link that opens a new resume in that template in the ResumeNext editor. The audience narrows the list to templates that suit it. |

## Flow

1. Examples: call `find_resume_examples` with the role or field. It returns up to three complete examples with page and editor links; without a match it returns the roles covered. Say that the people and companies are fictional.
2. Templates: call `list_resume_templates`, with `audience` when the user said who the resume is for. Explain which are ATS-safe and which are visual.
3. Share the `editor_url` of the example or template the user picks.

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
