# SoM-shortlist (i.MX95) — specs, prijs, lead time, EOL

> Research-spike (cloud-sessie), 2026-10-12. Prijzen/levensduur deels van vendor- en
> distributiesites; lead times vrijwel overal "op quote". Verifieer per config bij de
> vendor vóór een inkoopbeslissing. Dit scherpt SoM-doc §25 (shortlist) aan met cijfers.

## Keuze-samenvatting

Alle drie draaien dezelfde i.MX95 (Cortex-A55-cluster + M7 + M33 + eIQ Neutron NPU),
dus de **SoC-security (AHAB/ELE/TrustZone)** is gelijk. Het verschil zit in prijs-
transparantie, form factor, camera/dev-kit-ondersteuning en lifecycle-belofte.

| | Variscite DART-MX95 | PHYTEC phyFLEX-i.MX95 FPSC | Toradex Verdin iMX95 |
|---|---|---|---|
| CPU | tot 6× A55 @ 2 GHz + M7 + M33 | 2/4/6× A55 tot 2.0 GHz + M7 + M33 | hexa A55 + M7 + M33 |
| RAM / eMMC | tot 16 GB LPDDR5 / tot 128 GB | 4 GB def, 16 GB max / tot 256 GB | 4 of 8 GB / — |
| Form factor | ~55 × 30 mm (DART) | 45 × 48 mm (FPSC) | Verdin (SODIMM-stijl) |
| Security | AHAB/ELE | Secure boot, TrustZone, SNVS, **EdgeLock + PQC** | AHAB/ELE |
| Prijs (indicatie) | **vanaf $93**; eval 8GB/32GB **$455** | geen publieke prijs (op aanvraag) | geen lijstprijs; sample 1–99 koopbaar, volume op quote |
| Longevity-claim | **15 jaar** | 2036 / "2041+" (tegenstrijdig) | "2039+" (lijkt placeholder) |
| Dev/EVK-status | eval/prototype-configs los geprijsd | i.MX95 **Imaging Kit** beschikbaar | oude EVK wordt uitgefaseerd → nieuwere kits |
| Extra connectiviteit | dual GbE + **10 GbE**, Wi-Fi 6, BT 5.3 | NPU 2 TOPS, tot 3 displays | PCN mrt-2026 (voeding-filtering) |

(Er is ook een **Compulab UCM-iMX95** die mainline Linux + Yocto claimt — vierde optie
om in de gaten te houden, nog niet uitgediept.)

## Per kandidaat

### Variscite DART-MX95 — *beste startpunt voor de PoC*
- Enige met **transparante, publieke prijs**: vanaf $93/stuk (baseline, bulk op quote),
  eval/prototype-config (8 GB RAM / 32 GB eMMC) $455/stuk.
- **15-jaar-longevity** expliciet gemarket — sterkste lifecycle-belofte voor "platform,
  geen wegwerpproduct".
- Rijke connectiviteit (10 GbE is overkill voor een telefoon maar laat de ruimte zien).
- **Advies:** bestel hier het eval-kit voor de boot-keten-spike. Prijs + beschikbaarheid
  + longevity maken dit de laagste drempel om te starten.

### PHYTEC phyFLEX-i.MX95 FPSC — *beste security/camera-profiel*
- Noemt als enige expliciet **EdgeLock + Post-Quantum Cryptography**, plus TrustZone/SNVS/
  SRTC — het meest security-georiënteerde datasheet, past bij jouw security-by-design.
- **Imaging Kit** beschikbaar → beste basis voor het camera/libcamera/ISP-risico (een van
  de zwaarste Linux-mobiel-problemen).
- Industriële FPSC-form-factor (45×48 mm), -40..85 °C, NPU 2 TOPS.
- Nadeel: **geen publieke prijs**, alles op aanvraag; longevity-cijfers tegenstrijdig.
- **Advies:** tweede eval-kit zodra camera/ISP aan de beurt is, of als EdgeLock-API-diepte
  beslissend wordt.

### Toradex Verdin iMX95 — *sterkste industriële/upstream-reputatie*
- Toradex profileert zich "**upstream-first**" (mainline-kernel-cultuur) — relevant gezien
  de mainline-vs-BSP-spanning uit de feasibility-spike. **Let op:** ook Toradex draait voor
  i.MX95 nu nog een NXP-downstream-kernel, dus "upstream-first" is richting, geen status.
- Nieuwe 4/8 GB-varianten uitgebracht Q3 2026; oude EVK wordt uitgefaseerd (gebruik de
  nieuwste kits). Geen lijstprijs; Mouser toont 8 GB als "Factory Special Order".
- **Advies:** sterkste kandidaat als het project richting serieuze, lange-termijn productie
  schuift (het "leverbaar product"-einddoel). Voor de eerste PoC duurder/trager te sourcen.

## Harde kanttekening (geldt voor alle drie)

De i.MX95 A55-cluster bestaat uit **efficiency-cores**. Prima voor embedded/edge-AI,
maar CPU-gewijs onder een modern smartphone-SoC (en onder de CM5's A76's). Voor vloeiende
telefoon-UX is dit een reëel aandachtspunt — test GNOME Mobile/Phosh-UX vroeg op echt
silicon, niet alleen in theorie.

## Aanbeveling

1. **Start met het Variscite DART-MX95 eval-kit ($455)** voor de boot-keten-spike —
   laagste drempel, transparante prijs, beste longevity-belofte.
2. **Houd PHYTEC phyFLEX achter de hand** voor de camera-/EdgeLock-diepgang (Imaging Kit).
3. **Toradex Verdin** pas serieus overwegen bij de stap naar productie.
4. Niets kopen vóór de carrier-interface-spec (zie `compute-module-spec.md`) de pinout-/
   connector-eisen vastlegt, zodat je SoM's eerlijk tegen één CumClave-spec toetst.

## Bronnen
- Variscite DART-MX95: https://shop.variscite.com/product/system-on-module/dart-mx95-system-on-module/ · https://variscite.com/system-on-module-som/i-mx-9/i-mx-95/dart-mx95/ · https://linuxgizmos.com/variscite-dart-mx95-system-on-module/
- PHYTEC phyFLEX-i.MX95 FPSC: https://www.phytec.eu/en/produkte/system-on-modules/phyflex-imx-95-fpsc/ · https://www.phytec.eu/en/produkte/development-kits/phyflex-imx-95-imaging-kit/
- Toradex Verdin iMX95: https://www.toradex.com/computer-on-modules/verdin-arm-family/nxp-imx95 · https://developer.toradex.com/hardware/verdin-som-family/modules/verdin-imx95/ · https://www.toradex.com/blog/upstream-first-mainline-kernel-support-is-reality
- Compulab UCM-iMX95: https://www.compulab.com/products/computer-on-modules/ucm-imx95-nxp-i-mx-95-som-system-on-module/
