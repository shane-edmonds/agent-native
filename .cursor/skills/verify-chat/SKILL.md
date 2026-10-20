---
name: verify-chat
description: >-
  Drive the Agent-Native Chat template web UI the way a user does — launch an
  isolated local instance, doctor it, exercise mapped features with Playwright
  via control-chat, and capture proof under .tmp/verify-chat/evidence. Use for
  /verify-chat, smoke-checking Chat after a framework or template change, or
  when you need scripted proof of home, chat, actions, settings, or navigation.
---

# Verify Chat

Chat (`templates/chat`) is the primary user surface of this monorepo: a browser
app with Get started (`/home`), durable chat (`/chat`), shared actions, and
settings. Other surfaces exist (domain templates, docs site, desktop Electron,
`agent-native` CLI, hosted beta e2e) — this skill only drives Chat locally.

The harness is `control-chat` at `.cursor/skills/verify-chat/scripts/control-chat`.
Put it on `PATH` for the session:

```bash
export PATH="/workspace/.cursor/skills/verify-chat/scripts:$PATH"
# or from the repo root:
export PATH="$(pwd)/.cursor/skills/verify-chat/scripts:$PATH"
```

Never drive an instance you did not launch with `control-chat launch`. Never kill
by process name; only stop the PID recorded for the run.

## Launch

From the repo root (workspace packages must already be installed and built —
`pnpm install` at the root runs the postinstall builds):

```bash
control-chat launch --port 48173
```

What this does:

- Starts `pnpm exec agent-native dev --port <port>` in `templates/chat` (no `--open`).
- Uses a disposable PGlite directory: `.tmp/verify-chat/runs/<run-id>/pglite`
  via `DATABASE_URL=pglite:<abs-path>` so concurrent runs do not share state.
- Waits until `http://127.0.0.1:<port>/home` answers (usable after migrations /
  auto-dev-account, not merely that a port is open).
- Writes `.tmp/verify-chat/runs/<run-id>/run.json` and
  `.tmp/verify-chat/active-run.json`.

Ready means the server answers on the chosen port and `control-chat launch` has
completed loopback local-dev sign-in via `POST /_agent-native/auth/local-dev`,
writing Playwright `storage-state.json` for the run. The browser may still show
a `Continue as local dev` button if cookies are missing; `control-chat browser
goto --url /home` clicks it when needed and waits for the `Get started` heading.
Do not set `AUTH_DISABLED` and do not create accounts for verification.

Two instances can run side by side if each has its own `--run-id`, port, and
data directory. Refusing to double-drive a shared instance beats corrupting a
developer's session.

Teardown is `control-chat cleanup` (see Cleanup).

## Doctor

```bash
control-chat doctor
```

Read-only. Exit `0` only when the recorded PID is alive, the run's port/base URL
serves `/home` with HTTP 2xx/3xx, and the disposable data directory exists.
Stdout is JSON with `ok`, `pid`, `baseUrl`, `dataDir`, `httpStatus`. Run this
first whenever anything looks off.

## Drive

Prefer stable handles from the Chat UI (ARIA roles / accessible names from
`templates/chat/app/i18n/en-US.ts`):

| Control | Handle |
| --- | --- |
| Home heading | `getByRole('heading', { name: 'Get started' })` |
| Home composer | textbox / placeholder `Call the hello action for Alex` |
| Sidebar Home | link/button `Home` |
| New Chat | button `New Chat` |
| Settings title | heading `Settings` (route `/settings`) |
| Navigation landmark | `getByRole('navigation', { name: 'Navigation' })` |

Browser commands (each restores cookie jar + last URL for the active run):

```bash
control-chat browser goto --url /home
control-chat browser wait --role heading --name "Get started"
control-chat browser click --role button --name "New Chat"
control-chat browser fill --role textbox --name "Ask the agent..." --value "hello"
control-chat browser snapshot --aria --path .tmp/verify-chat/evidence/home/home.aria.txt
control-chat browser screenshot --path .tmp/verify-chat/evidence/home/home.png
```

Shared actions (same DB as the live server when it is up):

```bash
control-chat action -- hello --name Alex
```

Feature recipes live in [`features/`](./features/README.md). Drive the mapped
entry points; do not substitute an internal setter or a test-only endpoint for a
user path listed in the map.

## Evidence

Proof artifacts go under **`.tmp/verify-chat/evidence/<feature-id>/`** (gitignored).
That directory survives `control-chat cleanup`. Optionally copy key screenshots
into the Project store at
`/cursor/stores/bc-214b544f-ccb8-4106-9644-ae1b826ccd00/media/verify-skill/` for
handoff.

Proof standards:

- Exercise the real user path (browser navigation or `pnpm action` / CLI that the
  product documents), not private APIs invented for the test.
- Capture the action and the resulting state (ARIA snapshot + screenshot with
  Chat identity visible — heading `Get started` or nav `Chat` / `Home`).
- For mutations, confirm with a second read (reload the page or re-run a list/get /
  action).
- Mocks only at production boundaries that already isolate external systems.
  A local Chat run without a provider API key cannot complete a paid agent turn;
  prove shell/navigation/actions that do not require spend, and record the skip
  for agent-turn paths in the feature Gotchas.

## Cleanup

```bash
control-chat cleanup
```

Stops the process group for the recorded PID, removes the disposable PGlite data
dir for that run, and clears `active-run.json`. Does **not** delete
`.tmp/verify-chat/evidence/`. After cleanup, confirm evidence files still exist
before claiming a proof.

```bash
control-chat cleanup --keep-data   # stop server only
```

## Helpers

| Helper | Invocation |
| --- | --- |
| `control-chat` | `.cursor/skills/verify-chat/scripts/control-chat` (executable Node ESM) |

`control-chat --help` prints subcommands. Requires Node ≥ 22, workspace
`pnpm install`, Google Chrome (or set `VERIFY_CHAT_BROWSER_CHANNEL`), and
`@playwright/test` from the repo root.

## Maintenance

Keep the feature map honest with `/maintain-verification-skill` as Chat routes
and labels change.
