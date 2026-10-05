# Cocokalo Anime Plattform

![Cocokalo Logo](public/images/project-logo.png)

#### Beschreibung
Eine Open-Source-Anime-Video-Plattform mit UI-Design inspiriert von Bilibili. Nur für Lern- und Forschungszwecke.

#### Urheberrechtshinweis
1. Dies ist ein Open-Source-Lernprojekt ohne kommerzielle Zwecke
2. Die Verwendung von Cocokalo für nicht-akademische Aktivitäten ist strengstens untersagt
3. Alle in diesem Projekt verwendeten Anime-Inhalte gehören ihren ursprünglichen Schöpfern
4. Verwenden Sie dieses Projekt nicht zur Speicherung oder Verbreitung nicht autorisierter urheberrechtlich geschützter Inhalte

#### Lizenz
Dieses Projekt ist unter der GNU Affero General Public License 3.0 lizenziert, siehe [LICENSE](LICENSE) für vollständige Bedingungen. Unter dieser Lizenz:
1. Jede Nutzung, Änderung oder Verteilung muss dieser Lizenz entsprechen
2. Bei der Bereitstellung von Dienstleistungen über das Netzwerk muss der Quellcode verfügbar gemacht werden
3. Alle Änderungen müssen deutlich gekennzeichnet und die ursprünglichen Urheberrechtshinweise beibehalten werden
4. Der Projektname darf nicht ohne Erlaubnis für Werbezwecke verwendet werden

## Technologie-Stack
- Backend-Framework: Express 4.x
- Template-Engine: EJS 3.x
- Datenbank: MongoDB >=7.x
- Paketmanager: npm 9.x

## Schnellstart
```bash
# Abhängigkeiten installieren
npm install

# Im Entwicklungsmodus starten (mit Hot Reload)
npm run dev

# Im Produktionsmodus starten
npm start

# Datenbank initialisieren
node scripts/initDB.js
```

## Projektstruktur (MVC-Architektur)
```
├── app.js         # Anwendungseinstieg
├── models/        # Datenmodelle (M)
├── routes/        # Routencontroller (C)
├── views/         # Ansichtsvorlagen (V)
├── public/        # Statische Ressourcen
├── scripts/       # Datenbank-Migrationsskripte
└── config/        # Umgebungskonfiguration
```

## Umgebungskonfiguration
.env-Datei erstellen:
```ini
PORT=3000
MONGODB_URI=mongodb://localhost:27017/cocokalo
SESSION_SECRET=your_session_secret
```

## API-Dokumentation
Besuchen Sie `/api-docs` für die Swagger-Dokumentation