---
{"dg-publish":true,"permalink":"/vault/linux/backups/kiwix/"}
---

https://library.kiwix.org/#lang=eng

sudo dnf install kiwix-tools -y
sudo dnf install aria2 -y

aria2c -d /mnt/raid --seed-time=0 --follow-torrent=mem https://download.kiwix.org/zim/devdocs/devdocs_en_elixir_2025-10.zim.torrent
aria2c -d /mnt/raid --seed-time=0 --follow-torrent=mem https://download.kiwix.org/zim/devdocs/devdocs_en_nix_2025-10.zim.torrent
aria2c -d /mnt/raid --seed-time=0 --follow-torrent=mem https://download.kiwix.org/zim/devdocs/devdocs_en_homebrew_2025-10.zim.torrent
aria2c -d /mnt/raid --seed-time=0 --follow-torrent=mem https://download.kiwix.org/zim/devdocs/devdocs_en_phoenix_2025-10.zim.torrent
aria2c -d /mnt/raid --seed-time=0 --follow-torrent=mem https://download.kiwix.org/zim/devdocs/devdocs_en_postgresql_2025-10.zim.torrent
aria2c -d /mnt/raid --seed-time=0 --follow-torrent=mem https://download.kiwix.org/zim/devdocs/devdocs_en_python_2025-10.zim.torrent
aria2c -d /mnt/raid --seed-time=0 --follow-torrent=mem https://download.kiwix.org/zim/devdocs/devdocs_en_rust_2025-10.zim.torrent
aria2c -d /mnt/raid --seed-time=0 --follow-torrent=mem https://download.kiwix.org/zim/devdocs/devdocs_en_sqlite_2025-10.zim.torrent
aria2c -d /mnt/raid --seed-time=0 --follow-torrent=mem https://download.kiwix.org/zim/wikipedia/wikipedia_en_all_maxi_2025-08.zim.torrent
aria2c -d /mnt/raid --seed-time=0 --follow-torrent=mem https://download.kiwix.org/zim/stack_exchange/stackoverflow.com_en_all_2023-11.zim.torrent