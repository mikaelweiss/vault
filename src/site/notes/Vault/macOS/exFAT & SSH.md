---
{"dg-publish":true,"permalink":"/vault/mac-os/ex-fat-and-ssh/"}
---

If you SSH into a mac and then try and access anything in /Volumes, then it'll fail saying you don't have the right permissions.
1. System Settings → Privacy & Security → Full Disk Access
2. Click the + button
3. Press Cmd+Shift+G and type: /usr/libexec
4. Find and add sshd-keygen-wrapper
5. Also add Terminal (Ghostty for me) if you ever access locally