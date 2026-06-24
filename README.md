# GitHub Actions – Workflows, Variablen und Secrets

## Übersicht

Dieses Repository enthält einen GitHub Actions Workflow,
der grundlegende Sysadmin-Aufgaben automatisiert und
den sicheren Umgang mit Variablen, Secrets und
umgebungsspezifischen Konfigurationen demonstriert.

---

## Workflow-Details

### Name des Workflows
`Sysadmin Basics Workflow`

### Datei
`.github/workflows/sysadmin-basics.yml`

---

## Auslöser (Trigger)

| Auslöser | Beschreibung |
|----------|--------------|
| `push` auf `main` | Workflow startet automatisch bei jedem Push |
| `workflow_dispatch` | Manueller Start über die GitHub Actions UI |

---

## Verwendete Variablen

### Workflow-Ebene (global verfügbar)

| Variable | Wert | Beschreibung |
|----------|------|--------------|
| `APP_NAME` | `CloudApp-DCI` | Name der Anwendung |
| `TARGET_ENV` | `development` | Zielumgebung |
| `REGION` | `eu-west-1` | Deployment-Region |

### Step-Ebene (nur im jeweiligen Step verfügbar)

| Variable | Wert | Beschreibung |
|----------|------|--------------|
| `DEPLOY_VERSION` | `v1.0.3` | Version der Anwendung |

---

## Verwendete Secrets

| Secret | Beschreibung |
|--------|--------------|
| `CLOUD_API_TOKEN` | API-Token für Cloud-Dienste (nur Testwert) |
| `SSH_TARGET` | Zieladresse für SSH-Verbindung (nur Testwert) |

> ⚠️ Secrets werden niemals im Log ausgegeben.
> Im Log erscheint nur: `vorhanden ✅` oder `fehlt ❌`

---

## Erzeugte Artefakte

| Artefakt | Enthaltene Dateien | Beschreibung |
|----------|--------------------|--------------|
| `workflow-reports` | `reports/system-report.txt` | Systeminformationen des Runners |
| `workflow-reports` | `reports/runtime-info.txt` | Laufzeitvariablen und Konfiguration |

---

## Umgebungsregel (dev / prod)

| Bedingung | Umgebung | Konfigurationsdatei |
|-----------|----------|---------------------|
| Branch = `main` | `prod` | `config/prod.env` |
| Anderer Branch | `dev` | `config/dev.env` |

### Konfigurationswerte

**config/prod.env**
BASE_URL=https://cloudapp-dci.example.com

LOG_LEVEL=WARNING

MAINTENANCE_MODE=false

**config/dev.env**

BASE_URL=https://dev.cloudapp-dci.example.com

LOG_LEVEL=DEBUG

MAINTENANCE_MODE=false


---

## Verwendete Marketplace-Actions

| Action | Version | Zweck |
|--------|---------|-------|
| `actions/checkout` | v4 | Repository auschecken |
| `actions/upload-artifact` | v4 | Report-Dateien hochladen |

---

## Typische Fehlerquellen

| Fehler | Ursache | Lösung |
|--------|---------|--------|
| YAML-Einrückungsfehler | Falsche Leerzeichen | Genau 2 Leerzeichen pro Ebene |
| Secret nicht gefunden | Falscher Name | Namen in Settings prüfen |
| Artefakt leer | Path falsch | `reports/` Verzeichnis prüfen |
| Config nicht gefunden | Datei fehlt | `config/dev.env` vorhanden? |

---

## Workflow-Schritte (Übersicht)
1-Repository auschecken
2-Systeminfos anzeigen
3-Report-Verzeichnis erstellen
4-System-Report erstellen
5-Umgebungsvariablen protokollieren
6-Runtime-Info speichern
7-Report-Inhalt anzeigen
8-Secrets prüfen
9-Umgebung bestimmen und Konfiguration laden
10-Artefakte hochladen


---

## Erweiterungsaufgabe 3: Typische Fehlerquellen

### Fehlerfall 1: Secret fehlt

**Symptom:**

CLOUD_API_TOKEN : fehlt ❌

**Ursache:** Secret wurde gelöscht oder umbenannt.

**Lösung:** Secret unter Settings → Secrets → Actions neu anlegen.

**Erkennung im Log:** Der Workflow läuft weiter, zeigt aber `fehlt ❌`.
Kein Absturz, da der Fehler abgefangen wird.

---

### Fehlerfall 2: Konfigurationsdatei nicht gefunden

**Symptom:**

cat: config/prod.cfg: No such file or directory

Error: Process completed with exit code 1.

**Ursache:** Falsche Dateiendung `.cfg` statt `.env` im Workflow.

**Lösung:** Dateiname im Workflow korrigieren auf `${ENV_NAME}.env`.

**Erkennung im Log:** Workflow bricht mit Exit-Code 1 ab (rotes X).

---

### Lessons Learned

| # | Fehler | Auswirkung | Lösung |
|---|--------|------------|--------|
| 1 | Secret gelöscht | Warnung im Log, kein Absturz | Secret neu anlegen |
| 2 | Falscher Dateiname | Workflow bricht ab | Dateinamen prüfen |


