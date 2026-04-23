---
{"dg-publish":true,"permalink":"/vault/random/pass/","dg-note-properties":{}}
---

If pass stops working, try:
```zsh
export GPG_TTY=$(tty)
gpgconf --kill gpg-agent
```