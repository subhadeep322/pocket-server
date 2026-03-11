# 📱 PocketServer: Turn an Android Phone into a Private Home Server

Welcome! This guide will show you how to turn a standard Android phone (even one running strict Android 15) into a fully offline, private home server. 

By following this guide, you will host your own **Offline Wikipedia/Maps**, a **Private Local AI (LLM)**, and a **Personal E-Book Library**—all running simultaneously from your pocket and accessible by any computer on your home Wi-Fi.

## ⚠️ Important Warnings
* **Heat & Battery:** Running an AI server on a smartphone generates heat. If using your daily-driver phone, do not leave the AI running 24/7. Stop the servers when not in use to protect your battery health.
* **Network Security:** Only run this on a trusted private network (like your home Wi-Fi). Do **not** run these servers on public coffee shop Wi-Fi, as anyone on the network could access your files.

---

## Phase 1: The Proper Termux Setup
*Note: Do NOT download Termux from the Google Play Store. It is outdated and broken due to Google Play policy changes.*

1. Go to [F-Droid](https://f-droid.org/packages/com.termux/) or the Termux GitHub and download the latest `.apk`.
2. Install the app, open it, and run the initial update:
   pkg update -y && pkg upgrade -y
3. Grant storage permissions so you can move files to your server:
   termux-setup-storage
4. Install our required system tools (`tmux` for multi-tasking, `android-tools` for fixing Android 15):
   pkg install tmux android-tools -y

---

## Phase 2: Defeating the Android 15 "Phantom Process Killer"
Starting in Android 12 (and getting very strict in Android 15), Android will aggressively kill background terminal processes. To run a server, we must disable this using on-device Wireless Debugging.

1. Enable **Developer Options** on your phone (Tap "Build number" 7 times in Settings).
2. Go to **Developer Options -> Wireless Debugging** and turn it ON.
3. Put your phone in **Split-Screen Mode** (Settings on top, Termux on bottom).
4. In Settings, tap **Pair device with pairing code**. Note the IP, Port, and 6-digit code.
5. In Termux, pair the device:
   adb pair [IP_ADDRESS]:[PORT]
6. Check the main Wireless Debugging screen for the *new* connection port, and connect:
   adb connect [IP_ADDRESS]:[NEW_PORT]
7. Run this magic command to disable the Phantom Process Killer:
   adb shell device_config put activity_manager max_phantom_processes 2147483647
8. Disconnect: adb disconnect

---

## Phase 3: Finding Your Server IP & Using Tmux
To access your phone from a PC, you need its Local IP Address. Ensure your phone is connected to Wi-Fi.
   ifconfig
*Look under `wlan0` for `inet` (e.g., `192.168.1.15`). Write this down!*

Start the multi-window manager so we can run multiple servers at once:
   tmux
*(Shortcuts: `Ctrl+b` then `c` to create a new window. `Ctrl+b` then `0` or `1` to switch between them).*

---

## Phase 4: Setting up the Services

### 1. Offline Wikipedia & Maps (Kiwix) - *Window 0*
Kiwix serves compressed `.zim` files, allowing you to host massive libraries like Wikipedia and OpenStreetMap entirely offline.

   pkg install kiwix-tools -y
   wget https://download.kiwix.org/zim/wikipedia/wikipedia_en_simple_all_nopic_2024-10.zim
   kiwix-serve -p 8080 *.zim

**Access from PC:** Open your browser and go to `http://YOUR_IP_ADDRESS:8080`

### 2. Private Local AI (TinyLlama) - *Window 1*
*(Press `Ctrl+b`, then `c` to open a new window)*
Turn your phone into an offline AI chatbot using `llama.cpp`.

   pkg install llama-cpp -y
   wget https://huggingface.co/TheBloke/TinyLlama-1.1B-Chat-v1.0-GGUF/resolve/main/tinyllama-1.1b-chat-v1.0.Q4_K_M.gguf
   llama-server -m tinyllama-1.1b-chat-v1.0.Q4_K_M.gguf --port 8081 --host 0.0.0.0

**Access from PC:** Open your browser and go to `http://YOUR_IP_ADDRESS:8081`

### 3. Personal E-Book Library (Calibre-Web) - *Window 2*
*(Press `Ctrl+b`, then `c` to open a new window)*
Host your own legal e-books with a beautiful web UI.

   pkg install python git libxml2 libxslt libjpeg-turbo -y
   git clone https://github.com/janeczku/calibre-web.git
   cd calibre-web
   pip install --upgrade pip
   pip install -r requirements.txt
   python cps.py

**Access from PC:** Open your browser and go to `http://YOUR_IP_ADDRESS:8083` *(Default login: admin / admin123)*

---

## 🛑 How to Safely Stop the Servers
When you are done using the servers, it is vital to shut them down to save your phone's battery.
1. Go to each active tmux window and press `Ctrl + c` to kill the process.
2. Type `exit` to close the tmux windows.
3. Pull down your Android notification shade, find the Termux notification, and tap **Release Wakelock**.

## 🚀 Daily Quick-Start Guide
Once everything is installed, starting your server the next day takes 30 seconds:
1. Open Termux and type `termux-wake-lock`.
2. Type `tmux`.
3. Press the **Up Arrow** on your keyboard to find your `kiwix-serve...` command and press Enter.
4. Press `Ctrl+b` -> `c`. Press the **Up Arrow** to find your `llama-server...` command and press Enter.
5. *(Remember: Your local IP address might change if your router restarts, so use `ifconfig` to check it!)*

# llama-cpp
<img width="1863" height="1057" alt="image" src="https://github.com/user-attachments/assets/e38de433-d1c3-46ef-9f7f-d7638d6ac8e8" />


## kiwix-wikipedia
<img width="1919" height="1022" alt="Screenshot 2026-03-09 213302" src="https://github.com/user-attachments/assets/ecda8399-c768-4184-8758-18b92284bfdb" />
