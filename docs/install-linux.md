# Ethos on Linux — installation notes

Ethos drafts email replies in your own voice, using your own past replies as
examples, with a language model that runs entirely on this computer. Nothing
you write, receive or send leaves it. On Linux it drafts in **Gmail or
Outlook on the web**, through a browser extension.

**You need**: a 64-bit Linux desktop, an NVIDIA card with a current driver
(8 GB of memory on the card runs the small model, 12 GB the medium one,
16 GB or more the large one — chosen automatically), Python 3.11 or newer,
Chrome or Chromium, about 20 GB of free disk, and an **app password** from
your mail provider (section 2). From the release page: the wheel
`ethos-<version>-py3-none-any.whl`; the installer fetches the extension.

---

## 1. Install

```bash
python3 -m venv ~/.local/share/ethos-venv
~/.local/share/ethos-venv/bin/pip install "ethos-<version>-py3-none-any.whl[cuda,imap]" "py3langid==0.3.0"
echo 'export ETHOS_HOME="$HOME/.local/share/ethos"' >> ~/.profile
export ETHOS_HOME="$HOME/.local/share/ethos"
```

This installs PyTorch with its CUDA libraries — a few GB, the long step.
Put `~/.local/share/ethos-venv/bin` on your `PATH`, or call `ethos` by its
full path below.

Everything Ethos writes lives in `$ETHOS_HOME`, here `~/.local/share/ethos`:
the mail it read, the pool of your replies, your drafts. Delete that folder and Ethos has
forgotten everything. The model is cached in `~/.cache/huggingface`.

## 2. Answer the installer

```bash
read -rs IMAP_PASSWORD && export IMAP_PASSWORD   # only for an IMAP account: the app password
ethos install --serve
```

The install window opens on its own: a Chrome, Chromium, Edge, Brave or
Vivaldi app window, or, without one of those, a GTK window (it needs the
distribution's `python3-gi` and `gir1.2-webkit2-4.1`, which most desktops
have). It asks which account it learns your writing from and where you
write, and the extension is fetched only when you write in the browser. The model
downloads while your 300 most recent sent messages are read, then
`--serve` starts Ethos in this terminal with the password still in memory.
Without a desktop, open the address it prints in a browser on this machine
(from another one, forward that port with `ssh -L`); or answer every
question with flags (`--mail imap --imap-host … --imap-user … --use browser`)
and it installs with no window.

Gmail needs an **app password**, not your Google password: turn on 2-Step
Verification, then create one named "Ethos" at
https://myaccount.google.com/apppasswords. You can also type
it in the window. Microsoft accounts (Outlook.com, Microsoft 365) do not
accept one for IMAP; pick **Outlook** and the window shows a short code to
type at https://microsoft.com/devicelogin. Ethos asks for `Mail.Read` only and
keeps the sign-in in memory.

## 3. Run it

```bash
ethos serve
```

The model is already downloaded if `ethos install` finished; otherwise the
first start fetches the rest, and a draft asked for before it is done waits. While it runs, mail you send is folded into the pool
once an hour, as long as `IMAP_PASSWORD` is in its environment.

To start it at login, as a systemd user service:

```bash
mkdir -p ~/.config/systemd/user
cat > ~/.config/systemd/user/ethos.service <<EOF
[Unit]
Description=Ethos draft server

[Service]
Environment=ETHOS_HOME=%h/.local/share/ethos
ExecStart=$HOME/.local/share/ethos-venv/bin/ethos serve
Restart=on-failure

[Install]
WantedBy=default.target
EOF
systemctl --user enable --now ethos.service
```

The service does not know the app password, so it does not fold in new
mail. To give it the password for this session only — held in memory by
systemd, never written to disk:

```bash
read -rs IMAP_PASSWORD && export IMAP_PASSWORD && systemctl --user import-environment IMAP_PASSWORD
systemctl --user restart ethos.service
```

### The tray icon

On a desktop, `ethos install` puts an Ethos icon in the tray and starts it at
every login. It has the same menu as the Mac's menu bar item: what is loaded
and how much memory it holds, **Settings…** (a page in your browser to unload
or switch the model and pick your languages), **Start Ethos when I log in**
(untick it to stop the start at login), **Change what is installed…**, and
**Quit Ethos**. When Ethos is not running, **Start Ethos** starts `ethos serve`.

`ethos tray` starts the icon by hand. It runs under the system's Python, which
needs GTK 3's bindings and, on GNOME, an AppIndicator extension to show tray
icons:

```bash
sudo apt install python3-gi gir1.2-ayatanaappindicator3-0.1   # Debian, Ubuntu
sudo dnf install python3-gobject libayatana-appindicator-gtk3  # Fedora
```

If you start Ethos with the systemd service above instead, untick **Start
Ethos when I log in** so the two do not race for the port.

### Updating

Install the new wheel over the old one. `--force-reinstall --no-deps` replaces
Ethos even when the version number is unchanged; the second line refreshes its
add-ons with your saved answers:

```bash
~/.local/share/ethos-venv/bin/pip install --force-reinstall --no-deps ethos-<version>-py3-none-any.whl
~/.local/share/ethos-venv/bin/ethos install --version <version>
```

Then restart Ethos: **Quit Ethos** and **Start Ethos** in the tray icon's menu,
or `systemctl --user restart ethos.service`.

## 4. In Gmail or Outlook on the web

1. If you said you write in the browser, the installer put the extension in
   `~/.local/share/ethos/extension` (if not, `ethos install --reconfigure`).
2. In Chrome, open `chrome://extensions`, turn on **Developer mode**, click
   **Load unpacked**, and pick that folder.
3. **Reload the mail tab** — a tab already open does not get the extension
   until it reloads.
4. Open a conversation and click **Ethos** next to Reply and Forward in Gmail (or
   next to Send in a reply), press **Alt+Shift+D**, or click the Ethos icon.
   To answer a particular message, or to reply to everyone, click Reply or
   Reply all on it first.
5. Type what the reply should say, press **Draft**, then **Insert into
   reply**. Edit, and send as usual; the sent version is recorded.

**Or skip the panel and type in the reply box itself** (Gmail, Outlook on the
web, Superhuman). Click Reply, type a line of what the reply should say, and
press **Alt+Shift+D** with the cursor still in the box: Ethos replaces your line with
the reply the panel would have written from it, above the quote or below it,
wherever you typed it. In Gmail and Outlook you can also answer point by
point: expand the quote (the **…** button), type a short note on its own line
under each paragraph you answer, and press **Alt+Shift+D**: each note becomes the
passage it stands for, in order, and the quote stays as it is. (Outlook does not
mark its quote, so Ethos takes as a note any line that is not in the message you
answer.) Ctrl+Z brings your notes back. In an empty reply box,
the key opens the panel as before.

If the panel cannot reach the server, check that `ethos serve` is running.
If it still cannot, a field appears in the panel to paste the token: the
contents of `~/.local/share/ethos/data_store/api_token`.

In Firefox (140 or later), install the signed copy instead,
`ethos-firefox-<version>.xpi` from the release page: drag it onto a Firefox
window, click **Add**, and reload the mail tab. It stays installed. Without
the signed file, Firefox only loads the folder as a *temporary* add-on,
removed when it quits: `about:debugging#/runtime/this-firefox` → **Load
Temporary Add-on…** → `manifest.json` in that folder.

## 5. In Thunderbird

If you ticked Thunderbird, the installer put the add-on in the Ethos folder
as `ethos-thunderbird.xpi`, and its last page shows the path. Add it once:

1. In Thunderbird (128 or later), open **Tools → Add-ons and Themes** (under the **≡** menu).
2. Click the gear, choose **Install Add-on From File**, and pick that file.
   Thunderbird does not need it signed, and it stays installed.
3. Open a reply and click **Ethos** in its toolbar, or press **Alt+Shift+D**.
   Type what the reply should say, pick a draft, and it lands where the caret
   is. Nothing is sent for you. From a message you are reading, the same
   button sits above the message (and the same shortcut works): Insert opens
   the reply with the draft in it.
4. Or write in the email itself: type a line of what the reply should say,
   above or below the quote, or short notes between its paragraphs, and press
   **Alt+Shift+D**. Each note becomes the passage it stands for, in place, and the
   quote stays as it is. In an empty reply, the key opens Ethos as in step 3.
   Ethos may not be able to bring the notes back with Ctrl+Z: keep a copy if
   you want them.
