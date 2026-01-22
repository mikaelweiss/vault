---
{"dg-publish":true,"permalink":"/vault/3d-printing/prusa-connect-setup-raspberry-pi/"}
---

```zsh
sudo apt install git python3-pip libcap-dev python3-numpy libffi-dev libopenblas0 python3-libcamera
# For camera support (if using Pi camera):
sudo apt install -y python3-libcamera --no-install-recommends
pip install PrusaLink --break-system-packages
pip install legacy-cgi --break-system-packages
nvim .bashrc
Add: export PATH="$HOME/.local/bin:$PATH"
:q
sudo usermod -a -G dialout $USER
logout
# Sign out and back in again
prusalink -f # run in forground to check any issues
prusalink start # To run it as a daemon
```