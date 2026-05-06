## Aufbau

- **128 Bit**, aufgeteilt in **8 Hextets** (je 16 Bit) – durch `:` getrennt
- Hexadezimal (`0`–`9`, `a`–`f`)
- Standard-Präfix für Hosts: **/64** (Netzanteil = 64 Bit, Interface-Identifier = 64 Bit)

```text
2001:0db8:1234:0001:0000:0000:0000:0001 / 64
└─── Netzanteil (64 Bit) ──┘└─── Interface-ID ──┘
```

## Komprimierungsregeln

1. **Führende Nullen** im Hextet weglassen: `0db8` → `db8`
2. **Längste zusammenhängende Folge von `:0:`-Hextets** durch `::` ersetzen (nur **einmal** pro Adresse!)

```text
2001:0db8:1234:0001:0000:0000:0000:0001
→ 2001:db8:1234:1::1
```

| Voll | Kompakt |
|---|---|
| `2001:0db8:0000:0000:0000:0000:0000:0001` | `2001:db8::1` |
| `fe80:0000:0000:0000:ea62:06ff:fe01:0045` | `fe80::ea62:6ff:fe01:45` |
| `0000:0000:0000:0000:0000:0000:0000:0001` | `::1` |

## Wichtige Präfixe (Wiederholung Adresstypen)

| Präfix | Typ |
|---|---|
| `2000::/3` | Global Unicast |
| `fc00::/7` | Unique Local |
| `fe80::/10` | Link-Local |
| `ff00::/8` | Multicast |
| `::1/128` | Loopback |

## Interface-Identifier (EUI-64)

Aus einer 48-Bit MAC-Adresse einen 64-Bit Interface-Identifier ableiten:

1. MAC in 2 Teile splitten: `xx:xx:xx | yy:yy:yy`
2. In der Mitte `ff:fe` einfügen: `xx:xx:xx:ff:fe:yy:yy:yy`
3. **U/L-Bit (7. Bit des 1. Bytes) invertieren** (Universal/Local)

**Beispiel:** MAC `ea-62-06-01-00-45`
1. → `ea:62:06 | 01:00:45`
2. → `ea:62:06:ff:fe:01:00:45`
3. U/L-Bit invertieren: `ea` = `1110 1010` → 7. Bit flippen → `1110 1000` = `e8`
4. Ergebnis: `e862:06ff:fe01:0045`
5. Mit Link-Local-Präfix: `fe80::e862:6ff:fe01:45/64`

> [!warning] **Achtung – U/L-Bit wird invertiert!**
> Ist das 2. Hex-Zeichen der MAC `2/3/6/7/a/b/e/f`, ändert es sich. Aus `ea` wird `e8`, aus `02` wird `00` etc. Heute zunehmend durch **Privacy Extensions** (zufällige IID) ersetzt.

## Beispiel: Adresse interpretieren

```text
2001:db8:1234::1/64
```
- Präfix: `2001:db8:1234:0000` (64 Bit) – Global Unicast
- Interface-ID: `::1`

```text
fe80::ea62:6ff:fe01:45/64
```
- Link-Local
- Interface-ID basiert auf MAC `ea-62-06-01-00-45` (mit ff:fe in der Mitte und invertiertem U/L-Bit)
