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
   - opens the **install window** (Edge's app window, no tabs) with two questions:
     **which account it learns your writing from** (an IMAP account such as
     Gmail, Outlook, or none for now), and **where you write** (tick all that
     apply: Gmail or Outlook on the web in Chrome or Edge, Firefox, Outlook
     desktop). Your **app password** (below) goes in the same window,
   - starts downloading the model as soon as the window opens, and reads
     your 300 most recent sent messages while it downloads,
   - fetches the browser extension, if you write in the browser,
   - waits for the model, then starts the server.
3. Ethos is now an installed app, for your Windows account only (no
   administrator needed). It copies itself into `%LOCALAPPDATA%\Ethos`, so the
   downloaded file can be deleted, and it adds:
   - **Ethos in the Start menu**,
   - **a start when you sign in**, with no window: the tray icon shows it runs,
   - **an icon in the taskbar's tray** (by the clock; under the ^ arrow until
     you drag it out) with the same menu as the Mac's menu bar item: what is
     loaded and how much memory it holds, **Unload the model**, **Base model**
     to switch models, **Languages**, **Start Ethos when I
     sign in** (untick it to stop the start at sign-in), **Change
     what is installed…**, and **Quit Ethos**,
   - **Ethos in Settings → Apps → Installed apps**, where **Uninstall** removes
     it (below).

**The app password.** Gmail needs one for the single read of your sent mail.
Your Google password does not work here: turn on 2-Step Verification, then
create one named "Ethos" at https://myaccount.google.com/apppasswords. It is 16 characters. Ethos uses it for that read and to fold
in mail you send later; it is kept only in memory, never written to disk.

**Outlook users.** Outlook.com, Hotmail, Live and Microsoft 365 accounts
**do not accept a password for IMAP**, so answer **Outlook** to the mail
question: the install window shows a short code to type at
https://microsoft.com/devicelogin. Ethos asks Microsoft for `Mail.Read` only,
reads your Sent Items, and keeps the sign-in in memory, never on disk.

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
- **The app password is asked once, in the install window**, never when
  Ethos starts. While that first run lasts, mail you send is folded into the
  pool once an hour. After that the pool stays as it was until
  `Ethos.exe --setup` reads your sent mail again.
- **Everything lives in one folder**: `%LOCALAPPDATA%\Ethos` (open it with
  `Win+R`, then paste that). The runtime, the mail it read, the pool, your
  drafts. Nothing is sent anywhere. Delete the folder and Ethos has
  forgotten everything. The model itself is cached in
  `%USERPROFILE%\.cache\huggingface`.
- `Ethos.exe --setup` opens the install window again and installs what the
  new answers need;
  `Ethos.exe --reset` removes the runtime so the next launch reinstalls.
- **To uninstall**: Settings → Apps → Installed apps → Ethos → Uninstall, or
  `Ethos.exe --uninstall`. It stops Ethos, removes the shortcut, the start at
  sign-in and the runtime, and asks whether to delete what Ethos learned from
  your mail too (by default that stays, and a new install picks it up). The
  model stays in `%USERPROFILE%\.cache\huggingface`; delete its `models--*`
  folders to free that space.

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
4. Open a conversation and click **Ethos** next to Reply and Forward (or
   next to Send in a reply or a new email), press **Alt+Shift+D**, or click
   the Ethos icon in the toolbar. A panel appears at the top right.
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

## 5. In Firefox

The same extension works in Firefox 140 or later, for Gmail and Outlook on
the web alike. Install the signed copy, `ethos-firefox-<version>.xpi` from the
release page:

1. Drag the `.xpi` file onto a Firefox window (or File → Open File…), and
   click **Add** when Firefox asks.
2. Open `about:addons` → **Ethos** → **Permissions**, and check that access to
   `127.0.0.1` and to your mail site is on.
3. Reload the Gmail or Outlook tab, then use it exactly as in section 3 or 4.

It stays installed across restarts. Without the signed file, Firefox only
loads the folder as a *temporary* add-on, removed when Firefox quits: open
`about:debugging#/runtime/this-firefox`, click **Load Temporary Add-on…**, pick
`manifest.json` in `%LOCALAPPDATA%\Ethos\extension`, then steps 2 and 3.

---

## 6. In Thunderbird

If you ticked Thunderbird, the installer put the add-on in the Ethos folder
as `ethos-thunderbird.xpi`, and its last page shows the path. Add it once:

1. In Thunderbird (128 or later), open **Tools → Add-ons and Themes** (under the **≡** menu).
2. Click the gear, choose **Install Add-on From File**, and pick that file.
   Thunderbird does not need it signed, and it stays installed.
3. Open a reply and click **Ethos** in its toolbar, or press **Alt+Shift+D**.
   Type what the reply should say, pick a draft, and it lands where the caret
   is. Nothing is sent for you.

## 7. In the new Outlook

If you ticked the new Outlook, the installer set up the **Ethos add-in**: a
button in Outlook's ribbon that opens a pane where you say what the reply
should say and pick a draft, which lands in Outlook's own editor. Classic
Outlook is not supported.

Office loads add-ins over https only, so the install made a certificate for
`localhost` and asked Windows (confirm when it asks) to trust it once. Adding the add-in to Outlook
is one step the installer cannot do for you:

1. Open https://aka.ms/olksideload and sign in with your Outlook account.
2. Choose **My add-ins**, then under **Custom add-ins**: **Add a custom
   add-in** → **Add from file**.
3. Pick `outlook/manifest.xml` in the Ethos folder (the install window's last
   page shows the path).
4. Open a message and click **Ethos** in the ribbon (or under **Apps**). Ethos
   must be running.

Add-ins follow the mailbox, so it then appears in Outlook on Mac and on
the web too. If your organization blocks custom add-ins, an admin can deploy
the same manifest from the Microsoft 365 admin center (Integrated apps).

---

## Updating

Run the new `Ethos.exe`. It reinstalls Ethos itself (a minute; PyTorch is
already there), keeps your examples and the model, and stops the older Ethos if
it is running. The install window then opens once, with your answers already
ticked: check them, tick anything new, and click through. It fetches this
version's browser extension and add-ons into the same places, so afterwards
reload the extension once (the reload arrow on Ethos in `chrome://extensions`;
in Firefox, drag the new `.xpi` in).

## 8. If something is off

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
