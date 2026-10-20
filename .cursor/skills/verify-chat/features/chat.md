# Chat threads

Chat threads let a user open a durable conversation surface at `/chat`, start a
new thread with New Chat, and type in the shared agent composer.

## Sub-features

- `chat-open` opens the chat route shell from New Chat or `/chat`.
- `chat-composer` shows the Ask the agent composer on a thread.
- `chat-empty` shows the empty-state prompt when no messages exist yet.

## How to get to it (user POV)

- Choose the `New Chat` button in the sidebar.
- Visit `/chat` directly.
- Submit a prompt from the Get started home composer (hands off into a thread).

## Driving it with control-chat

Preconditions:

- Chat is healthy (`control-chat doctor` reports `ok: true`).
- The home page is reachable for the active run.
- No provider API key is required for shell proof; a live agent turn is out of
  scope for this recipe.

- **Open via New Chat.** From `/home`, choose New Chat. Run
  `control-chat browser goto --url /home` then
  `control-chat browser click --role button --name "New Chat"`. The URL matches
  `/chat` or `/chat/<threadId>`.
- **Confirm composer.** Wait for the chat composer. Run
  `control-chat browser wait --role textbox --name "Ask the agent..."`. The
  composer is visible.
- **Direct route.** Visit `/chat` without using the button. Run
  `control-chat browser goto --url /chat` and wait for the same textbox. The
  shell loads without a sign-in wall.
- **Proof.** Capture the empty chat shell. Run
  `control-chat browser snapshot --aria --path .tmp/verify-chat/evidence/chat/chat.aria.txt`
  and
  `control-chat browser screenshot --path .tmp/verify-chat/evidence/chat/chat.png`.
  Artifacts show the composer and Chat navigation.

## Gotchas

- Submitting a prompt starts an agent turn that needs a configured model
  provider. Without credentials the UI may show a connect/setup state — that is
  not a shell failure. Report the unmet provider precondition instead of forcing
  a turn.
- Home composer placeholder text differs from the chat composer placeholder;
  assert the string for the surface you are on.
- Thread ids in the URL are generated; assert the `/chat` prefix, not a fixed id.
