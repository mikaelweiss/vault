---
{"dg-publish":true,"permalink":"/vault/random/pass/"}
---

If pass stops working, try:
```zsh
export GPG_TTY=$(tty)
gpgconf --kill gpg-agent
```