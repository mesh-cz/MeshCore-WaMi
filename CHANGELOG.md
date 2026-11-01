# Changelog – MeshCore-WaMi

🇬🇧 [English version below](#english-version)

Přehled změn v jednotlivých vydáních firmware MeshCore-WaMi. Firmware ke stažení je v sekci [Releases](../../releases).

Značení verzí: **verze MeshCore + dvoumístná podverze tohoto forku**, např. `v1.18.0.01` = MeshCore 1.18.0, naše první vydání na ní.

---

## v1.18.0.01 – (připravuje se)

**Novinky**
- **PowerSaving 18** od [IoTThinks](https://github.com/IoTThinks/EasySkyMesh) – výrazně nižší odběr repeateru. Ve výchozím stavu **zapnutý**, vypnutí: `powersaving off`.
  - Úsporný příjem rádia: `set radio.rxps balanced` / `set radio.rxps off`, stav: `get radio.rxps`.
  - Pravidelný restart: `set reboot.interval <hodiny>` (0 = vypnuto).
- Radio Silence Watchdog funguje i se zapnutým PowerSaving; do čítače `get radio.reboots` se pravidelné restarty z `reboot.interval` nepočítají.

**Změny**
- Nové značení verze: příkaz `ver` zobrazí `v1.18.0.01-meshcz-<commit>`.
- Nové názvy souborů: `<deska>-meshcz-v1.18.0.01-<commit>`, např. `GAT562_Mesh_Tracker_Pro_repeater-meshcz-v1.18.0.01-a95a.zip`.

**Známé problémy**
- T-Echo: hlášené potíže s příjmem – řešení `set radio.rxps off`.
- ThinkNode M8: firmware v tomto vydání chybí (chyba sestavení této desky v oficiálním MeshCore 1.18.0, chyběl už ve v1.18.0).

## v1.18.0 – 15. 10. 2026

[Release](../../releases/tag/repeater-v1.18.0)

- Aktualizace na oficiální **MeshCore v1.18.0**.
- Všechny funkce tohoto forku z verze v1.17.1 beze změny.

## v1.17.1 – 3. 9. 2026

[Release](../../releases/tag/repeater-v1.17.1)

První vydání MeshCore-WaMi, postavené na MeshCore v1.17.1.

- **Ochrana proti zaseknutí komunikace s rádiem** – časový limit při čekání na rádiový modul (`BUSY`), firmware už nemůže zamrznout navždy.
- **Radio Silence Watchdog** – automatický restart repeateru, pokud po nastavenou dobu nepřijme ani neodešle žádný paket. Ve výchozím stavu vypnutý.
  - `set radio.silence on|off`, `set radio.silence.time <1-240>` (minuty, výchozí 60).
- **Persistentní čítač automatických restartů** – `get radio.reboots`.
- **Identifikace firmware** – přípona `-meshcz` ve verzi (příkaz `ver`).

---

# English version

🇨🇿 [Czech version above](#changelog--meshcore-wami)

Changes in each MeshCore-WaMi firmware release. Firmware downloads are in [Releases](../../releases).

Version scheme: **MeshCore version + a two-digit sub-version of this fork**, e.g. `v1.18.0.01` = MeshCore 1.18.0, our first release on top of it.

---

## v1.18.0.01 – (upcoming)

**New**
- **PowerSaving 18** by [IoTThinks](https://github.com/IoTThinks/EasySkyMesh) – significantly lower repeater power draw. **Enabled** by default; disable with `powersaving off`.
  - Power-saving radio receive: `set radio.rxps balanced` / `set radio.rxps off`, status: `get radio.rxps`.
  - Periodic reboot: `set reboot.interval <hours>` (0 = off).
- The Radio Silence Watchdog keeps working with PowerSaving enabled; scheduled `reboot.interval` reboots are not counted in `get radio.reboots`.

**Changed**
- New version label: the `ver` command shows `v1.18.0.01-meshcz-<commit>`.
- New file names: `<board>-meshcz-v1.18.0.01-<commit>`, e.g. `GAT562_Mesh_Tracker_Pro_repeater-meshcz-v1.18.0.01-a95a.zip`.

**Known issues**
- T-Echo: reported receive problems – workaround `set radio.rxps off`.
- ThinkNode M8: no firmware in this release (build error for this board in official MeshCore 1.18.0; also missing from v1.18.0).

## v1.18.0 – 2026-10-15

[Release](../../releases/tag/repeater-v1.18.0)

- Updated to official **MeshCore v1.18.0**.
- All fork features from v1.17.1 unchanged.

## v1.17.1 – 2026-09-03

[Release](../../releases/tag/repeater-v1.17.1)

First MeshCore-WaMi release, based on MeshCore v1.17.1.

- **Protection against radio communication lock-up** – a timeout while waiting for the radio module (`BUSY`), so the firmware can no longer hang forever.
- **Radio Silence Watchdog** – automatically restarts the repeater if no packet is received or sent for the configured time. Disabled by default.
  - `set radio.silence on|off`, `set radio.silence.time <1-240>` (minutes, default 60).
- **Persistent automatic reboot counter** – `get radio.reboots`.
- **Firmware identification** – `-meshcz` suffix in the version (`ver` command).
