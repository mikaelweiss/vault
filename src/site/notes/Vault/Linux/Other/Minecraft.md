---
{"dg-publish":true,"permalink":"/vault/linux/other/minecraft/"}
---

## Setting up Quadlet
```bash
mkdir -p ~/.config/containers/systemd
sudo nvim ~/.config/containers/systemd/minecraft-server.container
```

```
[Unit]
Description=Minecraft Server
After=network-online.target
Wants=network-online.target

[Container]
ContainerName=minecraft-server
Image=docker.io/itzg/minecraft-server:latest
PublishPort=25565:25565
Volume=/home/mikaelweiss/.minecraft-server/data:/data
Environment=EULA=TRUE
Environment=TYPE=FABRIC
Environment=MEMORY=2G
Environment=VERSION=LATEST
Environment=UID=1000
Environment=GID=1000
Environment=REMOVE_OLD_MODS=FALSE
SecurityLabelDisable=true

[Service]
Restart=always

[Install]
WantedBy=default.target
```

```bash
systemctl --user daemon-reload
systemctl --user start minecraft-server
```
## Giving items syntax
give Moroni56 minecraft:cooked_porkchop 14
give Moroni56 minecraft:diamond_sword[damage=50] 1
give Moroni56 minecraft:bow[enchantments={power:2,punch:2},damage=13] 1
give Moroni56 minecraft:diamond_pickaxe[enchantments={efficiency:2},damage=203,repair_cost=1] 1
give Moroni56 minecraft:potion[potion_contents={potion:"minecraft:strong_healing"}] 1
give Moroni56 minecraft:potion[potion_contents={potion:"minecraft:long_strength"}] 1
give Moroni56 minecraft:potion[potion_contents={potion:"minecraft:fire_resistance"}] 1
give Moroni56 minecraft:splash_potion 1
give Moroni56 minecraft:ghast_tear 2
give Moroni56 minecraft:diamond_boots[enchantments={feather_falling:2,protection:2},damage=160] 1
give Moroni56 minecraft:diamond_leggings[enchantments={fire_protection:1,unbreaking:2},damage=127] 1
give Moroni56 minecraft:diamond_chestplate[enchantments={protection:2},damage=160] 1
give Moroni56 minecraft:diamond_helmet[enchantments={blast_protection:1},damage=160] 1