# 🖥️ Pi Zero Video Looper  
*A lightweight Raspberry Pi Zero setup for looping videos and images on a business lobby TV with USB auto-updates.*

---

<p align="center">
  <img src="assets/Video%20Looper%20Bannere.png" alt="Pi Zero Video Looper Banner" width="1000" height="500"/>
</p>

---

<p align="center">
  <img src="https://img.shields.io/badge/Platform-Raspberry%20Pi-red?logo=raspberrypi&logoColor=white" />
  <img src="https://img.shields.io/badge/Language-Bash-blue?logo=gnu-bash&logoColor=white" />
  <img src="https://img.shields.io/badge/License-MIT-green" />
  <img src="https://img.shields.io/badge/Status-Stable-brightgreen" />
  <img src="https://img.shields.io/badge/Made%20By-%20Chris%20McIlrath-orange" />

</p>

---

## 📖 Overview
**Pi Zero Video Looper** is a self-contained digital signage system built on a Raspberry Pi Zero 2 W.  
It automatically loops through a playlist of videos and images on a connected TV — perfect for lobby displays, waiting rooms, or trade show booths.  

Simply copy your media files (videos or images) to a USB drive, plug it into the Pi, and it will automatically update and play the new content.  

---

## ⚙️ Features
✅ Plays multiple **videos** (`.mp4`) and **images** (`.jpg`, `.jpeg`, `.png`)  
✅ **USB auto-sync** – plug in a drive to update your content  
✅ **Autostart on boot** – hands-free operation  
✅ **Full-screen playback** via HDMI  
✅ Supports **1080p video** and automatic image rotation  
✅ Lightweight & stable – runs on Raspberry Pi OS Lite  

---

## 🧮 Hardware Requirements
| Component | Description |
|------------|-------------|
| Raspberry Pi Zero 2 W | Recommended (Pi Zero works but with reduced performance) |
| microSD Card | 16–32 GB, Class 10 |
| Power Supply | 5V 2.5A minimum |
| HDMI Adapter | Mini HDMI → HDMI |
| USB Drive | FAT32 or exFAT formatted |
| Display | Any HDMI-compatible TV or monitor |

---

## 💿 Software Requirements
- **Raspberry Pi OS Lite**
- **omxplayer**
- **feh**
- Basic Linux shell utilities (`bash`, `cp`, `ls`, etc.)

Install dependencies:
```bash
sudo apt update
sudo apt install omxplayer feh
```

---

## 🚀 Setup Instructions

### 1️⃣ Prepare Your Pi
Flash Raspberry Pi OS Lite and enable SSH/Wi-Fi if needed.  
Then create your media folder:
```bash
mkdir -p /home/pi/media
```

---

### 2️⃣ Create the Video Loop Script
Create and edit:
```bash
nano /home/pi/videoloop.sh
```

Paste this script:
```bash
#!/bin/bash

MEDIADIR="/home/pi/media"
IMAGE_DURATION=8

while true; do
  for DEV in /media/pi/*; do
    if [ -d "$DEV" ]; then
      echo "USB detected at $DEV"
      cp -u "$DEV"/*.mp4 "$MEDIADIR"/ 2>/dev/null
      cp -u "$DEV"/*.MP4 "$MEDIADIR"/ 2>/dev/null
      cp -u "$DEV"/*.jpg "$MEDIADIR"/ 2>/dev/null
      cp -u "$DEV"/*.JPG "$MEDIADIR"/ 2>/dev/null
      cp -u "$DEV"/*.jpeg "$MEDIADIR"/ 2>/dev/null
      cp -u "$DEV"/*.JPEG "$MEDIADIR"/ 2>/dev/null
      cp -u "$DEV"/*.png "$MEDIADIR"/ 2>/dev/null
      cp -u "$DEV"/*.PNG "$MEDIADIR"/ 2>/dev/null
    fi
  done

  PLAYLIST=( $(ls "$MEDIADIR" | sort) )

  for ITEM in "${PLAYLIST[@]}"; do
    FILEPATH="$MEDIADIR/$ITEM"

    if echo "$ITEM" | grep -iE '\.mp4$' >/dev/null; then
      omxplayer --no-osd --aspect-mode fill "$FILEPATH"
    fi

    if echo "$ITEM" | grep -iE '\.(jpg|jpeg|png)$' >/dev/null; then
      feh --hide-pointer --fullscreen --auto-rotate --zoom fill --slideshow-delay "$IMAGE_DURATION" "$FILEPATH"
    fi
  done
done
```

Make it executable:
```bash
chmod +x /home/pi/videoloop.sh
```

---

### 3️⃣ Autostart at Boot
Edit:
```bash
sudo nano /etc/rc.local
```

Add this line before `exit 0`:
```bash
su - pi -c "/home/pi/videoloop.sh &"
```

Reboot:
```bash
sudo reboot
```

---

## 🔁 Updating Content via USB

1. Format USB as **FAT32** or **exFAT**
2. Copy your media directly to the **root** of the USB (no folder needed)  
   Example:
   ```
   intro.mp4
   sale_promo.mp4
   menu_board.jpg
   holiday_banner.png
   ```
3. Plug the USB into the Pi  
4. Wait ~30 seconds for sync  
5. Content automatically updates on the next cycle  

---

## 🖼️ Example Setup

Images Coming soon!

| Lobby Display | Media Folder | Behind the Scenes |
|----------------|---------------|--------------------|
| ![TV Example](docs/example_tv.jpg) | ![Files](docs/example_folder.jpg) | ![Setup](docs/example_pi.jpg) |

---

## ⚙️ Optional Tweaks
- **Random Order:**  
  Replace  
  ```bash
  PLAYLIST=( $(ls "$MEDIADIR" | sort) )
  ```  
  with  
  ```bash
  PLAYLIST=( $(ls "$MEDIADIR" | shuf) )
  ```

- **Mute Audio:**  
  Add `--vol -6000` to the `omxplayer` command.

- **Force HDMI Display:**  
  In `/boot/config.txt`:
  ```
  hdmi_force_hotplug=1
  hdmi_group=1
  hdmi_mode=16
  ```

- **Hide Boot Text:**  
  In `/boot/cmdline.txt`, add:
  ```
  quiet splash
  ```

---

## 🧠 Troubleshooting
| Issue | Cause | Fix |
|-------|--------|-----|
| Videos don’t play | Wrong format | Convert to `.mp4` (H.264) |
| Images don’t show | Unsupported type | Use `.jpg` or `.png` |
| No video signal | HDMI not forced | Add HDMI settings to `/boot/config.txt` |
| USB not detected | Power or mount issue | Ensure FAT32/exFAT and reinsert |

---

## 🧪 License
This project is licensed under the **MIT License** — see [LICENSE](LICENSE) for details.

---

## ❤️ Credits
Developed by **Chris McIlrath**  
Created for display use at business lobbies, trade shows, and office environments.

---

### 🌱 Future Enhancements
- Remote content sync via SFTP or SMB share  
- Web dashboard for upload control  
- On-screen status display  
- Scheduled playlists (morning/afternoon loops)

---

> *If you like this project, give it a ⭐ on GitHub and feel free to fork or adapt it for your own signage setup!*
