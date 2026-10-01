# Ethos on Windows — installation notes

You have two things: **Ethos.exe** and this file. Ethos drafts email replies
in your own voice, using your own past replies as examples, with a language
model that runs entirely on this PC. Nothing you write, receive or send
leaves the machine.

**You need**: Windows 10 or 11, an NVIDIA graphics card with a current
driver (8 GB of memory on the card runs the small model, 12 GB the medium
one, 16 GB or more the large one — chosen automatically), 16 GB of system
memory, **20 GB of free disk** (the model comes pre-compressed: 2.5 GB for
the small one, 6 GB for the medium, 9.5 GB for the large, plus about 5 GB
for PyTorch), Gmail or Outlook on the web in Chrome (or Edge), and an **app
password** from your mail provider (below). An RTX 50-series card needs one
extra step — see the table at the end.

---

## 1. Install (the long part runs on its own)

1. Put **Ethos.exe** anywhere — the Desktop is fine — and double-click it.
   Windows SmartScreen will object once, because the file is not signed:
   click **More info → Run anyway**.
2. A console window opens. On the first launch it sets everything up:
   - downloads a Python runtime from github.com (about 45 MB),
   - installs PyTorch for your NVIDIA card and Ethos (**a few GB — this is
     the long step, tens of minutes on a slow connection**),
   - opens the **install window** (Edge's app window, no tabs) with three questions:
     **how this PC uses Ethos** (drafting here; serving the model to other
     computers, which connects to no mail account; or drafting on another
     computer's model), **which account it learns your writing from** (an
     IMAP account such as Gmail, Outlook, or none for now), and **where you
     write** (tick all that apply: Gmail or Outlook on the web in Chrome or
     Edge, Firefox, Outlook desktop). Your **app password** (below) goes in
     the same window,
   - starts downloading the model as soon as you answer the first question,
     and reads your 300 most recent sent messages while it downloads,
   - fetches the browser extension, if you write in the browser,
   - waits for the model, then starts the server.

**The app password.** Gmail needs one for the single read of your sent mail.
Google account → Security → 2-Step Verification → App passwords → create one
named "Ethos". It is 16 characters. Ethos uses it for that read and to fold
in mail you send later; it is kept only in memory, never written to disk.

**Outlook users, read this first.** Outlook.com, Hotmail, Live and
Microsoft 365 accounts **do not accept a password for IMAP**: Microsoft
requires its own sign-in, which this installer does not do yet. Answer
**Outlook** to the mail question: Ethos installs and runs, but every draft is
written from the thread alone, with no examples of how *you* write, which is
noticeably more generic.

**Read the next section while it installs.**

---

## 2. While it installs — what to expect

- **Leave the console window open.** It is Ethos. Closing it stops the
  server; double-clicking Ethos.exe again starts it, and the second launch
  skips the install.
- **Nothing is trained.** Setup builds a small store of who you write to and
  how you sign off, and a pool of your own replies. Every draft is written
  by the model with a few of those replies shown to it as examples.
- **The model downloads during the install**, while your sent mail is read,
  once. If that was interrupted, the server fetches the rest when it starts;
  a draft asked for before it is done waits.
- **It keeps itself current.** While it runs, mail you send is folded into
  the pool once an hour. Each launch asks for the app password again for
  that (press Enter to skip; the pool then just stays as it was).
- **Everything lives in one folder**: `%LOCALAPPDATA%\Ethos` (open it with
  `Win+R`, then paste that). The runtime, the mail it read, the pool, your
  drafts. Nothing is sent anywhere. Delete the folder and Ethos has
  forgotten everything. The model itself is cached in
  `%USERPROFILE%\.cache\huggingface`.
- `Ethos.exe --setup` opens the install window again and installs what the
  new answers need;
  `Ethos.exe --reset` removes the runtime so the next launch reinstalls.

---

## 3. In Gmail (Chrome)

The Gmail side is a browser extension. It only ever talks to Ethos on this
machine.

1. If you said you write in the browser, Ethos put the extension in
   `%LOCALAPPDATA%\Ethos\extension` — the console printed the full path. If
   not, run `Ethos.exe --setup` and pick the browser.
2. In Chrome, open `chrome://extensions`, turn on **Developer mode**, click
   **Load unpacked**, and pick that folder.
3. **Reload the Gmail tab** — a tab that was already open does not get the
   extension until it reloads.
4. Open a conversation and press **Alt+Shift+D**, or click the Ethos icon in
   the toolbar. A panel appears at the top right.
5. **To answer a particular message, or to reply to everyone, click Reply
   or Reply all on that message first**, then press the key — the panel
   says which message it is answering. With no reply box open, it answers
   the last message of the conversation.
6. Type what the reply should say — a few words; leave it empty and the
   model answers from the thread — press **Draft**, then **Insert into
   reply**: the text lands in the reply box (Gmail opens one if none is
   open). Edit, and send from Gmail. The sent version is recorded
   automatically.

If the panel says it cannot reach the server, check the Ethos window is
still open. If it still cannot, a field appears in the panel to paste the
token: it is the contents of `%LOCALAPPDATA%\Ethos\data_store\api_token`.

---

## 4. In Outlook on the web (Chrome or Edge)

The same extension, the same panel. It runs on **Outlook in the browser** —
`outlook.live.com` for a personal account, `outlook.office.com` for a work
one. It cannot run inside the **Outlook desktop app**, which is not a web
page; if you use that, open Outlook in the browser instead.

1. Load the extension exactly as in section 3, then **reload the Outlook
   tab**.
2. Open a message and press **Alt+Shift+D**, or click the Ethos icon.
3. **Click Reply or Reply all on the message first**, then press the key:
   the reply box decides who the reply goes to, and the panel says so. With
   no reply box open, it answers the message in the reading pane.
4. Type what the reply should say, press **Draft**, then **Insert into
   reply**. Edit, and send from Outlook; the sent version is recorded.

**This part has not been tested on a real Outlook account.**
If the panel says it cannot read the message, or Insert goes to the
clipboard, press F12, open the Console, and send the lines that start with
`ethos:` — they say what the page looked like to the extension, which is
exactly what is needed to fix it.

---

## 5. If something is off

| What you see | What it means |
|---|---|
| SmartScreen refuses to run it | More info → Run anyway. Once. |
| "torch" fails to install | Check the NVIDIA driver is current, then `Ethos.exe --reset` and launch again. |
| "no CUDA device" when drafting | The NVIDIA driver is older than this PyTorch needs: update it, then `Ethos.exe --reset` and launch again. |
| An RTX 50-series (Blackwell) card | Before the first launch, set the environment variable `ETHOS_TORCH_INDEX` to `https://download.pytorch.org/whl/cu128` (System → Advanced → Environment Variables), so the newer PyTorch is installed. |
| "could not download the runtime from github.com" | The network blocks GitHub; try another connection. |
| The first draft takes minutes | The model is downloading. Once. |
| Keys typed in the panel vanish | Reload the extension at `chrome://extensions`, then the Gmail tab. |
| Alt+Shift+D does nothing in a Gmail window installed as an app | Reload the extension, then reload that window. An app window gets no extension shortcuts from the browser, so Ethos listens for the keys itself. |
| The panel cannot reach the server | Is the Ethos window open? Paste the token (above). |

The console window is the log. If you report a problem, copy what it shows
— select all, Enter — together with what the panel said.
