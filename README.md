# Thread Status

**See which agent conversations are discussions, working, waiting, blocked, interrupted, saved for later, or finished—at a glance.**

💬 Discussion · ⏳ Working · ⌛ Waiting · ⚠️ Needs attention · 🛑 Blocked · ⏸️ Interrupted · 💤 Saved for later · ✅ Completed

Optional testing activity: **🖥️ UI/browser testing · 📱 Mobile testing**

An **agent-agnostic, host-agnostic skill** with shared status rules and adapters for emoji prefix updates. Use it with any agent or orchestrator that can load the instructions. The same eight statuses apply across models, local apps, and cloud sessions.

**Only the emoji prefix changes.** The skill never rewrites, shortens, corrects, or generates the title text. It preserves wording, capitalization, punctuation, spacing, and unrelated subject emojis exactly. If the host requires a full-title setter, the skill submits the unchanged title text with only the managed prefix changed. If it cannot safely preserve the title, it reports status in chat.

## Why this skill exists

When you work across several chats, the sidebar becomes a list of subjects without enough context to decide what to do next.

One chat is waiting for a build. Another needs your answer. A third is an idea you want to revisit next week. A fourth has finished. Others are open-ended discussions, blocked by an unavailable dependency, or interrupted before completion. To tell them apart, you keep reopening conversations and reading the last few messages.

An idle chat can mean any of those things. Ending a turn does not mean the task is finished.

Thread Status adds that distinction as an emoji prefix before the existing title so you can scan your work and find what needs attention. During UI testing, a second emoji also tells you whether the agent is checking general UI/browser flows or mobile behavior.

## The user story

> As someone managing conversations across different agents, I want each title to show the state of its work and its current testing activity, so I can find the chats that need me, distinguish UI/browser checks from mobile checks, return to ideas I saved, and recognize completed tasks without reopening every conversation.

### Before

```text
Fix payment scrolling
Prepare mobile release
Explore product sharing
Review landing page
```

### After

```text
⏳ 🖥️ Fix payment scrolling
⚠️ Prepare mobile release
💬 Explore product sharing
✅ Review landing page
```

These are examples of emoji prefix formatting; the title text stays exactly the same. They do not represent a live status dashboard.

## The eight statuses

| Emoji | Status | What it tells you |
|---|---|---|
| 💬 | Discussion | Questions, learning, or brainstorming without a defined deliverable; stays 💬 after a reply. |
| ⏳ | Working | The agent is researching, implementing, reviewing, testing, or refreshing a requested task. |
| ⌛ | Waiting | Work awaits an external process expected to finish normally, such as a running build. |
| ⚠️ | Needs attention | The agent needs your answer, approval, access, or action to proceed. |
| 🛑 | Blocked | No useful progress is possible until an external condition changes, with no workable alternative. |
| ⏸️ | Interrupted | Confirmed execution stop with unfinished work; applying the label requires host support. |
| 💤 | Saved for later | You deliberately put unfinished work aside to revisit. |
| ✅ | Completed | The full requested task is finished, including necessary verification. |

Use 💬 for "let's think about this idea" and ⏳ for "write an implementation plan." A complete requested plan can earn ✅; answering a discussion question does not.

Keep ⏳ while useful independent work remains. Once progress stops, use ⌛ for a process expected to finish, ⚠️ when you must act, or 🛑 for an external blocker. Use ⏸️ only with reliable evidence of interruption; an idle chat or stale working title is insufficient. 💤 means you deliberately set the work aside.

Resumption preserves the unfinished objective and rechecks what completed and what still blocks progress. A blocked native goal remains unfinished; these emoji prefix labels do not change the goal's underlying status.

## Optional testing activity

The status always comes first. A second managed emoji appears only during the actual testing phase:

| Emoji | Activity | Example |
|---|---|---|
| 🖥️ | General UI, browser, and user-flow testing | `⏳ 🖥️ Test checkout flow` |
| 📱 | Testing on a phone, mobile emulator/simulator, or mobile browser viewport | `⏳ 📱 Test checkout flow` |

Mobile testing uses 📱 even when driven from a desktop browser. The skill replaces the activity when switching modes; it never adds both. Merely working on UI/mobile code, writing tests, or running unit tests does not activate either marker.

The activity disappears when testing ends, work moves to another phase, testing is blocked or interrupted, or the task is saved for later or completed. A still-running external test can retain its activity with ⌛. Finishing tests does not earn ✅ if other requested work remains.

```text
⏳ Test checkout flow
⏳ 🖥️ Test checkout flow
⏳ 📱 Test checkout flow
⏳ Test checkout flow
✅ Test checkout flow
```

The skill preserves unrelated subject emojis and tracks which activity prefix it actually added. If ownership is uncertain after an interruption, it preserves the ambiguous emoji rather than deleting part of your title.

## Install

Install globally with the [skills CLI](https://github.com/vercel-labs/skills), then choose the agents you use:

```sh
npx skills@latest add ishaqyusuf/thread-status --skill thread-status --global
```

The installer supports hosts including Claude Code, Codex, Cursor, and OpenCode. Select your hosts in its prompts; installation does not grant prefix-update access. Keep one active version per host if you already have a local copy.

For a cloud orchestrator or another environment, install or upload the `skills/thread-status` folder through that host's supported skill mechanism. A global local installation applies across projects for your selected local agents; it does not install into remote or cloud workspaces.

## Host capabilities

The skill selects a mode from the capabilities actually available:

| Mode | Available capability | Behavior |
|---|---|---|
| Automatic | Reliable current identity, full-title read, and a prefix operation or full-title setter | Changes only the status and optional testing emoji prefix, preserving title text exactly. |
| Manual | A documented user command and a known full title | Suggests the changed emoji prefix with the unchanged title text and the manual command. |
| Status-only | No supported prefix integration or no safely preserved title text | Shows the current status without claiming a sidebar update. |

| Host | Integration route | Verification |
|---|---|---|
| Codex desktop | Exposed chat-read and full-title setter tools, used only for prefix changes | Automatic prefix updates exercised. |
| Claude Code | Runtime integration when available; otherwise its documented `/rename` command for manual use | Documentation-backed; automatic prefix updates not tested. |
| OpenCode | Existing authorized session API connection with the current session ID | Documentation-backed; live prefix updates not tested. |
| Other local or cloud hosts | Existing tools, API, CLI, or an orchestrator adapter implementing the same capabilities | Depends on the supplied integration. |

Read the [host adapters](skills/thread-status/references/host-adapters.md) for the operation mappings and sources. The portable rules do not require a particular model provider.

## Use it in one chat

Ask your agent to load the skill:

```text
Use the thread-status skill while you work on this task.
```

Hosts may also expose their own invocation syntax, such as `/thread-status` in Claude Code or `$thread-status` in Codex. Once the skill is active, ordinary instructions drive the transitions:

| What you say or what happens | Result |
|---|---|
| “Keep this for later.” | 💤 |
| “Resume this.” | ⏳ for unfinished task work; 💬 for an open-ended discussion |
| “Refresh this and check what has changed.” | ⏳ while revisiting task work; 💬 for discussion |
| A required answer or action is missing | ⚠️ |
| Only an external process expected to finish normally is pending | ⌛ |
| No useful progress is possible until an external condition changes | 🛑 |
| Execution is confirmed interrupted with unfinished work and the host permits an update | ⏸️ |
| An open-ended discussion reply ends | 💬 |
| The full requested work is complete | ✅ |
| General UI/browser testing starts | Status followed by 🖥️ |
| Mobile device or viewport testing starts | Status followed by 📱 |
| Testing ends or is set aside | Activity marker is removed |

“Keep this for later” records a status; it does not create a reminder. Ask separately if you want a scheduled follow-up.

## Use it across your conversations

For a consistent default, append this portable instruction to your host's effective global instructions:

```markdown
## Conversation status

Use the thread-status skill when substantive work starts, at status
changes, and before ending a turn. Read its SKILL.md and follow the
relevant host adapter. Change only the emoji prefix; never rewrite,
shorten, correct, or generate the title text. Preserve it exactly with
one status prefix:
💬 discussion, ⏳ working, ⌛ waiting, ⚠️ needs attention, 🛑 blocked,
⏸️ interrupted, 💤 saved for later, ✅ completed.
While actually testing, add one activity after the status: 🖥️ for general
UI/browser testing or 📱 for mobile device, emulator, or viewport testing.
Use 📱 instead of 🖥️ for mobile checks. Clear the managed activity when
testing ends, is blocked or interrupted, or work is saved or completed.
Preserve unrelated emojis already in the title.
"Keep this for later" means 💤. "Resume this" or "refresh this" means ⏳
while revisiting task work, or 💬 when resuming an open-ended discussion.
Keep discussion-only chats at 💬 after replies. Use ✅ only for a defined
requested task or deliverable that is finished.
Keep ⏳ while useful work remains. Use ⌛ for a process expected to finish,
⚠️ when my action is required, and 🛑 for an external blocker with no
useful work remaining. Use ⏸️ only for a confirmed interruption with
unfinished work and host support; idle sessions do not prove interruption.
Preserve unfinished objectives when resuming. Emoji labels do not change
native goal status. Do not infer completion from an idle session or the
end of a turn. Update only the current conversation unless I request
others. This authorizes title-prefix updates within the host's normal
permissions. A full-title setter may only submit the unchanged title
text with a changed emoji prefix. If safe automatic prefix updates are
unavailable, use manual or status-only mode and do not claim a sidebar
update.
```

Typical global instruction locations:

| Host | Location | Reference |
|---|---|---|
| Codex | `~/.codex/AGENTS.md`, or the effective override | [Instruction discovery](https://learn.chatgpt.com/docs/agent-configuration/agents-md) |
| Claude Code | `~/.claude/CLAUDE.md` | [User instructions](https://code.claude.com/docs/en/memory) |
| OpenCode | `~/.config/opencode/AGENTS.md` | [Global rules](https://opencode.ai/docs/rules/) |
| Other hosts or cloud orchestrators | Their supported persistent instruction setting | Check the host's documentation. |

Preserve your other instructions. Start a new conversation or session to verify that the skill and global rule load. Existing conversations update when the skill runs in them; this setup does not bulk-label history or continuously watch stopped agents.

## How it behaves

- **Status plus optional activity:** one status emoji, then at most one managed testing emoji. A transition replaces the previous managed prefix.
- **Preserved titles:** the title text stays exactly the same, including wording, punctuation, spacing, and unrelated emojis. The skill preserves the latest manual title edits and never creates a title for an empty one.
- **Meaningful updates:** change only the managed emoji prefix when status or testing activity changes, with no repeated write when the prefix is already correct.
- **Scope-aware completion:** a requested plan can be complete when delivered; a requested implementation still needs implementation and verification.
- **Reopened conversations:** a new task changes a completed chat to ⏳; open-ended discussion changes it to 💬. An acknowledgment alone does not reopen it.
- **Unfinished work:** interruption and blockage preserve the objective. Resumption checks what actually completed before continuing.
- **Current chat only:** bulk updates or changes to other chats require an explicit request.

The skill uses the host's existing title capabilities and supports manual/status-only fallback. It contains no background service or polling script. It cannot update a prefix during a crash or forced interruption; it reconciles the status when work resumes. A missing title integration does not block the main task; the agent reports the status without claiming a sidebar update.

Read the complete behavior in [SKILL.md](skills/thread-status/SKILL.md).

## Repository layout

```text
skills/thread-status/
├── SKILL.md
├── references/host-adapters.md
└── agents/openai.yaml
```

The `SKILL.md` and host adapters define the portable workflow. `agents/openai.yaml` is optional display metadata for hosts that use it; other hosts can ignore it.

## Inspiration and license

The README's problem-first presentation was inspired by [Matt Pocock's skills repository](https://github.com/mattpocock/skills). Thread Status is an independent skill for organizing agent conversations across hosts.

[MIT](LICENSE) © 2026 Yusuf Ishaq.
