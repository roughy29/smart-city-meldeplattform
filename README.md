# 🏙️ Smart City Bürger-Meldeplattform Wien

> A citizen issue reporting platform that automatically routes urban problem reports to the responsible city department — built as a Systems Integration project using Microsoft Azure Logic Apps.

**Live Demo:** [smart-city-meldeplattform](https://roughy29.github.io/smart-city-meldeplattform/)

---

## What it does

Citizens can report urban problems (potholes, noise complaints, broken streetlights) via a web form. Each report is automatically routed to the correct city department based on its category — no manual handling required.

```
Browser Form  ──POST──▶  Receiver Logic App
                              │
               ┌──────────────┼──────────────┐
               ▼              ▼              ▼
        Infrastruktur      Lärm         Beleuchtung
          Handler         Handler        Handler
               │              │              │
               ▼              ▼              ▼
           Tiefbauamt    Ordnungsamt     Stadtwerke
```

---

## Architecture

The integration consists of **4 Azure Logic Apps**:

| Logic App | Role |
|---|---|
| `meldung-receiver` | Receives HTTP POST from the frontend, forwards to all 3 handlers |
| `infrastruktur-handler` | Filters for infrastructure reports, sends email to Tiefbauamt |
| `laerm-handler` | Filters for noise complaints, sends email to Ordnungsamt |
| `beleuchtung-handler` | Filters for lighting issues, sends email to Stadtwerke |

Each handler evaluates a condition on the `kategorie` field. If it matches, a formatted email is dispatched. If not, the message is ignored by that handler.

---

## Tech Stack

| Layer | Technology |
|---|---|
| Frontend | HTML, CSS, JavaScript (vanilla) |
| Middleware | Microsoft Azure Logic Apps (Consumption) |
| Messaging | Outlook.com via Logic Apps connector |
| Hosting | GitHub Pages |

---

## Message Format

The frontend sends the following JSON payload to the receiver:

```json
{
  "name": "Max Mustermann",
  "email": "max@beispiel.at",
  "adresse": "Hauptstraße 12, 1010 Wien",
  "kategorie": "Infrastruktur",
  "beschreibung": "Schlagloch auf der Fahrbahn"
}
```

Routing is determined by the `kategorie` field:

| Value | Routed to |
|---|---|
| `Infrastruktur` | Tiefbauamt |
| `Lärmbelästigung` | Ordnungsamt |
| `Beleuchtung` | Stadtwerke |

---

## Local Development

```bash
git clone https://github.com/roughy29/smart-city-meldeplattform
cd smart-city-meldeplattform
python3 -m http.server 8080
```

Then open `http://localhost:8080` in your browser.

To connect your own Azure Logic App, replace the URL in `index.html`:

```js
const LOGIC_APP_URL = "YOUR_LOGIC_APP_URL_HERE";
```

---

## Project Context

Built as part of the **Systems Integration** course at FH Technikum Wien.

The project demonstrates a message routing integration pattern: a single entry point receives heterogeneous messages and dispatches them to specialized handlers based on message content — analogous to enterprise middleware patterns used in real-world banking and government systems.

**Group 8** — Marcel & Hayk
