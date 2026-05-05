[[Netzwerkmanagement|zurück]]

---

# API & REST

Eine **API (Application Programming Interface)** ist eine definierte Schnittstelle, über die Software mit anderer Software kommuniziert. **REST (Representational State Transfer)** ist der dominierende Architekturstil für Web-APIs im Netzwerkmanagement (z.B. bei SDN-Controllern, Netzwerkgeräten mit REST-API).

## REST-Prinzipien

REST ist kein Protokoll, sondern ein Architekturstil mit 6 Constraints:

1. **Client-Server:** klare Trennung – Client initiiert, Server antwortet
2. **Stateless:** jede Anfrage enthält alle nötigen Infos – kein Session-State auf dem Server
3. **Cacheable:** Antworten können gecacht werden (Cache-Control Header)
4. **Uniform Interface:** einheitliche Schnittstelle (Ressourcen-URLs, HTTP-Methoden, Representationen)
5. **Layered System:** Client weiß nicht, ob er direkt mit dem Server oder einem Proxy kommuniziert
6. **Code on Demand (optional):** Server kann ausführbaren Code liefern

## HTTP-Methoden (CRUD-Mapping)

| HTTP-Methode | CRUD-Operation | Bedeutung | Idempotent? |
|-------------|----------------|-----------|-------------|
| GET | Read | Ressource lesen | ✅ |
| POST | Create | Neue Ressource anlegen | ❌ |
| PUT | Update (vollständig) | Ressource ersetzen | ✅ |
| PATCH | Update (partiell) | Teilupdate | ❌ |
| DELETE | Delete | Ressource löschen | ✅ |

> [!tip] **Merksatz**
> **GET P**utzt **P**atch **D**eleted → GET, POST, PUT, PATCH, DELETE

## HTTP-Statuscodes

| Code | Kategorie | Bedeutung |
|------|-----------|-----------|
| 200 | Success | OK |
| 201 | Success | Created (nach POST) |
| 204 | Success | No Content (nach DELETE) |
| 400 | Client Error | Bad Request |
| 401 | Client Error | Unauthorized (keine/falsche Auth) |
| 403 | Client Error | Forbidden (keine Berechtigung) |
| 404 | Client Error | Not Found |
| 500 | Server Error | Internal Server Error |

## REST API – Aufbau einer Anfrage

```text
GET https://api.example.com/v1/devices/switch1/interfaces
Authorization: Bearer <Token>
Accept: application/json
```

**URL-Struktur:** `https://<host>/api/v<Version>/<Ressource>/<ID>/<Sub-Ressource>`

**Antwort (JSON):**
```json
{
  "id": "eth0",
  "status": "up",
  "speed": 1000,
  "mtu": 1500
}
```

## Authentifizierung bei REST APIs

| Methode | Beschreibung | Sicherheit |
|---------|-------------|-----------|
| API Key | Statischer Schlüssel im Header/URL | mittel |
| Basic Auth | Base64(user:pass) im Header | schwach (nur über HTTPS) |
| Bearer Token / JWT | Token nach Login | gut |
| OAuth 2.0 | Delegierte Autorisierung | sehr gut |

## REST vs. SOAP vs. NETCONF

| | REST | SOAP | NETCONF |
|-|------|------|---------|
| Format | JSON/XML | XML | XML |
| Transport | HTTP/S | HTTP, SMTP | SSH (TCP 830) |
| Einsatz | Web-APIs, SDN | Enterprise-Services | Netzwerkgeräte |
| Standard | de-facto | W3C | RFC 6241 |

> [!important] **Kernregel**
> REST ist **zustandslos** – der Server speichert keinen Session-State. Jede Anfrage muss für sich allein verständlich sein (inkl. Auth-Token).

> [!warning] **Achtung Falle**
> **401 vs. 403:** 401 = nicht authentifiziert (wer bist du?), 403 = authentifiziert aber keine Berechtigung (du darfst nicht).
