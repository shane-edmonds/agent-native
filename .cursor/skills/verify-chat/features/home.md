# Get started home

Get started home is the authenticated landing page at `/home`. It shows the Chat
branding, the Get started heading, a short lead about shared actions, and the
home composer that hands prompts into a chat thread.

## Sub-features

- `home-land` reaches `/home` after loopback auto-dev sign-in on a fresh DB.
- `home-heading` shows the `Get started` heading and lead copy.
- `home-composer` shows the home composer (Connect AI gate or hello placeholder).
- `home-docs` exposes Documentation and community links below the fold.

## How to get to it (user POV)

- Open the app on loopback after `control-chat launch` (auto-dev account signs in
  and lands on `/home`).
- Choose the `Home` item in the sidebar navigation.
- Visit `/home` directly in the browser.

## Driving it with control-chat

Preconditions:

- Chat is healthy (`control-chat doctor` reports `ok: true`).
- The run uses a disposable data directory with no real user accounts.
- Evidence directory `.tmp/verify-chat/evidence/home/` is writable.

- **Open home.** Go to `/home`. Run `control-chat browser goto --url /home`. The
  harness completes local-dev sign-in if the sign-in page appears. The URL ends
  with `/home` and the document title includes `Chat`.
- **Confirm heading.** Wait for the Get started heading. Run
  `control-chat browser wait --role heading --name "Get started"`. The heading
  is visible.
- **Confirm shell chrome.** Wait for New Chat in the rail. Run
  `control-chat browser wait --role button --name "New Chat"`. The sidebar
  navigation is visible with `Home` and `New Chat`.
- **Confirm composer region.** The home page shows either a Connect AI gate
  (`Use Builder.io` / textbox `Message agent`) when no agent engine is
  configured, or the hello placeholder `Call the hello action for Alex` when an
  engine is ready. Assert one of those with
  `control-chat browser wait --role button --name "Use Builder.io"` **or**
  `control-chat browser wait --role textbox --name "Call the hello action for Alex"`.
  Record which branch you saw.
- **Proof.** Capture ARIA and screenshot. Run
  `control-chat browser snapshot --aria --path .tmp/verify-chat/evidence/home/home.aria.txt`
  and
  `control-chat browser screenshot --path .tmp/verify-chat/evidence/home/home.png`.
  Both artifacts show `Get started` and Chat shell chrome (`Home` or `New Chat`).

## Gotchas

- A shared developer `data/pglite` with a real user shows the full sign-in page.
  Always launch through `control-chat` so the DB is disposable.
- Do not set `AUTH_DISABLED` or create accounts to bypass sign-in. Use
  `Continue as local dev` / the launch-time local-dev session only.
- Prefer `127.0.0.1` over `localhost` — they are different cookie jars.
- Clicking `Continue as local dev` before client JS hydrates does nothing; wait
  for the button to be enabled or use `browser goto --url /home`, which waits.
- Without a provider/engine connection the composer is disabled and labeled
  `Message agent`; that still counts as a successful home shell proof.
- Documentation links open external docs; proving them is optional and needs a
  separate network assertion.
