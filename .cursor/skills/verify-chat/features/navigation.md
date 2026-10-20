# Sidebar navigation

Sidebar navigation is the persistent rail that moves the user between Home and
Chat threads and exposes New Chat plus collapse controls.

## Sub-features

- `nav-landmark` exposes a navigation landmark named `Navigation`.
- `nav-home` returns to Get started via the Home control.
- `nav-new-chat` starts a thread via New Chat.
- `nav-collapse` toggles the sidebar collapsed state when the control is shown.

## How to get to it (user POV)

- Use the left sidebar on any authenticated page.
- On narrow viewports, open navigation with `Open navigation` if the rail is
  hidden behind the header control.

## Driving it with control-chat

Preconditions:

- Chat is healthy (`control-chat doctor` reports `ok: true`).
- The browser session is authenticated on `/home` or `/chat`.

- **See navigation.** From `/home`, confirm the landmark. Run
  `control-chat browser goto --url /home` then
  `control-chat browser wait --role navigation --name "Navigation"`. The
  landmark is present.
- **Home control.** From `/chat`, choose Home. Run
  `control-chat browser goto --url /chat`,
  `control-chat browser click --role link --name "Home"` (or button if the
  control is exposed that way), then
  `control-chat browser wait --role heading --name "Get started"`. The home
  heading returns.
- **New Chat.** Choose New Chat. Run
  `control-chat browser click --role button --name "New Chat"`. The URL matches
  `/chat` or `/chat/<threadId>`.
- **Proof.** Capture the rail with Home visible. Run
  `control-chat browser goto --url /home`,
  `control-chat browser snapshot --aria --path .tmp/verify-chat/evidence/navigation/nav.aria.txt`,
  and
  `control-chat browser screenshot --path .tmp/verify-chat/evidence/navigation/nav.png`.
  Artifacts include `Home` and `New Chat`.

## Gotchas

- When the sidebar is collapsed, labels may be `aria-label` only; role+name
  still works, visible text may not.
- Mobile layouts require `Open navigation` before Home/New Chat are clickable.
- Do not confuse the Chat brand link (app title `Chat`) with the Home nav item.
