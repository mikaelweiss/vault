---
{"dg-publish":true,"permalink":"/vault/mac-os/clean-up-nix-store/"}
---

```
# Remove old generations and garbage collect
nix-collect-garbage -d

# Just garbage collect (keep old generations)
nix-collect-garbage

# Delete generations older than 30 days
nix-collect-garbage --delete-older-than 30d

# See what would be deleted (dry run)
nix-store --gc --print-dead
```


### Nix Darwin Specific
```
# Clean up system profiles
sudo nix-collect-garbage -d

# Clean up user profiles
nix-collect-garbage -d

# Delete old darwin generations
sudo nix-env --delete-generations old -p /nix/var/nix/profiles/system
```

run `darwin-rebuild switch` to double check that everything was done correctly