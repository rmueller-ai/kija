# KIJA MAIL-Agent — Build Guide

## Empfehlung: Import Package verwenden

Das fertige Import-Package `KIJA-MailAgent-Import.zip` enthält alle 10 Actions
vorkonfiguriert. Verwende diesen Guide nur wenn du den Flow manuell aufbauen willst.

**→ Import-Anleitung: siehe Eltern-Ordner README**

---

## Manueller Aufbau (Fallback)

### Voraussetzungen
- Power Automate Account mit Zugriff auf `Default-bd9c4f3c-...` Environment
- Office 365 Outlook Connection (`shared_office365`)
- Microsoft Teams Connection (`shared_teams`)
- Anthropic API Key (gespeichert im Passwort-Manager)

---

### Schritt 1 — Trigger

**Action:** When a new email arrives (V3)
| Parameter | Wert |
|-----------|------|
| Folder | Inbox |
| Include Attachments | Yes |
| Importance | Any |

---

### Schritt 2 — Variable: API Key

**Action:** Initialize variable
| Parameter | Wert |
|-----------|------|
| Name | `AnthropicApiKey` |
| Type | String |
| Value | `sk-ant-...` (deinen Key hier) |

**Danach:** ... → Settings → **Secure Inputs: ON**

---

### Schritt 3 — Variable: SenderEmail

**Action:** Initialize variable
| Parameter | Wert |
|-----------|------|
| Name | `SenderEmail` |
| Type | String |
| Value | `@{triggerBody()?['from']}` |

---

### Schritt 4 — Variable: EmailSubject

**Action:** Initialize variable — Name: `EmailSubject`, Value: `@{triggerBody()?['subject']}`

---

### Schritt 5 — Variable: EmailBody

**Action:** Initialize variable — Name: `EmailBody`, Value: `@{triggerBody()?['body']}`

---

### Schritt 6 — Early-Exit-Filter

**Action:** Condition (If)

**Expression (OR):**
```
contains(toLower(variables('SenderEmail')), 'noreply')
contains(toLower(variables('SenderEmail')), 'no-reply')
contains(toLower(variables('SenderEmail')), 'donotreply')
contains(toLower(variables('SenderEmail')), 'newsletter')
contains(toLower(variables('EmailSubject')), 'unsubscribe')
```

- **True-Branch:** Terminate (Run Status: Succeeded)
- **False-Branch:** weiter mit Schritt 7

---

### Schritt 7 — Absender-Historie (im False-Branch)

**Action:** Get emails (V2) — Office 365 Outlook
| Parameter | Wert |
|-----------|------|
| Folder | Inbox |
| From | `@{variables('SenderEmail')}` |
| Top | 5 |
| Fetch Only Unread | No |

---

### Schritt 8 — Kalender-Events (nach Schritt 7, runAfter: Succeeded + Failed)

**Action:** Get events (V2) — Office 365 Outlook
| Parameter | Wert |
|-----------|------|
| Calendar ID | Calendar |
| $top | 3 |
| $filter | enthält SenderEmail (Expression) |

---

### Schritt 9 — Claude API Call

**Action:** HTTP
| Parameter | Wert |
|-----------|------|
| Method | POST |
| URI | `https://api.anthropic.com/v1/messages` |
| Header: x-api-key | `@{variables('AnthropicApiKey')}` |
| Header: anthropic-version | `2023-06-01` |
| Header: content-type | `application/json` |
| Body | siehe `../../../agents/mail-agent/system-prompt.md` |

**Danach:** ... → Settings → **Secure Inputs: ON**

---

### Schritt 10 — Parse JSON

**Action:** Parse JSON
| Parameter | Wert |
|-----------|------|
| Content | `@{body('Call_Claude_API')?['content']?[0]?['text']}` |
| Schema | `{"type":"object","properties":{"action":{"type":"string"},"category":{"type":"string"},"priority":{"type":"integer"},"reasoning":{"type":"string"},"draft_subject":{"type":"string"},"draft_body":{"type":"string"},"open_questions":{"type":"array","items":{"type":"string"}}}}` |

---

### Schritt 11 — Ignore-Filter

**Action:** Condition (If)

**Expression:** `less(body('Parse_Claude_Response')?['priority'], 3)`

- **True-Branch:** Terminate (Succeeded)
- **False-Branch:** weiter mit Schritt 12

---

### Schritt 12 — Teams Adaptive Card

**Action:** Post adaptive card (Microsoft Teams)
| Parameter | Wert |
|-----------|------|
| Post as | Flow bot |
| Post in | Channel |
| Team | (dein Teams-Team wählen) |
| Channel | (deinen Kanal wählen) |
| Adaptive Card | siehe `teams-adaptive-card.json` |

---

## Testen

1. **Save** → grünes Banner erscheint
2. **Test** → Manually → **Run**
3. Schick dir eine Test-Mail von einer externen Adresse
4. **Run History** → letzte Ausführung prüfen
5. Teams-Kanal: Adaptive Card sollte erscheinen
