# CumClave Phone — onderzoeksdossier

Soevereine, geharde, modulaire smartphone — de hardware-tegenhanger van CumClave OS.
Doel: **R&D / WBSO / proof-of-concept nu**, met een **leverbaar product als horizon**.

> Design the device as a platform, not as a disposable product.
> Trust starts in hardware and ends in the application.

## Gekozen richting

**Custom-hardware/platform-route** — eigen SoM-carrier + CumClave OS (Fedora Atomic/bootc).
*Niet* de consumentenroute (GrapheneOS-op-Pixel); zie het afgesloten zijspoor hieronder.

- **V1 (dev/UX):** Raspberry Pi CM5 + Hackberry-platform, Fedora/bootc. Bewijst software, UX, apps.
- **V2 (product):** NXP i.MX95 + EdgeLock Secure Enclave + OP-TEE + secure boot.
- **Platform:** vervangbare SoM, modulaire carrier, één OS-concept voor Phone/Desktop/NAS.

## Documenten

| Bestand | Inhoud | Herkomst |
|---|---|---|
| `v2-imx95-edgelock-architecture.md` | V2 security-architectuur (i.MX95 + EdgeLock + bootc), PoC-plan, threat model | **Canoniek — eigenaar** |
| `som-comparison.md` | SoM/compute-module-vergelijking, shortlist, carrier-strategie | **Canoniek — eigenaar** |
| `feasibility-imx95-security.md` | Research-spike: EdgeLock/OP-TEE/secure-boot/bootc haalbaarheid | Research (cloud-sessie) |
| `som-shortlist.md` | Research-spike: SoM-kandidaten — specs, prijs, lead time, EOL | Research (cloud-sessie) |
| `compute-module-spec.md` | Concept "Compute Module Specification" (toetssteen voor alle SoM's) | Research (cloud-sessie) |
| `wbso-positioning.md` | WBSO-positie: fundabele technische onzekerheden + cijfers 2026 | Research (cloud-sessie) |
| `sidetrack-grapheneos-consumer-route.md` | Afgesloten zijspoor: waarom GrapheneOS-op-Pixel niet gekozen is | Research (cloud-sessie) |

## Status van de drie grootste onzekerheden

1. **i.MX95 + Fedora bootc** — mainline-kernel vs NXP-BSP-spanning. → `feasibility-imx95-security.md`
2. **EdgeLock / OP-TEE / secure-boot-keten** — moet op dev-board bewezen vóór carrier-ontwerp.
3. **Modem / RF / EU-RED-certificering** — zwaarste brok richting leverbaar product; eigenaar wordt
   fabrikant onder 2014/53/EU zodra de carrier zelf ontworpen is.

## Werkafspraak

Dit dossier leeft in deze (GitHub-scratch) repo omdat het pure research/documentatie is.
De canonieke bron voor het *project* blijft de private self-hosted omgeving; cross-agent acties
(bv. richting Fedora-agent voor CumClave OS, of hardware-inkoop) lopen via de Orchestrator
als taakbestand, niet direct vanuit dit dossier.
