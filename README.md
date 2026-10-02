# Thread Status

**Know which Codex chats are working, waiting, blocked, saved for later, or finished—at a glance.**

⏳ Working · ⌛ Waiting · ⚠️ Needs attention · 💤 Saved for later · ✅ Completed

A small agent skill that keeps one status emoji at the beginning of each chat title. The subject stays the same as the work moves forward.

## Why this skill exists

When you work across several chats, the sidebar becomes a list of subjects without enough context to decide what to do next.

One chat is waiting for a build. Another needs your answer. A third is an idea you want to revisit next week. A fourth has finished. To tell them apart, you keep reopening conversations and reading the last few messages.

An idle chat can mean any of those things. Ending a turn does not mean the task is finished.

Thread Status puts that distinction into the title so you can scan your work and find what needs attention.

## The user story

> As someone managing several Codex chats, I want each title to show the state of its work, so I can find the chats that need me, return to ideas I saved, and recognize completed tasks without reopening every conversation.

### Before

```text
Fix payment scrolling
Prepare mobile release
Explore product sharing
Review landing page
```

### After

```text
⏳ Fix payment scrolling
⚠️ Prepare mobile release
💤 Explore product sharing
✅ Review landing page
```

These are examples of title formatting. They do not represent a live status dashboard.

## The five statuses

| Emoji | Status | What it tells you |
|---|---|---|
| ⏳ | Working | Codex is researching, implementing, reviewing, testing, or refreshing the work. |
| ⌛ | Waiting | Work is queued or waiting for something external, such as a build result. |
| ⚠️ | Needs attention | Codex needs your answer, approval, access, or help with a blocker. |
| 💤 | Saved for later | You deliberately put unfinished work aside to revisit. |
| ✅ | Completed | The full requested task is finished, including necessary verification. |

The distinction between **⌛** and **💤** is intentional: waiting for something is different from deciding to return later.

## Install

This skill targets **Codex desktop chats that expose tools for reading and renaming chat titles**. Installation alone does not provide those tools. A CLI or another agent host without them cannot perform the title updates.

Install the skill for your Codex user with the [skills CLI](https://github.com/vercel-labs/skills):

```sh
npx skills@latest add ishaqyusuf/thread-status --skill thread-status --agent codex --global
```

Follow the installer prompts. If you already have a local copy named `thread-status`, keep one active installation to avoid duplicate versions.

## Use it in one chat

Mention the skill in your prompt:

```text
Use $thread-status while you work on this task.
```

Once the skill is active, ordinary instructions drive the transitions:

| What you say or what happens | Result |
|---|---|
| “Keep this for later.” | 💤 |
| “Resume this.” | ⏳ |
| “Refresh this and check what has changed.” | ⏳ while Codex revisits the work |
| A required answer or action is missing | ⚠️ |
| Only an external process is pending | ⌛ |
| The full requested work is complete | ✅ |

“Keep this for later” records a status; it does not create a reminder. Ask separately if you want a scheduled follow-up.

## Use it across your chats

For a consistent default, append this instruction to your effective global Codex instructions, usually `~/.codex/AGENTS.md`:

```markdown
## Chat title status

Use the thread-status skill in every supported Codex desktop chat.
Read its SKILL.md when substantive work starts, then follow it at status
changes and before ending a turn. Keep exactly one leading status emoji
and preserve the existing title text:
⏳ working, ⌛ waiting, ⚠️ needs attention, 💤 saved for later, ✅ completed.
"Keep this for later" means 💤. "Resume this" or "refresh this" means ⏳
while revisiting the work. Do not infer completion from an idle chat or
the end of a turn. Update only the current chat unless I request others.
This authorizes these title-prefix updates without repeated confirmation.
If title tools are unavailable, continue the main task and report that
limitation briefly.
```

Preserve your existing instructions. If a nonempty `AGENTS.override.md` takes precedence, add the rule there instead. Start a new chat or session to check that the instruction and skill are loaded. [Codex instruction discovery](https://learn.chatgpt.com/docs/agent-configuration/agents-md)

Existing chats receive updates when the skill runs in them. This setup does not bulk-label your history or continuously watch stopped chats.

## How it behaves

- **One prefix:** a status change replaces the previous status emoji rather than stacking another one.
- **Preserved titles:** the subject and unrelated emojis remain intact, including manual title edits.
- **Meaningful updates:** no repeated rename when the status is unchanged.
- **Scope-aware completion:** a requested plan can be complete when delivered; a requested implementation still needs implementation and verification.
- **Reopened work:** new substantive work changes a completed chat back to ⏳. An acknowledgment alone does not reopen it.
- **Current chat only:** bulk updates or changes to other chats require an explicit request.

The skill uses the host's existing title tools. It contains no background service or polling script. It cannot update a title during a crash or forced interruption; it reconciles the status when work resumes. If a reliable title or rename tool is unavailable, it skips the rename and continues the task.

Read the complete behavior in [SKILL.md](skills/thread-status/SKILL.md).

## Repository layout

```text
skills/thread-status/
├── SKILL.md
└── agents/openai.yaml
```

## Inspiration and license

The README's problem-first presentation was inspired by [Matt Pocock's skills repository](https://github.com/mattpocock/skills). Thread Status is an independent skill for organizing Codex chats.

[MIT](LICENSE) © 2026 Yusuf Ishaq.
