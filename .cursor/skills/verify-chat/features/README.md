# Chat verification map

This directory is the maintained source for verifying the user-facing behavior of
the Chat template (`templates/chat`). Read the index before driving the app, then
use the matching feature file as the recipe.

## Baseline preconditions

- Launch Chat with `control-chat launch` so the instance uses a disposable
  PGlite directory under `.tmp/verify-chat/runs/<run-id>/`.
- Put `control-chat` on `PATH` (see the parent skill).
- Run `control-chat doctor` and require `ok: true` with the expected `baseUrl`
  and data directory.
- Never drive an instance that was not started by this verification run.
- Prefer English UI strings from `templates/chat/app/i18n/en-US.ts`.

## Driving conventions

- Start every recipe from the baseline state unless its preconditions say otherwise.
- Prefer ARIA roles and accessible names over CSS selectors or DOM position.
- Treat every command as literal. Keep quoted names and flags unchanged.
- Run browser actions through `control-chat browser`.
- Run shared actions through `control-chat action -- <name> …`.
- Restore disposable state by cleaning up the run. Do not remove proof artifacts.

## Proof and skip reporting

- Capture the user action and the resulting state, not only the final screen.
- UI proof includes an ARIA snapshot and a screenshot with Chat identity visible
  (`Get started`, `Home`, or `New Chat`).
- Action/CLI proof includes the command, stdout, stderr, and exit code.
- Mutation proof includes a read-only second view of the stored value.
- Record the feature ID and entry point used with every artifact under
  `.tmp/verify-chat/evidence/<feature-id>/`.
- Report an unreachable path with the attempted command and the unmet precondition.
- Do not report a skipped entry point as verified through a different path.

## Feature entry contract

Each feature file starts with an H1 title and one paragraph describing the
user-visible behavior. It then uses exactly four H2 sections in this order.

1. `Sub-features` lists short IDs with one line for each behavior.
2. `How to get to it (user POV)` lists every user entry point.
3. `Driving it with control-chat` starts with `Preconditions:` and uses labeled
   bullets that pair each user action with an exact command and observable result.
4. `Gotchas` lists traps that can waste or invalidate a verification run.

Keep implementation details out of the map. Name only user paths, stable handles,
required state, commands, and observable proof.

## Features

- [Get started home](./home.md) covers landing on `/home` after auto-dev sign-in.
- [Chat threads](./chat.md) covers New Chat and the `/chat` composer shell.
- [Hello action](./hello-action.md) covers the shared `hello` action from the CLI.
- [Settings](./settings.md) covers opening the Settings page.
- [Sidebar navigation](./navigation.md) covers Home and New Chat from the rail.
