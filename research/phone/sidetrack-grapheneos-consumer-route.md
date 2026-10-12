# CumClave Smartphone — GrapheneOS-consumentenroute (AFGESLOTEN ZIJSPOOR)

> Status: **afgesloten zijspoor per 2026-10-12.** Dit rapport onderzocht de
> *consumentenroute*: GrapheneOS op een bestaand Pixel-toestel, MDM-beheerd.
> Die route is **niet gekozen**. Het CumClave Phone-project gaat de
> **custom-hardware/platform-route** (eigen SoM-carrier, i.MX95 + EdgeLock,
> CumClave OS op Fedora bootc) — zie `README.md` en de architectuurdocs in deze map.
> Headwind MDM is bovendien van Server02 verwijderd, dus de MDM-stukken hieronder
> zijn dubbel achterhaald. Bewaard als vergelijkingsmateriaal (waarom niet deze route)
> en voor de concurrentie-/EU-RED-bevindingen, die op de fabrikantroute juist
> relevánter worden.

## 0. Wat is het project (werkdefinitie)

Een soevereine, geharde smartphone op **GrapheneOS**-basis — de telefoon-tegenhanger
van CumClave OS (desktop). Out-of-the-box voorzien van de CumClave-apps (XMPP-chat
met OMEMO, mail op `cumclave.nl`, evt. Nextcloud-files), MDM-enrolled bij aflevering,
zonder Google-diensten, met zelf-gehoste backends. Past één-op-één in
PRIVACY · SECURITY · SOVEREIGNTY.

Twee scenario's lopen door dit rapport heen:
- **A — eigen gebruik / familie:** alleen techniek en haalbaarheid tellen.
- **B — product voor anderen:** juridisch, supply, support en merk tellen zwaar mee.

---

## 1. Technische haalbaarheid (GrapheneOS customizen)

**Wat kan:**
- GrapheneOS is from-source te bouwen. Een eigen build vereist `OFFICIAL_BUILD=true`
  om de Updater-app mee te nemen, en je wijst de Updater naar je **eigen OTA-server**
  (statische webserver; het release-signing-script genereert de metadata ernaast).
- Pre-installed apps (App Store, Auditor, Camera, PdfViewer) zitten als prebuilt-APK's
  in `external/`-repos. Datzelfde mechanisme is het pad om **je eigen CumClave-apps mee
  te bakken** in een custom build.
- Community-precedent voor self-hosted OTA bestaat (bv. OTA via GitHub Pages + de
  Custota-app; of avbroot + Docker + cron).

**Harde kanttekeningen:**
- Een build gesigneerd met je **eigen keys** werkt *niet* tegen de officiële
  update-server — je moet dan je hele OTA-infra zelf draaien én onderhouden, inclusief
  de security-updates die GrapheneOS in hoog tempo uitbrengt. Dat is een doorlopende
  verplichting, geen eenmalige klus.
- Er is geen officiële GrapheneOS-gids voor build-time app-provisioning buiten het
  prebuilt-APK-mechanisme. Verwacht eigen uitzoekwerk.

**Pragmatisch alternatief:** níet zelf GrapheneOS forken, maar **stock GrapheneOS +
een post-install provisioning-flow** (script/MDM) die jouw apps en instellingen
oplegt. Veel minder onderhoudslast, je blijft op de officiële OTA-trein, en het sluit
direct aan op je bestaande MDM-werk. Dit is vrijwel zeker het verstandige startpunt.

## 2. MDM / enrollment — directe link met je Headwind-blocker

Dit is het meest relevante stuk voor CumClave *nu*:

- Standaard enterprise-MDM (Intune, Workspace ONE) is op GrapheneOS **onbetrouwbaar
  tot onbruikbaar**: geen Managed Google Play, geen Google device-integrity/attestatie
  op systeemniveau, work-profile + sandboxed Play botst op "unknown sources".
- **Device Owner via `DevicePolicyManager`** werkt wél zonder Google — dat is het juiste
  frame voor een soeverein toestel. Er is een open-source precedent
  (`themark-net/grapheneos-mdm`) dat precies dit doet.
- **Kernobstakel, en het raakt jouw MDM-project direct:** QR-based Device-Owner
  enrollment is op stock GrapheneOS nog niet betrouwbaar — de SetupWizard mist de
  QR-provisioning-scanner, dit zit **upstream geblokkeerd**. Device-Owner enrollment
  leunt daarom nu op **ADB** (kabel/handmatig). Dat verklaart waarschijnlijk (deels)
  waarom je Headwind-enrollment-flow blokkeert.
- Device-Owner-restricties zijn "sticky": eenmaal gezet blijven ze tot een factory reset.

➡️ **Routing-notitie (jouw conventie):** dit is een cross-agent bevinding (Smartphone-idee
raakt de MDM-stack). Hoort via de Orchestrator als taak-entry naar de MDM-agent, niet
direct hier opgelost.

## 3. Hardware & levensduur (Pixel)

- GrapheneOS steunt ~21 Pixels (Pixel 6 t/m 10a). Geen ander merk officieel; Motorola
  is aangekondigd als toekomstige uitzondering (richting 2027).
- **EOL is de bepalende factor voor een product:**
  - Pixel 6-serie: Google-firmware-updates stoppen ~okt 2026 → **nu al niet meer
    verkopen/uitrollen voor nieuw**.
  - Pixel 8/9: OEM-support grofweg 2030–2031.
  - Pixel 10-serie: ~2032, Pixel 10a tot ~maart 2033.
  - (Deze datums komen van secundaire aggregators — verifiëren tegen Google's eigen
    update-policy vóór je er iets op baseert.)
- Les: standaardiseer op **Pixel 8a/9a of 10a** als instap — langste runway,
  betaalbaarst, en GrapheneOS-aanbevolen (niet onder Pixel 6).

## 4. Concurrentie / precedent

| Speler | Wat | Les voor CumClave |
|---|---|---|
| **NitroPhone** (Nitrokey) | Turnkey Pixel + GrapheneOS, geharde hardware | Dichtst bij jouw idee; bewijst dat het model kan |
| **Murena** | Verkoopt toestellen met /e/OS | Turnkey & gebruiksvriendelijk, maar cloud-backups niet volledig E2EE → zwakker op jouw confidentialiteit-as |
| **Fairphone** | Repareerbaar, lange support, vaak /e/OS | Sterk op duurzaamheid; updates lopen achter op Google |
| **Purism Librem 5** | Hardware kill switches | Levering berucht traag (2021-orders eind 2025 nog open) → waarschuwing tegen eigen hardware |
| **Volla** | Duits, privacy-forward, Ubuntu Touch | Mikt op minder-technische gebruiker |

Rode draad in de 2026-gidsen: **Pixel + GrapheneOS is de sterkste security-keuze.**
CumClave's onderscheid zit niet in "nog een deGoogled telefoon", maar in de
**geïntegreerde soevereine stack** (eigen chat + mail + files + MDM + OS, allemaal
zelf-gehost). Dat is het verhaal dat niemand in deze lijst compleet heeft.

## 5. Juridisch & regulatoir (alleen relevant voor scenario B)

- **GrapheneOS-merk:** naam/logo zijn trademarks (US-geregistreerd; common-law elders;
  geen EU-registratie gevonden). Verkopen van toestellen *mét* GrapheneOS is
  **toegestaan**, maar het project treedt op tegen gebruik van hun merk op sites die
  EOL-hardware verkopen. → Verkoop geen toestellen < Pixel 6; wees voorzichtig met hoe
  je het GrapheneOS-merk voert.
- **EU Radio Equipment Directive (2014/53/EU):** een distributeur die een toestel **onder
  eigen naam/merk verkoopt** óf het **zo wijzigt dat conformiteit geraakt kan worden**,
  wordt juridisch als **fabrikant** behandeld (art. 10-verplichtingen: CE,
  conformiteitsverklaring). Alleen doorverkopen-as-is houdt je in de smallere
  distributeur-rol. Waar precies "OS flashen" op die lijn valt, is een juridische
  beoordeling — dit is een advocaat-vraag, geen agent-vraag.
- Praktische consequentie: "CumClave Phone" als **eigen merk** met geflashte OS duwt je
  richting fabrikant-verplichtingen. Dat is de duurste variant.

---

## 6. Voorlopige conclusie & advies

1. **Begin bij scenario A (eigen/familie).** Alle techniek is daar haalbaar, nul
   juridische last. Het levert meteen een werkend, dogfood-baar resultaat op.
2. **Fork GrapheneOS niet** om te starten. Gebruik **stock GrapheneOS + post-install
   provisioning (MDM/script)**. Zelf OTA + eigen keys = permanente onderhoudslast die je
   pas aangaat als er een echt productargument is.
3. **Standaardiseer op Pixel 8a/9a/10a.** Nooit < Pixel 6.
4. **De MDM-enrollment-flow is het echte kritieke pad** — en het is dezelfde blocker als
   je lopende Headwind-project. Hier eerst winst halen betaalt dubbel uit. Route via
   Orchestrator → MDM-agent.
5. **Scenario B (verkoop) pas overwegen** als A werkt. Dan eerst de RED/fabrikant-vraag
   en het GrapheneOS-merkgebruik juridisch toetsen.
6. **Positionering:** niet "deGoogled telefoon" maar "de telefoon die in de volledige
   zelf-gehoste CumClave-stack klikt". Dat is het verdedigbare onderscheid.

## 7. Wat een volledige deep-research-run hieraan toevoegt

Deze scan is breed maar ondiep. Een volle parallelle run (meerdere subagents) zou:
- GrapheneOS' officiële build/OTA-docs uitputtend doornemen i.p.v. 1 zoekopdracht;
- de `DevicePolicyManager` Device-Owner-mogelijkheden precies in kaart brengen (wat kan
  wél/niet zonder Google) en koppelen aan Headwind's concrete enrollment-stap;
- een echte kost-/BOM-raming per Pixel-model maken (inkoop, marge, support);
- de EU-RED/CE-vraag en NL-specifieke regels uitzoeken (maar: eindoordeel blijft jurist);
- een concreet provisioning-architectuurvoorstel opleveren (build-flow → OTA → MDM →
  app-set), klaar om als taakbestand naar de juiste agents te routeren.

Zeg "ga dieper" en ik zet die run op.

---

### Bronnen
- GrapheneOS build/source/OTA: https://grapheneos.org/build · https://grapheneos.org/source · https://grapheneos.org/faq
- Self-hosted OTA precedent: https://github.com/schnatterer/rooted-graphene · https://github.com/MuratovAS/grapheneos-magisk
- Custom Device-Owner MDM voor GrapheneOS: https://github.com/themark-net/grapheneos-mdm · https://github.com/themark-net/grapheneos-mdm/issues/8 · https://github.com/themark-net/grapheneos-mdm/issues/30
- Work-profile/Play beperking: https://github.com/GrapheneOS/os-issue-tracker/issues/5529
- MDM-beperkingen (secundair): https://vucense.com/privacy-sovereignty/digital-independence/grapheneos-complete-setup-guide-sovereign-android-2026/ · https://sesamedisk.com/grapheneos-privacy-security-2026/
- Supported devices / EOL (secundair, verifiëren): https://www.efani.com/blog/grapheneos-supported-devices · https://en.wikipedia.org/wiki/GrapheneOS
- Concurrentie (secundair): https://proton.me/learn/european-alternatives/european-phones · https://en.wikipedia.org/wiki/List_of_custom_Android_distributions
- GrapheneOS resale/merk-standpunt: https://grapheneos.social/@GrapheneOS/111513159752824708 · https://x.com/GrapheneOS/status/1810643018925437410
- EU RED (primair): https://eur-lex.europa.eu/eli/dir/2014/53/oj/eng · https://eur-lex.europa.eu/legal-content/EN/TXT/HTML/?uri=CELEX:02014L0053-20241228
