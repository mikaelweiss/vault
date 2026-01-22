---
{"dg-publish":true,"permalink":"/vault/mac-os/direnv/"}
---

To ignore the flake.nix file from git, but make direnv think its tracked:

```zsh
git add --intent-to-add flake.nix .envrc
git update-index --skip-worktree flake.nix .envrc
```

Here's the flake.nix for packages:

```flake.nix
{
  description = "ClipSpeak dev environment";

  inputs = {
    nixpkgs.url = "github:NixOS/nixpkgs/nixpkgs-unstable";
    flake-utils.url = "github:numtide/flake-utils";
  };

  outputs = { self, nixpkgs, flake-utils }:
    flake-utils.lib.eachDefaultSystem (system:
      let
        pkgs = nixpkgs.legacyPackages.${system};
      in
      {
        devShells.default = pkgs.mkShell {
          buildInputs = [
            pkgs.ANY_PACKAGE_YOU_WANT
          ];
        };
      });
}
```