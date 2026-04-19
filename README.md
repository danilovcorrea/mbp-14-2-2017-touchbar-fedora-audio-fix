# MBP 14,2 (2017) Audio Fix for Fedora 43 (Kernel 6.19+)

This repository serves as a technical record for enabling internal audio on MacBook Pro 14,2 (2017 Touch Bar) models using the **Cirrus Logic CS8409** chip on Fedora 43.

## 📖 Credits & Context
Properly initializing the CS8409 on Apple hardware requires specific verb sequences and DKMS management. This guide is based on:
* **Reference Article:** [Ranquetat's Medium Post](https://medium.com/@ranquetat/how-to-install-kernel-6-17-and-fix-sound-on-macs-cirrus-cs8409-running-linux-8641c1cf4d98)
* **Local Backups:** Backup forks are available in this account.

---

## 🛠️ The Fix Procedure

### 1. Environment Setup
Install the necessary compilation tools on Fedora:
```bash
sudo dnf groupinstall "Development Tools" -y
sudo dnf install dkms alsa-tools git kernel-devel kernel-headers -y
