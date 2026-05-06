Relationale Datenbanken (MySQL/MariaDB) speichern Daten in Tabellen mit festem Schema und Beziehungen. NoSQL-Datenbanken (z.B. MongoDB) verzichten auf feste Schemata und sind für flexible oder dokumentenbasierte Daten geeignet.

## Datenbankdesign: ER-Modell → Relationales Modell

Vor dem SQL-Code kommt das Design: Ein **Entity-Relationship-Modell (ER-Modell)** beschreibt die Struktur, ein **relationales Modell** überführt diese in Tabellen.

### ER-Modell – Grundbegriffe

| Begriff | Bedeutung | Beispiel |
|---|---|---|
| **Entität** | Objekt der realen Welt | Kasse, Verkaufsraum, Person |
| **Attribut** | Eigenschaft einer Entität | KassenNr, PIN, Betrag |
| **Primärschlüssel (PK)** | Minimale Kombination von Attributen, die einen Datensatz eindeutig identifiziert | KassenNr |
| **Fremdschlüssel (FK)** | Attribut, das auf den PK einer anderen Tabelle verweist | RaumNr in Kasse |
| **Beziehung** | Verbindung zwischen Entitäten | Kasse *steht in* Verkaufsraum |

### Beziehungstypen

| Typ | Bedeutung | Beispiel | Lösung im Relationalmodell |
|---|---|---|---|
| **1:1** | Ein Datensatz ↔ genau ein anderer | Person ↔ Ausweis | FK in einer der beiden Tabellen |
| **1:n** | Ein Datensatz ↔ mehrere andere | Raum ↔ Kassen | FK auf der **n-Seite** (Kasse bekommt RaumNr) |
| **m:n** | Viele ↔ viele | Personal ↔ Verkaufsräume | **Zwischentabelle** mit beiden FKs |

> [!important] **Kernregel**
> Bei **1:n**: FK kommt immer in die Tabelle auf der **n-Seite**. Bei **m:n**: immer eine Zwischentabelle anlegen – der PK ist meist die Kombination der beiden FKs.

### Relationenschreibweise

```text
Tabellenname(Attribut1, Attribut2, Attribut3)
              ─────────                           ← Primärschlüssel: unterstrichen
                                    ─────────    ← Fremdschlüssel: gestrichelt unterstrichen
```

**Konvention in der Prüfung:** PK wird unterstrichen, FK durch Kennzeichnung (z.B. kursiv oder Fußnote) markiert. Beide können zusammenfallen (zusammengesetzter PK mit FK-Anteil).

### Beispiel: Winter 2024/25 – Markthalle

**Aufgabe:** Verkaufsräume haben mehrere Kassen. Personal arbeitet in mehreren Räumen (m:n).

```text
Verkaufsraum(RaumNr, Bezeichnung)
Kasse(KassenNr, PIN, Betrag, RaumNr)          ← RaumNr ist FK → Verkaufsraum
Personal(PersonalNr, Name)
PersonalRaum(RaumNr, PersonalNr)              ← Zwischentabelle für m:n
              ───────  ──────────               beide zusammen = PK, beide = FK
```

> [!tip] **Merksatz**
> **m:n → immer Zwischentabelle**. Der PK der Zwischentabelle ist die Kombination der beiden FKs (sofern ein Paar eindeutig ist). Wenn nicht eindeutig: eigenen Surrogatschlüssel ergänzen.

> [!warning] **Achtung Falle**
> Der PK ist die **minimale** Kombination – nicht einfach alle Felder. Wenn `KassenNr` allein eindeutig ist, reicht sie als PK – `RaumNr` gehört dann nicht dazu (wäre dann nur FK).

### Überführungsregeln: ER → Relational (Kurzfassung)

1. Jede Entität wird eine Tabelle mit ihren Attributen
2. PK der Entität wird PK der Tabelle
3. **1:n-Beziehung**: PK der 1-Seite als FK in die n-Seite
4. **m:n-Beziehung**: neue Zwischentabelle mit FKs beider Seiten
5. **1:1-Beziehung**: FK in eine der Tabellen (meist die abhängigere)

---

## SQL – DDL (Data Definition Language)

DDL definiert Struktur: Datenbanken und Tabellen anlegen, ändern, löschen.

```sql
CREATE DATABASE firma;
USE firma;
DROP DATABASE firma;
```

### Tabelle erstellen

```sql
CREATE TABLE Artikel (
    ArtikelNr     INT(6)        AUTO_INCREMENT,
    Bezeichnung   VARCHAR(20)   NOT NULL UNIQUE BINARY,
    Einkaufspreis DOUBLE(6,2),
    Verkaufspreis DOUBLE(6,2),
    PRIMARY KEY (ArtikelNr)
);

-- Mit Fremdschlüssel
CREATE TABLE Bestellpositionen (
    BestellNr  INT(6),
    ArtikelNr  INT(6),
    Anzahl     INT(3),
    PRIMARY KEY (BestellNr, ArtikelNr),
    FOREIGN KEY (BestellNr) REFERENCES Bestellungen(BestellNr),
    FOREIGN KEY (ArtikelNr) REFERENCES Artikel(ArtikelNr)
);
```

**Wichtige Datentypen:**

| Typ | Beschreibung |
|---|---|
| `INT(n)` | Ganzzahl |
| `DOUBLE(g,d)` | Fließkomma, g Stellen gesamt, d Nachkomma |
| `VARCHAR(n)` | variabler String bis n Zeichen |
| `CHAR(n)` | fixer String, immer n Zeichen |
| `DATE` | Datum (YYYY-MM-DD) |

**Constraints:**

| Constraint | Bedeutung |
|---|---|
| `PRIMARY KEY` | eindeutiger Datensatz-Identifikator |
| `FOREIGN KEY ... REFERENCES` | Fremdschlüssel, verweist auf andere Tabelle |
| `AUTO_INCREMENT` | Wert wird automatisch hochgezählt |
| `NOT NULL` | Feld darf nicht leer sein |
| `UNIQUE` | Wert darf nur einmal vorkommen |
| `BINARY` | Groß-/Kleinschreibung wird beachtet |

### Tabellenstruktur ändern (ALTER TABLE)

```sql
ALTER TABLE personen RENAME person;                       -- Tabelle umbenennen
ALTER TABLE personen CHANGE vorname name CHAR(25);        -- Feld umbenennen/Typ ändern
ALTER TABLE personen ADD gehalt DOUBLE;                   -- Feld hinzufügen
ALTER TABLE personen DROP gehalt;                         -- Feld löschen
```

## SQL – DML (Data Manipulation Language)

DML liest und schreibt Daten.

### SELECT

```sql
SELECT * FROM festplatten;
SELECT hersteller, typ, preis FROM festplatten WHERE gb > 250;
SELECT * FROM festplatten WHERE typ LIKE 'Fire%';        -- LIKE mit Wildcard %
SELECT * FROM festplatten WHERE gb > 250 AND preis < 85 ORDER BY gb DESC;

-- Aggregatfunktionen
SELECT COUNT(*), AVG(preis), MAX(preis), MIN(preis) FROM festplatten;
SELECT hersteller, COUNT(*) FROM festplatten GROUP BY hersteller;
```

### INSERT

```sql
-- Alle Felder (Reihenfolge beachten)
INSERT INTO festplatten VALUES ('WD', 'WD Blue', 250, 49.00, 'HD-022', '2024-03-19');

-- Bestimmte Felder (Reihenfolge egal)
INSERT INTO personen (name, p_nr) VALUES ('Schmitz', 355690);

-- Aus anderer Tabelle übernehmen
INSERT INTO Kontakte SELECT * FROM MeineKontakte;
```

### UPDATE

```sql
UPDATE personen SET gehalt = 3000 WHERE name = 'Müller' AND vorname = 'Tim';
UPDATE personen SET gehalt = gehalt * 1.1 WHERE name = 'Müller';  -- 10% Erhöhung
UPDATE personen SET gehalt = 3000, name = 'Mueller' WHERE name = 'Müller';

-- Mit Datumsfunktion
UPDATE bestellung SET
    LIEFERDATUM  = DATE_ADD(LIEFERDATUM, INTERVAL 4 YEAR),
    BESTELLDATUM = DATE_ADD(BESTELLDATUM, INTERVAL 4 YEAR);
```

### DELETE

```sql
DELETE FROM personen WHERE gehalt IS NULL OR gehalt = '';
DELETE FROM personen WHERE gehalt = 500;
```

### UNION

```sql
-- Ergebnisse zweier SELECT-Abfragen zusammenführen (gleiche Spaltenzahl!)
SELECT * FROM personen UNION SELECT * FROM hobby;
```

### JOIN – Tabellen verknüpfen

JOINs kombinieren Spalten aus mehreren Tabellen über eine gemeinsame Bedingung (meist FK = PK).

```sql
-- INNER JOIN: nur Zeilen, die in BEIDEN Tabellen einen Partner haben
SELECT k.KassenNr, k.Betrag, v.Bezeichnung
FROM Kasse k
INNER JOIN Verkaufsraum v ON k.RaumNr = v.RaumNr;

-- LEFT JOIN: alle Zeilen aus der linken Tabelle, NULLs wenn kein Partner rechts
SELECT p.Name, pr.RaumNr
FROM Personal p
LEFT JOIN PersonalRaum pr ON p.PersonalNr = pr.PersonalNr;

-- JOIN mit WHERE kombinieren
SELECT p.Name, v.Bezeichnung
FROM Personal p
INNER JOIN PersonalRaum pr ON p.PersonalNr = pr.PersonalNr
INNER JOIN Verkaufsraum v  ON pr.RaumNr    = v.RaumNr
WHERE v.RaumNr = 2;
```

**JOIN-Typen im Überblick:**

| Typ | Liefert |
|---|---|
| `INNER JOIN` | Nur Zeilen mit Treffer in **beiden** Tabellen |
| `LEFT JOIN` | **Alle** Zeilen links + Treffer rechts (NULL wenn kein Treffer) |
| `RIGHT JOIN` | **Alle** Zeilen rechts + Treffer links (selten verwendet) |

> [!tip] **Merksatz**
> `INNER JOIN` = Schnittmenge. `LEFT JOIN` = linke Tabelle vollständig + was passt von rechts. Tabellenaliase (`k`, `v`) vermeiden lange Tipparbeit.

> [!warning] **Achtung Falle**
> Bei JOIN-Abfragen immer **Tabellen-Alias.Spaltenname** schreiben wenn Spaltennamen in mehreren Tabellen vorkommen (`k.RaumNr` statt nur `RaumNr`) – sonst: *ambiguous column name*.

## Transaktionen

Mehrere SQL-Befehle werden als Einheit ausgeführt – entweder alle oder keiner.

```sql
BEGIN;
INSERT INTO hersteller VALUES (NULL, 'IBM', 'Boston');
INSERT INTO hersteller VALUES (NULL, 'WD', 'London');
COMMIT;   -- erst jetzt werden Änderungen gespeichert
```

> [!important] **Kernregel**
> Ohne `COMMIT` werden Änderungen nicht dauerhaft gespeichert. `ROLLBACK` macht alle Änderungen seit `BEGIN` rückgängig.

## Zugriffsrechte / Privilegien

| Privileg | Beschreibung |
|---|---|
| `ALL` | alle Rechte |
| `SELECT` | Daten lesen |
| `INSERT` | Datensätze einfügen |
| `UPDATE` | Datensätze ändern |
| `DELETE` | Datensätze löschen |
| `CREATE` | Datenbanken/Tabellen anlegen |
| `DROP` | Datenbanken/Tabellen löschen |
| `ALTER` | Tabellenstruktur ändern |
| `GRANT` | Rechte anderer MySQL-Konten ändern |
| `SHUTDOWN` | MySQL-Server herunterfahren |

## Python + MySQL (mysql.connector)

```python
import mysql.connector

# Verbindung aufbauen
try:
    connection = mysql.connector.connect(
        host="localhost", user="root", passwd="", db="shop"
    )
except:
    print("Keine Verbindung zum Server")

cursor = connection.cursor()

# Daten lesen
cursor.execute("SELECT * FROM artikel")
result = cursor.fetchall()
for datensatz in result:
    for feld in datensatz:
        print(feld, end=" ")
    print()

# Daten einfügen (parameterisiert – verhindert SQL Injection)
name = input("Name: ")
geb  = input("Geburtsdatum: ")
pw   = input("Passwort: ")
cursor.execute("INSERT INTO Kunden VALUES (NULL, %s, %s, %s);", [name, geb, pw])
connection.commit()   # Änderungen speichern!

cursor.close()
connection.close()
```

> [!warning] **Achtung Falle**
> Niemals Benutzereingaben direkt in SQL-Strings einbauen (`f"... WHERE name='{name}'"`) – das ermöglicht **SQL Injection**. Immer **Parameterized Queries** mit `%s` verwenden.

## Passwort-Hashing mit bcrypt

Passwörter werden **nie im Klartext** gespeichert – immer als Hash.

```python
import mysql.connector, bcrypt

# Hash prüfen beim Login
name     = input("Name: ")
passwort = input("Passwort: ").encode('utf-8')   # als Bytes kodieren

cursor.execute("SELECT Passwort FROM kunden WHERE Name = %s;", (name,))
hash_aus_db = (cursor.fetchone()[0]).encode('utf-8')

if bcrypt.checkpw(passwort, hash_aus_db):
    print("Login OK")
else:
    print("Falsches Passwort")
```

> [!important] **Kernregel**
> `bcrypt.checkpw(eingabe_bytes, hash_bytes)` – beide Argumente müssen `bytes` sein (`.encode('utf-8')`). Bcrypt vergleicht intern – der Hash selbst wird nie entschlüsselt.

## NoSQL – MongoDB (pymongo)

MongoDB speichert Daten als **JSON-ähnliche Dokumente** (BSON) ohne festes Schema.

```python
from pymongo import MongoClient

client = MongoClient('mongodb://localhost:27017')
db = client.artikel   # Datenbank "artikel" auswählen (wird auto-erstellt)

pricelist = [
    {'ProductID': 1, 'Name': 'Laptop',  'preis': 250.50},
    {'ProductID': 2, 'Name': 'TV',      'preis': 400.70},
    {'ProductID': 3, 'Name': 'Router',  'preis': 20.70},
]

db.products.insert_many(pricelist)    # Collection "products", viele Dokumente
print(client.list_database_names())   # alle Datenbanken auflisten
```

**Konzepte:**
- **Database** ↔ Datenbank in SQL
- **Collection** ↔ Tabelle in SQL
- **Document** ↔ Datensatz/Zeile in SQL (JSON-Objekt, flexibles Schema)
- **`_id`** wird automatisch vergeben (ObjectId)

## Datenformate: JSON & XML

Beide Formate transportieren strukturierte Daten – z.B. von Sensoren oder APIs.

### JSON (JavaScript Object Notation)

```json
{
  "UserAccount": {
    "persid": 34,
    "vorname": "Max",
    "LogEntries": {
      "LogEntry": [
        {"id": 23, "Type": "kommt",  "Time": "2022-12-02 07:12:22"},
        {"id": 25, "Type": "geht",   "Time": "2022-12-02 18:37:22"}
      ]
    }
  }
}
```

### XML (Extensible Markup Language)

```xml
<UserAccount>
  <persid>34</persid>
  <vorname>Max</vorname>
  <LogEntries>
    <LogEntry>
      <id>23</id>
      <Type>kommt</Type>
      <Time>2022-12-02 07:12:22</Time>
    </LogEntry>
  </LogEntries>
</UserAccount>
```

### JSON vs. XML

| | JSON | XML |
|---|---|---|
| Syntax | `{}`, `[]`, `""` | Tags `<tag>...</tag>` |
| Lesbarkeit | kompakter | ausführlicher |
| Datentypen | nativ (int, bool, array) | alles Strings (ohne Schema) |
| Verbreitung | REST APIs, IoT, NoSQL | Legacy, Konfiguration, SOAP |
| Python | `import json` | `import xml.etree.ElementTree` |

> [!tip] **Merksatz**
> **JSON = moderner, kompakter, Standard für REST APIs und IoT**. **XML = älter, ausführlicher, noch in Enterprise-/Legacy-Systemen**. Inhaltlich gleichwertig – nur andere Syntax.
