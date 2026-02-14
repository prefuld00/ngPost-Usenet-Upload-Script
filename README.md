# 🚀 ngPost Usenet Upload Script

<div align="center">
  <img src="https://img.shields.io/badge/Status-Active-brightgreen" alt="Status Badge">
  <img src="https://img.shields.io/badge/License-Personal%20Use-blue" alt="License Badge">
  <img src="https://img.shields.io/badge/Privacy-Focused-purple" alt="Privacy Badge">
</div>

Welcome to the **ngPost Usenet Upload Script**! 🎉 This repository provides a sleek, privacy-focused configuration for uploading files to Usenet using ngPost. Perfect for securing your private backups with obfuscation, random naming, and encryption-like features. Only the NZB file unlocks your treasures! 🔐

## 🎯 Purpose

Imagine uploading your personal backups to Usenet without anyone stumbling upon them. That's the magic here! Files get compressed, obfuscated, and posted with random names and passwords. Your data stays hidden from indexers, accessible only via the generated NZB file. 🛡️✨

> **Important Warnings:** ⚠️
> - Usenet isn't fully anonymous—your IP might be logged. Use a VPN for extra peace of mind! 🌐
> - Only upload legal content (yours only!). No copyright stuff, please. 📜
> - Test with small files first to ensure everything works smoothly. 🧪
> - Use at your own risk—no absolute privacy guarantees, but we're making it tough! 😎

## 📋 Prerequisites

Get set up in no time! Here's what you need:

- **ngPost AppImage** 📦: This is the essential software you need for uploading! Grab the latest release from [ngPost Releases](https://github.com/mbruel/ngPost/releases) (e.g., `ngPost_v4.16_libssl3-x86_64.AppImage`). For more info, visit the [official ngPost repo](https://github.com/mbruel/ngPost).
- **PAR2 Tool** 🛠️: Needed for creating recovery files. Install it with `sudo apt-get install par2` on Linux.
- **Screen Tool** 🖥️: For running uploads in the background. Install with `sudo apt-get install screen` on Linux.
- **Usenet Account** 🔑: Sign up with a provider like Eweka (SSL support required). Score 77% off with this [promo code](https://www.eweka.nl/en/landing/beste-promo-a)! Replace `*********` with your creds.
- **Directories** 📁: Create these gems:
  - `/your_path/usenet_upload` (upload monitor dir).
  - `/your_path/usenet_tmp` (temp processing space).
  - `/your_path/nzb` (NZB output haven).

## 🖥️ Command

Fire up your terminal and run this beauty! Adjust paths as needed. 🚀

Here's the full **Bash script** for easy copying:

```bash
#!/bin/bash

SESSION_NAME="ngpost"  # Name der screen-Session

# Prüfe nur, wenn nicht in screen
if [[ -z "$STY" ]]; then
    # Nicht in screen-Session
    if screen -ls | grep -q "$SESSION_NAME"; then
        echo "Screen-Session '$SESSION_NAME' läuft bereits. Skript beendet, um Doppelstarts zu verhindern."
        exit 1
    else
        echo "Screen-Session '$SESSION_NAME' läuft nicht. Starte sie..."
        screen -dmS "$SESSION_NAME" bash "$0"
        echo "Session gestartet. Skript läuft nun darin."
        exit 0
    fi
fi

LOG_FILE="/home/sb100/Desktop/ngpost.log"

stdbuf -o0 -e0 ./ngPost_v4.16_libssl3-x86_64.AppImage \
  --monitor /your_path/usenet_upload \
  --rm_posted \
  --compress \
  --gen_par2 \
  --par2_pct 40 \
  --gen_name \
  --length_name 50 \
  --gen_pass \
  --length_pass 99 \
  --rar_path /usr/bin/rar \
  --rar_size 0 \
  --tmp_dir /your_path/usenet_tmp \
  --groups "alt.binaries.test,alt.binaries.misc" \
  --article_size 716800 \
  --retry 5 \
  --thread 16 \
  --obfuscate \
  --gen_from \
  --disp_progress files \
  --host post.eweka.nl \
  --port 563 \
  --ssl \
  --user ********* \
  --pass ********* \
  --connection 40 \
  --no-latest_first \
  --output /your_path/nzb 2>&1 | tee -a "$LOG_FILE"
```

## 📖 Parameter Explanations

Why these params? We chose them for **privacy**, **efficiency**, and **reliability**. Here's the breakdown in a neat table:

| Parameter | Why We Love It | Emoji |
|-----------|----------------|-------|
| `--monitor /your_path/usenet_upload` | Auto-monitors for uploads—batch like a boss! | 👀 |
| `--rm_posted` | Cleans up after success—no clutter! | 🧹 |
| `--compress` | Shrinks files + obfuscates for stealth. | 🗜️ |
| `--gen_par2` & `--par2_pct 40` | Adds error recovery—40% size for balance. | 🛠️ |
| `--gen_name` & `--length_name 50` | Random 50-char names—hard to spot! | 🎭 |
| `--gen_pass` & `--length_pass 99` | 99-char passwords—super secure! | 🔑 |
| `--rar_path /usr/bin/rar` | RAR for compression + password magic. | 📦 |
| `--rar_size 0` | No splits—keep it whole! | 🔗 |
| `--tmp_dir /your_path/usenet_tmp` | Organized temp space. | 📂 |
| `--groups "alt.binaries.test,alt.binaries.misc"` | Low-traffic groups for less visibility. | 🌐 |
| `--article_size 716800` | ~700KB articles—fast & reliable. | ⚡ |
| `--retry 5` | Retries on fails—persistent! | 🔄 |
| `--thread 16` | 16 threads—speed demon! | 🏎️ |
| `--obfuscate` | Obfuscates metadata—privacy powerhouse. | 🕵️‍♂️ |
| `--gen_from` | Random "From" header—no traces! | 🎭 |
| `--disp_progress files` | Progress feedback—stay informed! | 📊 |
| `--host post.eweka.nl`, `--port 563`, `--ssl` | Secure SSL connection. | 🔒 |
| `--user *********` & `--pass *********` | Your creds (censored). | 👤 |
| `--connection 40` | 40 connections—max speed! | 🚀 |
| `--no-latest_first` | Default order—no latest bias. | 📋 |
| `--output /your_path/nzb` | NZB output—your access key! | 📄 |

## 🚀 Usage Instructions

1. **Drop Files** 📥: Place backups in `/your_path/usenet_upload`.
2. **Run Command** ▶️: Execute in terminal—watch the magic! For long uploads, save the script as `ngpost.sh` and run in background with `screen -dmS ngpost bash ngpost.sh` to keep it running even if your terminal closes. Reattach later with `screen -r ngpost`.
3. **Monitor Progress** 👀: Files process & vanish on success.
4. **Grab NZB** 📄: Snag from `/your_path/nzb` for downloads.
5. **Download Later** ⬇️: Use NZBGet or similar with the NZB.

## 🛠️ Troubleshooting

Stuck? No worries—here's help! 😊

- **Permissions** 🔓: Make AppImage executable: `chmod +x ngPost_v4.16_libssl3-x86_64.AppImage`.
- **Errors** ❌: Check logs for connection woes—verify creds & limits.
- **Discoverability** 🔍: If indexers find it, switch groups or amp up obfuscation.
- **Legal Note** ⚖️: For education only—check your local laws!

## 🤝 Contributing

Love this? Star ⭐, fork 🍴, and contribute! Ideas welcome—let's make it even better. Ensure changes respect privacy & legality. 💡

## 📜 License

Free as in freedom, but don't be a pirate! 🏴‍☠️ Personal use only—keep it clean, ethical, and super clandestine. No license drama, just data liberty unleashed! 🔓🕶️🌟

---

*Brewed with ☕, shielded by 🛡️, and coded by 🚀 wizards for Usenet renegades. Upload like a boss, vanish like a ghost—stay epic! 👻💥*
