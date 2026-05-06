HTML (HyperText Markup Language) strukturiert Webinhalte. CSS (Cascading Style Sheets) gestaltet sie. In der Prüfung relevant: Formularerstellung, GET vs. POST, Responsive Design mit Media Queries und Fehlersuche in HTML-Code.

## HTML – Grundstruktur

```html
<!DOCTYPE html>
<html lang="de">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Seitentitel</title>
    <link rel="stylesheet" href="style.css">
</head>
<body>
    <h1>Überschrift</h1>
    <p>Absatz</p>
</body>
</html>
```

**Wichtige Tags:**

| Tag | Bedeutung |
|---|---|
| `<h1>` – `<h6>` | Überschriften (h1 = größte) |
| `<p>` | Absatz |
| `<a href="url">` | Hyperlink |
| `<img src="..." alt="...">` | Bild |
| `<div>` | Block-Container (kein semantischer Inhalt) |
| `<span>` | Inline-Container |
| `<table>`, `<tr>`, `<td>` | Tabelle, Zeile, Zelle |
| `<ul>`, `<ol>`, `<li>` | Unsortierte / sortierte Liste |

## HTML-Formulare

Formulare übertragen Benutzereingaben an einen Server.

```html
<form action="registry.php" method="post">

    <!-- Texteingabe -->
    <label for="name">Name:</label>
    <input type="text" id="name" name="name" required>

    <!-- E-Mail -->
    <input type="email" name="email" placeholder="max@beispiel.de">

    <!-- Passwort -->
    <input type="password" name="passwort" minlength="8">

    <!-- Zahl -->
    <input type="number" name="alter" min="0" max="120">

    <!-- Datum -->
    <input type="date" name="geburtsdatum">

    <!-- Checkbox -->
    <input type="checkbox" id="agb" name="agb" value="ja">
    <label for="agb">AGB akzeptieren</label>

    <!-- Radio-Buttons (name gleich = Gruppe, nur eines wählbar) -->
    <input type="radio" id="privat"  name="kundentyp" value="p"> Privat
    <input type="radio" id="bildung" name="kundentyp" value="b"> Bildungseinrichtung

    <!-- Dropdown -->
    <select name="land">
        <option value="de">Deutschland</option>
        <option value="at">Österreich</option>
    </select>

    <!-- Mehrzeiliges Textfeld -->
    <textarea name="nachricht" rows="4" cols="40"></textarea>

    <!-- Absenden -->
    <button type="submit">Absenden</button>

</form>
```

> [!warning] **Achtung Falle**
> Der Submit-Button muss **innerhalb** des `<form>`-Tags stehen – sonst wird beim Klick nichts übertragen. Häufiger Prüfungsfehler: `</form>` vor dem Button-Tag.

**Wichtige `<input>`-Typen:**

| type | Eingabe |
|---|---|
| `text` | freier Text |
| `email` | E-Mail-Adresse (Browser validiert Format) |
| `password` | verdeckte Eingabe |
| `number` | Zahl (mit min/max) |
| `date` | Datum-Picker |
| `checkbox` | Mehrfachauswahl |
| `radio` | Einfachauswahl (alle mit gleichem `name`) |
| `hidden` | unsichtbarer Wert (z.B. Session-Token) |
| `submit` | Absendeknopf |

## GET vs. POST

| | GET | POST |
|---|---|---|
| Daten werden | an URL angehängt (`?name=Max&alter=30`) | im Request-Body übertragen |
| Sichtbarkeit | in der Adresszeile sichtbar | nicht sichtbar |
| Länge | max. ~2000 Zeichen (URL-Limit) | unbegrenzt |
| Sicherheit | unsicher (Passwörter/persönliche Daten nie mit GET!) | sicherer |
| Verwendung | Suchanfragen, Filter | Login, Registrierung, Formulare mit sensiblen Daten |
| Caching | Browser kann cachen | nicht gecacht |

> [!important] **Kernregel**
> Passwörter, persönliche Daten, sensible Formulardaten → **immer POST**. GET nur für harmlose Abfragen (z.B. Suche). Bei HTTPS ist beides verschlüsselt – aber GET-Parameter stehen trotzdem im Browser-Verlauf und Server-Logs.

## PHP – Formulardaten verarbeiten

PHP läuft serverseitig und verarbeitet die übertragenen Formulardaten.

```php
<?php
// POST-Daten empfangen
$name  = $_POST['name'];
$email = $_POST['email'];

// GET-Daten empfangen
$suche = $_GET['q'];

// Einfache Ausgabe
echo "Hallo, " . $name;

// Eingabe bereinigen (verhindert XSS)
$name = htmlspecialchars($_POST['name']);
?>
```

> [!warning] **Achtung Falle**
> Niemals `$_POST['name']` direkt in HTML ausgeben – das ermöglicht **XSS (Cross-Site-Scripting)**. Immer `htmlspecialchars()` verwenden, um `<`, `>`, `"` etc. zu escapen.

## CSS – Grundlagen

```css
/* Selektor: Element */
p {
    color: red;
    font-size: 16px;
}

/* Selektor: Klasse */
.meine-klasse {
    background-color: lightblue;
}

/* Selektor: ID */
#mein-element {
    margin: 10px;
    padding: 5px;
}
```

**Box-Modell:** Jedes HTML-Element hat `content` → `padding` → `border` → `margin`.

## Responsive Design – Media Queries

Media Queries passen das Layout an die Bildschirmbreite (Gerät) an. In der Prüfung häufig: verschiedene Hintergrundfarben oder Layouts je nach Gerät.

```css
/* Standard (Desktop) */
body {
    background-color: lightgreen;
}

/* Tablet (max. 768px) */
@media (max-width: 768px) {
    body {
        background-color: lightblue;
    }
}

/* Smartphone (max. 480px) */
@media (max-width: 480px) {
    body {
        background-color: yellow;
    }
}
```

**Typische Breakpoints:**

| Gerät | Bereich |
|---|---|
| Desktop | > 768px (kein @media nötig, ist Standard) |
| Tablet | ≤ 768px |
| Smartphone | ≤ 480px |

> [!tip] **Merksatz**
> **Mobile First**: CSS erst für kleine Screens, dann mit `min-width` für größere erweitern. **Desktop First**: Standard für Desktop, `max-width` für kleinere. In der Prüfung meist Desktop First.

> [!important] **Kernregel**
> `@media (max-width: 768px)` greift, wenn die Bildschirmbreite **kleiner oder gleich** 768px ist. Browser prüfen von oben nach unten – engeres `max-width` muss **nach** dem weiteren stehen, sonst wird es überschrieben.

## Häufige HTML-Fehler in der Prüfung

In der AP2 wird oft fehlerhafter HTML-Code zur Analyse vorgelegt:

| Fehler | Symptom | Lösung |
|---|---|---|
| Submit-Button außerhalb `<form>` | Klick sendet nichts | Button vor `</form>` verschieben |
| Fehlende `name`-Attribute bei `<input>` | Feld wird nicht übertragen | `name="feldname"` ergänzen |
| Falsches `method`-Attribut | Daten in URL sichtbar | `method="post"` setzen |
| Radio-Buttons verschiedene `name` | mehrere gleichzeitig wählbar | alle gleiche `name` vergeben |
| `action` zeigt auf falsche Datei | Formular landet falsch | `action="korrekte-datei.php"` |
| Tag nicht geschlossen | Folgeinhalte falsch dargestellt | schließendes Tag ergänzen |

> [!warning] **Achtung Prüfung**
> Wenn nach dem Fehler gefragt wird: Zeile nennen + **was** falsch ist + **wie** die Korrektur aussieht. Reihenfolge von Elementen im Quellcode hat direkte Auswirkung auf Funktionalität.
