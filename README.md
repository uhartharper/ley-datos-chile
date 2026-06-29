# Ley 21.719 Compliance Auditor

**Audit any Chilean website for Ley 21.719 compliance directly inside Claude Code.**

Chile's Personal Data Protection Law (Ley 21.719, published December 2024) replaces
the previous Ley 19.628 and introduces GDPR-like obligations: explicit consent, data
subject rights (including portability and restriction), mandatory privacy policy
content, international transfer rules, and a potential Data Protection Officer (EPD)
requirement. The 24-month adaptation period ends December 2026.

This Claude Code skill inspects a website externally — no backend access required —
and produces a scored compliance report with actionable remediation steps per issue.

---

## What it audits

10 compliance blocks, each mapped to specific articles of Ley 21.719:

| Block | Topic | Article(s) |
|-------|-------|-----------|
| 1 | Data controller identification | Art. 5 |
| 2 | Privacy policy — existence and access | Art. 14 |
| 3 | Privacy policy — minimum required content | Art. 14 a–k |
| 4 | Legal basis declared per processing purpose | Art. 12 |
| 5 | Cookie consent and opt-in mechanism | Art. 12 + 17 |
| 6 | ARCOP+L rights channel (Access, Rectification, Cancellation, Opposition, Portability, Limitation) | Art. 4–11 |
| 7 | Sensitive data handling (health, biometrics, ethnicity, minors) | Art. 2 + 16 |
| 8 | International data transfers declared with guarantees | Art. 26 |
| 9 | Technical security (HTTPS, HSTS, security headers) | Art. 19 |
| 10 | Data Protection Officer (EPD) — applicable or not | Art. 36 |

**Automatic sector detection:** the skill detects the site's vertical (health, e-commerce,
fintech, education, SaaS, media, professional services) and applies sector-specific
requirements — stricter rules for health data, minor-consent logic for education sites,
CMF regulatory overlay for fintech, etc.

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

## Install

Copy `SKILL.md` into your Claude Code skills directory:

**macOS / Linux**
```bash
mkdir -p ~/.claude/skills/ley-datos-chile
curl -o ~/.claude/skills/ley-datos-chile/SKILL.md \
  https://raw.githubusercontent.com/uhartharper/ley-datos-chile/main/SKILL.md
```

**Windows (PowerShell)**
```powershell
New-Item -ItemType Directory -Force "$env:USERPROFILE\.claude\skills\ley-datos-chile"
Invoke-WebRequest `
  -Uri "https://raw.githubusercontent.com/uhartharper/ley-datos-chile/main/SKILL.md" `
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

Every issue in the report — critical, medium, or low — includes:

```
**[Issue name]** (Block X — Art. Y)
Evidence: [what was found or what is missing]
→ Action: [concrete remediation step]
→ Suggested timeline: [30 / 60 days]   ← medium issues only
```

---

## Stack integration

### With seo-audit (AgriciDaniel/claude-seo)

When `/seo audit` detects a Chilean site (`.cl` TLD, RUT/SII/CMF/SERNAC signals,
CLP currency), it spawns `ley-datos-chile` as a conditional subagent and folds the
compliance score into the unified SEO health report.

### With anonimizar (uhartharper/anonimizador-datos)

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
- Form consent mechanisms
- HTTP headers (HTTPS, HSTS, CSP, X-Frame-Options)

**Out of scope** (requires internal access):

- Data processor agreements (Art. 24)
- Records of processing activities (Art. 15)
- Internal rights-request response procedures
- Internal security measures (encryption at rest, access controls)
- Breach notifications to CPLT (Art. 42)

The report always includes a methodology note distinguishing what was verified
externally from what remains unverifiable without backend access.

---

## Normative note

Ley 21.719 was published 13 December 2024. The 24-month adaptation period for most
obligations ends December 2026. The implementing regulation (*reglamento*) is still
in drafting as of this skill's current version — some implementation details may be
refined upon publication. The audit flags this where relevant.

---

## License

MIT — see [LICENSE](LICENSE).

---

## Related tools

- [uhartharper/anonimizador-datos](https://github.com/uhartharper/anonimizador-datos) — multi-jurisdiction PII anonymizer (RGPD, Ley 21.719, LGPD, LFPDPPP, Ley 1581, CCPA)
- [AgriciDaniel/claude-seo](https://github.com/AgriciDaniel/claude-seo) — full SEO audit suite for Claude Code
