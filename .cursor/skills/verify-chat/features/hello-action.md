# Hello action

Hello is the example shared action shipped with Chat. The UI and the agent call
the same `hello` action; verification can invoke it through the action CLI
against the live disposable database.

## Sub-features

- `hello-cli` runs `hello` with a name and returns a greeting message.
- `hello-default` returns the default greeting when name is omitted.
- `hello-repeat` returns a stable greeting for the same input (read-only action).

## How to get to it (user POV)

- Ask the agent on Get started or Chat to call the hello action for a name.
- Run the action from a terminal with `pnpm action hello --name <name>` while
  the app (or its database) is available.

## Driving it with control-chat

Preconditions:

- Chat was launched with `control-chat launch` (preferred so the CLI shares the
  disposable DB with the server).
- `control-chat doctor` reports `ok: true` when proving against the live server.

- **Named greeting.** Call hello for Alex. Run
  `control-chat action -- hello --name Alex`. Exit code `0` and stdout contain
  `Hello, Alex!` (or a JSON object whose `message` field is that string).
- **Default greeting.** Call hello with defaults. Run
  `control-chat action -- hello`. Exit code `0` and stdout contain
  `Hello, world!`.
- **Repeat.** Call hello for Alex again. Run
  `control-chat action -- hello --name Alex`. The greeting matches the first
  call.
- **Proof.** Write the CLI transcript beside UI proof if a browser session is
  open. Save stdout to
  `.tmp/verify-chat/evidence/hello-action/hello-alex.txt` (redirect or copy the
  command output). The file contains `Hello, Alex!`.

## Gotchas

- When the dev server is running, the CLI forwards to it; when it is not, the
  action still runs in-process against `DATABASE_URL`. Prefer a launched run so
  doctor and action share one instance.
- The agent-chat path that "calls hello for Alex" needs a working model
  provider. Do not mark that entry point verified unless an agent turn actually
  invoked the tool and the transcript shows the greeting.
- Assert the greeting string, not only exit code `0`.
