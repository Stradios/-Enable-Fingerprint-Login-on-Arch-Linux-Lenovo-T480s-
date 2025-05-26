# 🛡️ Enable Fingerprint Login on Arch Linux (Lenovo T480s)

This guide walks you through configuring fingerprint authentication for login and `sudo` on Arch Linux with a Lenovo T480s using **python-validity** and **open-fprintd**.

---

## 📦 Step 1: Install Required Packages

```bash
sudo pacman -S python-validity
yay -S open-fprintd
```

> `yay` is an AUR helper. Install it if you haven’t already.

---

## ⚙️ Step 2: Enable Required Services

```bash
sudo systemctl enable --now python3-validity
sudo systemctl enable --now open-fprintd
```

---

## ✋ Step 3: Enroll Your Fingerprint

Run the enrollment command and follow the prompts:

```bash
fprintd-enroll
```

Then test it:

```bash
fprintd-verify
```

---

## 🔐 Step 4: Configure PAM for Fingerprint Login

### Edit GDM login configuration:
```bash
sudo nano /etc/pam.d/gdm-password
```

**Replace contents with (or modify to include):**

```pam
#%PAM-1.0

auth       sufficient                 pam_fprintd.so
auth       include                    system-local-login
auth       optional                   pam_gnome_keyring.so

account    include                    system-local-login

password   include                    system-local-login
password   optional                   pam_gnome_keyring.so use_authtok

session    include                    system-local-login
session    optional                   pam_gnome_keyring.so auto_start
```

---

## 🧠 Step 5: Enable Fingerprint for `sudo`

```bash
sudo nano /etc/pam.d/sudo
```

**Add at the top:**
```pam
auth       sufficient                 pam_fprintd.so
```

---

## ✅ Step 6: Test It

- Lock your screen or log out → swipe your finger to log in.
- Run a `sudo` command in terminal → swipe instead of typing password.

---

## 🧯 Optional: Troubleshooting

- Restart fingerprint services:
  ```bash
  sudo systemctl restart python3-validity open-fprintd
  ```

- Check device detection:
  ```bash
  lsusb | grep Validity
  ```

- Check status:
  ```bash
  systemctl status python3-validity
  systemctl status open-fprintd
  ```

---

## 🔁 Undo/Remove Fingerprint Login

To revert changes, simply remove the line `auth sufficient pam_fprintd.so` from any edited PAM files:

```bash
sudo nano /etc/pam.d/gdm-password
sudo nano /etc/pam.d/sudo
```
