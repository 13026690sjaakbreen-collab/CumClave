# CumClave Phone — Compute Module Specification (CONCEPT v0.1)

> Concept-spec (cloud-sessie), 2026-10-12. Invulling van SoM-doc §28. Doel: één
> CumClave-interfacespec waartegen CM5, i.MX95 en toekomstige SoM's getoetst worden,
> zodat de carrier-board zo SoM-onafhankelijk mogelijk is. Waarden met **[TBD]** zijn
> ontwerpbeslissingen voor de eigenaar; de rest zijn voorstellen om op te schieten.

## 0. Principe

> Design the device as a platform, not as a disposable product.

De carrier definieert een vaste **CumClave SoM-interface**. Een SoM is "CumClave-
compatibel" als het deze interface via zijn connector levert (direct of via een kleine
per-SoM adapter). Realiteit (SoM-doc §23): connectoren/pinouts verschillen per SoM, dus
100% universeel kan niet — vandaar het adapter-concept per SoM-familie.

## 1. Mechanisch

| Item | Voorstel | Opmerking |
|---|---|---|
| SoM-montage | **[TBD]**: directe board-to-board vs per-SoM adapterprint | CM5 (55×40), DART (~55×30), FPSC (45×48), Verdin verschillen te veel voor één connector |
| Carrier-afmetingen | **[TBD]** — telefoon-formfactor | begrens door grootste SoM + modem + batterij |
| Koeling | passief + thermal pad naar chassis | A55 lichter dan A76, maar modem+NVMe tellen mee |
| Thermal budget | **[TBD]** W sustained | bepaalt UX-throttling; meet op dev-board |

## 2. Power

| Rail | Voorstel | Opmerking |
|---|---|---|
| Hoofdinvoer | 1-cell Li-ion, PMIC op carrier | i.MX95 heeft geen telefoon-PMIC → zelf ontwerpen |
| SoM-rails | per SoM-datasheet | grootste bron van per-SoM-verschil |
| Suspend/resume | **vereist, te bewijzen** | beruchte zwakte op embedded Linux; vroeg testen |
| USB-C PD | sink + (optioneel) source | laden + data + display-alt-mode **[TBD]** |

## 3. Verplichte hoge-snelheidsinterfaces

| Interface | Minimum | Gebruik |
|---|---|---|
| PCIe | ≥1× Gen2 (Gen3 gewenst) | NVMe-storage; i.MX95 biedt 2× Gen3, CM5 slechts 1× Gen2 |
| USB 3.x | ≥1 poort | **5G-modem** (verwisselbaar, USB-first voor SoM-onafhankelijkheid) |
| USB 2.0 | ≥1 | keyboard/RP2040/randapparaat |
| MIPI CSI-2 | ≥1 (2 gewenst) | camera('s) |
| MIPI DSI | ≥1 | display |

Keuze: **modem via USB3** (niet PCIe) als default, omdat USB-modems SoM-onafhankelijker
en makkelijker verwisselbaar zijn — sluit aan op de modulariteitseis.

## 4. Verplichte lage-snelheidsinterfaces

I²C, SPI, UART (debug-console), I²S (audio), SDIO, meerdere GPIO — minimumaantallen **[TBD]**
na inventarisatie van display-touch, sensors, PMIC, fuel-gauge, RP2040-keyboard.

## 5. Security (hard vereist voor V2, optioneel/gedegradeerd voor V1)

| Eis | V2 (i.MX95) | V1 (CM5) |
|---|---|---|
| Hardware root of trust | ELE/AHAB | beperkt — CM5 heeft dit niet gelijkwaardig |
| Secure boot | verplicht (AHAB) | niet gelijkwaardig → V1 bewijst security níét |
| Secure element / HSM | ELE (on-SoC) | evt. externe SE op carrier **[TBD]** |
| Hardware-backed device key | verplicht | gedegradeerd |
| Measured/verified boot → disk key | ELE → OP-TEE → LUKS2 | software-only op V1 |

**Ontwerpregel:** de carrier mag optioneel een **externe secure element** (bv. via I²C/SPI)
dragen, zodat SoM's zónder sterke on-SoC-HSM (zoals CM5) toch een hardware-backed identity
kunnen bieden. **[TBD]** of dit de moeite waard is of dat V1 bewust software-only blijft.

## 6. Boot & OS-contract

De SoM moet kunnen booten naar het CumClave-OS-contract:
`ROM/firmware → verified bootloader → (U-Boot EFI-payload) → signed UKI → Fedora bootc →
systemd/SELinux`. Zie feasibility-spike: op i.MX95 vergt dit AHAB→U-Boot-EFI→UKI; op CM5
is er geen gelijkwaardige verified-boot. Een SoM scoort op:
- kan het een **door ons gesigneerde** bootloader/kernel verified laden?
- **mainline- of onderhouden-BSP-kernel** met de nodige drivers (display/camera/PCIe/USB/WiFi/BT)?
- **bootc image-update + rollback** werkbaar (incl. firmware-caveat: bootc update vaak géén
  bootloader/firmware)?

## 7. RF / modem / antenne

Modem-module verwisselbaar (USB3). **[TBD]**: EU-banden, VoLTE/VoNR, GNSS-isolatie, eSIM.
RF/antenne-ontwerp is SoM-onafhankelijk op carrier-niveau maar certificering (EU-RED, CE)
valt op de eigenaar als fabrikant zodra de carrier zelf ontworpen is — zie README §3.

## 8. Toetsmatrix per SoM

| Criterium | CM5 | i.MX95 (DART/FPSC/Verdin) | Toekomstige SoM |
|---|---|---|---|
| PCIe ≥Gen2 | ✓ (1×Gen2) | ✓ (2×Gen3) | ? |
| USB3 voor modem | ✓ | ✓ | ? |
| MIPI CSI + DSI | ✓ | ✓ | ? |
| HW root of trust / secure boot | ✗ | ✓ | ? |
| Verified boot → onze keys | ✗ | ✓ (AHAB) | ? |
| Mainline/onderhouden kernel | ✓ | deels (BSP) | ? |
| bootc + rollback | experimenteel | te bewijzen | ? |
| Longevity ≥10 jr | ✓ (tot 2036) | ✓ (15 jr Variscite) | ? |
| Publieke prijs | ✓ laag | deels (Variscite) | ? |

## 9. Openstaande ontwerpbeslissingen (voor de eigenaar)

1. SoM-montage: universele connector (onhaalbaar) vs **per-SoM adapterprint** (realistisch).
2. Externe secure element op carrier: ja/nee (bepaalt of V1 hardware-identity kan).
3. Modem: vaste keuze EU-banden + VoLTE/VoNR-ondersteuning onder ModemManager.
4. Display/touch/camera-sensoren: vastleggen vóór MIPI-pinout definitief wordt.
5. Thermal/power-budget: meten op dev-board, dan carrier dimensioneren.

> Volgende stap na deze spec: de boot-keten-spike (feasibility §6) draaien op een
> Variscite DART-MX95 eval-kit, en de **[TBD]**'s invullen met meetresultaten i.p.v.
> aannames.
