# 📖 Dual-Phone Scrcpy Setup Guide (Xiaomi + Samsung)

## 1. Scan the LAN for phones
I connected the phones via cables initially and also turned on USB debugging and then:

```bash
nmap 192.168.0.1/24
```

👉 Find your phones’ IP addresses here (example: `192.168.0.100` for Xiaomi, `192.168.0.111` for Samsung).

---

## 2. Enable Wi-Fi debugging for each phone

### Xiaomi (Android 14, port 5555)

```bash
adb devices
adb -s <xiaomi_serial> tcpip 5555
adb connect 192.168.0.100:5555
```

### Samsung (SM series, port 5556)

```bash
adb devices
adb -s <samsung_serial> tcpip 5556
adb connect 192.168.0.111:5556
```

---

## 3. Create scrcpy wrapper scripts

### 🔹 Xiaomi (Android 14) → `/usr/local/bin/scrcpy14-namura`

```bash
#!/bin/bash
# Wrapper for scrcpy on Xiaomi (Android 14) with Wi-Fi connection
# Keeps audio on the phone itself

PHONE_IP="192.168.0.100"
SCRCPY_BIN="$HOME/tools/screenshare/scrcpy-linux-x86_64-v3.3.1/scrcpy"

# Kill any stale adb session
adb disconnect ${PHONE_IP}:5555 >/dev/null 2>&1
adb connect ${PHONE_IP}:5555

# Launch scrcpy with stable settings
${SCRCPY_BIN} --serial ${PHONE_IP}:5555 --video-codec=h264 -m 1920 --no-audio
```

### 🔹 Samsung (SM series) → `/usr/local/bin/scrcpySM-koma`

```bash
#!/bin/bash
# Wrapper for scrcpy on Samsung SM phone with Wi-Fi connection
# Keeps audio on the phone itself

PHONE_IP="192.168.0.111"
PORT="5556"
SCRCPY_BIN="$HOME/tools/screenshare/scrcpy-linux-x86_64-v3.3.1/scrcpy"

# Kill any stale adb session
adb disconnect ${PHONE_IP}:${PORT} >/dev/null 2>&1
adb connect ${PHONE_IP}:${PORT}

# Launch scrcpy with stable settings
${SCRCPY_BIN} --serial ${PHONE_IP}:${PORT} --video-codec=h264 -m 1920 --no-audio
```

---

## 4. Make them executable

```bash
sudo chmod +x /usr/local/bin/scrcpy14-namura
sudo chmod +x /usr/local/bin/scrcpySM-koma
```

---

## 5. Run directly from anywhere

```bash
scrcpy14-namura   # Launch Xiaomi (Android 14)
scrcpySM-koma     # Launch Samsung SM
```

---

## 6. Reset adb if stuff breaks

```bash
adb kill-server
adb disconnect
```

---

And now I have a two screen setup yay~
and phone opens up via face ID so, fully no-touch huhu~