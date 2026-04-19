# KIJA — Persönlicher KI-Assistent

**KIJA** ist der digitale Hauptassistent von Rolf Müller (Partner MÜLLER PAPARIS AG, Zürich).
Claude ist das Gehirn, Power Automate ist der Nervenstrang.

## Architektur

```
                    ┌─────────────────┐
                    │      KIJA       │
                    │  (Orchestrator) │
                    └────────┬────────┘
         ┌──────────┬────────┴────────┬──────────────┐
         ▼          ▼                 ▼              ▼
    ┌────────┐ ┌────────┐      ┌──────────┐  ┌──────────┐
    │ MAIL   │ │ FRIST  │      │  REISE   │  │ BRIEFING │
    │ -Agent │ │ -Agent │      │ -Agent   │  │ -Agent   │
    └────────┘ └────────┘      └──────────┘  └──────────┘
    ✅ v0.1     🔜 v0.2          🔜 v0.3       🔜 v0.3
```

## Status

| Komponente | Status | Version |
|------------|--------|---------|
| MAIL-Agent Flow | ✅ Import-ZIP bereit | v0.1 |
| rolf-schreibstil Skill | ✅ destilliert | v0.1.1 |
| FRIST-Agent | 🔜 geplant | — |
| REISE-Agent | 🔜 geplant | — |
| KIJA-Daily-Briefing | 🔜 geplant | — |
| SharePoint-Logging | 🔜 geplant für v0.2 | — |

## Struktur

```
kija/
├── agents/
│   └── mail-agent/
│       ├── system-prompt.md      ← Claude's Anweisungen
│       └── examples.md           ← Testfälle
├── flows/
│   └── mail-agent/
│       ├── KIJA-MailAgent-Import.zip  ← fertiges Power Automate Paket
│       ├── BUILD-GUIDE.md             ← manueller Aufbau (Fallback)
│       └── src/                       ← Quell-JSON
│           ├── definition.json
│           └── manifest.json
├── skills/
│   └── rolf-schreibstil/
│       ├── SKILL.md              ← Skill-Einstiegsdatei
│       └── stilregeln.md         ← destillierte Stilregeln v0.1.1
├── logging/
│   └── sharepoint-schema.md      ← SharePoint-Listenschema für v0.2
└── setup/
    └── DEPLOYMENT.md             ← Schritt-für-Schritt Deployment
```

## Schnellstart

1. `flows/mail-agent/KIJA-MailAgent-Import.zip` in Power Automate importieren
2. Connections + API-Key zuweisen (→ `setup/DEPLOYMENT.md`)
3. Flow einschalten und testen

## Technischer Stack

- **Trigger:** Power Automate (M365)
- **Gehirn:** Claude API (claude-opus-4-7)
- **Stil-Skill:** `skills/rolf-schreibstil/` (v0.1.1, aus echten Beispielen destilliert)
- **Output:** Teams Adaptive Card + (ab v0.2) Outlook-Draft
- **Logging:** SharePoint-Liste KIJA-Activity (ab v0.2)
