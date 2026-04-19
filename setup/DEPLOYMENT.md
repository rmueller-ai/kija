# KIJA — Deployment-Anleitung

## Voraussetzungen

| System | Status | Notizen |
|--------|--------|---------|
| Microsoft 365 (Exchange + Teams) | ✅ vorhanden | rmueller@muellerpaparis.ch |
| Power Automate | ✅ vorhanden | Environment: Default-bd9c4f3c-... |
| Anthropic API | ✅ Key erstellt | Workspace: MP Email, Key: KIJA-MailAgent |
| GitHub Repo | ✅ erstellt | rmueller-ai/kija (Private) |

---

## Phase 1 — Flow importieren (empfohlen)

1. `flows/mail-agent/KIJA-MailAgent-Import.zip` herunterladen
2. Power Automate → **My flows** → **Import** → **Import Package (Legacy)**
3. ZIP hochladen, dann Connections zuweisen:
   - `shared_office365` → deine Office 365 Outlook Verbindung
   - `shared_teams` → deine Teams Verbindung
   - Flow → **Create as new**
4. Nach Import: Flow öffnen → **Init_AnthropicApiKey** → Wert ersetzen:
   - `DEIN_ANTHROPIC_API_KEY_HIER_EINSETZEN` → deinen `sk-ant-...` Key
   - Settings → **Secure Inputs: ON**
5. **Post_Teams_Adaptive_Card** öffnen → Teams-Gruppe und Kanal eintragen
6. Flow **Turn on** → Test

---

## Phase 2 — Notify-Only-Modus (Woche 1–2)

Flow läuft, aber:
- `ignore_filter` Schwelle auf `< 2` setzen (nur offensichtlicher Spam wird ignoriert)
- Teams-Karte zeigt Klassifikation + Empfehlung, erstellt aber noch **keinen Outlook-Draft**
- Ziel: Vertrauen in Claude's Klassifikation aufbauen

---

## Phase 3 — Auto-Draft aktivieren (Woche 3+)

Wenn Klassifikation zuverlässig (3–5 Tage Beobachtung):
1. Outlook-Draft-Aktion nach `Post_Teams_Adaptive_Card` hinzufügen:
   - Action: **Create email draft (V2)** (Reply to original mail)
   - Subject: `@{body('Parse_Claude_Response')?['draft_subject']}`
   - Body: `@{body('Parse_Claude_Response')?['draft_body']}`
2. SharePoint-Logging aktivieren (Schema: `logging/sharepoint-schema.md`)
3. `ignore_filter` Schwelle auf `< 3` zurücksetzen

---

## Phase 4 — Daily Briefing (Woche 4)

Zweiter Flow, Trigger täglich **07:30**:
1. Liest SharePoint-Log der letzten 24h
2. Liest heutige Termine (Kalender)
3. Liest offene Fristen (ELLE/Dataverse)
4. Claude-Call → strukturierte Teams-Nachricht:
   - 🔴 Kritisch heute
   - 📧 Drafts im Entwurfsordner
   - 📅 Heutige Termine
   - ⚠️ Unentschiedene Fälle

---

## Kosten-Schätzung

| Komponente | Schätzung |
|------------|-----------|
| Claude API (40 Mails/Tag, ~500 Tokens/Aufruf) | ~$9–12/Monat |
| Power Automate | im bestehenden M365-Plan |
| SharePoint-Liste | im bestehenden M365-Plan |

---

## Support / Probleme

Bei Problemen: neuen Claude-Chat starten, Screenshot der Run-History reinziehen.
