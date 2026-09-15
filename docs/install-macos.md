# Ethos on macOS — installation notes

You have two things: **Ethos.app** and this file. Ethos drafts email replies
in your own voice, using your own past replies as examples, with a language
model that runs entirely on this Mac. Nothing you write, receive or send
leaves the machine.

**You need**: a Mac with Apple Silicon, macOS 13 or newer, 16 GB of memory
(8 GB works, with a smaller model), and either Mail with your account set up
or Gmail in Chrome / Vivaldi. About 10 GB of free disk for the model.

---

## 1. Install (two minutes)

1. Drag **Ethos.app** wherever you like — Applications is fine.
2. **Open it once past Gatekeeper.** The app is not signed by Apple, so the
   first double-click is refused ("cannot be opened"). Then open **System
   Settings → Privacy & Security**, scroll to the message about Ethos, click
   **Open Anyway**, and confirm. Once. (On macOS 13 and 14, right-click →
   Open does the same.)
3. A dialog says Ethos has not learned your voice yet. Click **Set Up Now**.
   A Terminal window opens and runs the setup. macOS asks whether
   **Terminal** may control Mail — that is this step; allow it. (The first
   time you press the hotkey later, it asks the same about **Ethos**; allow
   that too.)
4. Setup reads your 300 most recent sent messages and asks which of the
   addresses it saw are yours. Answer, then let it run.
5. When Terminal says setup is done, **launch Ethos again.** The first
   launch handed setup to Terminal and quit, so nothing is running yet.
   This one starts Ethos and begins the model download. macOS now asks
   whether **Ethos** may control Mail: allow that one too.

**Step 4 takes a minute or two. Read the next section while it runs.**

---

## 2. While it reads your mail — what to expect

- **Nothing is trained.** Setup builds a small store of who you write to and
  how you sign off, and a pool of your own replies. Every draft is written
  by the model with a few of those replies shown to it as examples.
- **The model is downloaded when Ethos first starts**, a few GB, once. That
  is the one long wait: a few minutes on a good connection, in the
  background — a draft asked for before it is done simply waits. The model
  is chosen for this Mac's memory — the 14B on 24 GB, the 8B on 16 GB, the
  4B on 8 GB.
- **Ethos must be running** for the hotkeys to work. Double-clicking the app
  starts it; add it to *Login Items* (System Settings → General) so it is
  always there. The first press after a reboot is slower, because the model
  has to load.
- **It keeps itself current.** While it runs, it folds mail you send into
  the pool: once an hour, two minutes after you insert a draft, and right
  after ⌃⌥⌘S (below). You never need to run setup again unless your
  addresses change.
- **Everything lives in one folder**: `~/Library/Application Support/Ethos`
  — the mail it read, the pool, your drafts. Owner-only permissions, never
  sent anywhere. Delete the folder and Ethos has forgotten everything. The
  model itself is cached in `~/.cache/huggingface`.

---

## 3. In Mail: one key

**⌃⌥⌘R — Control, Option, Command, R.** Mail decides what it means:

- **Reply the way you always do first.** Press ⌘R (reply), ⇧⌘R (reply all),
  ⇧⌘F (forward) or ⌘N (new message) in Mail, then press ⌃⌥⌘R over that
  window. Mail has already chosen which message you are answering and who
  it goes to, and Ethos drafts into that window. This is how you reply to a
  message that is not the last one in a thread, and how you reply to
  everyone.
- **Or just select a message** and press ⌃⌥⌘R with nothing open. Ethos
  replies to it — to the newest message, if you selected a whole
  conversation.

The draft window opens in your browser with **To, Cc and Subject** filled in,
editable like in any mail client, with suggestions from the people you have
written to. The line under the recipient says where the draft came from —
*from the reply you have open in Mail* or *from the message selected in
Mail*. If it names the wrong person, that line is the first thing to check.

Say in a few words what the reply should say — one line is enough; leave it
empty and the model answers from the thread — and press **Write** (or ⌘↵).
Edit the draft as you like, then **Insert**: the text lands in the Mail
window, above the quoted thread, with the recipients and subject you set.
Finish in Mail and send it as usual.

**One case to know about.** If your reply window has been open for a while,
Mail saves it as a draft, and Mail will not let Ethos write into a saved
draft. Insert then puts the text on the clipboard and the window tells you
to press ⌘V in your draft. Everything else works the same.

**⌃⌥⌘S after sending** records the message exactly as you sent it — your
edits are what Ethos learns from — and folds it into the examples at once.
Press it with the sent message selected, or right after clicking Send.

---

## 4. In Gmail (Chrome or Vivaldi)

The Gmail side is a browser extension. It only ever talks to Ethos on
this machine.

1. Ethos put the extension in
   `~/Library/Application Support/Ethos/extension`.
2. In the browser, open `chrome://extensions` (Vivaldi: `vivaldi://extensions`),
   turn on **Developer mode**, click **Load unpacked**, and pick that folder.
3. **Reload the Gmail tab** — a tab that was already open does not get the
   extension until it reloads.
4. Open a conversation and press **⌥⇧D** (Option, Shift, D), or click the
   Ethos icon in the toolbar. A panel appears at the top right.
5. Same rule as in Mail: **to answer a particular message, or to reply to
   everyone, click Reply or Reply all on that message first**, then press
   the key — the panel says which message it is answering. With no reply
   box open, it answers the last message of the conversation.
6. Type what the reply should say, press **Draft**, then **Insert into
   reply**: the text lands in the reply box (Gmail opens one if none is
   open). Edit, and send from Gmail. The sent version is recorded
   automatically; there is no ⌃⌥⌘S step here.

If the panel says it cannot reach the server, check that Ethos is running.
If it still cannot, a field appears in the panel to paste the token: it is
the contents of `~/Library/Application Support/Ethos/data_store/api_token`.

---

## 5. If something is off

| What you see | What it means |
|---|---|
| "No message is selected in Mail" | Select the message, or open a reply, then press the key again. |
| "cannot be opened" when launching the app | Gatekeeper: System Settings → Privacy & Security → Open Anyway (step 2). |
| The window says *from the message selected in Mail* but you had a reply open | Mail hid the reply window from Ethos. Close it, select the message, press the key. |
| The first draft takes minutes | The model is downloading. Once. |
| A draft takes many seconds every time | Something else is using the memory. Ethos picks a smaller model if you set `[model] name` in `~/Library/Application Support/Ethos/config.toml` to `"mlx-community/Qwen3-8B-4bit"`. |
| Keys typed in the Gmail panel vanish | Reload the extension at `chrome://extensions`, then the Gmail tab. |
| ⌥⇧D does nothing in a Gmail window you installed as an app | Reload the extension, then reload that window. An app window gets no extension shortcuts from the browser, so Ethos listens for the keys itself — which needs the reloaded copy. |

The log is at `~/Library/Logs/Ethos/server.log`. If you report a problem,
that file plus what the window said under the recipient is what we need.
