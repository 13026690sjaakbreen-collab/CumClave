# Feasibility-spike: i.MX95 security + Fedora bootc

> Research-spike (cloud-sessie), 2026-10-12. Gebaseerd op web-research; NXP's
> officiële i.MX Linux User's Guide / meta-imx security-layer / OP-TEE-build-docs
> vereisen een NXP-account en zijn **niet** geverifieerd — zie "Te verifiëren".
> Doel: de grootste technische onzekerheid uit de V2-architectuur scherp krijgen.

## TL;DR

De securityketen is op papier compleet en de bouwstenen bestaan — **maar** de twee
aannames van Fedora bootc (mainline-kernel + UEFI/secure-boot) botsen met de realiteit
van i.MX95 (NXP-downstream-BSP + AHAB/U-Boot-boot). **Dát** is de kern-R&D-onzekerheid,
niet EdgeLock of OP-TEE op zich. Haalbaar, maar genuinely novel — en precies daarom
WBSO-fundabel.

## 1. Secure boot op i.MX95 = AHAB + ELE (niet UEFI Secure Boot)

- i.MX95 gebruikt **AHAB** (Advanced High Assurance Boot): een container-gebaseerde
  secure-boot-architectuur. De immutable **Boot ROM** start, de **EdgeLock Secure
  Enclave (ELE)** neemt de authenticatie-/autorisatiebeslissingen, en bootcomponenten
  laden alleen software die aan het beleid voldoet.
- Keten: `Boot ROM → ELE-geverifieerde AHAB-containers → TF-A → U-Boot → kernel`.
- **Tooling bestaat:** NXP **SPSDK** + de `nxpele`-tool genereren AHAB-containers,
  key blobs en ELE-output; er is een SPSDK-voorbeeld "AHAB with U-Boot on i.MX95".
  RidgeRun heeft een (third-party) Yocto security-flow-handleiding.
- **Belangrijk gevolg:** dit is géén UEFI Secure Boot met shim/GRUB/systemd-boot zoals
  Fedora standaard doet op x86/aarch64-UEFI-machines. De vertrouwensketen is NXP-eigen
  tot aan U-Boot.

## 2. EdgeLock (ELE) — Advanced Profile

- ELE is NXP's nieuwste HSM-generatie op AHAB-parts: key-bescherming, device identity,
  secure-boot-beslissingen, secure provisioning, crypto-operaties. PHYTEC-pagina's
  noemen voor i.MX95 expliciet ook **Post-Quantum Cryptography (PQC)**-ondersteuning.
- Dit dekt de architectuurwens (hardware-backed keys, device identity, LUKS-sleutel in
  hardware-boundary) in principe volledig.
- **Onzeker:** de exacte API's, welke keys écht binnen de boundary blijven, en de
  Linux-integratie (ELE-driver + userspace) — te valideren op het gekozen SoM.

## 3. OP-TEE

- OP-TEE wordt genoemd als trusted OS in de secure world op AHAB/ELE-parts (i.MX 8ULP/9x).
- **Geen** officiële "OP-TEE op i.MX95"-bouwgids gevonden. Aanwezigheid in de NXP-BSP is
  waarschijnlijk, maar de ELE↔OP-TEE↔Linux-integratie is het te bewijzen stuk.

## 4. De kernbotsing: Fedora bootc vs i.MX95-realiteit

Dit is het hart van de spike. Twee spanningen:

**(a) Kernel: mainline vs downstream-BSP.**
- Toradex bevestigt dat hun i.MX95-modules een **NXP-downstream-kernel** gebruiken, óók
  in BSP 6.0+. Mainline i.MX95-support is pas gedeeltelijk: de **display-driver** werd
  in juni 2026 nog uitgevochten voor upstream (monolithische downstream-driver; een
  split-driver-serie is nu klaar om te posten). Compulab/SBCwiki claimen "mainline
  supported" maar zonder feature-detail.
- Fedora (Atomic/bootc) draait het liefst op een **mainline**-kernel. Een downstream-BSP-
  kernel in een Fedora-bootc-image bakken kan, maar kost je de Fedora-kernel-updatetrein
  en vergt eigen kernel-onderhoud — precies de last die je bij GrapheneOS wilde vermijden.

**(b) Boot: UEFI/UKI/secure-boot vs AHAB/U-Boot.**
- Fedora's "sealed" bootc (secure-boot + rollback) is officieel alleen **UEFI op x86_64 en
  aarch64**, met **systemd-boot + signed UKI + composefs/fs-verity**. Op aarch64 is er een
  bekend GRUB-chainloader-gat in F44 (UKI-boot via GRUB brak; systemd-boot is de route).
  aarch64-edk2-firmware had historisch secure-boot/variable-store-problemen.
- i.MX95 boot níét via UEFI-firmware maar via Boot ROM→AHAB→TF-A→U-Boot. Je moet dus
  **U-Boot als EFI-payload** inzetten (U-Boot's EFI-loader) om Fedora's UKI/systemd-boot-
  model te kunnen draaien, mét een door AHAB geverifieerde en beschermde variable store.
  Dat die keten (AHAB → U-Boot-EFI → signed UKI → bootc-rollback) sluit en de
  anti-rollback-garanties behoudt, is **nergens aangetoond voor i.MX95** → dit is de
  grootste onzekerheid en de eerste die je op een dev-board moet de-risken.

**(c) Rollback.** Fedora's UKI-fase-2 kent "boot-once-then-promote" (BootNext → bij succes
BootOrder). Dat is kernel-promotie, niet per se volledige image-rollback; hoe bootc image-
rollback samenwerkt met AHAB anti-rollback-counters moet praktisch getest worden.

## 5. V1 (CM5) is óók niet gratis

De V1-aanname "CM5 + Fedora/bootc bewijst de software" heeft eigen frictie:
- Fedora-images voor Pi 5 bestaan sinds sep 2025, maar **CM5 (rev d0) crashte bij boot**
  in vroege images; graphics vergt een CMA-kernelparameter; Pi5 USB/NVMe-boot was in
  Fedora recent nog niet ondersteund.
- bootc-op-Pi-projecten zijn **experimenteel**: firmware/bootloader worden door bootc
  **niet** geüpdatet (blijven op initiële versie), en meerdere repos melden "nog geen
  werkend Pi5-image".
- Pi heeft geen UEFI-secure-boot-model vergelijkbaar met het einddoel → V1 bewijst UX en
  apps, maar **niet** de securityketen. Houd die scope-grens scherp.

## 6. Voorgestelde de-risk-volgorde (aanscherping van PoC-plan §23)

Draai de volgorde zo dat de duurste onzekerheid het eerst valt:

1. **Boot-keten-spike eerst** (niet laatst): op één i.MX95 dev-board de keten
   `AHAB → TF-A → U-Boot(EFI) → signed UKI → kernel` sluitend krijgen met eigen keys.
   Slaagt dit niet elegant, dan heroverweeg je "Fedora bootc" vs "eigen OSTree/U-Boot-
   flow" vóór je carrier-geld uitgeeft.
2. **ELE + secure boot**: unsigned/gewijzigde container → reject; documenteer API's.
3. **OP-TEE** trusted-app + secure call.
4. **LUKS2 met ELE-backed key** (OP-TEE key service).
5. **Signed OTA + rollback** incl. AHAB anti-rollback-interactie.
6. **Device identity → CumClave Identity server.**
7. **SELinux enforcing policies.**

Pas als 1–5 staan, is het i.MX95-pad bewezen genoeg om de carrier te ontwerpen.

## 7. Te verifiëren (NXP-account / hands-on)

- NXP i.MX Linux User's Guide + meta-imx security-layer: zit OP-TEE kant-en-klaar in de BSP?
- Is er een door NXP/SoM-vendor geleverde **U-Boot-EFI + UKI**-combinatie, of moet je die bouwen?
- Welke exacte ELE-keys verlaten de boundary nooit (voor de LUKS-sleutelarchitectuur)?
- Mainline-kernel feature-matrix i.MX95 (display/camera/PCIe/USB) vs wat jij nodig hebt.
- AHAB anti-rollback-counter-mechaniek vs bootc image-rollback.

## Bronnen
- i.MX95 secure boot/AHAB/ELE: https://www.nxp.com/products/i.MX95 · https://developer.ridgerun.com/wiki/index.php/RidgeRun_Platform_Security_Manual/Getting_Started/NXP_i.MX95/Yocto_Build_Process/Security_Flow · https://spsdk.readthedocs.io/en/latest/examples/ahab/imx95/imx95_ahab_uboot.html · https://xterra2.avnet.com/nxp/mpu/security/-/blob/main/imx-security-documentation.md
- Mainline vs BSP: https://developer.toradex.com/software/toradex-embedded-software/toradex-embedded-linux-support-strategy/ · https://ratatoskr.run/linux-arm-kernel/2026/06/17170118/t · https://sbcwiki.com/docs/soc-manufacturers/nxp/imx95/
- Fedora bootc secure boot/UKI/aarch64: https://fedoramagazine.org/sealed-atomic-desktops-test-images/ · https://fedoraproject.org/wiki/Changes/Unified_Kernel_Support_Phase_2 · https://github.com/bootc-dev/bootc/pull/2527
- CM5/Pi Fedora bootc: https://nullr0ute.com/2026/03/fedora-44-on-the-raspberry-pi-5/ · https://github.com/ondrejbudai/fedora-bootc-raspi · https://github.com/mrguitar/fedora-rpi5-bootc
