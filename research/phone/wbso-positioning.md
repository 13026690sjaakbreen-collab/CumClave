# WBSO-positionering — CumClave Phone

> Research-notitie (cloud-sessie), 2026-10-12. Cijfers/voorwaarden komen van
> subsidie-adviessites, **niet** van RVO zelf — verifieer alles tegen de officiële
> RVO-handleiding WBSO 2026 vóór een aanvraag. Geen fiscaal/juridisch advies.

## Waarom dit project goed bij WBSO past

WBSO beloont het oplossen van **technische onzekerheid** (niet marktrisico), en embedded
systemen / elektronica / apparaten zijn expliciet passende projecten. Software telt mee
als het **technisch nieuwe programmatuur** in een formele taal is waarvan jij zelf de
programmeertechnische knelpunten oplost — het louter *toepassen* van bestaande componenten
telt niet. De CumClave Phone zit vol echte, zelf-op-te-lossen onzekerheden.

## Fundabele technische onzekerheden (uit de feasibility-spike)

De sterkste WBSO-onderbouwing zit in deze, omdat ze aantoonbaar nergens opgelost zijn:

1. **AHAB → U-Boot(EFI) → signed UKI → Fedora bootc met behoud van anti-rollback** op
   i.MX95 — niet aangetoond, vergt eigen bootloader-/boot-engineering.
2. **ELE (EdgeLock) ↔ OP-TEE ↔ LUKS2-sleutelarchitectuur** zó dat disk-keys de
   hardware-boundary niet verlaten — eigen trusted-app- en key-service-ontwikkeling.
3. **Hardware-backed device identity → CumClave Identity** (attestatie zonder Google) —
   eigen protocol-/integratiewerk.
4. **SoM-onafhankelijke carrier-/boot-abstractie** (één OS-contract over CM5 én i.MX95) —
   technisch nieuw, niet van de plank.
5. **Modem-isolatie + VoLTE/VoNR onder ModemManager op een zelf-ontworpen platform** —
   bekende, onopgeloste Linux-mobiel-knelpunten.

Let op de WBSO-grens: zodra het **werkingsprincipe is aangetoond**, stopt het S&O-deel —
productrealisatie en productievoorbereiding daarna vallen er níét onder. De PoC-fase is dus
de WBSO-fase; serieproductie niet.

## Kerncijfers 2026 (indicatief, verifiëren bij RVO)

- **Zelfstandige:** vaste S&O-aftrek **€ 15.979**, plus **€ 7.996** extra voor starters.
- **Werkgever:** 36% over eerste € 391.020 S&O-grondslag, 16% daarboven; starters 50% in
  eerste schijf.
- **Zelfstandige-urendrempel:** min. 500 uur S&O per jaar (en 1.225 uur ondernemerschap).
- **Indienen:** vóór aanvang werkzaamheden; portaal opent ~medio november; let op de
  deadline voor startdatum 1 januari.
- RVO scherpt de beoordeling (m.n. software) aan → concrete technische onderbouwing van de
  onzekerheid is cruciaal. De feasibility-spike in dit dossier levert daarvoor de basis.

## Aanbeveling

Formuleer de aanvraag rond onzekerheid **#1 en #2** (boot-keten + ELE/OP-TEE/LUKS) — die
zijn het scherpst af te bakenen, aantoonbaar onopgelost, en passen binnen de PoC-fase.
Laat een WBSO-adviseur de definitieve tekst en uren toetsen; dit dossier levert de techniek.

## Bronnen
- https://klarensubsidieadvies.nl/wbso/ · https://klarensubsidieadvies.nl/wbso-voor-nieuwe-programmatuur/ · https://www.hezelburcht.com/subsidies/wbso-subsidie/ · https://craeghs.nl/nieuws/wbso-in-2026/
- (Verifiëren bij) RVO: handleiding WBSO 2026 — https://www.rvo.nl/subsidies-financiering/wbso
