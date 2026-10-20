# Settings

Settings lets a user open workspace and agent preferences at `/settings` and
related nested routes under `/settings/*`.

## Sub-features

- `settings-open` opens the Settings page from the URL.
- `settings-title` shows the Settings heading.
- `settings-agent` reaches agent management entry points from Settings copy.

## How to get to it (user POV)

- Visit `/settings` directly.
- Use in-app Settings / Manage agent links from the agent inspector or settings
  cards (labels: `Settings`, `Manage agent`, `Open team settings`).

## Driving it with control-chat

Preconditions:

- Chat is healthy (`control-chat doctor` reports `ok: true`).
- The auto-dev account session is available for the active run.

- **Open settings.** Go to `/settings`. Run
  `control-chat browser goto --url /settings`. The URL includes `/settings`.
- **Confirm title.** Wait for the Settings heading. Run
  `control-chat browser wait --role heading --name "Settings"`. The heading is
  visible.
- **Proof.** Capture the settings shell. Run
  `control-chat browser snapshot --aria --path .tmp/verify-chat/evidence/settings/settings.aria.txt`
  and
  `control-chat browser screenshot --path .tmp/verify-chat/evidence/settings/settings.png`.
  Artifacts show `Settings` and authenticated Chat chrome.

## Gotchas

- Nested routes such as `/settings/agent` may render toolkit-owned panels; wait
  for the heading before snapshotting.
- Org/team settings may show create-organization empty states on a fresh
  disposable DB — that is expected, not a failure of opening Settings.
- Do not claim credential or billing flows verified unless those entry points
  are exercised with real (or clearly isolated) credentials.
