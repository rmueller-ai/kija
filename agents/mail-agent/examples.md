# MAIL-Agent Testfälle

## Fall 1 — Neue Mandatsanfrage
**Input:**
- Von: peter.suter@gmail.com
- Betreff: Anfrage Arbeitsrechtsfall
- Body: "Ich wurde fristlos entlassen und suche Unterstützung."

**Erwarteter Output:**
```json
{
  "action": "notify",
  "reasoning": "Neue Mandatsanfrage — Rolf muss selbst entscheiden ob Annahme",
  "category": "mandant",
  "priority": 2,
  "draft_subject": "",
  "draft_body": "",
  "open_questions": ["Mandat annehmen? Interessenkonflikt prüfen.", "Kostenvorschuss-Betrag festlegen."]
}
```

## Fall 2 — Gericht Fristverlängerung
**Input:**
- Von: sekretariat.bezirksgericht.zh@zh.ch
- Betreff: OG.2024.00512 — Replik Frist
- Body: "Hiermit setzen wir Ihnen Frist zur Replik bis 15. Mai 2026."

**Erwarteter Output:**
```json
{
  "action": "urgent",
  "reasoning": "Gerichtliche Fristsetzung mit klarem Datum — Frist in Kalender eintragen",
  "category": "gericht",
  "priority": 1,
  "draft_subject": "",
  "draft_body": "",
  "open_questions": ["Frist 15. Mai 2026 in Kalender eintragen.", "Replik-Vorbereitung starten."]
}
```

## Fall 3 — Mandant Statusfrage
**Input:**
- Von: h.kaya@musterfirma.ch
- Betreff: Stand Betreibungsverfahren
- Body: "Guten Tag, gibt es Neuigkeiten zum Verfahren?"

**Erwarteter Output:**
```json
{
  "action": "draft",
  "reasoning": "Routine-Statusfrage, Antwort mit verfügbaren Infos möglich",
  "category": "mandant",
  "priority": 3,
  "draft_subject": "Re: Stand Betreibungsverfahren",
  "draft_body": "Sehr geehrter Herr Kaya\n\nBesten Dank für Ihre Nachricht. Ich werde Sie in Kürze über den aktuellen Stand informieren. Gerne prüfe ich den Fortschritt und melde mich bei Ihnen.\n\nFreundliche Grüsse",
  "open_questions": ["Aktuellen Stand im ELLE-Dossier prüfen bevor Senden."]
}
```

## Fall 4 — Newsletter
**Input:**
- Von: newsletter@legaltech-event.com
- Betreff: Legal Innovation Summit 2026 — Jetzt anmelden!

**Erwarteter Output:**
```json
{
  "action": "ignore",
  "reasoning": "Newsletter — kein Handlungsbedarf",
  "category": "newsletter",
  "priority": 5,
  "draft_subject": "",
  "draft_body": "",
  "open_questions": []
}
```
