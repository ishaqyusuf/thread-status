# Host adapters

Read only the section relevant to the current host. These mappings implement the same status rules; they do not create additional access. Other hosts can implement the generic contract below.

Every adapter changes only the managed emoji prefix, including the optional 🖥️ or 📱 activity after the status. When a host accepts a complete title, submit the exact existing title text with only that prefix changed. Never rewrite or generate the title text. Record ownership of a managed activity prefix in trusted session context only after a successful prefix update. The same mapping works for starting, switching, and clearing testing activity.

## Generic host or orchestrator

An automatic adapter needs:

| Capability | Required behavior |
|---|---|
| Current conversation | Return a reliable identity for the caller, or supply an explicitly documented self-target. |
| Read title | Return the full current title for that identity. |
| Update prefix | Change only the managed emoji prefix, preserving the full title text, and return a confirmed result or actionable failure. A full-title setter is acceptable only with the preservation check from the main skill. |

Use an existing authorized integration. Validate the actual tool/CLI/API schema in the current runtime before calling it. This contract works for local apps and cloud orchestrators; the model provider is irrelevant. If any capability is missing, use the main skill's manual or status-only mode. Do not create a service or request credentials just to apply a prefix.

## Codex desktop

When these app tools are available:

- Identify the calling chat from trusted task context.
- Read its title with `mcp__codex_app__list_threads` or `mcp__codex_app__read_thread`, matching its established ID.
- Apply the prefix with `mcp__codex_app__set_thread_title`, passing the exact title text read above with only the managed emoji prefix changed. Despite its name, this tool must never be used by this skill to rewrite the title text. Omit `threadId` to target the calling chat, or supply the established ID. Use `source` when the exposed schema requires the backing kind.
- Inspect the returned result for success and confirm that the title text is unchanged. If a safe full-title read or preservation check is unavailable, use status-only mode.

Tool availability depends on the host, not the model or whether the skill was installed. A Codex CLI session without these operations uses the generic fallback. Emoji prefix updates through the full-title setter have been exercised in Codex desktop.

## Claude Code

Claude Code loads standard skills and supports `/thread-status` invocation. It documents `/rename [name]` for naming the current session; this is a host command, not a shell command. [Skills](https://code.claude.com/docs/en/skills) · [Commands](https://code.claude.com/docs/en/commands)

If the current runtime exposes an agent-callable integration for identity, full-title reads, and renames, use its documented schema. Otherwise use manual/status-only mode. When the full title is known, suggest the manual command with only its managed emoji prefix changed. For an existing title of `Plan product sharing`:

```text
/rename 💤 Plan product sharing
```

Do not execute `/rename` through Bash, start a nested Claude process, or assume an interactive user command is an available agent tool. The documented CLI rename route also depends on version and runtime access; it is not an automatic adapter supplied by this skill. Claude's documented title length limit is 200 characters, so use status-only mode if adding the prefix would exceed it; never shorten the title text. This mapping is documentation-backed, not a live cross-host rename test.

## OpenCode

An authorized OpenCode server connection can supply the required operations:

- `GET /session/:id` reads the session, including its title.
- `PATCH /session/:id` with a JSON body containing `title` can apply the managed emoji prefix and returns the session. Read the current title first and preserve its title text exactly in the submitted value.

Use the existing server address, authentication, project scope, and current session ID supplied by trusted runtime context or the user. Validate them against the current server's documentation. Never assume a default port or choose the newest session from a listing. Without that connection and identity, use manual/status-only mode. [Session API](https://opencode.ai/docs/server/)

OpenCode can discover this skill through its native skill paths and its documented Claude-compatible paths. [Skill discovery](https://opencode.ai/docs/skills/)

This mapping is documentation-backed; no live OpenCode prefix update has been tested as part of this release.
