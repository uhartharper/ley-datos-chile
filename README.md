# Ley 21.719 Compliance Auditor

Desarrollado por [Zythos Media](https://zythos.media) — Especialistas en SEO & IA Search

**Audit any Chilean website for Ley 21.719 compliance directly inside Claude Code.**

Chile's Personal Data Protection Law (Ley 21.719, published December 2024) replaces
the previous Ley 19.628 and introduces GDPR-like obligations: explicit consent, seven
data subject rights (including portability, restriction, and protection from automated
decisions), mandatory privacy policy content, international transfer rules, and a Data
Protection Officer (EPD) requirement in certain cases. The 24-month adaptation period
ends December 2026.

This Claude Code skill inspects a website externally — no backend access required —
and produces a scored compliance report with actionable remediation steps and
**sanction-level risk per issue**.

---

## What it audits

10 compliance blocks mapped to Ley 21.719:

| Block | Topic | Notes |
|-------|-------|-------|
| 1 | Data controller identification | Art. 5. Foreign entities must designate a Chilean representative |
| 2 | Privacy policy — existence and access | Art. 14 |
| 3 | Privacy policy — minimum required content | Art. 14 a–k + public display obligation |
| 4 | Legal basis declared per processing purpose | Art. 12. Six valid bases under Chilean law |
| 5 | Cookie consent, opt-in and **marketing direct opt-out** | Art. 12 |
| 6 | **Seven** data subject rights channel (ARCOP+L+A) | Incl. right against automated decisions |
| 7 | Sensitive data handling — **9 categories** | Art. 2 + 16. Socioecon. status, ideology, sexual life added in v1.1 |
| 8 | International data transfers with guarantees | Art. 26. BCR and certification mechanisms included |
| 9 | Technical security — confidentiality, integrity, availability, resilience | Art. 19 |
| 10 | Data Protection Officer (EPD) — applicability under Chilean law | Art. 36 |

**Automatic sector detection:** the skill detects the site's vertical (health,
e-commerce, fintech, education, SaaS, media, professional services) and applies
sector-specific requirements — stricter rules for health data, minor-consent logic
for education sites, CMF regulatory overlay for fintech, etc.

---

## Score

Each block is weighted. The total score is 0–100:

| Range | Risk posture |
|-------|-------------|
| 80–100 | Defensible — substantial compliance |
| 60–79 | Moderate risk — important gaps, no critical blockers |
| 40–59 | High risk — multiple material non-compliances |
| 0–39 | Critical risk — essential elements missing |

---

## Sanctions

Every issue in the report includes the applicable sanction level:

| Level | Maximum fine | Examples |
|-------|-------------|---------|
| Gravísima | 20,000 UTM (~USD 1,590,000) or 2–4% annual revenue | Sensitive data without consent; international transfer without guarantees |
| Grave | 10,000 UTM (~USD 795,000) | No privacy policy; rights channel missing; no declared legal basis |
| Leve | 5,000 UTM (~USD 397,500) | Incomplete policy; no footer link; retention periods unspecified |

Recidivism: up to 3× the base fine. UTM = Chilean monthly tax unit (indexed monthly).

---

## Install

Copy `SKILL.md` into your Claude Code skills directory:

**macOS / Linux**
```bash
mkdir -p ~/.claude/skills/ley-datos-chile
curl -o ~/.claude/skills/ley-datos-chile/SKILL.md \
  https://raw.githubusercontent.com/carlosuhart/ley-datos-chile/main/SKILL.md
```

**Windows (PowerShell)**
```powershell
New-Item -ItemType Directory -Force "$env:USERPROFILE\.claude\skills\ley-datos-chile"
Invoke-WebRequest `
  -Uri "https://raw.githubusercontent.com/carlosuhart/ley-datos-chile/main/SKILL.md" `
  -OutFile "$env:USERPROFILE\.claude\skills\ley-datos-chile\SKILL.md"
```

The skill is available in Claude Code at the start of the next session.

---

## Usage

```
# Markdown report (internal use)
/ley-datos-chile https://ejemplo.cl

# Word document with executive summary and actionables
/ley-datos-chile https://ejemplo.cl --docx

# GDPR comparison column (only when explicitly requested)
/ley-datos-chile https://ejemplo.cl --rgpd
```

The skill always asks before generating any document: internal use (Markdown) or
client-facing (Word with executive language and prioritized next steps).

---

## What you get

Every issue — critical, medium, or low — includes evidence, a concrete action, and sanction risk:

```
**[Issue name]** (Block X — Ley 21.719)
Evidence: [what was found or what is missing]
Sanction risk: [Infracción grave/gravísima — up to X UTM]
→ Action: [concrete remediation step]
→ Suggested timeline: [30 / 60 days]   ← medium issues only
```

---

## Stack integration

### With seo-audit (AgriciDaniel/claude-seo)

When `/seo audit` detects a Chilean site (`.cl` TLD, RUT/SII/CMF/SERNAC signals,
CLP currency), it spawns `ley-datos-chile` as a conditional subagent and folds the
compliance score into the unified SEO health report.

### With anonimizar (carlosuhart/anonimizador-datos)

Audit reports may contain the audited site's PII (responsible party name, email,
RUT, EPD contact). Before sharing externally:

```
/anonimizar auditoria_ley21719_2026-06-28.md --ley chile
/anonimizar auditoria_ley21719_2026-06-28.docx --ley chile
```

Use `--ley chile` (not `--ley todo`) to avoid false positives on article numbers
(Art. 12, Art. 26, etc.).

---

## Audit scope

This skill audits what is observable externally without backend access:

- Public site documents (privacy policy, terms)
- Cookie and consent banner behavior
- Form consent mechanisms and marketing opt-out
- HTTP headers (HTTPS, HSTS, CSP, X-Frame-Options)

**Out of scope** (requires internal access):

- Data processor agreements (Art. 24)
- Records of processing activities (Art. 15)
- Internal rights-request response procedures
- Internal security measures (encryption at rest, access controls)
- Breach notifications to the APDP

The report always includes a methodology note distinguishing what was verified
externally from what remains unverifiable without backend access.

---

## Normative note

Ley 21.719 was published 13 December 2024. The 24-month adaptation period for most
obligations ends December 2026.

**Control authority:** Ley 21.719 creates the **Agencia de Protección de Datos
Personales (APDP)** as the supervisory authority. During the transition period until
the APDP is operational (estimated 2026–2027), the **Consejo para la Transparencia
(CPLT)** exercises its functions.

**Implementing regulation (*reglamento*):** still in drafting as of v1.2.1 — some
implementation details (age threshold for minors, EPD applicability criteria, third-country
adequacy decisions) may be refined upon publication. The audit flags this where relevant.

---

## Changelog

### v1.2.1 — 2026-07-03
- **Credit line added:** Zythos Media credit emitted at the start of each audit run
- Markdown template and .docx footer updated

### v1.2.0 — 2026-06-28
- **Block 0 added:** automatic stack detection (CMS, page builder, CDN, server, plugins, third-party scripts)

### v1.1.0 — 2026-06-28
- **Authority corrected:** CPLT replaced by APDP throughout, with transition note
- **Seventh data subject right added:** right not to be subject to decisions based solely on automated processing (omitted in v1.0.0)
- **Sensitive data categories completed:** socioeconomic status, ideological/philosophical convictions, sexual life, and professional affiliations added
- **Six legal bases:** added economic/financial obligations and legal defense as valid bases (Chilean law specific, absent in v1.0.0)
- **Marketing direct opt-out:** new verifiable sub-block in Block 5
- **Sanctions table:** every issue now includes the applicable fine level in UTM
- **EPD criteria corrected:** removed GDPR Art. 37 criteria; replaced with Chilean law framework
- **International transfers:** BCR and certification mechanisms added as valid guarantees
- **Security attributes:** confidentiality, integrity, availability, resilience explicitly required in policy check
- **Article references marked ⚠️** where BCN text was inaccessible for direct verification

### v1.0.0 — 2026-06-28
- Initial release

---

## License

MIT — see [LICENSE](LICENSE).

---

## Related tools

- [carlosuhart/anonimizador-datos](https://github.com/carlosuhart/anonimizador-datos) — multi-jurisdiction PII anonymizer (RGPD, Ley 21.719, LGPD, LFPDPPP, Ley 1581, CCPA)
- [AgriciDaniel/claude-seo](https://github.com/AgriciDaniel/claude-seo) — full SEO audit suite for Claude Code
