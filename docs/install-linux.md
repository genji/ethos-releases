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
`ethos-<version>-py3-none-any.whl` and `ethos-extension-<version>.zip`.

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

## 2. Read your sent mail, once

Gmail needs an **app password**: Google account → Security → 2-Step
Verification → App passwords → create one named "Ethos". Personal
Outlook.com accounts do not accept one for IMAP; skip this section and Ethos
drafts from the thread alone, without examples of how you write.

Tell Ethos where your mail is (replace the address):

```bash
mkdir -p ~/.local/share/ethos
cat > ~/.local/share/ethos/config.toml <<'EOF'
backend = "torch"

[mail]
source = "imap"

[ingest.email]
host = "imap.gmail.com"
username = "you@example.com"
EOF
```

Then read your 300 most recent sent messages — about a minute. The password
is read from the terminal and kept in this shell's memory only:

```bash
read -rs IMAP_PASSWORD && export IMAP_PASSWORD
ethos setup --source imap --me you@example.com
```

## 3. Run it

```bash
ethos serve
```

The first start downloads the model — a few GB, once; a draft asked for
before it is done waits. While it runs, mail you send is folded into the pool
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

## 4. In Gmail or Outlook on the web

1. Unzip `ethos-extension-<version>.zip` into a folder you keep.
2. In Chrome, open `chrome://extensions`, turn on **Developer mode**, click
   **Load unpacked**, and pick that folder.
3. **Reload the mail tab** — a tab already open does not get the extension
   until it reloads.
4. Open a conversation and press **Alt+Shift+D**, or click the Ethos icon.
   To answer a particular message, or to reply to everyone, click Reply or
   Reply all on it first.
5. Type what the reply should say, press **Draft**, then **Insert into
   reply**. Edit, and send as usual; the sent version is recorded.

If the panel cannot reach the server, check that `ethos serve` is running.
If it still cannot, a field appears in the panel to paste the token: the
contents of `~/.local/share/ethos/data_store/api_token`.

## 5. What is tested

Said plainly. These steps were run on a Linux machine with an NVIDIA card
(Quadro RTX 8000, driver 580, Python 3.12, PyTorch 2.14 with CUDA 13): the
wheel installs with `[cuda,imap]` into a clean environment, language
detection loads, and `ethos serve` loads the 14B model and drafts a reply
through the same calls the extension makes. Not run on Linux: reading real
mail over IMAP (its code is shared with Windows and tested against a
stand-in server), the extension in Chrome on a Linux desktop, and the systemd
service. If one of them fails, the lines that start with `ethos:` in the
browser console, or `journalctl --user -u ethos`, say what happened.
