# Ethos on macOS — installation notes

You have two things: **Ethos.app** and this file. Ethos drafts email replies
in your own voice, using your own past replies as examples, with a language
model that runs entirely on this Mac. Nothing you write, receive or send
leaves the machine.

**You need**: a Mac with Apple Silicon, macOS 13 or newer, 16 GB of memory
(8 GB works, with a smaller model), and Mail with your account set up —
setup reads your sent mail through Mail, even if you draft in Gmail in Chrome
or Vivaldi. About 10 GB of free disk for the model.

---

## 1. Install (two minutes)

1. Drag **Ethos.app** wherever you like — Applications is fine.
2. **Open it once past Gatekeeper.** The app is not signed by Apple, so the
   first double-click is refused ("cannot be opened"). Then open **System
   Settings → Privacy & Security**, scroll to the message about Ethos, click
   **Open Anyway**, and confirm. Once. (On macOS 13 and 14, right-click →
   Open does the same.)
3. The **install window** opens (a window of its own) and asks three questions:
   - **How this Mac uses Ethos**: drafting here; serving the model to other
     computers (it then connects to no mail account at all); or drafting on
     another computer's model (no model is downloaded here).
   - **Which account Ethos learns your writing from**: Mail.app, an IMAP
     account (Gmail, iCloud, Fastmail, your own server), Outlook, or none for
     now. This is only where your sent mail is read from. A Gmail account
     set up in Mail.app works for drafting in Mail.app *and* in Gmail on the
     web, so pick Mail.app.
   - **Where you write**: tick all that apply, such as Mail.app *and* Gmail
     on the web in Chrome. The browser extension is downloaded only if you
     tick a browser.
4. Press **Install**. The model has been downloading since you answered the
   first question; the window shows it, the read of your 300 most recent
   sent messages, and the extension. With Mail.app, macOS asks whether
   **Ethos** may control Mail; that is the read, so allow it. (Without
   Chrome, Vivaldi, Brave, Edge or Arc, it later also asks about Safari and
   Finder, to place the draft window beside Mail; allow both.)
5. When everything is in, Ethos starts by itself and the window shows what
   to do next, such as loading the extension. To change the answers later,
   use **Change what is installed…** in Ethos's menu bar item.

**Step 4 is the long one: the model is a few GB. Read the next section while it runs.**

---

## 2. While it reads your mail — what to expect

- **Nothing is trained.** Setup builds a small store of who you write to and
  how you sign off, and a pool of your own replies. Every draft is written
  by the model with a few of those replies shown to it as examples.
- **The model is downloaded during the install**, a few GB, once, while
  your mail is read. That is the one long wait: a few minutes on a good
  connection. If it was interrupted, Ethos fetches the rest when it starts,
  and a draft asked for before it is done simply waits. The model
  is chosen for this Mac's memory — the 14B on 24 GB, the 8B on 16 GB, the
  4B on 8 GB.
- **The hotkeys start Ethos when it is not running** (that first press waits
  for the model to load); the Gmail panel needs it already running.
  Double-clicking the app starts it; add it to *Login Items* (System
  Settings → General) so it is always there.
- **It keeps itself current.** While it runs, it folds mail you send into
  the pool: once an hour, two minutes after you insert a draft, and right
  after ⌃⌥⌘S (below). Only while Mail is open — Ethos never launches Mail
  to read it. You never need to run setup again unless your
  addresses change.
- **Everything lives in one folder**: `~/Library/Application Support/Ethos`
  — the mail it read, the pool, your drafts. Owner-only permissions, never
  sent anywhere. Delete the folder and the log in `~/Library/Logs/Ethos`,
  and Ethos has forgotten everything. The model itself is cached in
  `~/.cache/huggingface`.

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

The draft window opens in your browser and shows who the reply goes to on one
line; click **change** to edit To, Cc and Subject, with suggestions from the
people you have written to. The line under the recipient says where the draft came from —
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

**⌃⌥⌘S after sending** records the message exactly as you sent it, your
edits included, and folds it into the examples at once.
Press it with the sent message selected, or right after clicking Send.

---

## 4. In Gmail (Chrome or Vivaldi)

The Gmail side is a browser extension. It only ever talks to Ethos on
this machine.

1. If you said you write in the browser, Ethos put the extension in
   `~/Library/Application Support/Ethos/extension`. If not, pick **Change
   what is installed…** in Ethos's menu bar item and tick the browser.
2. In the browser, open `chrome://extensions` (Vivaldi: `vivaldi://extensions`),
   turn on **Developer mode**, click **Load unpacked**, and pick that folder.
   In the folder picker press ⇧⌘G and paste
   `~/Library/Application Support/Ethos/extension`.
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
If the panel says Ethos refused the token request, a field appears in it:
paste the contents of `~/Library/Application Support/Ethos/data_store/api_token`.

**In Firefox** (140 or later), install the signed copy instead,
`ethos-firefox-<version>.xpi` from the release page: drag it onto a Firefox
window, click **Add**, and reload the Gmail tab. It stays installed. Without
the signed file, Firefox only loads the folder as a *temporary* add-on,
removed when it quits: `about:debugging#/runtime/this-firefox` → **Load
Temporary Add-on…** → `manifest.json` in that folder (⇧⌘G works in the
picker too).

## 5. In Thunderbird

If you ticked Thunderbird, the installer put the add-on in the Ethos folder
as `ethos-thunderbird.xpi`, and its last page shows the path. Add it once:

1. In Thunderbird (128 or later), open **Tools → Add-ons and Themes**.
2. Click the gear, choose **Install Add-on From File**, and pick that file.
   Thunderbird does not need it signed, and it stays installed.
3. Open a reply and click **Ethos** in its toolbar, or press **Alt+Shift+D**.
   Type what the reply should say, pick a draft, and it lands where the caret
   is. Nothing is sent for you.

## 6. In the new Outlook

If you ticked the new Outlook, the installer set up the **Ethos add-in**: a
button in Outlook's ribbon that opens a pane where you say what the reply
should say and pick a draft, which lands in Outlook's own editor. Classic
Outlook is not supported.

Office loads add-ins over https only, so the install made a certificate for
`localhost` and asked macOS for your password to trust it once. Adding the add-in to Outlook
is one step the installer cannot do for you:

1. Open https://aka.ms/olksideload and sign in with your Outlook account.
2. Choose **My add-ins**, then under **Custom add-ins**: **Add a custom
   add-in** → **Add from file**.
3. Pick `outlook/manifest.xml` in the Ethos folder (the install window's last
   page shows the path).
4. Open a message and click **Ethos** in the ribbon (or under **Apps**). Ethos
   must be running.

Add-ins follow the mailbox, so it then appears in Outlook on Windows and on
the web too. If your organization blocks custom add-ins, an admin can deploy
the same manifest from the Microsoft 365 admin center (Integrated apps).

---

## 7. If something is off

| What you see | What it means |
|---|---|
| "No message is selected in Mail" | Select the message, or open a reply, then press the key again. |
| ⌃⌥⌘R does nothing | With Mail frontmost, check System Settings → Keyboard → Keyboard Shortcuts… → Services → General for *Draft with Ethos*. If it is missing, log out and back in. |
| "cannot be opened" when launching the app | Gatekeeper: System Settings → Privacy & Security → Open Anyway (step 2). |
| The window says *from the message selected in Mail* but you had a reply open | Mail hid the reply window from Ethos. Press ⌘S in that reply window, then ⌃⌥⌘R again; Insert then goes through the clipboard. |
| The first draft takes minutes | The model is downloading. Once. |
| A draft takes many seconds every time | Something else is using the memory. Pick a smaller model from the menu bar's *Base model* submenu, or set `[model] name` in `~/Library/Application Support/Ethos/config.toml` to a smaller one such as `"mlx-community/Qwen3-4B-4bit"`. |
| Keys typed in the Gmail panel vanish | Reload the extension at `chrome://extensions`, then the Gmail tab. |
| ⌥⇧D does nothing in a Gmail window you installed as an app | Reload the extension, then reload that window. An app window gets no extension shortcuts from the browser, so Ethos listens for the keys itself — which needs the reloaded copy. |

The log is at `~/Library/Logs/Ethos/server.log`. If you report a problem,
that file plus what the window said under the recipient is what we need.
