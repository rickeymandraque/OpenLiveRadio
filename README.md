# OpenLiveRadio

> Redonner vie à la **Liveradio Vintage** (2009) d’Orange/AwoX — un poste web-radio encore vaillant mais abandonné côté services.

**Pourquoi ce projet ?**  
Parce qu’un appareil **fonctionnel** ne mérite pas la benne juste parce qu’un portail web a été éteint. Et parce que payer ~**100 €** pour *trois bouts de médium imitation bois, deux plastiques et un hardware à 20 €*, pour qu’en 2025 ça ne lise plus aucune radio… comment dire… **ça craint**. (Orange, on vous fait un petit signe 👃💭.)

---

## Pourquoi ce petit tacle ?

La **Freebox Révolution** (même époque) fonctionne encore en 2025 et continue de bénéficier d'améliorations et de mises à jour. Notre **Liveradio Vintage**, elle, est devenue "muette" à cause d’un service cloud retiré. En moins de 10 ans d'existence seulement 3 firmwares ont été publié, on saluera l'effort titanesque pour un appareille vendu aux alentour de 100€ tout de même. **OpenLiveRadio** veut prouver qu’un objet simple peut **survivre à son portail** — et que la meilleure obsolescence… c’est celle qu’on **refuse**.

---

## Motivation

- **Recycler** un équipement parfaitement capable : lecture MP3/WMA/AAC, écran, IR, Ethernet/Wi-Fi b/g.
- **Rendre l’appareil autonome** : playlists à jour, contrôle via web/HTTP, mises à jour simples.
- **Documenter** la machine (hard/soft) pour que chacun puisse la maintenir, la réparer, la modder.

---

## C’est quoi la Liveradio Vintage ?

Un poste radio connecté vendu par Orange vers **2009** (fab. AwoX/Inventel). À l’intérieur :  
- **SoC** ARM9 (Atmel/Microchip **AT91SAM9260**, ARMv5TE)  
- **OS** : Linux **2.6.19**, **rootfs CramFS** (RO), appli AwoX sous `/opt/awox`  
- **Réseau** : Ethernet + Wi-Fi 802.11b/g  
- **Affichage** : écran LCD ~**128×80** monochrome + télécommande IR  
- **Audio** : MP3 / WMA / WAV / RealAudio / **AAC** (USB & flux réseau)  
- **Alim** : **12 V – 2 A** (≈ 24 W)

> Les services “cloud” d’Orange étant coupés, la radio démarre mais n’affiche plus de répertoires valides. Le hardware, lui, fonctionne.

---

## Objectifs du projet

1. **Documentation** (FR)  
   - Cartographie matérielle (photos, références, pads UART/JTAG, alimentation, éventuels points MCI).  
   - Cartographie logicielle (arborescence firmware, scripts d’init, formats de playlists/favoris).  
   - Guides de flash / recovery sûrs.

2. **Quick-Fix firmware**  
   - Repack **rootfs** minimal avec **playlists/favoris** à jour (aucun service lourd).  
   - Builds reproductibles & releases via **GitHub Actions**.

3. **CFW-NAND (services de base)**  
   - **SSH (dropbear)**, **HTTP/FTP** (BusyBox), **WebUI** simple (upload de playlists, télécommande réseau), **cron**.  
   - Entrée **U-Boot** “rescue” (noyau + initramfs minimal en NAND).

4. **CFW-MCI (mod carte SD interne, si faisable)**  
   - NAND = boot minimal + **initramfs rescue** ; **rootfs sur SD** (plus rapide que l’USB FS).  
   - **Auto-update**, presets **Jamendo**, option **MQTT**, **curl**, **NTP client** (si souhaité).  
   - Dual-boot **mmc0/mmc1**, fallback **NFS/USB** → rescue.

5. **Mods hardware (facultatifs)**  
   - **UART** pour console, **USB interne + swap**, **antenne Wi-Fi** améliorée, **lecteur microSD sur MCI** (si lignes accessibles).  
   - Fiches “niveau/difficulté/bénéfice” pour chaque mod.

---

## Non-objectifs

- Pas de transcodage vidéo/serveur multimédia “lourd” : **ARM9 + 64 Mo** ≈ sobre et fiable, pas Kodi.  
- Pas d’exposition WAN par défaut (services destinés au **réseau local**).  
- Pas d’utilisation de marques/droits d’Orange — projet **non affilié**.

---

## Feuille de route (résumé)

- **M0 – Mise en place** : README, licences, structure, workflow CI.  
- **M1 – Doc & inventaire** : photos PCB, arborescence firmware, points d’entrée.  
- **M2 – Quick-Fix** : rootfs + playlists actuelles → **Release QF-YYYYMMDD**.  
- **M3 – CFW-NAND** : dropbear/httpd/ftp/WebUI/cron + entrée U-Boot **rescue**.  
- **M4 – CFW-MCI** : variante SD (si mod validé) + auto-update + options.  
- **M5 – Mods** : guides pas-à-pas (UART, USB interne, Wi-Fi, MCI/µSD).

---

## Structure du dépôt (prévisionnelle)

```
OpenLiveRadio/
├─ README.md
├─ LICENSE-CODE (MIT ou GPL-2.0-or-later ou je ne sais pas encore !)
├─ LICENSE-DOCS (CC-BY-SA-4.0 ou je ne sais pas encore !)
├─ docs/
│  ├─ hardware/     # photos, pinouts, mods
│  ├─ software/     # firmware layout, scripts boot
│  ├─ mods/         # guides (UART, USB, MCI…)
│  └─ recovery/     # flash USB, SAM-BA, rescue
├─ firmware/
│  ├─ original/     # firmwares d’origine (hashes)
│  ├─ quickfix/     # builds QF
│  ├─ cfw-nand/     # overlay + scripts
│  └─ cfw-mci/      # overlay + scripts
├─ overlays/
│  ├─ nand/
│  └─ mci/
├─ scripts/
│  ├─ extract.sh    # cramfsck -> work/
│  ├─ assemble.sh   # overlay -> out/
│  ├─ pack.sh       # mkcramfs -> rootfs.bin
│  └─ release.sh    # checksums + artefacts
└─ .github/workflows/build.yml
```

---

## Remerciements & sources communautaires

- **Porciello / Inventel** (guides, firmwares, flasher, forum)  
  - https://www.porciello.com/inventel/liveradio_bidouiller.html  
  - https://www.porciello.com/inventel/liveradio_flasher.html  
  - https://forum.porciello.com/phpBB3/viewforum.php?f=11
- **LinuxFr** — discussions techniques “Liveradio Vintage”  
  - https://linuxfr.org/forums/linux-embarque/posts/liveradio-vintage  
- Les bidouilleurs anonymes qui ont partagé des infos sur `/opt/awox`, `go.sh`, `rc.timesys`, CramFS & co.  
- Et tous ceux qui préfèrent **réparer** et **comprendre** plutôt que jeter.

---

## Statut légal & sécurité

- **Projet non affilié** (Orange/AwoX/Inventel).  
- Toute manip matérielle/logicielle se fait à vos risques (perte de garantie, risque d’endommagement).  
- Ne **publiez** pas d’images contenant des fichiers sous droit sans autorisation.  
- Par défaut, **pas d’exposition Internet** : laissez les services en LAN.

---

## Contribuer

Issues, photos HD, dumps de dmesg, retours de flash, PR bienvenue 🙌  
Langue du projet : **français**, PR en anglais acceptées.
