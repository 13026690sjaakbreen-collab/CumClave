# Cumclave Phone V2 — i.MX95 + EdgeLock Secure Enclave

**Platform:** NXP i.MX95  
**Security:** EdgeLock Secure Enclave — Advanced Profile  
**OS:** Cumclave OS, gebaseerd op Fedora Atomic / bootc  
**Status:** Technische architectuur en R&D-plan  
**Versie:** 1.0

## 1. Doel

De Cumclave Phone V2 wordt ontworpen rond de NXP i.MX95 als centrale compute- en securityplatform. De **EdgeLock Secure Enclave (Advanced Profile)** wordt vanaf het begin onderdeel van de hardware- en softwarearchitectuur.

Gewenste vertrouwensketen:

```text
i.MX95 hardware
      ↓
Hardware Root of Trust
      ↓
EdgeLock Secure Enclave
      ↓
Secure Boot
      ↓
TF-A / U-Boot
      ↓
OP-TEE / Trusted World
      ↓
Linux kernel
      ↓
Fedora Atomic / bootc
      ↓
SELinux + systemd
      ↓
Cumclave OS
      ↓
Cumclave applications
```

## 2. Waarom i.MX95?

De i.MX95 past bij Cumclave door de combinatie van Linux, embedded interfaces en hardwarematige security. Relevante eigenschappen zijn onder meer Cortex-A55, Cortex-M7/M33, LPDDR5, eMMC, PCIe Gen3, USB 3, MIPI CSI-2/DSI, NPU/GPU en de EdgeLock Secure Enclave.

Voor Cumclave is vooral deze combinatie belangrijk:

```text
i.MX95
  +
EdgeLock Secure Enclave
  +
Secure Boot
  +
OP-TEE
  +
Fedora Atomic / bootc
```

## 3. Security-by-design

Security wordt niet achteraf toegevoegd. Hardware, bootproces, OS en applicaties worden vanuit één securitymodel ontworpen.

Belangrijke principes:

- hardware root of trust
- secure boot
- hardware-backed device identity
- beschermde cryptografische sleutels
- encrypted storage
- signed firmware en OS-images
- secure OTA
- rollback en anti-rollback
- secure recovery
- secure debug lifecycle
- SELinux enforcing
- minimale service privileges
- gecontroleerde provisioning

## 4. EdgeLock Secure Enclave

De EdgeLock Secure Enclave wordt de hardwarematige security anchor.

Te onderzoeken en te valideren functies:

- cryptografische sleutelbescherming
- device identity
- secure boot ondersteuning
- secure provisioning
- lifecycle management
- beveiligde cryptografische operaties
- trusted security services

De precieze mogelijkheden moeten worden gevalideerd op het gekozen i.MX95 SoM en de bijbehorende NXP software/BSP-stack.

> Private keys en andere kritieke secrets mogen niet onnodig als gewone bestanden in Linux bestaan.

## 5. Secure Boot

Gewenste bootketen:

```text
i.MX95 Boot ROM / hardware
          ↓
Secure Boot verification
          ↓
TF-A
          ↓
U-Boot
          ↓
OP-TEE
          ↓
Linux kernel
          ↓
Fedora Atomic / bootc deployment
          ↓
Cumclave OS
```

Een gemanipuleerde bootloader, kernel of andere beveiligde bootcomponent moet worden geweigerd.

## 6. Fedora Atomic / bootc

Cumclave OS wordt gebaseerd op Fedora Atomic / bootc en image-based beheerd.

```text
Cumclave source
      ↓
Fedora Atomic image
      ↓
Security tests
      ↓
SBOM
      ↓
Image signing
      ↓
Cumclave update repository
      ↓
Phone
      ↓
Signature verification
      ↓
Atomic deployment
      ↓
Reboot
```

Voordelen:

- reproduceerbare OS-images
- gecontroleerde updates
- rollback
- minder configuratiedrift
- fleet management
- één OS-concept voor Phone, Desktop en NAS

## 7. Signed OS en Secure OTA

Iedere officiële Cumclave OS-release wordt ondertekend.

```text
Build server
    ↓
Build image
    ↓
Tests + security scan
    ↓
SBOM
    ↓
Sign release
    ↓
Publish
    ↓
Phone verifies
    ↓
Atomic deployment
```

Bij een mislukte update moet de telefoon naar een bekende goede deployment kunnen terugvallen.

## 8. Rollback en anti-rollback

**Rollback:** een defecte update kan worden teruggedraaid.

**Anti-rollback:** een aanvaller kan niet onbeperkt terug naar een oude kwetsbare versie.

De implementatie moet worden afgestemd op i.MX95 secure lifecycle, EdgeLock, bootloader, bootc en de gekozen signing/version policy. Dit moet praktisch worden getest.

## 9. OP-TEE

OP-TEE kan als Trusted Execution Environment fungeren.

```text
NORMAL WORLD
────────────────────────
Fedora Linux
Cumclave OS
Applications
Services
────────────────────────
          │ secure calls
          ▼
TRUSTED WORLD
────────────────────────
OP-TEE
Trusted applications
Security services
────────────────────────
          │
          ▼
EdgeLock Secure Enclave
```

Mogelijke functies:

- sleutelgebruik
- cryptografische operaties
- device authentication
- secure provisioning
- security tokens

De exacte EdgeLock/OP-TEE/Linux-integratie moet met de NXP softwarestack worden gevalideerd.

## 10. Hardware-backed Device Identity

Iedere Cumclave Phone krijgt een unieke cryptografische identiteit.

```text
Physical Cumclave Phone
          ↓
Hardware-backed identity
          ↓
Device certificate + Device key
          ↓
Cumclave Device ID
```

Deze identiteit kan worden gebruikt voor:

- Cumclave Cloud
- Seafile
- Cumclave Chat
- device management
- OTA
- Tailscale/WireGuard
- Cumclave Mesh

## 11. Secure provisioning

```text
Blank i.MX95 device
        ↓
Secure provisioning station
        ↓
Generate / provision identity
        ↓
Configure secure boot
        ↓
Configure lifecycle
        ↓
Install signed base software
        ↓
Register device
        ↓
Cumclave Phone
```

Waar mogelijk worden private keys direct binnen de hardware security boundary gegenereerd.

## 12. Encrypted storage

Basis:

```text
LUKS2
  ↓
dm-crypt
  ↓
eMMC / NVMe
```

Gewenste architectuur:

```text
EdgeLock / Secure Enclave
          ↓
Hardware-backed key material
          ↓
Secure key service / OP-TEE
          ↓
LUKS2 / dm-crypt
          ↓
User data
```

De exacte sleutelarchitectuur moet tijdens de PoC worden vastgesteld.

## 13. Secure recovery

Recovery moet zelf onderdeel zijn van de trust chain.

```text
Normal OS
   │
   ├── OK → normal boot
   │
   └── failure
          ↓
       signed recovery
          ↓
     repair / reinstall
          ↓
     verify installation
          ↓
       normal boot
```

Recovery mag geen bypass van secure boot vormen.

## 14. Secure debug

Tijdens ontwikkeling zijn JTAG/debuginterfaces noodzakelijk. In productie moeten deze worden beperkt.

**Development**
- debug enabled
- JTAG
- serial console
- development credentials

**Production**
- secure lifecycle
- restricted debug
- geen onbeperkte secret extraction

De exacte i.MX95 secure-debug/lifecycle-instellingen moeten in de productiefase worden vastgesteld.

## 15. SELinux en systemd

Cumclave OS moet SELinux bij voorkeur in enforcing mode gebruiken.

```text
Application
    ↓
systemd service
    ↓
SELinux policy
    ↓
Linux kernel
    ↓
hardware
```

systemd kan services verder sandboxen met onder andere:

- `ProtectSystem`
- `ProtectHome`
- `PrivateTmp`
- `PrivateDevices`
- `NoNewPrivileges`
- `RestrictAddressFamilies`
- `RestrictNamespaces`
- `CapabilityBoundingSet`
- `MemoryDenyWriteExecute`

Iedere service wordt afzonderlijk getest.

## 16. 5G modem security

Het 5G-modem vormt een aparte securityzone.

```text
5G modem
    ↓
controlled interface
    ↓
ModemManager
    ↓
NetworkManager
    ↓
Cumclave OS
```

Te onderzoeken:

- modem firmware
- Linux/ModemManager support
- firmware updates
- USB/PCIe permissions
- network isolation
- firewalling
- SIM/eSIM security
- GNSS isolation
- logging

Voor V2 moeten Europese bands, VoLTE, VoNR, GNSS, eSIM, Linux/ModemManager support en lange beschikbaarheid worden meegenomen.

## 17. Modulaire hardware

Cumclave Phone moet modulair en repareerbaar zijn.

```text
                  MAINBOARD
                      │
       ┌──────────────┼──────────────┐
       │              │              │
    Display         Camera         Modem
       │              │              │
     module         module         module
```

Andere mogelijke modules:

- batterij
- USB-C board
- speakers
- microphone
- camera
- NVMe
- 5G modem
- antennes
- keyboard
- sensor board

Modulariteit betekent niet dat ieder onderdeel automatisch vertrouwd wordt.

## 18. Cumclave Cloud

De hardware-backed identity kan de basis worden voor device authentication.

```text
Cumclave Phone
       │
       │ hardware-backed identity
       ▼
Cumclave Identity
       │
       ├── Seafile
       ├── Cumclave Chat
       ├── Device Management
       ├── Cumclave Mesh
       └── OTA
```

## 19. Tailscale / WireGuard

De bestaande Cumclave-infrastructuur gebruikt Tailscale en Cloudflare. Voor device-to-device verbindingen kan WireGuard/Tailscale worden gebruikt.

```text
Cumclave Phone
      ↓
Tailscale / WireGuard
      ↓
Cumclave NAS / Desktop / Cloud / Mesh
```

Hardware-backed device identity kan als aanvullende trustlaag worden gebruikt.

## 20. Supply-chain security

```text
Source code
    ↓
Controlled build environment
    ↓
Automated tests
    ↓
Security scanning
    ↓
SBOM
    ↓
Artifact signing
    ↓
Release repository
    ↓
Signed OTA
    ↓
Phone
```

Bijhouden:

- firmwareversies
- bootloader
- kernel
- packages
- container images
- CVE's
- SBOM
- build provenance
- signing keys

Production signing keys worden gescheiden gehouden van development credentials.

## 21. Key hierarchy

Een mogelijke architectuur:

```text
                 Root of Trust
                       │
                       ▼
              EdgeLock Secure Enclave
                       │
          ┌────────────┼────────────┐
          │            │            │
          ▼            ▼            ▼
     Device ID     Key services   Secure Boot
          │
     ┌────┼─────┐
     ▼    ▼     ▼
   Cloud Chat   OTA
```

Doel: compartmentalisatie. Een gecompromitteerde applicatie mag niet automatisch toegang krijgen tot alle device secrets.

## 22. Threat model

### Gestolen telefoon

Maatregelen:

- encrypted storage
- hardware-backed keys
- secure boot
- screen lock
- remote device revocation
- secure reset

### Malicious OTA

Maatregelen:

- signed images
- protected signing infrastructure
- secure boot
- update policy

### Gemodificeerde firmware

Maatregelen:

- secure boot
- hardware root of trust
- secure lifecycle

### Fysieke aanval

Maatregelen:

- encrypted storage
- secure debug
- hardware-backed secrets
- secure lifecycle

### Supply-chain aanval

Maatregelen:

- SBOM
- controlled builds
- signed firmware
- component provenance

## 23. i.MX95 Security PoC

Het definitieve Cumclave Phone V2 motherboard wordt pas ontworpen nadat de security chain op een development platform is bewezen.

### Stap 1 — Development platform

Kandidaten:

- Variscite DART-MX95
- Toradex Verdin iMX95
- PHYTEC phyFLEX i.MX95

Selectiecriteria:

- EdgeLock ondersteuning
- NXP BSP
- bootloader support
- OP-TEE support
- Fedora/bootc haalbaarheid
- beschikbaarheid
- lange leverbaarheid
- documentatie

### Stap 2 — NXP software stack

Breng eerst de standaard NXP stack volledig werkend.

```text
i.MX95
 ↓
NXP BSP
 ↓
Secure Boot
 ↓
EdgeLock
```

### Stap 3 — Secure Boot

Test:

- normale signed boot
- gewijzigde bootloader
- gewijzigde kernel
- unsigned image

Verwacht:

```text
authorized image → boot
unauthorized image → reject
```

### Stap 4 — EdgeLock

Documenteer:

- beschikbare functies
- gebruikte API's
- benodigde software
- welke secrets binnen de security boundary blijven

### Stap 5 — OP-TEE

Test:

- startup
- trusted application
- secure call vanuit Linux
- cryptografische service
- key handling

### Stap 6 — Fedora Atomic / bootc

Test:

```text
EdgeLock
 ↓
Secure Boot
 ↓
TF-A/U-Boot
 ↓
OP-TEE
 ↓
Fedora Atomic / bootc
```

### Stap 7 — Storage

Test:

- LUKS2
- dm-crypt
- key handling
- reboot
- power-off scenario

### Stap 8 — Signed OTA

Test:

- valid update
- modified update
- unsigned update
- interrupted update
- failed boot
- rollback

### Stap 9 — Device identity

Test:

```text
Phone → Cumclave Identity Server
```

De server moet het toestel cryptografisch kunnen identificeren.

### Stap 10 — SELinux

Activeer SELinux enforcing en ontwikkel de noodzakelijke service policies.

## 24. Security test matrix

| Onderdeel | Test | Verwacht resultaat |
|---|---|---|
| Secure Boot | gewijzigde boot image | geweigerd |
| Bootloader | unsigned bootloader | geweigerd |
| Kernel | ongeautoriseerde kernel | geweigerd |
| EdgeLock | key operation | hardware-backed |
| Device ID | identity check | uniek toestel |
| Storage | uitgeschakeld toestel | data niet leesbaar |
| OTA | unsigned update | geweigerd |
| OTA | beschadigde update | niet actief |
| Rollback | defecte update | vorige versie actief |
| Recovery | ongeautoriseerde recovery | geweigerd |
| SELinux | policy test | enforcing |
| Debug | production test | beperkt |
| Modem | isolation test | gecontroleerde toegang |
| Cloud | device authentication | device verified |

## 25. V1 → V2

### V1 — Raspberry Pi CM5 / HackberryPi

V1 bewijst vooral:

- Cumclave OS
- Fedora Atomic / bootc
- touchscreen
- keyboard
- camera
- audio
- batterij
- modem
- Cumclave Chat
- Cumclave Cloud
- updates
- UX

### V2 — i.MX95

V2 brengt de hardwarematige security naar het platform:

```text
V1 CM5
   │
   ├── Cumclave OS
   ├── UX
   ├── Apps
   └── Connectivity
          │
          ▼
V2 i.MX95
   │
   ├── EdgeLock
   ├── Secure Boot
   ├── OP-TEE
   ├── Device Identity
   ├── Encrypted Storage
   └── Fedora Atomic / bootc
```

## 26. R&D / subsidiepositie

Belangrijke technische onzekerheden:

- i.MX95 + Fedora Atomic/bootc
- EdgeLock-integratie
- secure boot
- OP-TEE
- hardware-backed keys
- encrypted storage
- secure OTA
- rollback
- device identity
- modem isolation
- één immutable OS-platform voor Phone, Desktop en NAS

Deze technische onzekerheden kunnen relevant zijn voor Nederlandse R&D-regelingen zoals WBSO en mogelijke regionale innovatieprogramma's. Voor iedere aanvraag moeten actuele voorwaarden en openstellingen worden gecontroleerd.

## 27. Definitieve voorlopige architectuur

**Processor:** NXP i.MX95

**Security:** EdgeLock Secure Enclave — Advanced Profile

**Boot:** Secure Boot + TF-A / U-Boot

**Trusted Execution:** OP-TEE, mits gevalideerd op de gekozen SoM/softwarestack

**OS:** Cumclave OS gebaseerd op Fedora Atomic / bootc

**MAC:** SELinux enforcing

**Storage:** LUKS2 / dm-crypt met hardware-backed key architecture

**Updates:** Signed atomic OTA + rollback

**Identity:** Hardware-backed Cumclave Device Identity

**Networking:** Tailscale / WireGuard + Cumclave networking

**Cloud:** Cumclave Cloud + Seafile

**Hardwarefilosofie:** modulair, repareerbaar en upgradebaar

## 28. Wanneer is V2 security-ready?

Cumclave Phone V2 wordt pas als security-ready beschouwd wanneer aantoonbaar is dat:

- secure boot werkt;
- ongeautoriseerde firmware wordt geweigerd;
- EdgeLock daadwerkelijk wordt gebruikt;
- device keys hardwarematig worden beschermd;
- OP-TEE correct functioneert waar nodig;
- Fedora Atomic/bootc veilig boot;
- signed OS updates worden gecontroleerd;
- rollback werkt;
- anti-rollbackbeleid is getest;
- encrypted storage werkt;
- recovery geen secure-boot bypass vormt;
- SELinux enforcing actief is;
- production debug is beperkt;
- het modem voldoende is geïsoleerd;
- hardware-backed device identity werkt;
- Cumclave Cloud het toestel kan authenticeren.

## 29. Kernprincipe

> **Trust starts in hardware and ends in the application.**

Voor Cumclave:

> **De Cumclave Phone moet zichzelf eerst veilig kunnen vertrouwen voordat Cumclave erop kan vertrouwen.**

De EdgeLock Secure Enclave is daarom geen extra feature, maar een fundamenteel onderdeel van de Cumclave Phone V2 security architecture.

## 30. Volgende stap

De eerstvolgende technische stap is een **i.MX95 Security Proof-of-Concept**:

```text
i.MX95
   ↓
EdgeLock Secure Enclave
   ↓
Secure Boot
   ↓
TF-A / U-Boot
   ↓
OP-TEE
   ↓
Fedora Atomic / bootc
   ↓
LUKS2
   ↓
SELinux
   ↓
Signed OTA
   ↓
Hardware-backed Device Identity
   ↓
Cumclave Cloud
```

Pas wanneer deze keten werkt, wordt het definitieve Cumclave Phone V2 motherboard ontworpen.

Het einddoel is niet simpelweg een Linux-smartphone met een i.MX95, maar een **hardwarematig beveiligd, modulair en langdurig onderhoudbaar Linux-computingplatform** waarvan dezelfde Cumclave OS- en securityprincipes kunnen worden gebruikt voor Phone, Desktop en NAS.
