Das BSI (Bundesamt für Sicherheit in der Informationstechnik) stellt mit dem **IT-Grundschutz** einen Rahmen zur systematischen Absicherung von IT-Systemen bereit.

## Schutzziele (CIA-Triade + Erweiterungen)

| Schutzziel | Kürzel | Definition | Verletzung durch |
|---|---|---|---|
| Vertraulichkeit | C (Confidentiality) | Nur Berechtigte haben Zugriff | Datenleck, Abhören, Diebstahl |
| Integrität | I (Integrity) | Daten sind vollständig & unverändert | Manipulation, Übertragungsfehler |
| Verfügbarkeit | A (Availability) | Systeme sind erreichbar wenn gebraucht | DoS, Hardware-Ausfall, Feuer |
| Authentizität | – | Identität ist verifiziert | Spoofing, Phishing |
| Verbindlichkeit | – | Handlungen sind nachweisbar | Logs fehlen, Signatur fehlt |

> [!tip] **Merksatz**
> **CIA = Confidentiality, Integrity, Availability** – die drei Säulen der IT-Sicherheit. Jede Bedrohung lässt sich einem oder mehreren Schutzzielen zuordnen.

## BSI IT-Grundschutz

Methodischer Rahmen zur Risikoanalyse und Absicherung:

```text
1. Strukturanalyse        → Was haben wir? (Systeme, Netze, Prozesse)
2. Schutzbedarfsfeststellung → Wie kritisch ist es? (Normal / Hoch / Sehr hoch)
3. Modellierung           → IT-Grundschutz-Bausteine zuordnen
4. Basis-Absicherung      → Mindestmaßnahmen aus Bausteinen umsetzen
5. Standard-Absicherung   → Vollständige Grundschutz-Maßnahmen
6. Kern-Absicherung       → Fokus auf kritische Assets
```

## Schutzbedarf

| Kategorie | Definition | Beispiel |
|---|---|---|
| **Normal** | Schäden begrenzt und überschaubar | Interne Dokumente |
| **Hoch** | Erhebliche Schäden möglich | Kundendaten, Personaldaten |
| **Sehr hoch** | Katastrophale Schäden möglich | Gesundheitsdaten, Kritische Infrastruktur |

Der **maximale Schutzbedarf** aller enthaltenen Informationen bestimmt den Schutzbedarf des Systems (**Maximumprinzip**).

## BSI Gefährdungskatalog

Das BSI kategorisiert Gefährdungen in:

| Kategorie | Kürzel | Beispiele |
|---|---|---|
| Höhere Gewalt | G 0.x | Brand, Überschwemmung, Blitzschlag |
| Organisatorische Mängel | G 0.x | Fehlende Richtlinien, unzureichende Schulung |
| Menschliche Fehlhandlungen | G 0.x | Fehlkonfiguration, versehentliches Löschen |
| Technisches Versagen | G 0.x | Hardware-Defekt, Softwarefehler |
| Vorsätzliche Handlungen | G 0.x | Hacking, Sabotage, Diebstahl |

## Risikoanalyse – Grundschema

```text
Risiko = Eintrittswahrscheinlichkeit × Schadenshöhe

Risikobewertung:
┌─────────────────┬──────────────┬──────────────┬──────────────┐
│                 │ gering       │ mittel       │ hoch         │
│ Schaden         │              │              │              │
├─────────────────┼──────────────┼──────────────┼──────────────┤
│ hoch            │ mittleres R. │ hohes R.     │ sehr hohes R.│
│ mittel          │ geringes R.  │ mittleres R. │ hohes R.     │
│ gering          │ sehr gering  │ geringes R.  │ mittleres R. │
└─────────────────┴──────────────┴──────────────┴──────────────┘
                    Eintrittswahrscheinlichkeit →
```

## Risikobehandlung

| Option | Beschreibung | Wann |
|---|---|---|
| **Risikovermeidung** | Aktivität einstellen / nicht durchführen | Risiko nicht tragbar |
| **Risikoreduktion** | Maßnahmen ergreifen (technisch/organisatorisch) | Standard-Fall |
| **Risikoübertragung** | Versicherung, Outsourcing | Finanzielle Risiken |
| **Risikoakzeptanz** | Bewusstes Tragen des Risikos | Kosten > Schaden |

## Technische vs. Organisatorische Maßnahmen

| Technisch | Organisatorisch |
|---|---|
| Firewall, IDS/IPS | Sicherheitsrichtlinien |
| Verschlüsselung | Schulungen & Awareness |
| Backup-Systeme | Notfallpläne (BCM) |
| Patch-Management | Zutrittskontrollen |
| Virenschutz | ISMS nach ISO 27001 |

> [!warning] **Achtung Falle**
> Das **Maximumprinzip**: Ein System, das normale und hochkritische Daten verarbeitet, bekommt den Schutzbedarf der kritischsten Daten – also **hoch**.

> [!important] **Kernregel**
> IT-Grundschutz = **Prozess, kein Produkt**. Einmalige Maßnahmen reichen nicht – Sicherheit muss kontinuierlich überprüft und angepasst werden (PDCA-Zyklus: Plan–Do–Check–Act).
