# Ethos

Ethos drafts your email replies in your own voice, learning from your own past
replies, with a language model that runs **entirely on your machine**. Nothing
you write, receive or send leaves it — no account, no API key, no server.

You press a hotkey on the message you are answering, type a line about what the
reply should say, and it writes the reply. You edit it and send it from your
normal mail client.

> Early access. It is in daily use by its author and a small number of testers,
> and it has rough edges. Bug reports are welcome.

## Download

**[Latest release](https://github.com/genji/ethos-releases/releases/latest)**

Download one file, the one for your system. Nothing else is needed: the
installer asks how you will use Ethos and fetches the model, the browser
extension and anything else that use needs.

| | |
|---|---|
| **macOS** | `Ethos-vX.Y.Z.zip` — Apple Silicon, macOS 13+, 16 GB of memory (8 GB works, with a smaller model), ~10 GB of disk for the model |
| **Windows** | `Ethos-vX.Y.Z.exe` — an NVIDIA GPU to run the model here |
| **Linux** | `ethos-X.Y.Z-py3-none-any.whl` — Python 3.11+, an NVIDIA GPU to run the model here; then `ethos install --serve` opens the installer |

The other files on a release are fetched by the installer; you do not need to
download them. The one exception is `Ethos-Remote-vX.Y.Z.zip`, an optional add-on
for drafting on another computer's model (see below).

The installation notes: [macOS](docs/install-macos.md) · [Windows](docs/install-windows.md) ·
[Linux](docs/install-linux.md). The macOS and Windows downloads also carry theirs.

On macOS the app is not signed by Apple, so the first launch has to be approved
once in System Settings → Privacy & Security. The macOS notes walk through it.

## What the installer asks

1. **How this computer is used**: it runs the model and you draft on it; it is
   a server other computers draft on (no mail account is connected); or you
   draft here on another computer's model (no model is downloaded).
2. **Which mail to learn from**: Mail.app, any IMAP account such as Gmail, or
   Outlook.com and Microsoft 365 through a Microsoft sign-in.
3. **Where you write**: Mail.app, Gmail or Outlook on the web in Chrome, Edge,
   Brave, Vivaldi or Firefox, Thunderbird, or the new Outlook. Pick as many as apply; the same Gmail account
   can be used in both Mail.app and the browser.

The model starts downloading as soon as the first question is answered, while
your recent sent mail is read to learn how you write. That reading, the model,
and every draft stay on the machine.

## Several computers, one model

`Ethos-Remote-vX.Y.Z.zip` is the only extra download, and only for this setup:
one computer holds the model and others draft on it over a tailnet. Install
Ethos on every computer as above (the installer asks which role each one has),
then add the Remote add-on on each. Its install notes are inside the zip.

## This repository

Releases only — the downloads above and their installation notes. The source is
kept elsewhere and is not public.

MIT licensed; see [LICENSE](LICENSE).
