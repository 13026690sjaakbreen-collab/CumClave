# Cumclave Phone — SoM / Compute Module Vergelijking

## Kernconclusie

De Raspberry Pi Compute Module 5 (CM5) is een uitstekende kandidaat voor **Cumclave Phone V1**, maar Cumclave moet niet afhankelijk worden van één compute-module.

Strategische richting:

- **V1:** Raspberry Pi CM5 — snel prototype, Hackberry-platform, Fedora/bootc.
- **V2/product:** NXP i.MX95 via bijvoorbeeld Variscite, Toradex of PHYTEC.
- **Low-power:** i.MX8M Plus en vergelijkbare SoM's.
- **Andere Cumclave hardware:** TI AM62x en kleinere i.MX-modules kunnen interessant zijn voor NAS, mesh, router en embedded apparaten.

> **De Cumclave Phone moet worden ontworpen rond een modulair compute-platform, niet rond één processor.**

---

## 1. Kandidaten

1. Raspberry Pi Compute Module 5
2. Variscite DART-MX95
3. Toradex Verdin iMX95
4. PHYTEC phyFLEX i.MX95
5. Variscite DART-MX8M-PLUS
6. Toradex Verdin iMX8M Plus
7. PHYTEC phyCORE-AM62x
8. Variscite DART-MX8M-MINI

---

## 2. Overzicht

| Compute module | SoC | CPU | PCIe | Camera | Linux | Cumclave rol |
|---|---|---|---|---|---|---|
| Raspberry Pi CM5 | BCM2712 | 4× A76 2.4 GHz | Gen2 x1 | 2× 4-lane CSI | uitstekend | V1 prototype |
| Variscite DART-MX95 | i.MX 95 | tot 6× A55 2 GHz | 2× Gen3 | 2× CSI-2 | goed | V2/product |
| Toradex Verdin iMX95 | i.MX 95 | 6× A55 | i.MX95 | CSI-2 | goed | V2/product |
| PHYTEC phyFLEX i.MX95 | i.MX 95 | A55 + M7 + M33 | i.MX95 | CSI-2 | goed | security/product |
| Variscite DART-MX8M-PLUS | i.MX 8M Plus | 4× A53 1.8 GHz | Gen3 | 2× CSI-2 | goed | low-power V2 |
| Toradex Verdin iMX8M Plus | i.MX 8M Plus | 4× A53 | Gen3 | CSI | goed | low-power |
| PHYTEC phyCORE-AM62x | TI AM62x | 4× A53 | beperkt | CSI-2 | goed | low-power / NAS |
| Variscite DART-MX8M-MINI | i.MX 8M Mini | 4× A53 1.8 GHz | Gen2 | CSI | goed | budget/efficiency |

---

## 3. Raspberry Pi CM5

Belangrijkste eigenschappen:

- BCM2712
- 4× Cortex-A76 @ 2,4 GHz
- 2 / 4 / 8 / 16 GB LPDDR4-4267 ECC
- optioneel eMMC
- PCIe Gen2 ×1
- 2× USB 3
- USB 2
- 2× 4-lane MIPI CSI/DSI
- Gigabit Ethernet
- Wi-Fi/Bluetooth opties
- maximaal 30 GPIO
- HDMI
- I²C / SPI / UART
- 4K60 HEVC decode
- 55 × 40 × 4,7 mm
- Raspberry Pi geeft productieondersteuning tot minimaal januari 2036 aan.

Voor Cumclave is de architectuur zeer praktisch:

```text
CM5
 │
 ├── MIPI → Display
 ├── MIPI → Camera
 ├── PCIe → NVMe
 ├── USB3 → 5G modem
 ├── I²S → Audio
 ├── I²C → Sensors
 └── USB → Keyboard / RP2040
```

Belangrijk nadeel: slechts één PCIe Gen2-lane.

Daarom voor V1:

```text
PCIe → NVMe
USB3 → 4G / 5G modem
```

**Beoordeling:** zeer geschikt voor prototype; definitief product naast andere SoM's onderzoeken.

---

## 4. Variscite DART-MX95

Gebaseerd op NXP i.MX 95.

Belangrijk:

- tot 6× Cortex-A55 @ 2 GHz
- Cortex-M7
- Cortex-M33
- 4–16 GB LPDDR5
- 8–128 GB eMMC
- 2× PCIe Gen3
- USB 3
- 2× Gigabit Ethernet
- 2× MIPI CSI-2
- MIPI DSI
- NPU
- 3D GPU
- 4K60 video
- Wi-Fi 6
- Bluetooth 5.4
- circa 55 × 30 mm
- lange productlevensduur

Mogelijke Cumclave-architectuur:

```text
                 i.MX95
                   │
        ┌──────────┼──────────┐
        │          │          │
      PCIe       PCIe       USB3
        │          │          │
      NVMe       Future      5G
                 expansion
```

**Beoordeling:** zeer interessante V2/productkandidaat.

---

## 5. Toradex Verdin iMX95

Ook gebaseerd op i.MX95.

Sterke punten:

- industrieel SoM-platform
- lange productlevensduur
- Linux-focus
- carrier-board architectuur
- goede mogelijkheden voor migratie binnen het Toradex-platform

Dit past goed bij:

> **Hardware moet een platform zijn en geen wegwerpproduct.**

**Beoordeling:** sterke V2/productkandidaat.

---

## 6. PHYTEC phyFLEX i.MX95

Ook gebaseerd op i.MX95.

Voor Cumclave is vooral de security-oriëntatie interessant:

- Cortex-A55
- Cortex-M7
- Cortex-M33
- NPU
- hardware security
- secure boot
- secure debug
- cryptografische hardware
- secure update-mogelijkheden
- EdgeLock Secure Enclave

**Beoordeling:** zeer interessant voor een security-focused productplatform.

---

## 7. Variscite DART-MX8M-PLUS

Belangrijk:

- 4× Cortex-A53 @ 1,8 GHz
- Cortex-M7
- 1–8 GB RAM
- tot 128 GB eMMC
- PCIe Gen3
- 2× USB3
- 2× CSI-2
- MIPI DSI
- NPU
- Wi-Fi 6 mogelijkheden
- Bluetooth 5.4 mogelijkheden
- circa 55 × 30 mm
- lange beschikbaarheid

Sterk punt: energie-efficiëntie en volwassenheid.

Zwak punt: CPU aanzienlijk minder krachtig dan CM5 en i.MX95.

**Beoordeling:** interessante low-power V2-optie.

---

## 8. Toradex Verdin iMX8M Plus

Vergelijkbaar met andere i.MX8M Plus-platformen.

Interessant voor:

- low-power telefoon
- communicatieapparaat
- mesh-device
- NAS
- embedded Cumclave-node

Minder interessant wanneer maximale CPU-performance vereist is.

---

## 9. PHYTEC phyCORE-AM62x

Kenmerken:

- 4× Cortex-A53
- Cortex-M4F
- PRU-ICSS
- DDR4
- eMMC
- MIPI CSI-2
- display
- audio
- Ethernet
- secure boot-mogelijkheden
- laag energieverbruik

Vooral interessant voor:

- Cumclave NAS
- Cumclave router
- Cumclave mesh node
- IoT
- low-power edge

Minder aantrekkelijk voor de hoofd-Cumclave Phone omdat het platform minder smartphone-georiënteerd is.

---

## 10. Variscite DART-MX8M-MINI

Kenmerken:

- 4× Cortex-A53 @ 1,8 GHz
- 1–4 GB RAM
- PCIe Gen2
- MIPI CSI
- MIPI DSI
- USB2
- Wi-Fi/Bluetooth opties
- lange beschikbaarheid

Het ontbreken van USB3 maakt deze module minder aantrekkelijk voor de 5G-modemarchitectuur.

Wel interessant voor kleine Cumclave nodes, IoT en embedded hardware.

---

## 11. Vergelijking voor Cumclave Phone

| Eigenschap | CM5 | i.MX95 | i.MX8M Plus | AM62x | i.MX8M Mini |
|---|---:|---:|---:|---:|---:|
| CPU performance | ★★★★★ | ★★★★ | ★★★ | ★★★ | ★★★ |
| GPU/multimedia | ★★★★★ | ★★★★ | ★★★★ | ★★★ | ★★★ |
| NPU/AI | - | ★★★★★ | ★★★★ | beperkt | - |
| PCIe | ★★★ | ★★★★★ | ★★★★★ | ★★ | ★★ |
| USB voor 5G | ★★★★★ | ★★★★★ | ★★★★★ | ★★ | ★ |
| Camera | ★★★★★ | ★★★★★ | ★★★★★ | ★★★ | ★★★ |
| Display | ★★★★★ | ★★★★★ | ★★★★ | ★★★ | ★★★ |
| Security | ★★★ | ★★★★★ | ★★★★ | ★★★★ | ★★★★ |
| Linux | ★★★★★ | ★★★★ | ★★★★★ | ★★★★★ | ★★★★★ |
| Fedora/bootc potentieel | ★★★★★ | ★★★★ | ★★★★ | ★★★★ | ★★★★ |
| Low power | ★★★ | ★★★★ | ★★★★★ | ★★★★★ | ★★★★★ |
| Lifecycle | ★★★★★ | ★★★★★ | ★★★★★ | ★★★★ | ★★★★ |
| Prototypegemak | ★★★★★ | ★★★ | ★★★ | ★★ | ★★★ |
| Smartphone-potentieel | ★★★★ | ★★★★★ | ★★★★ | ★★ | ★★ |

**Opmerking:** de sterren zijn een technische beoordeling voor de specifieke Cumclave Phone-use-case. Ze zijn geen fabrikantbenchmarks of objectieve totaalscores.

---

## 12. Cumclave Compute Architecture

Niet:

> "De Cumclave Phone gebruikt een CM5."

Maar:

> **"De Cumclave Phone gebruikt een vervangbare compute-module."**

CM5 is dan één ondersteunde module.

```text
                 CUMCLAVE PHONE
                       │
                STANDARD SOM
                       │
          ┌────────────┼────────────┐
          │            │            │
         CM5         i.MX95       Future
          │            │          Cumclave SoM
          └────────────┼────────────┘
                       │
                CUMCLAVE CARRIER
                       │
       ┌───────────────┼────────────────┐
       │               │                │
    Display         Cellular          Storage
       │               │                │
    Camera          5G/GNSS           NVMe
       │               │                │
    Audio           Security          LoRa
```

---

## 13. Cumclave SoM-interface

De carrier board moet zoveel mogelijk onafhankelijk zijn van de specifieke SoM.

```text
               CUMCLAVE SoM
                    │
        ┌───────────┼───────────┐
        │           │           │
      PCIe         USB        MIPI
        │           │           │
      NVMe         5G       Camera/display
        │
        ├── I²C
        ├── SPI
        ├── UART
        ├── GPIO
        ├── I²S
        ├── SDIO
        └── Power
```

De precieze connector en pinout moeten nog worden ontworpen.

---

## 14. Cumclave Phone V1

**Compute:** Raspberry Pi CM5

Redenen:

- bestaande Hackberry hardware
- snelle prototypeontwikkeling
- sterke Linux-ondersteuning
- Fedora/bootc haalbaar
- goede MIPI ondersteuning
- USB3 voor modem
- PCIe voor NVMe
- lage ontwikkelingsdrempel
- veel bestaande communitykennis

---

## 15. Cumclave Phone V2

**Belangrijkste kandidaat: NXP i.MX95**

Bijvoorbeeld via:

- Variscite
- Toradex
- PHYTEC

Waarom:

- krachtige embedded architectuur
- meerdere PCIe-mogelijkheden
- Cortex-A55
- Cortex-M7
- Cortex-M33
- NPU
- moderne security
- MIPI
- USB3
- lange productlevensduur
- professioneel SoM-ecosysteem

---

## 16. Low-Power Cumclave Platform

```text
i.MX8M Plus
       │
       ├── Cumclave Mesh
       ├── NAS
       ├── Router
       ├── Embedded Phone
       └── IoT
```

---

## 17. Security

Belangrijke SoM-eigenschappen:

- Secure Boot
- hardware crypto
- secure key storage
- secure debug
- hardware-backed device identity
- trusted execution
- secure firmware updates
- rollback protection
- secure element / secure enclave

Concept:

```text
Device
  │
  └── Secure Element
          │
          ├── Private key
          ├── Device certificate
          └── Device identity
```

De private key mag het beveiligde hardwaregebied niet verlaten.

---

## 18. Fedora Atomic / bootc

De compute module moet geschikt zijn voor:

```text
Boot ROM
   ↓
Firmware
   ↓
Bootloader
   ↓
bootc
   ↓
Fedora image
   ↓
systemd
   ↓
Cumclave OS
```

Onderzoeken:

- kernel support
- device tree
- graphics
- MIPI display
- camera drivers
- Wi-Fi
- Bluetooth
- modem
- suspend/resume
- hardware acceleration
- secure boot
- bootc update/rollback

---

## 19. 5G-modem

V1:

```text
CM5
 │
 └── USB3
       │
       └── 4G/5G modem
```

Toekomstig:

```text
i.MX95
 │
 ├── PCIe → NVMe
 ├── PCIe → expansion
 └── USB3 → modem
```

De cellular module blijft daarbij zelf verwisselbaar.

---

## 20. Modulariteit

De compute module wordt onderdeel van het Cumclave Modular Hardware Platform.

```text
             CUMCLAVE PHONE
                    │
             Compute Module
                    │
       ┌────────────┼────────────┐
       │            │            │
      CM5         i.MX95       Future
       │            │            │
       └────────────┼────────────┘
                    │
             Same platform
                    │
       ┌────────────┼────────────┐
       │            │            │
     Display      Modem        NVMe
       │            │            │
     Camera       GNSS        Keyboard
       │            │            │
     Audio       Sensors       LoRa
```

---

## 21. Vervangbaarheid

Ideaal:

```text
2026
Cumclave Phone V1
CM5 + 5G modem A

2029
zelfde platform
nieuwe compute module + 5G modem B

2031
nieuwe compute + grotere NVMe

2033
nieuwe batterij
```

De gebruiker hoeft daardoor niet automatisch een volledig nieuwe telefoon te kopen.

---

## 22. Ontwerpregel

> **Design the device as a platform, not as a disposable product.**

Een onderdeel dat snel veroudert, defect kan raken, slijt of in de toekomst verbeterd kan worden, moet waar technisch en economisch verantwoord vervangbaar of upgradebaar zijn.

---

## 23. Belangrijkste risico's

### SoM-connector

Verschillende SoM's gebruiken verschillende connectoren en pinouts. Een universele carrier is daarom niet automatisch mogelijk.

### MIPI

Camera- en displayinterfaces verschillen per SoC.

### GPU

Linux graphics support kan per platform verschillen.

### Camera

Camera-ondersteuning is sterk afhankelijk van ISP, kernel, libcamera, sensor, drivers en autofocus.

### Power

Een krachtigere SoM kan meer warmte produceren en de batterijduur verminderen.

### RF

SoM, modem en antennes moeten samen worden ontworpen.

### Kosten

Industriële SoM's zijn vaak aanzienlijk duurder dan een Raspberry Pi CM5.

---

## 24. Strategische keuze

```text
                    CUMCLAVE PHONE
                           │
                    DEVELOPMENT
                           │
                         CM5
                           │
                    ─────────────
                           │
                    PRODUCT RESEARCH
                           │
                         i.MX95
                           │
                    ─────────────
                           │
                    FUTURE PLATFORM
                           │
                  Cumclave-compatible
                        SoM family
```

---

## 25. Voorlopige shortlist

### Tier A — direct onderzoeken

**Raspberry Pi CM5**  
Rol: Cumclave Phone V1 development.

**NXP i.MX95**  
Rol: Cumclave Phone V2 / product platform.

### Tier B — verder onderzoeken

**Toradex Verdin iMX95** — industriële lange-termijn productie.

**PHYTEC phyFLEX i.MX95** — security en professionele hardware.

**Variscite DART-MX8M-PLUS** — low-power toepassingen.

**Toradex Verdin iMX8M Plus** — low-power en lange lifecycle.

### Tier C — andere Cumclave apparaten

**TI AM62x** — NAS, router, mesh, embedded server, low-power edge.

**i.MX8M Mini** — eenvoudige embedded devices, low-cost nodes, IoT.

---

## 26. Aanbevolen ontwikkelstrategie

### Fase 1 — CM5

```text
CM5
 ↓
Hackberry
 ↓
Fedora
 ↓
bootc
 ↓
Cumclave OS
```

Doel: alle software- en hardwareconcepten bewijzen.

### Fase 2 — i.MX95

Parallel een i.MX95 development board onderzoeken.

```text
CM5 prototype
       │
       ├── software
       │
       └── hardware requirements
                    │
                    ↓
                 i.MX95
```

Doel: vaststellen of i.MX95 geschikt is voor het uiteindelijke Cumclave platform.

### Fase 3 — Cumclave Carrier Board

```text
              CUMCLAVE CARRIER
                     │
        ┌────────────┼────────────┐
        │            │            │
      Compute       Modem       Storage
       SoM           5G           NVMe
        │            │            │
        └────────────┼────────────┘
                     │
        ┌────────────┼────────────┐
        │            │            │
     Display       Camera       Audio
        │            │            │
     Keyboard      Sensors      LoRa
```

---

## 27. Eindconclusie

De CM5 is **niet de enige optie**.

Voor Cumclave is het beter om vanaf het begin rekening te houden met meerdere SoM-families.

### V1

**Raspberry Pi CM5** — snel ontwikkelen en testen.

### V2

**NXP i.MX95** — serieuzer, modulair en security-georiënteerd productplatform.

### Andere Cumclave-apparaten

**i.MX8M Plus / AM62x / andere low-power SoM's** — NAS, mesh, routers en embedded Cumclave hardware.

De belangrijkste architectuurkeuze:

> **Cumclave Phone moet een modulair compute-platform worden waarbij de SoM zelf vervangbaar is.**

Niet:

> "Cumclave = Raspberry Pi."

Maar:

> **"Raspberry Pi is één van de compute-modules waarop Cumclave kan draaien."**

---

## 28. Volgende technische stap

Maak vóór het definitieve carrier-board een aparte:

**Cumclave Phone Compute Module Specification**

Minimaal vastleggen:

- mechanische afmetingen
- connectorstrategie
- pinout
- power rails
- PCIe
- USB
- MIPI CSI
- MIPI DSI
- I²C
- SPI
- UART
- I²S
- GPIO
- secure boot
- secure element
- TPM/security
- Fedora/Linux
- bootc
- suspend/resume
- modem interface
- camera interface
- display interface
- thermal limits
- power limits

Daarna kunnen CM5, i.MX95 en toekomstige Cumclave SoM's tegen dezelfde Cumclave-specificatie worden getest.

---

## Bronnen

- Raspberry Pi CM5: https://www.raspberrypi.com/products/compute-module-5/
- Variscite DART-MX95: https://variscite.com/system-on-module-som/i-mx-9/i-mx-95/dart-mx95/
- Toradex Verdin iMX95: https://developer.toradex.com/hardware/verdin-som-family/modules/verdin-imx95/
- Toradex long-term availability: https://developer.toradex.com/hardware/hardware-resources/general-product-information/long-term-availability/
- Variscite DART-MX8M-PLUS: https://variscite.com/system-on-module-som/i-mx-8/i-mx-8m-plus/dart-mx8m-plus/
- Variscite DART-MX8M-MINI: https://variscite.com/system-on-module-som/i-mx-8/i-mx-8m-mini/dart-mx8m-mini/
- PHYTEC: https://www.phytec.com/
