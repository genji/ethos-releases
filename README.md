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

| | |
|---|---|
| **macOS** | `Ethos-vX.Y.Z.zip` — Apple Silicon, macOS 13+, 16 GB of memory (8 GB works, with a smaller model), ~10 GB of disk for the model |
| **Windows** | `Ethos-vX.Y.Z.exe` — an NVIDIA GPU, and Gmail in Chrome |

Each download carries its own installation notes, and they are also here:
[macOS](docs/install-macos.md) · [Windows](docs/install-windows.md).

On macOS the app is not signed by Apple, so the first launch has to be approved
once in System Settings → Privacy & Security. The macOS notes walk through it.

## What it works with

- **Mail.app** on macOS — a hotkey on the selected message or the reply window
  you already have open.
- **Gmail** in Chrome or Vivaldi, through the extension both packages carry —
  replies and new emails alike.

The first run reads your recent sent mail to learn how you write. That reading,
the model, and every draft stay on the machine.

## This repository

Releases only — the downloads above and their installation notes. The source is
kept elsewhere and is not public.

MIT licensed; see [LICENSE](LICENSE).
