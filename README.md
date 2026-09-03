# MeshCore-WaMi

🇬🇧 [English version below](#english-version)

> **Upstream:** [MeshCore](https://github.com/meshcore-dev/MeshCore)  
> **Zaměření:** Repeater firmware / zvýšení provozní spolehlivosti  
> **Self-recovery:** Ano  
> **Radio Silence Watchdog:** Volitelný, ve výchozím stavu vypnutý  
> **Výchozí timeout:** 60 minut  
> **Identifikace buildu:** `-meshcz`

MeshCore-WaMi je komunitní fork projektu [MeshCore](https://github.com/meshcore-dev/MeshCore), zaměřený především na zvýšení spolehlivosti repeaterů, zejména při dlouhodobém bezobslužném provozu.

Repozitář obsahuje upravený zdrojový kód, předpřipravené firmware buildy a dokumentaci pro koncové uživatele.

---

## 📘 O projektu MeshCore-WaMi

MeshCore-WaMi rozšiřuje oficiální firmware MeshCore o doplňkové ochranné a zotavovací mechanismy, které mají zvýšit stabilitu repeaterů v reálném provozu.

Je určen především pro instalace, kde je fyzický přístup k zařízení obtížný — například pro střešní, stožárové, solárně napájené nebo jinak vzdálené repeatery.

---

## ❓ Proč tento fork?

MeshCore repeatery jsou často instalovány na místech, kde je ruční zásah komplikovaný nebo nemožný.

Pokud se rádio nebo komunikace mezi MCU a rádiovým modulem dostane do chybového stavu, může repeater přestat předávat provoz a zůstat nefunkční až do ručního restartu.

K podobnému stavu může dojít například v důsledku:

- chyby komunikace mezi MCU a rádiovým modulem,
- zaseknutí nebo neodpovídajícího rádiového modulu,
- nekonečného čekání na stavový signál rádia,
- krátkodobého podpětí nebo přepětí,
- poklesu napětí baterie pod stabilní provozní úroveň,
- nestabilního napájení během vybíjení nebo po opětovném nabití baterie,
- napěťových špiček,
- elektromagnetického nebo vysokofrekvenčního rušení,
- bouřky nebo blízkého úderu blesku,
- jiného neočekávaného stavu, při kterém rádio přestane správně reagovat.

Typickým příkladem je **solárně napájený repeater**. Pokud napětí baterie výrazně poklesne, rádiový modul nebo jeho komunikace s MCU se může dostat do nestandardního stavu. Po opětovném dobití baterie už může být napájení dostatečné, ale samotné rádio se bez restartu nemusí správně zotavit.

Cílem tohoto forku tedy není pouze opravit konkrétní známou chybu, ale také doplnit **mechanismus automatického zotavení**, který rozpozná dlouhodobou neaktivitu rádia a v případě potřeby zařízení restartuje.

---

## ⚡ Hlavní změny oproti oficiálnímu MeshCore

### 1. Ochrana proti zaseknutí komunikace s rádiem

Byl opraven problém v obsluze rádia, kdy firmware mohl za určitých okolností čekat na stav `BUSY` bez časového limitu.

Pokud rádio přestalo správně reagovat, mohlo dojít k zablokování dalšího běhu firmware.

Tento fork doplňuje timeout a zabraňuje nekonečnému čekání na rádiový modul.

### 2. Radio Silence Watchdog

Firmware obsahuje dodatečný softwarový watchdog sledující skutečný příjem paketů z rádiového modulu.

Pokud repeater po nastavenou dobu nepřijme žádný validní rádiový paket, watchdog vyhodnotí stav jako možný problém rádia a automaticky restartuje zařízení.

To umožňuje zotavení repeateru z některých stavů zaseknutí rádia bez nutnosti fyzického zásahu.

> **Poznámka:** Radio Silence Watchdog je **ve výchozím stavu vypnutý**.

### 3. Persistentní čítač automatických restartů

Firmware ukládá počet restartů vyvolaných Radio Silence Watchdogem.

Tento čítač zůstává zachován i po běžném restartu zařízení, takže je možné zjistit, zda se repeater již automaticky zotavoval a kolikrát k tomu došlo.

---

## 🔧 Nové CLI příkazy

Firmware přidává následující CLI příkazy:

| Příkaz | Popis |
|---|---|
| `set radio.silence on` | Zapne Radio Silence Watchdog. |
| `set radio.silence off` | Vypne Radio Silence Watchdog. |
| `set radio.silence.time <1-240>` | Nastaví maximální dobu bez přijetí skutečného rádiového paketu, po jejímž překročení dojde k automatickému restartu repeateru. Hodnota je v minutách. |
| `get radio.reboots` | Zobrazí počet restartů vyvolaných Radio Silence Watchdogem. |

### Výchozí nastavení

- **Radio Silence Watchdog:** vypnutý
- **Časový limit:** 60 minut
- **Rozsah nastavení:** 1–240 minut

### Možnosti konfigurace

Watchdog lze konfigurovat:

- lokálně přes sériovou CLI konzoli,
- vzdáleně přes MeshCore admin přístup.

Konfigurace watchdogu je persistentní a zůstává zachována po restartu zařízení i po aktualizaci firmware.

---

## 🧠 Jak watchdog funguje

Zjednodušená logika:

```text
Rádio přijímá pakety
        │
        ▼
Aktualizace času posledního příjmu
        │
        ▼
Je rádio aktivní?
     │       │
    ANO      NE
     │       │
     │       ▼
     │   Byl překročen
     │   nastavený časový limit?
     │       │
     │      ANO
     │       │
     │       ▼
     │   Restart zařízení
     │
     └── pokračování běžného provozu
```

Watchdog tedy nekontroluje pouze běh hlavní smyčky firmware, ale sleduje **skutečnou aktivitu rádia**.

Pomáhá tak i v situacích, kdy rádio zůstane zaseknuté po problému s napájením, například při **podpětí baterie a následném oživení po dobití**.

---

## 🏷️ Identifikace firmware

Buildy vytvořené z tohoto forku používají vlastní příponu verze:

```text
v1.17.1-meshcz-<commit>
```

Aktuální verzi firmware lze zobrazit příkazem:

```text
ver
```

Přípona `-meshcz` umožňuje snadno rozlišit oficiální firmware MeshCore od firmware vytvořeného z tohoto forku.

Součástí verze je také identifikátor zdrojového commitu, takže lze přesně určit, z jaké revize zdrojového kódu byl konkrétní build vytvořen.

---

## 📦 Stažení firmware

Předpřipravené firmware buildy jsou publikovány v sekci:

[Releases](../../releases)

Firmware tedy není nutné kompilovat ručně ze zdrojových kódů.

---

## ⚠️ Upozornění

Tento projekt je **komunitní fork** MeshCore a **není oficiální součástí** projektu MeshCore.

Úpravy jsou zaměřeny především na zvýšení odolnosti repeaterů provozovaných nepřetržitě nebo na obtížně dostupných místech.

Automatický restart je **poslední záchranný mechanismus**. Nenahrazuje:

- správně navržené napájení,
- ochranu proti podpětí,
- přepěťovou ochranu,
- kvalitní RF instalaci,
- ani odpovídající ochranu před vlivy prostředí a elektrickými poruchami.

---

## 📄 Licence

MeshCore-WaMi vychází z projektu [MeshCore](https://github.com/meshcore-dev/MeshCore), který je licencován pod MIT licencí.

Původní copyright:

```text
Copyright (c) 2025 Scott Powell / rippleradios.com
```

Úplné znění licence je dostupné v souboru [LICENSE.txt](LICENSE.txt).

---

# English version

🇨🇿 [Czech version above](#meshcore-wami)

> **Upstream:** [MeshCore](https://github.com/meshcore-dev/MeshCore)  
> **Focus:** Repeater firmware / improved operational reliability  
> **Self-recovery:** Yes  
> **Radio Silence Watchdog:** Optional, disabled by default  
> **Default timeout:** 60 minutes  
> **Build identifier:** `-meshcz`

MeshCore-WaMi is a community fork of [MeshCore](https://github.com/meshcore-dev/MeshCore), focused primarily on improving repeater reliability, especially in long-term unattended deployments.

The repository contains modified source code, pre-built firmware images, and documentation for end users.

---

## 📘 About MeshCore-WaMi

MeshCore-WaMi extends the official MeshCore firmware with additional protection and recovery mechanisms designed to improve repeater stability under real-world operating conditions.

It is intended primarily for installations where physical access is difficult — for example rooftop, mast-mounted, solar-powered, or otherwise remote repeaters.

---

## ❓ Why this fork?

MeshCore repeaters are often deployed in locations where manual intervention is difficult or impossible.

If the radio module or communication between the MCU and the radio enters an invalid state, the repeater may stop forwarding traffic and remain unavailable until it is manually restarted.

Such a condition may occur, for example, as a result of:

- communication errors between the MCU and radio module,
- a locked or unresponsive radio module,
- indefinite waiting for a radio status signal,
- temporary undervoltage or overvoltage,
- battery voltage dropping below a stable operating level,
- unstable power during battery discharge or after recharging,
- voltage spikes,
- electromagnetic or RF interference,
- thunderstorms or nearby lightning strikes,
- other unexpected conditions in which the radio stops responding correctly.

A typical example is a **solar-powered repeater**. If the battery voltage drops significantly, the radio module or its communication with the MCU may enter an invalid state. After the battery is charged again, the supply voltage may be sufficient, but the radio itself may not recover correctly without a restart.

The purpose of this fork is therefore not only to fix a specific known issue, but also to provide an **automatic recovery mechanism** capable of detecting prolonged radio inactivity and restarting the device when necessary.

---

## ⚡ Key Improvements Compared to Official MeshCore

### 1. Protection against radio communication lock-up

A problem in the radio handling code was fixed where, under certain conditions, the firmware could wait for the radio `BUSY` state without a timeout.

If the radio stopped responding correctly, this could block further firmware execution.

This fork adds a timeout and prevents the firmware from waiting indefinitely for the radio module.

### 2. Radio Silence Watchdog

The firmware includes an additional software watchdog that monitors actual packet reception from the radio module.

If the repeater does not receive any valid radio packet for the configured period of time, the watchdog treats the condition as a potential radio failure and automatically restarts the device.

This allows the repeater to recover from certain radio lock-up conditions without requiring physical access.

> **Note:** Radio Silence Watchdog is **disabled by default**.

### 3. Persistent automatic reboot counter

The firmware stores the number of restarts triggered by the Radio Silence Watchdog.

The counter remains available after a normal device restart, making it possible to determine whether the repeater has already recovered automatically and how many recovery events have occurred.

---

## 🔧 New CLI Commands

The firmware adds the following CLI commands:

| Command | Description |
|---|---|
| `set radio.silence on` | Enables the Radio Silence Watchdog. |
| `set radio.silence off` | Disables the Radio Silence Watchdog. |
| `set radio.silence.time <1-240>` | Sets the maximum period without receiving a valid radio packet before the repeater is automatically restarted. The value is specified in minutes. |
| `get radio.reboots` | Displays the number of restarts triggered by the Radio Silence Watchdog. |

### Default settings

- **Radio Silence Watchdog:** disabled
- **Timeout:** 60 minutes
- **Configurable range:** 1–240 minutes

### Configuration methods

The watchdog can be configured:

- locally through the serial CLI console,
- remotely through MeshCore admin access.

The watchdog configuration is persistent and remains stored after a device restart or firmware update.

---

## 🧠 How the watchdog works

Simplified logic:

```text
Radio receives packets
        │
        ▼
Update last receive timestamp
        │
        ▼
Is the radio active?
     │       │
    YES      NO
     │       │
     │       ▼
     │   Has the configured
     │   timeout expired?
     │       │
     │      YES
     │       │
     │       ▼
     │   Restart device
     │
     └── continue normal operation
```

The watchdog therefore does not only monitor whether the main firmware loop is running — it monitors **actual radio activity**.

This also helps in situations where the radio remains stuck after a power-related issue, such as **battery undervoltage followed by recovery after recharging**.

---

## 🏷️ Firmware Identification

Builds produced from this fork use a custom version suffix:

```text
v1.17.1-meshcz-<commit>
```

The currently running firmware version can be displayed using:

```text
ver
```

The `-meshcz` suffix makes it easy to distinguish official MeshCore firmware from firmware built from this fork.

The version string also includes the source commit identifier, making it possible to determine exactly which source revision was used for a particular build.

---

## 📦 Firmware Downloads

Pre-built firmware images are published in the:

[Releases](../../releases)

section of this repository.

Users therefore do not need to compile the firmware manually from source code.

---

## ⚠️ Important Notice

This project is a **community-maintained fork** of MeshCore and is **not an official part** of the MeshCore project.

The modifications are primarily intended to improve the resilience of repeaters operating continuously or installed in difficult-to-access locations.

Automatic restart is a **last-resort recovery mechanism**. It does not replace:

- properly designed power supplies,
- undervoltage protection,
- surge protection,
- a reliable RF installation,
- or appropriate protection against environmental and electrical disturbances.

---

## 📄 License

MeshCore-WaMi is based on [MeshCore](https://github.com/meshcore-dev/MeshCore), which is licensed under the MIT License.

Original copyright:

```text
Copyright (c) 2025 Scott Powell / rippleradios.com
```

See [LICENSE.txt](LICENSE.txt) for the full license text.
