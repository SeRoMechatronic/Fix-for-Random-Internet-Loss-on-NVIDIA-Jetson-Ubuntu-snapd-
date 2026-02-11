# 🚀 Fix for Random Internet Loss on NVIDIA Jetson (Ubuntu + snapd)

## 🧩 What Is This About?
Some NVIDIA Jetson devices (Nano, Xavier, Orin…) running Ubuntu with **snapd** experience a strange issue:
👉 **The Jetson suddenly loses Internet connectivity**, even when the network is fine.

This README explains **what causes the issue** and provides a **reliable fix** using a manual rollback of snapd.

---

# ❗ The Problem

## 🐛 Root Cause
Certain versions of **snapd** include a bug that can break system components responsible for networking. This may cause:

- ❌ NetworkManager crashes
- ❌ DNS failure via systemd-resolved
- ❌ Network interfaces disappearing
- ❌ Connection drops (even over Ethernet)

On Jetson devices, which use custom NVIDIA kernels and AppArmor profiles, snapd's automatic refresh system can cause conflicts during installation hooks or sandboxing transitions.

💡 *When snapd breaks, several system services related to networking may also fail, resulting in total loss of Internet.*

---

# 💡 Why This Fix Works
The solution works because you:

### ✔️ Revert snapd to a previously working version
```
sudo snap revert snapd
```

### ✔️ Freeze automatic updates of snapd
```
sudo snap refresh --hold snapd
```

### ✔️ Install a known stable version manually
```
snap download snapd --revision=24724
sudo snap ack snapd_24724.assert
sudo snap install snapd_24724.snap
```

This ensures a **stable and kernel‑compatible** snapd version.

---

# 🛠️ Full Fix Instructions
Run the following commands in order:

```bash
# 1. Roll back snapd
sudo snap revert snapd

# 2. Pause auto‑updates of snapd
sudo snap refresh --hold snapd

# 3. Download a stable revision
snap download snapd --revision=24724

# 4. Approve the signature
sudo snap ack snapd_24724.assert

# 5. Install the exact stable version
sudo snap install snapd_24724.snap

# 6. (Optional) Verify
snap version
```

---

# 🧪 How to Verify It Worked
Run:
```bash
systemctl status snapd
nmcli d
ping 8.8.8.8
```
If NetworkManager no longer throws errors, the fix is successful.

---

# 🛡️ Tips to Prevent Future Issues
- ⭐ Keep snapd updates on hold
- ❌ Avoid installing snaps on Jetson unless necessary
- ⚠️ Prefer **apt** packages instead of snap
- 🔌 Avoid kernel updates that may break compatibility

---

# 📝 Credits
Fix discovered and documented by **Sebastián Rojas**.
Share this repository to help other Jetson developers 💛.
