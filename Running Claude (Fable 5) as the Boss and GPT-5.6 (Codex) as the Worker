# Running Claude (Fable 5) as the Boss and GPT-5.6 (Codex) as the Worker

A step-by-step setup for making Claude Code and OpenAI's Codex work together on the same project: Claude plans and reviews, Codex builds. This uses OpenAI's official `codex-plugin-cc` plugin for Claude Code — no custom scripts, no unofficial bridges.

> Tested with: Claude Code (latest), OpenAI Codex CLI, `codex-plugin-cc` v1.x.
> You will need a Claude subscription/API access and either a ChatGPT subscription (Free tier works) or an OpenAI API key.

---

## Why bother

If you use Claude and GPT separately, you're doing every step yourself in both tools and burning through both usage limits solo. This setup splits the work instead:

- **Claude (Fable 5)** acts as the orchestrator — it interviews you about the task, writes the plan, hands it off, reviews what comes back, and sends it back for fixes until the project is done.
- **GPT-5.6 (via Codex)** acts as the executor — it receives the plan and writes the actual implementation.

You use less of both tools individually, but the two together get further on a project than either would alone.

---

## Prerequisites

- **Node.js 18.18+** installed
- **Claude Code** installed and signed in
- **A ChatGPT account** (Free tier is enough to sign in to Codex) or an **OpenAI API key**

---

## Step 1 — Install Codex and sign in with your ChatGPT account

Install the Codex CLI:

```bash
npm install -g @openai/codex
```

Sign in with your ChatGPT account (recommended, uses your existing subscription):

```bash
codex login
```

Or, if you'd rather use an API key instead of a ChatGPT login:

```bash
codex login --api-key "YOUR_OPENAI_API_KEY"
```

Confirm it's working:

```bash
codex --version
```

---

## Step 2 — Open Claude Code and add OpenAI's official Codex plugin

Open a Claude Code session in your project directory:

```bash
claude
```

Inside the Claude Code session, run these four commands one at a time:

```
/plugin marketplace add openai/codex-plugin-cc
```

```
/plugin install codex@openai-codex
```

```
/reload-plugins
```

```
/codex:setup
```

`/codex:setup` checks whether the Codex CLI is installed and authenticated. If it isn't installed yet, it will offer to install it for you automatically via npm — so if you want to skip Step 1 entirely, you can just run `/codex:setup` here and let it handle installation.

Once setup completes, you should see the plugin's commands available: `/codex:review`, `/codex:rescue`, `/codex:transfer`, `/codex:status`, `/codex:result`, and `/codex:cancel`. These let Claude Code delegate tasks to Codex, run cross-model code reviews, and hand off full sessions — all through your existing local Codex CLI and its authentication.

---

## Step 3 — Give Claude one prompt that lets Fable 5 call GPT-5.6 through the plugin

Paste this directly into Claude Code after setup is complete:

```
You now have access to OpenAI's Codex plugin (codex@openai-codex), installed and authenticated in this environment. From now on, treat Codex as a worker model you can delegate implementation tasks to.

Specifically:
- When I ask you to build, implement, or write something substantial, do not write the full implementation yourself by default. Instead, break the task into a clear, unambiguous specification (goals, constraints, file structure, acceptance criteria) and hand that specification to Codex using /codex:rescue or the equivalent delegation command, so GPT-5.6 does the actual writing.
- After Codex returns work, review it yourself line by line as if you were a senior engineer doing a PR review. Check it against the original specification and acceptance criteria.
- If anything is wrong, incomplete, or diverges from the spec, send it back to Codex with specific, actionable feedback rather than fixing it yourself, and repeat this review cycle.
- Only tell me the task is complete once you have verified the output meets every requirement in the original specification.
- Keep me informed at each handoff: tell me when you're delegating to Codex, what you asked it to do, and what came back.

Confirm you understand this workflow, then wait for my first task.
```

---

## Step 4 — Give Claude a second prompt that builds a custom skill (`/route`)

This turns the workflow above into a reusable slash command, so you don't have to re-paste the Step 3 prompt every session. Paste this into Claude Code:

```
Create a custom Claude Code skill for this project that implements an orchestrator/executor workflow between you (Claude) and OpenAI's Codex plugin (codex@openai-codex).

Set it up as follows:
1. Create the directory .claude/skills/route/ in this project.
2. Inside it, create a SKILL.md file with YAML frontmatter containing a "description" field that clearly states this skill should be used whenever I want to plan and build a project by splitting work between Claude as the planner/reviewer and Codex (GPT-5.6) as the implementer.
3. In the body of SKILL.md, write clear instructions for yourself that say:
   - Start by interviewing me about what I want to build: ask about goals, constraints, tech stack, and what "done" looks like, one focused question at a time.
   - Once you have enough detail, write a complete implementation plan broken into discrete, ordered tasks.
   - For each task, hand it off to Codex via the codex plugin's delegation command (e.g. /codex:rescue) with the task spec and acceptance criteria.
   - Review everything Codex sends back against the acceptance criteria before accepting it. If it fails, send specific feedback back to Codex and repeat.
   - Continue task by task, updating me on progress, until every task in the plan is complete and verified.
   - Do not mark the project done until all tasks pass your own review.
4. Name the skill "route" so it can be invoked as a slash command.
5. After creating the files, run /reload-plugins (or restart the session if needed) and confirm the skill is loaded and available.

Once this is set up, I should be able to just type /route to start this workflow at any time.
```

---

## Using it

Once both prompts have been run once, everyday usage is just:

```
/route
```

Claude takes over, interviews you about the task, plans it out, and works with Codex/GPT-5.6 in the background to build it — sending work back and forth between the two models until it's done, while you watch the whole thing happen in your terminal.

---

## Notes and troubleshooting

- If `/plugin` or `/codex:setup` aren't recognized, update Claude Code to the latest version first.
- If `/codex:setup` can't find the Codex CLI and auto-install fails, install it manually with `npm install -g @openai/codex` and run `codex login` yourself, then re-run `/codex:setup`.
- The Codex plugin uses your **local** Codex CLI and its existing config/authentication — it doesn't require a separate login inside Claude Code.
- Codex usage is billed against your ChatGPT subscription or OpenAI API key, separately from your Claude usage — keep an eye on both if you're on limited plans.
- If you want Codex to act as a stricter reviewer rather than just an executor, the plugin also supports an optional review-gate mode (`/codex:setup --enable-review-gate`) that blocks Claude from finishing until Codex has reviewed the output — this can increase usage on both sides, so enable it only if you're actively watching the session.

---

## Source

Official plugin repository: https://github.com/openai/codex-plugin-cc
