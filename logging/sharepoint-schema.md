# KIJA-Activity — SharePoint-Listenschema

SharePoint-Liste für Flow-Logging. Jede Ausführung des MAIL-Agent schreibt einen Eintrag.

## Spalten

| Spaltenname | Typ | Pflicht | Beschreibung |
|-------------|-----|---------|--------------|
| Title | Einzeiligen Text | Ja | Auto: `KIJA-MAIL-{Datum}` |
| AgentName | Einzeiligen Text | Ja | z.B. `MAIL-Agent` |
| FlowRunId | Einzeiligen Text | Nein | Power Automate Run-ID |
| Timestamp | Datum und Uhrzeit | Ja | Trigger-Zeitpunkt |
| SenderEmail | Einzeiligen Text | Ja | Von-Adresse |
| EmailSubject | Einzeiligen Text | Ja | Betreff |
| Category | Auswahl | Ja | mandant, gericht, kollegen, admin, spam, newsletter, privat |
| Priority | Zahl | Ja | 1–5 |
| Action | Auswahl | Ja | ignore, notify, draft, urgent |
| DraftCreated | Ja/Nein | Ja | Wurde ein Entwurf erstellt? |
| DraftModified | Ja/Nein | Nein | Hat Rolf den Entwurf geändert? (v0.2) |
| SentAt | Datum und Uhrzeit | Nein | Wann wurde gesendet? (v0.2) |
| Reasoning | Mehrzeiliger Text | Nein | Claude's Begründung |
| OpenQuestions | Mehrzeiliger Text | Nein | JSON-Array |

## Verwendung

Power Automate Action: **Create item** (SharePoint)
- Nach dem Parse-JSON-Schritt
- Vor dem Ignore-Filter

## Lern-Schleife (v0.2)

Wöchentlicher Flow:
1. Liest alle Einträge der letzten 7 Tage wo `DraftModified = true`
2. Vergleicht Entwurf vs. tatsächlich gesendete Mail (via Graph API)
3. Extrahiert Diff-Muster
4. Claude schlägt Updates für `skills/rolf-schreibstil/stilregeln.md` vor
