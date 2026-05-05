[[IoT|zurück]]

---

# Programmierung, IDEs & Python

Im Unterricht wurde **Python 3** als primäre Sprache verwendet – für Datenbankzugriff, REST APIs, Datenverarbeitung und Automatisierung.

## Python – Grundlagen

### Datentypen

```python
x   = 42           # int
f   = 3.14         # float
b   = True         # bool
s   = "hallo"      # str
lst = [1, 2, 3]    # list  – geordnet, veränderlich
dic = {"a": 1}     # dict  – Schlüssel-Wert-Paare
```

Typumwandlung:
```python
float("3.14")   # → 3.14
int(3.9)        # → 3  (kein Runden – abschneiden!)
str(42)         # → "42"
bin(88)         # → '0b1011000'  (Binärdarstellung)
```

### Kontrollstrukturen

```python
# if / elif / else
if pentfernung <= 25 and pnutzlast <= 12:
    print("Transport möglich")
elif pnutzlast > 12:
    print("Zu schwer")
else:
    print("Zu weit")

# for-Schleife (Liste)
mac = ['00-80-41-ae-fd-7e', '008041-aefd7e', '00:80:41:ae:fd:7e']
for adresse in mac:
    print(adresse)

# for-Schleife (range)
for i in range(1, 11):
    print(i)

# while
x = 0
while x < 5:
    x += 1
```

### Funktionen

```python
def fallhöhe(t):
    g = 9.81
    s = 0.5 * g * t**2
    return s

zeit  = float(input('Fallzeit in Sekunden: '))
höhe  = fallhöhe(zeit)
print('Fallhöhe:', round(höhe, 2), 'm')
```

```python
# Rückgabe True/False
def ausdruck(liste, x):
    for adresse in liste:
        if adresse == x:
            return True
    return False

ok = ausdruck(mac, '00-80-41-ae-fd-7e')
print(ok)   # True
```

### Listen

```python
werte = []
for value in range(1, 11):
    wert = round(random.uniform(19.5, 25.7), 2)
    werte.append(wert)   # Element anhängen

print(len(werte))        # Länge
print(werte[0])          # erstes Element (Index 0)
print(werte[-1])         # letztes Element
```

**Indexing & Slicing:**

```python
lst = [10, 20, 30, 40, 50]

lst[0]      # 10  – erstes Element
lst[-1]     # 50  – letztes Element
lst[1:3]    # [20, 30]  – Index 1 bis 2 (3 exklusiv)
lst[:2]     # [10, 20]  – Anfang bis Index 1
lst[2:]     # [30, 40, 50]  – ab Index 2 bis Ende
lst[::-1]   # [50, 40, 30, 20, 10]  – umgekehrt
```

**Wichtige Listen-Methoden:**

```python
lst = [3, 1, 4, 1, 5]

lst.append(9)       # Element ans Ende anfügen       → [3,1,4,1,5,9]
lst.insert(0, 99)   # Element an Position einfügen   → [99,3,1,...]
lst.remove(1)       # erstes Vorkommen entfernen     → entfernt erste 1
lst.pop()           # letztes Element entfernen + zurückgeben
lst.pop(0)          # Element an Index 0 entfernen
lst.sort()          # in-place sortieren (aufsteigend)
lst.sort(reverse=True)  # absteigend
lst.reverse()       # in-place umkehren
lst.index(4)        # Index des ersten Vorkommens → 2
lst.count(1)        # wie oft kommt 1 vor? → 2
lst.clear()         # alle Elemente löschen
```

**Nützliche Built-in-Funktionen:**

```python
zahlen = [5, 2, 8, 1, 9]

sorted(zahlen)              # sortierte Kopie (Original bleibt)
sorted(zahlen, reverse=True)
min(zahlen)   # 1
max(zahlen)   # 9
sum(zahlen)   # 25

# enumerate: Index + Wert gleichzeitig
for i, wert in enumerate(zahlen):
    print(i, wert)    # 0 5 / 1 2 / 2 8 / ...

# zip: zwei Listen parallel durchlaufen
namen  = ['Anna', 'Bob', 'Clara']
punkte = [85, 92, 78]
for name, p in zip(namen, punkte):
    print(name, p)    # Anna 85 / Bob 92 / ...
```

**List Comprehension (kompakte Listenerzeugung):**

```python
# Quadratzahlen von 1 bis 5
quadrate = [x**2 for x in range(1, 6)]   # [1, 4, 9, 16, 25]

# Nur gerade Zahlen
gerade = [x for x in range(1, 11) if x % 2 == 0]   # [2,4,6,8,10]

# Strings transformieren
namen = ['anna', 'bob']
gross = [n.upper() for n in namen]   # ['ANNA', 'BOB']
```

**2D-Listen (Liste von Listen / Matrix):**

```python
matrix = [
    [1, 2, 3],
    [4, 5, 6],
    [7, 8, 9]
]

matrix[0][0]   # 1  – erste Zeile, erste Spalte
matrix[1][2]   # 6  – zweite Zeile, dritte Spalte

# Alle Werte ausgeben
for zeile in matrix:
    for wert in zeile:
        print(wert, end=" ")
    print()

# Matrix mit List Comprehension erstellen (3×3 Nullen)
nullen = [[0] * 3 for _ in range(3)]
```

> [!tip] **Merksatz**
> Listen in Python sind **nullbasiert** (`lst[0]` = erstes Element). Slicing ist **exklusiv am Ende** (`lst[1:3]` → Index 1 und 2, nicht 3). Negative Indizes zählen von hinten: `lst[-1]` = letztes Element.

### Dictionaries

```python
mac = {
    'rechner_1': '00-80-41-ae-fd-7e',
    'rechner_2': '008041-aefd7e',
    'rechner_3': '00:80:41:ae:fd:7e'
}

print(mac.keys())           # alle Schlüssel
print(mac.values())         # alle Werte
print(mac['rechner_3'])     # Wert per Schlüssel

for schlüssel, wert in mac.items():
    print(schlüssel, ':', wert)
```

### Hilfsfunktionen

```python
import random
x = random.uniform(19.5, 25.7)   # Zufallszahl im Bereich
x = round(x, 2)                   # auf 2 Nachkommastellen runden

# Binärzahl
x1 = bin(88)          # '0b1011000'
x2 = x1.lstrip('0b') # '1011000'
x3 = x2.zfill(8)     # '01011000'  (mit führenden Nullen auf 8 Bit auffüllen)
```

## Python + MySQL (mysql.connector)

```python
import mysql.connector

# Verbindung herstellen
try:
    connection = mysql.connector.connect(
        host="localhost", user="root", passwd="", db="shop"
    )
except:
    print("Keine Verbindung zum Server")

cursor = connection.cursor()

# Lesen
cursor.execute("SELECT * FROM artikel")
result = cursor.fetchall()          # alle Zeilen als Liste von Tuples
print('Anzahl:', len(result))
for datensatz in result:
    for feld in datensatz:
        print(feld, end=" ")
    print()

# Schreiben – IMMER parameterisiert (kein f-String!)
name = input("Name: ")
geb  = input("Geburtsdatum: ")
pw   = input("Passwort: ")
cursor.execute(
    "INSERT INTO Kunden VALUES (NULL, %s, %s, %s);",
    [name, geb, pw]
)
connection.commit()   # ohne commit → keine Änderung gespeichert!

cursor.close()
connection.close()
```

> [!warning] **Achtung Falle – SQL Injection**
> Niemals so: `f"SELECT * FROM kunden WHERE name='{name}'"` – ein Angreifer kann `'; DROP TABLE kunden; --` eingeben. Immer `%s` als Platzhalter + Liste als zweites Argument.

## Passwort-Hashing mit bcrypt

```python
import mysql.connector, bcrypt

# Login prüfen
name     = input("Name: ")
passwort = input("Passwort: ").encode('utf-8')   # str → bytes!

cursor.execute("SELECT Passwort FROM kunden WHERE Name = %s;", (name,))
hash_db = cursor.fetchone()[0].encode('utf-8')

if bcrypt.checkpw(passwort, hash_db):
    print("OK")
else:
    print("Nicht OK")
```

> [!important] **Kernregel**
> `.encode('utf-8')` wandelt `str` in `bytes` um – bcrypt braucht Bytes. `checkpw()` vergleicht intern; der Hash wird nie entschlüsselt (Einwegfunktion).

## REST API – requests (HTTP-Client)

```python
import requests, json

# GET-Request an eine API
url = 'https://api.awattar.de/v1/marketdata'
response = requests.get(url)
jsondata = response.json()          # JSON → Python-Dictionary

for price in jsondata['data']:
    print(price['marketprice'])

# Mit Header / Auth-Key
headers = {'Authorization': 'Bearer MEIN_API_KEY'}
response = requests.get(url, headers=headers)
```

## REST API – Flask (HTTP-Server)

```python
from flask import Flask

app = Flask(__name__)

@app.route("/")                         # GET, Standard
def startseite():
    return "Startseite"

@app.get("/produkte")                   # nur GET
def produktseite():
    return "Produktseite"

@app.route("/test", methods=['POST', 'GET'])   # GET und POST
def produkt():
    return "Hallo"

if __name__ == '__main__':
    app.run(port=5001)
```

**HTTP-Methoden:**

| Methode | Bedeutung | Verwendung |
|---|---|---|
| `GET` | Daten abrufen | Seite laden, API-Abfrage |
| `POST` | Daten senden | Formular absenden, Datensatz anlegen |
| `PUT` | Daten ersetzen | kompletten Datensatz aktualisieren |
| `DELETE` | Daten löschen | Datensatz entfernen |

## JSON in Python verarbeiten

```python
import json, requests

response = requests.get('https://api.example.com/data')
data = response.json()       # direkt als dict

# oder aus Datei:
with open('zeiterfassung.json', 'r') as f:
    data = json.load(f)

print(data['UserAccount']['vorname'])           # "Max"
eintraege = data['UserAccount']['LogEntries']['LogEntry']
for eintrag in eintraege:
    print(eintrag['Type'], eintrag['Time'])
```

## IDEs

| IDE | Stärke |
|---|---|
| **IDLE** | Standard-Python-IDE, kommt mit Python |
| **VS Code** | professionell, Erweiterungen, Debugger |
| **Thonny** | einfach, gut für Einsteiger, REPL |
| **PyCharm** | vollständige Python-IDE, Refactoring |

> [!tip] **Merksatz**
> Python-Grundstruktur: **Einrückung** statt `{}`. **Keine Semikolons**. **Dynamische Typisierung** – der Typ wird beim Zuweisen festgelegt, nicht deklariert. Alles ist ein Objekt.

## Pseudocode

Pseudocode ist sprachunabhängige, strukturierte Beschreibung eines Algorithmus – keine echte Syntax, aber klare Logik. In der Prüfung oft als Alternative zu Python akzeptiert.

**Konventionen (prüfungsüblich):**

```
PROGRAMM PruefePasswort(sPass: String): String

    WENN Länge(sPass) < 10 DANN
        RÜCKGABE "Entspricht nicht den Anforderungen"
    ENDE WENN

    hatZahl      ← FALSCH
    hatBuchstabe ← FALSCH
    hatSonderz   ← FALSCH

    FÜR JEDES Zeichen z IN sPass:
        WENN z ist Ziffer DANN hatZahl ← WAHR
        WENN z ist Buchstabe DANN hatBuchstabe ← WAHR
        WENN z ist Sonderzeichen DANN hatSonderz ← WAHR
    ENDE FÜR

    WENN hatZahl UND hatBuchstabe UND hatSonderz DANN
        RÜCKGABE "Das Passwort erfüllt die Anforderungen"
    SONST
        RÜCKGABE "Entspricht nicht den Anforderungen"
    ENDE WENN

ENDE PROGRAMM
```

**Typische Schlüsselwörter:**

| Konstrukt | Pseudocode | Python |
|---|---|---|
| Zuweisung | `x ← 5` | `x = 5` |
| Bedingung | `WENN ... DANN ... SONST ... ENDE WENN` | `if ... : else:` |
| Zählschleife | `FÜR i VON 1 BIS 10` | `for i in range(1, 11):` |
| Kopfgesteuerte Schleife | `SOLANGE Bedingung` | `while Bedingung:` |
| Ausgabe | `AUSGABE "Text"` | `print("Text")` |
| Eingabe | `EINGABE variable` | `variable = input()` |
| Funktion | `FUNKTION Name(Parameter): Typ` | `def Name(parameter):` |
| Rückgabe | `RÜCKGABE wert` | `return wert` |

> [!tip] **Merksatz**
> Pseudocode ist kein Standard – solange Logik klar ist und Kontrollstrukturen (Schleifen, Bedingungen, Rückgaben) korrekt sind, werden Punkte vergeben. Sauber einrücken!

## Struktogramm (Nassi-Shneiderman-Diagramm)

Struktogramme visualisieren Algorithmen grafisch ohne Sprungsymbole (kein `goto`). Werden in Prüfungen als Vorlage ausgeteilt oder müssen ergänzt werden.

**Bausteine:**

```
┌─────────────────────────────┐
│ Anweisung / Prozess         │   ← einfache Aktion (Rechteck)
└─────────────────────────────┘

┌─────────────────────────────┐
│ Bedingung erfüllt?          │   ← Entscheidung (Dreieck teilt Rechteck)
├──────────────┬──────────────┤
│  JA          │  NEIN        │
│  ...         │  ...         │
└──────────────┴──────────────┘

┌─────────────────────────────┐
│ SOLANGE Bedingung           │   ← Schleife mit Bedingung oben
│ ┌───────────────────────┐   │
│ │ Schleifenkörper       │   │
│ └───────────────────────┘   │
└─────────────────────────────┘
```

**Beispiel: Passwortprüfung (Sommer 2024 Aufgabe 3)**

```
┌────────────────────────────────────────────────┐
│ FUNKTION PruefePasswort(sPass)                  │
├────────────────────────────────────────────────┤
│ hatZahl ← Falsch; hatBuchstabe ← Falsch        │
│ hatSonderz ← Falsch                            │
├────────────────────────────────────────────────┤
│ SOLANGE i < Länge(sPass)                       │
│ ┌──────────────────────────────────────────┐   │
│ │ z ← sPass[i]                            │   │
│ ├────────────────┬─────────────────────────┤   │
│ │ z ist Ziffer?  │ NEIN: weiter            │   │
│ │ hatZahl←Wahr   │                         │   │
│ ├────────────────┴─────────────────────────┤   │
│ │ ... (analog für Buchstabe, Sonderz.)    │   │
│ │ i ← i + 1                               │   │
│ └──────────────────────────────────────────┘   │
├────────────────────────────────────────────────┤
│ Länge(sPass)≥10 UND hatZahl UND hatBuchstabe  │
│ UND hatSonderz?                                │
├──────────────────────┬─────────────────────────┤
│ JA                   │ NEIN                    │
│ RÜCKGABE             │ RÜCKGABE                │
│ "...erfüllt..."      │ "...nicht..."           │
└──────────────────────┴─────────────────────────┘
```

> [!warning] **Achtung Prüfung**
> In der AP2 wird oft ein **unvollständiges Struktogramm** zur Ergänzung vorgelegt (Anlage). Fehlende Bedingungen, Zuweisungen oder Rückgabewerte eintragen. Die Struktur (Verschachtelung) darf nicht verändert werden.
