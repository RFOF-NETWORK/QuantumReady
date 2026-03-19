# QuantumReady
#42E0

# Repositorystruktur zur Orientierung

```
portable/
│
├── README.md
├── LICENSE
├── index.html
├── portable.js
├── portable.css
│
├── core/
│   ├── state-machine.js
│   ├── event-store.js
│   ├── vigilant-fetch.js
│   ├── coldnet-warmnet.js
│   ├── identity.js
│   └── parity.js
│
├── backend/
│   ├── auth.md
│   ├── sync.md
│   └── status.md
│
├── docs/
│   ├── architecture.md
│   ├── state-machine.md
│   ├── event-model.md
│   ├── parity.md
│   ├── coldnet-warmnet.md
│   └── vigilance.md
│
└── examples/
    ├── minimal-client.html
    ├── offline-demo.html
    └── multi-domain-demo.md
```

---

README.md

```markdown

PORTABLE — Domain-agnostischer, monolithischer, globaler Client

PORTABLE ist ein monolithischer, auditierbarer und portabler Client, der auf
beliebigen Domains, in verschiedenen Browsern und auf unterschiedlichen Geräten
denselben logischen Zustand darstellen kann. Der Code ist überall identisch,
der Zustand ist überall identisch, und die Realität ist überall identisch –
ohne Simulation.

Ziele

- Monolithischer Client (Single-File-Ansatz)
- Domain-agnostisches Verhalten
- Browser- und Device-übergreifender Zustand
- Offline/Online-Zustände (ColdNet/WarmNet)
- Vigilanz-Layer (Auth, Fetch, Parität)
- Globaler Event-Store (serverlos)
- Deterministische State-Machine
- Auditierbarkeit und Nachvollziehbarkeit

Kernkonzept

Der Client modelliert alle relevanten Zustände (Identität, Authentifizierung,
Netzwerkzustand) in einer expliziten State-Machine. Alle Änderungen werden als
Events beschrieben und deterministisch angewendet. Ein minimaler serverloser
Backend-Teil (auth, sync, status) dient als globaler Event-Store und
Vigilanz-Instanz.

Komponenten

- index.html – Einstiegspunkt des monolithischen Clients
- portable.js – zentrale Logik des Clients (State, Events, Vigilanz)
- portable.css – optionale Darstellung
- core/ – modulare Spezifikationen für State, Events, Vigilanz, Identität, Parität
- backend/ – Spezifikation der serverlosen Funktionen
- docs/ – Architektur- und Modellbeschreibungen
- examples/ – Anwendungs- und Szenariobeschreibungen

Status

Dieses Repository beschreibt die Architektur, Modelle und Spezifikationen von
PORTABLE. Die Dateien enthalten bewusst keine Implementierungscode, sondern
dienen als präzise Grundlage für eine spätere, auditierbare Implementierung.
```

---

LICENSE

`text
Platzhalter für Lizenztext.

Hier wird die gewünschte Lizenz für das Projekt PORTABLE eingetragen
(zum Beispiel MIT, Apache-2.0 oder eine individuelle Lizenz).
```

---

index.html

```text
Diese Datei ist der Einstiegspunkt des monolithischen Clients PORTABLE.

Inhaltlich soll sie:
- das HTML-Grundgerüst des Clients definieren,
- die Einbindung von portable.js und portable.css beschreiben,
- einen klaren Mount-Punkt für die Anwendung definieren (z.B. ein Root-Element),
- keine Logik enthalten, sondern nur Struktur und Verweise.

Die Implementierung dieser Datei folgt strikt den in docs/architecture.md
beschriebenen Prinzipien:
- monolithisch,
- deterministisch,
- ohne externe Abhängigkeiten.
```

---

portable.js

```text
Diese Datei enthält die zentrale Client-Logik von PORTABLE.

Sie ist verantwortlich für:
- Initialisierung der State-Machine,
- Laden und Anwenden von Events,
- Übergänge zwischen ColdNet und WarmNet,
- Nutzung des Vigilanz-Layers (Auth, Fetch, Parität),
- Interaktion mit dem globalen Event-Store über die spezifizierten Backend-Endpunkte.

In dieser Phase ist portable.js als logische Beschreibung zu verstehen:
- Welche Module aus core/ konzeptionell genutzt werden,
- Welche Reihenfolge der Initialisierung vorgesehen ist,
- Wie der Client auf Zustandsänderungen reagiert.

Es wird bewusst kein ausführbarer Code hinterlegt, sondern nur die Rolle und
Verantwortung dieser Datei beschrieben.
```

---

portable.css

```text
Diese Datei beschreibt die stilistische Ebene des PORTABLE-Clients.

Ziel:
- eine klare, minimalistische Darstellung,
- visuelle Unterscheidung von Zuständen (z.B. ColdNet/WarmNet),
- keine Logik, nur Präsentation.

Die konkrete Gestaltung ist austauschbar und hat keinen Einfluss auf die
Architektur, den Zustand oder die Parität des Systems.
```

---

core/state-machine.js

```text
Dieses Modul beschreibt die State-Machine von PORTABLE.

Zustandsdimensionen:
- identity: UNREGISTERED | REGISTERED
- auth: LOGGEDOUT | LOGGEDIN
- net: COLDNET | WARMNET

Der kombinierte Zustand ergibt sich als kartesisches Produkt dieser Dimensionen.

Beispiele für Zustände:
- UNREGISTERED + LOGGED_OUT + COLDNET
- REGISTERED + LOGGED_IN + WARMNET

Dieses Modul definiert:
- die Menge aller gültigen Zustände,
- die erlaubten Übergänge zwischen Zuständen,
- die Bedingungen für Übergänge (z.B. erfolgreiche Authentifizierung, Netzwechsel),
- die Invarianten (z.B. LOGGED_IN ist nur möglich, wenn identity = REGISTERED).

Es enthält keine Implementierung, sondern die formale Spezifikation der
Zustandslogik.
```

---

core/event-store.js

```text
Dieses Modul beschreibt das Event-Modell und den Event-Store von PORTABLE.

Ein Event besteht aus:
- type (Art des Ereignisses),
- payload (Dateninhalt),
- timestamp (Zeitpunkt der Erzeugung),
- clientId (Identifikation des Clients),
- eventId (eindeutige Event-ID),
- version (Version des globalen Zustands oder Streams).

Dieses Modul definiert:
- das Schema für Events,
- Regeln für Versionierung,
- Idempotenz-Anforderungen (ein Event darf bei Wiederholung keine doppelte Wirkung entfalten),
- die Rolle des Event-Stores als einzige Quelle der Wahrheit für Zustandsänderungen,
- die Möglichkeit, den Zustand durch deterministisches Replay der Events zu rekonstruieren.

Es beschreibt die Logik und Anforderungen, aber keine konkrete Speicher- oder
Transportimplementierung.
```

---

core/vigilant-fetch.js

```text
Dieses Modul beschreibt den Vigilanz-Layer für Netzwerkzugriffe.

Aufgaben:
- zentrale Abstraktion für alle Netzwerkaufrufe,
- Prüfung des aktuellen Authentifizierungszustands,
- Prüfung des aktuellen Netzwerkzustands (ColdNet/WarmNet),
- Entscheidung, ob ein Request sofort gesendet oder als Event in eine Outbox
  geschrieben wird,
- Behandlung von Antworten im Kontext der Parität (z.B. Abgleich von Versionen).

Dieses Modul definiert:
- welche Eingaben ein Vigilanz-Aufruf benötigt (z.B. Ziel-Endpunkt, Event-Daten),
- welche Ausgaben erwartet werden (z.B. Bestätigung, Fehler, neue Events),
- wie Fehlerzustände klassifiziert werden (z.B. Netzfehler vs. Auth-Fehler),
- wie der Vigilanz-Layer mit dem Event-Store und der State-Machine interagiert.

Es enthält keine konkrete Implementierung von Netzwerkaufrufen, sondern die
konzeptionelle Struktur und Verantwortung.
```

---

core/coldnet-warmnet.js

```text
Dieses Modul beschreibt das Modell der Netzwerkzustände ColdNet und WarmNet.

ColdNet:
- Der Client ist offline oder kann das Backend nicht erreichen.
- Events werden lokal in einer Outbox persistiert.
- Der lokale Zustand wird ausschließlich aus lokalen Events abgeleitet.

WarmNet:
- Der Client kann das Backend erreichen.
- Ausstehende Events werden mit dem Backend synchronisiert.
- Der Client kann neue Events vom Backend empfangen und anwenden.

Dieses Modul definiert:
- die Kriterien für den Übergang von ColdNet zu WarmNet und umgekehrt,
- die Pflichten beim Übergang (z.B. Outbox-Synchronisation),
- die Invarianten (z.B. keine direkte Zustandsänderung ohne Event).

Es beschreibt die Zustandslogik, nicht die technische Umsetzung der
Netzwerkprüfung.
```

---

core/identity.js

```text
Dieses Modul beschreibt das Identitätsmodell von PORTABLE.

Identität umfasst:
- eine User-Identität (z.B. userId),
- optional eine Device-Identität (z.B. deviceId),
- optionale kryptografische Schlüssel oder Token, die zur Authentifizierung
  gegenüber dem Backend genutzt werden.

Dieses Modul definiert:
- wie Identität konzeptionell erzeugt wird (z.B. Registrierung),
- wie Identität persistiert und wiederhergestellt wird,
- wie Identität mit Events verknüpft wird (z.B. clientId, userId in Events),
- welche Anforderungen an die Stabilität der Identität gestellt werden
  (z.B. domain-agnostisch, browser-übergreifend).

Es enthält keine konkrete Implementierung von Kryptografie oder Token-Handling,
sondern die logische Struktur der Identität.
```

---

core/parity.js

```text
Dieses Modul beschreibt das Paritätskonzept von PORTABLE.

Parität bedeutet:
- der lokale Zustand und der vom Backend bekannte Zustand sind konsistent,
- alle relevanten Events sind beidseitig bekannt und in derselben Reihenfolge
  angewendet,
- Wiederholungen von Events führen nicht zu abweichenden Zuständen
  (Idempotenz).

Dieses Modul definiert:
- welche Kriterien erfüllt sein müssen, damit Parität als gegeben gilt,
- wie Parität geprüft wird (z.B. durch Versionsnummern, Checksummen oder
  Event-IDs),
- wie mit Paritätsverletzungen umgegangen wird (z.B. erneutes Laden von Events,
  Konfliktauflösung nach definierten Regeln),
- welche Rolle der Vigilanz-Layer bei der Sicherstellung von Parität spielt.

Es beschreibt die Regeln und Prüfmechanismen, nicht deren technische Umsetzung.
```

---

backend/auth.md

```markdown

Backend-Funktion: /auth

Die Funktion /auth ist für alle Authentifizierungsvorgänge im System PORTABLE
zuständig. Sie ist als serverlose Funktion konzipiert und domain-agnostisch.

Aufgaben

- Registrierung neuer Identitäten
- Login bestehender Identitäten
- Ausgabe von Authentifizierungs-Tokens
- Refresh ablaufender Tokens
- Logout (Invalidierung von Tokens)

Request-Schema

Allgemeine Felder:
- clientId: Identifikation des Clients
- action: "register" | "login" | "refresh" | "logout"
- payload: aktionsspezifische Daten

Beispiele für payload:
- register: { credentials, optional metadata }
- login: { credentials }
- refresh: { refreshToken }
- logout: { token }

Response-Schema

Allgemeine Felder:
- status: "ok" | "error"
- message: optionale Beschreibung
- data: aktionsspezifische Daten

Beispiele für data:
- bei erfolgreichem Login/Refresh:
  - { token, refreshToken, userId, expiresAt }

Idempotenz

- Mehrfache Refresh-Anfragen mit demselben gültigen Refresh-Token sollen
  deterministisch behandelt werden.
- Logout-Anfragen für bereits invalidierte Tokens sollen keinen Fehler
  verursachen, sondern eine neutrale Bestätigung liefern.

Sicherheit

- Alle sensiblen Daten müssen transportverschlüsselt übertragen werden.
- Tokens dürfen nur an den berechtigten Client ausgegeben werden.
- Die Funktion darf keine domain-spezifischen Annahmen treffen.
`

---

backend/sync.md

```markdown

Backend-Funktion: /sync

Die Funktion /sync ist für die Synchronisation von Events zwischen Client und
globalem Event-Store zuständig. Sie ist als serverlose Funktion konzipiert.

Aufgaben

- Entgegennahme von Events vom Client
- Validierung und Speicherung der Events im globalen Event-Store
- Bereitstellung neuer oder fehlender Events für den Client
- Sicherstellung von Idempotenz und Parität

Request-Schema

Allgemeine Felder:
- clientId: Identifikation des Clients
- userId: Identität des Users (falls vorhanden)
- token: Authentifizierungs-Token
- events: Liste von Events, die der Client senden möchte
- lastKnownVersion: Version des globalen Zustands, die der Client zuletzt kennt

Event-Schema (siehe docs/event-model.md):
- eventId
- type
- payload
- timestamp
- clientId
- version (optional, je nach Modell)

Response-Schema

Allgemeine Felder:
- status: "ok" | "error"
- message: optionale Beschreibung
- data:
  - acceptedEvents: Liste der akzeptierten Events (mit finaler Version)
  - newEvents: Liste von Events, die der Client noch nicht kennt
  - currentVersion: aktuelle Version des globalen Zustands

Idempotenz

- Events mit bereits bekannter eventId dürfen keine doppelte Wirkung entfalten.
- Wiederholte Übertragung derselben Events muss zu einem konsistenten Ergebnis
  führen.

Parität

- Die Funktion nutzt lastKnownVersion und currentVersion, um festzustellen,
  ob der Client mit dem globalen Zustand im Gleichlauf ist.
- Bei Abweichungen werden fehlende Events in newEvents zurückgegeben.

Sicherheit

- Authentifizierung über token ist erforderlich.
- Der Zugriff auf Events ist an userId und/oder clientId gebunden.
```

---

backend/status.md

```markdown

Backend-Funktion: /status

Die Funktion /status dient als Health- und Vigilanz-Endpunkt für PORTABLE.
Sie ist als serverlose Funktion konzipiert.

Aufgaben

- Bereitstellung eines einfachen Health-Checks
- Bereitstellung von Metadaten zur aktuellen Backend-Version
- Unterstützung des Clients bei der Entscheidung, ob WarmNet möglich ist

Request-Schema

Allgemeine Felder:
- clientId: optional
- userId: optional
- token: optional (für erweiterte Statusinformationen)

Response-Schema

Allgemeine Felder:
- status: "ok" | "degraded" | "down"
- message: optionale Beschreibung
- data:
  - backendVersion: Version der Backend-Implementierung
  - time: aktuelle Serverzeit
  - features: optionale Liste aktiver Features oder Flags

Nutzung im Client

- Der Client kann /status nutzen, um:
  - den Übergang von ColdNet zu WarmNet zu prüfen,
  - Latenz und Erreichbarkeit zu bewerten,
  - Versionsunterschiede zu erkennen.

Sicherheit

- Basis-Health-Informationen können öffentlich sein.
- Erweiterte Informationen können eine Authentifizierung erfordern.
```

---

docs/architecture.md

```markdown

Architektur von PORTABLE

PORTABLE ist als monolithischer Client mit einem minimalen serverlosen Backend
konzipiert. Ziel ist ein domain-agnostischer, browser-übergreifender und
device-übergreifender Zustand.

Komponenten

- Monolithischer Client:
  - index.html
  - portable.js
  - portable.css
- Core-Module:
  - State-Machine
  - Event-Store
  - Vigilanz-Layer
  - ColdNet/WarmNet-Modell
  - Identität
  - Parität
- Serverloses Backend:
  - /auth
  - /sync
  - /status

Datenfluss

1. Der Client initialisiert die State-Machine und lädt lokale Events.
2. Der Client prüft den Netzwerkzustand (ColdNet/WarmNet).
3. Bei WarmNet synchronisiert der Client Events über /sync.
4. Authentifizierungsvorgänge laufen über /auth.
5. Health- und Statusinformationen werden über /status abgefragt.
6. Der Zustand des Clients wird ausschließlich aus Events abgeleitet.

Prinzipien

- Monolithisch: ein klar definierter Client ohne verteilte Frontend-Logik.
- Deterministisch: gleicher Input führt zu gleichem Output.
- Event-basiert: alle Änderungen sind Events.
- Domain-agnostisch: keine Abhängigkeit von der Host-Domain.
- Auditierbar: Zustand ist aus Events rekonstruierbar.
```

---

docs/state-machine.md

```markdown

State-Machine von PORTABLE

Die State-Machine modelliert die zentralen Zustände des Clients.

Zustandsdimensionen

- identity:
  - UNREGISTERED
  - REGISTERED

- auth:
  - LOGGED_OUT
  - LOGGED_IN

- net:
  - COLDNET
  - WARMNET

Kombinierte Zustände

Der Gesamtzustand ist das kartesische Produkt der drei Dimensionen.

Beispiele:
- UNREGISTERED + LOGGED_OUT + COLDNET
- REGISTERED + LOGGED_OUT + WARMNET
- REGISTERED + LOGGED_IN + WARMNET

Übergänge

Beispiele für erlaubte Übergänge:
- UNREGISTERED → REGISTERED (durch erfolgreiche Registrierung)
- LOGGEDOUT → LOGGEDIN (durch erfolgreichen Login)
- WARMNET → COLDNET (bei Netzverlust)
- COLDNET → WARMNET (bei erfolgreichem Status-Check)

Invarianten

- LOGGED_IN ist nur möglich, wenn identity = REGISTERED.
- Netzwechsel (COLDNET/WARMNET) beeinflusst nicht direkt identity oder auth,
  aber die Art der Event-Verarbeitung.
```

---

docs/event-model.md

```markdown

Event-Modell von PORTABLE

Events sind die einzige Quelle der Wahrheit für Zustandsänderungen.

Event-Schema

Ein Event besteht aus:
- eventId: eindeutige Identifikation
- type: Art des Ereignisses
- payload: Dateninhalt
- timestamp: Zeitpunkt der Erzeugung
- clientId: Identifikation des Clients
- userId: optional, Identifikation des Users
- version: optionale Versionsinformation

Eigenschaften

- Events sind unveränderlich.
- Events werden in der Reihenfolge ihrer Anwendung betrachtet.
- Der Zustand kann durch Replay der Events rekonstruiert werden.

Idempotenz

- Die Verarbeitung eines Events mit derselben eventId darf bei Wiederholung
  keine zusätzliche Wirkung entfalten.
- Der Event-Store muss erkennen, ob ein Event bereits verarbeitet wurde.

Kategorien von Events

Beispiele:
- Identitätsbezogene Events (Registrierung, Profiländerung)
- Authentifizierungsbezogene Events (Login, Logout)
- Domänenspezifische Events (z.B. Einstellungen, Aktionen des Users)
```

---

docs/parity.md

```markdown

Parität in PORTABLE

Parität beschreibt die Übereinstimmung zwischen lokalem Zustand und dem
Zustand im globalen Event-Store.

Kriterien für Parität

- Alle relevanten Events sind sowohl lokal als auch im Backend vorhanden.
- Die Reihenfolge der Events ist konsistent.
- Die aus den Events abgeleiteten Zustände sind identisch.

Prüfmechanismen

- Vergleich von Versionsnummern (z.B. currentVersion vs. lastKnownVersion).
- Vergleich von Event-IDs oder Checksummen.
- Nutzung von /sync, um fehlende Events zu identifizieren.

Umgang mit Paritätsverletzungen

- Nachladen fehlender Events vom Backend.
- Erneutes Senden ausstehender Events aus der lokalen Outbox.
- Anwendung definierter Konfliktlösungsstrategien, falls erforderlich.

Parität ist ein zentrales Ziel des Vigilanz-Layers und des Event-Stores.
```

---

docs/coldnet-warmnet.md

```markdown

ColdNet und WarmNet in PORTABLE

ColdNet und WarmNet modellieren den Netzwerkzustand des Clients.

ColdNet

- Der Client kann das Backend nicht erreichen.
- Events werden lokal in einer Outbox gespeichert.
- Der Zustand wird ausschließlich aus lokalen Events abgeleitet.

WarmNet

- Der Client kann das Backend erreichen.
- Ausstehende Events werden mit /sync übertragen.
- Der Client kann neue Events vom Backend empfangen und anwenden.

Übergänge

- COLDNET → WARMNET:
  - Erfolgreicher Aufruf von /status oder /sync.
- WARMNET → COLDNET:
  - wiederholte Fehler beim Zugriff auf das Backend.

Auswirkungen

- In ColdNet werden keine direkten Backend-Aufrufe erwartet.
- In WarmNet wird versucht, Parität mit dem Backend herzustellen und zu halten.
```

---

docs/vigilance.md

```markdown

Vigilanz in PORTABLE

Der Vigilanz-Layer überwacht und steuert alle kritischen Aspekte der
Kommunikation und des Zustands.

Aufgaben

- Überwachung des Authentifizierungszustands
- Überwachung des Netzwerkzustands (ColdNet/WarmNet)
- Sicherstellung von Parität
- Klassifikation und Behandlung von Fehlern
- Entscheidung, ob Requests gesendet oder Events gepuffert werden

Interaktion mit anderen Komponenten

- nutzt die State-Machine, um den aktuellen Zustand zu kennen,
- nutzt den Event-Store, um Events zu erzeugen und zu verarbeiten,
- nutzt die Backend-Endpunkte /auth, /sync, /status, um mit dem
  globalen Event-Store zu interagieren.

Vigilanz ist kein optionaler Zusatz, sondern ein zentrales Prinzip der
Architektur von PORTABLE.
```

---

examples/minimal-client.html

```text
Diese Datei beschreibt ein minimales Szenario, in dem der PORTABLE-Client
eingebunden wird.

Sie dokumentiert:
- welche Elemente im HTML vorhanden sein müssen,
- wie portable.js und portable.css referenziert werden,
- welche minimalen Voraussetzungen für die Initialisierung gelten.

Es wird kein ausführbarer Code hinterlegt, sondern eine textuelle Beschreibung
des minimalen Setups.
```

---

examples/offline-demo.html

```text
Diese Datei beschreibt ein Szenario, in dem der Client gezielt im ColdNet-Modus
arbeitet.

Sie dokumentiert:
- wie der Client ohne Backend-Verbindung startet,
- wie Events lokal erzeugt und in der Outbox gehalten werden,
- wie der Übergang zu WarmNet konzeptionell ablaufen würde.

Ziel ist es, das Verhalten im Offline-Fall verständlich zu machen, ohne
Implementierungscode zu liefern.
```

---

examples/multi-domain-demo.md

```markdown

Multi-Domain-Demo für PORTABLE

Dieses Dokument beschreibt ein Szenario, in dem derselbe PORTABLE-Client auf
verschiedenen Domains eingesetzt wird und dennoch denselben logischen Zustand
nutzt.

Annahmen

- Der Client-Code (index.html, portable.js, portable.css) ist identisch
  auf allen Domains.
- Die Identität des Users ist domain-agnostisch (z.B. über userId und Token).
- Der globale Event-Store ist über das Backend erreichbar.

Ablauf

1. User nutzt Domain A, führt Aktionen aus → Events werden erzeugt und
   synchronisiert.
2. User wechselt zu Domain B, nutzt denselben Client-Code.
3. Der Client auf Domain B identifiziert den User und synchronisiert Events
   über /sync.
4. Der Zustand auf Domain B entspricht dem Zustand auf Domain A.

Dieses Szenario zeigt, wie PORTABLE domain-agnostisch und global konsistent
arbeiten kann.
```

---

```
<!DOCTYPE html>
<html lang="de">
<head>
    <meta charset="UTF-8">
    <title>Monolithische PyScript-API</title>

    <!-- PyScript laden -->
    <link rel="stylesheet" href="https://pyscript.net/latest/pyscript.css">
    <script defer src="https://pyscript.net/latest/pyscript.js"></script>

    <style>
        body { font-family: sans-serif; }
        .status { margin: 10px 0; }
        .online { color: green; }
        .offline { color: red; }
    </style>
</head>
<body>

<h1>Monolithische API in index.html</h1>

<div class="status">
    Online-Status: <span id="online-status">unbekannt</span><br>
    User-Status: <span id="user-status">logged out / unregistered</span>
</div>

<form id="login-form">
    <input type="text" id="username" placeholder="Benutzername">
    <input type="password" id="password" placeholder="Passwort">
    <button type="submit">Login / Register</button>
</form>

<button id="logout-btn">Logout</button>
<button id="sync-btn">Sync mit externer API</button>

<py-script>
from pyscript import document
from pyscript import storage
import js
import json
import asyncio

# -----------------------------
# 1. "Pseudo-Datenbank" via pyscript.storage (IndexedDB)
# -----------------------------
# Name des Storage-Bereichs
DB_NAME = "app-state"

# globaler State
state = {
    "online": bool(js.navigator.onLine),
    "logged_in": False,
    "registered": False,
    "username": None,
    "token": None,   # z.B. von externer API
}

async def load_state():
    global state
    db = await storage(DB_NAME)
    # Falls schon etwas gespeichert ist, laden
    if "state" in db:
        state = db["state"]

async def save_state():
    db = await storage(DB_NAME)
    db["state"] = state
    await db.sync()

def update_status_ui():
    online_el = document.getElementById("online-status")
    user_el = document.getElementById("user-status")

    online_el.innerText = "online" if state["online"] else "offline"
    online_el.className = "online" if state["online"] else "offline"

    if state["logged_in"]:
        reg = "registered" if state["registered"] else "unregistered"
        user_el.innerText = f'logged in as {state["username"]} ({reg})'
    else:
        user_el.innerText = "logged out / unregistered"

# -----------------------------
# 2. Online/Offline-Events aus JS
# -----------------------------
def set_online_status(event=None):
    state["online"] = bool(js.navigator.onLine)
    update_status_ui()
    asyncio.ensure_future(save_state())

js.window.addEventListener("online", set_online_status)
js.window.addEventListener("offline", set_online_status)

# -----------------------------
# 3. Login / Register Logik
# -----------------------------
async def handle_login(event):
    event.preventDefault()
    username = document.getElementById("username").value
    password = document.getElementById("password").value

    # Beispiel: Wenn online, versuche bei externer API zu authentifizieren
    if state["online"]:
        # Hier würdest du pyfetch/js.fetch nutzen, um eine echte API aufzurufen
        # Pseudo-Code:
        # resp = await js.fetch("https://deine-api/login", {...})
        # data = await resp.json()
        # if data["ok"]:
        #     state["logged_in"] = True
        #     state["registered"] = True
        #     state["username"] = username
        #     state["token"] = data["token"]
        # else: ...
        state["logged_in"] = True
        state["registered"] = True
        state["username"] = username
        state["token"] = "dummy-token"
    else:
        # Offline: nur lokal einloggen (z.B. als unregistered oder cached user)
        state["logged_in"] = True
        state["registered"] = False
        state["username"] = username
        state["token"] = None

    update_status_ui()
    await save_state()

async def handle_logout(event):
    event.preventDefault()
    state["logged_in"] = False
    state["username"] = None
    state["token"] = None
    update_status_ui()
    await save_state()

async def handle_sync(event):
    event.preventDefault()
    if not state["online"]:
        js.console.log("Offline, kein Sync möglich.")
        return

    # Hier würdest du lokale Daten mit externer API synchronisieren
    # Pseudo:
    # db = await storage("user-data")
    # lokale_daten = db["..."]
    # resp = await js.fetch("https://deine-api/sync", {...})
    js.console.log("Sync mit externer API (Pseudo).")

# -----------------------------
# 4. Event-Handler verbinden
# -----------------------------
def setup_events():
    login_form = document.getElementById("login-form")
    logout_btn = document.getElementById("logout-btn")
    sync_btn = document.getElementById("sync-btn")

    login_form.addEventListener("submit", handle_login)
    logout_btn.addEventListener("click", handle_logout)
    sync_btn.addEventListener("click", handle_sync)

async def main():
    await load_state()
    set_online_status()
    update_status_ui()
    setup_events()

asyncio.ensure_future(main())
</py-script>

</body>
</html>
```
