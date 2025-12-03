# HMS Analytical Software - GitHub Copilot Prompt Library

Eine kuratierte Sammlung von wiederverwendbaren GitHub Copilot Prompts für Data Analytics, KI und Software Engineering in regulierten Umgebungen.

> **Hinweis**: Diese Bibliothek basiert auf [github/awesome-copilot](https://github.com/github/awesome-copilot) und wurde speziell für HMS Analytical Software angepasst und auf unsere Kernkompetenzen (Data Analytics, ML, GxP Compliance, Cloud Engineering) fokussiert.

## Über HMS Analytical Software GmbH

HMS Analytical Software ist ein Boutique-Dienstleister spezialisiert auf:

- **Data Analytics & KI-Lösungen**: End-to-End Beratung und Implementierung
- **Software Engineering**: Entwicklung maßgeschneiderter, auditierbarer Anwendungen
- **Cloud & Big Data**: Microsoft Azure, AWS, Palantir, Snowflake
- **Generative AI**: Pilotierung und skalierbarer Einsatz
- **SAS-Migration**: Modernisierung legacy SAS-Systeme
- **Regulierte Branchen**: Life Sciences, Pharma, Manufacturing, GxP-konforme Lösungen

## Verfügbare Prompt-Kategorien

### 1. Software Development & Engineering
Prompts für Code-Qualität, Testing und Best Practices:
- Unit-Tests nach GxP-Standards
- Code-Refactoring für Performance
- CI/CD-Pipeline-Erstellung
- Code-Reviews und Dokumentation

### 2. Data Analytics & Business Intelligence
Prompts für Datenanalyse und Reporting:
- SQL-Optimierung (Snowflake, Azure SQL, AWS Redshift)
- Data Pipeline Development
- ETL/ELT-Prozesse
- Business Intelligence Dashboards

### 3. Machine Learning & AI
Prompts für ML-Entwicklung und Deployment:
- Feature Engineering
- Model Training & Validation
- Predictive Maintenance
- Anomaly Detection
- Yield Optimization

### 4. Cloud Engineering
Prompts für Cloud-Infrastruktur und DevOps:
- Infrastructure-as-Code (Terraform, Bicep)
- Azure/AWS Resource Deployment
- Container Orchestration
- Cloud Migration Strategies

### 5. Compliance & Validation
Prompts für regulierte Umgebungen:
- GxP-Validierungsdokumentation
- Audit-Checklisten
- Compliance-Testing
- Regulatory Summaries (GLP/GMP/GxP)

### 6. Legacy Modernization
Prompts für System-Migration:
- SAS zu Python/R Migration
- Database Migration Planning
- Legacy Code Refactoring
- Technology Stack Updates

## Wie verwende ich die Prompts?

Es gibt **3 Hauptwege**, diese Prompt Library zu nutzen:

### Option 1: Direkt in GitHub Copilot Chat verwenden (Empfohlen)

**Am einfachsten**: Öffnen Sie eine Prompt-Datei und nutzen Sie sie als Kontext

1. **Prompt-Datei öffnen**: z.B. `prompts/data-analytics/sql-optimization.prompt.md`
2. **GitHub Copilot Chat öffnen**: `Ctrl+I` oder über Chat-Icon
3. **Datei als Kontext hinzufügen**: 
   - Klicken Sie auf das `+` Icon im Chat
   - Wählen Sie "Add File" → Prompt-Datei auswählen
   - **ODER** schreiben Sie: `#file:prompts/data-analytics/sql-optimization.prompt.md`
4. **Ihre Frage stellen**: "Optimiere diese SQL-Query für Snowflake: [Ihre Query]"

**Beispiel**:
```
#file:prompts/ml-ai/predictive-maintenance.prompt.md

Erstelle ein Feature Engineering Pipeline für diese Vibrationssensor-Daten:
[Ihre Daten hier]
```

---

### Option 2: Als Workspace Instructions (Auto-Apply)

**Für automatische Best Practices**: Instructions werden automatisch auf passende Dateien angewendet

1. **`.github` Ordner erstellen** in Ihrem Projekt:
   ```bash
   mkdir .github
   ```

2. **Instructions kopieren**:
   ```bash
   # Beispiel: Python Best Practices
   cp instructions/python-best-practices.instructions.md .github/
   
   # Beispiel: SQL Best Practices
   cp instructions/sql-best-practices.instructions.md .github/
   ```

3. **Fertig!** GitHub Copilot wendet diese Guidelines automatisch an wenn Sie `.py` oder `.sql` Dateien bearbeiten

**Was passiert?**
- Bei `.py` Dateien: Copilot schlägt Code nach HMS Python Standards vor
- Bei `.sql` Dateien: Copilot optimiert Queries für Cloud Data Warehouses
- Bei Test-Dateien: Copilot erstellt GxP-konforme Tests

---

### Option 3: Agents aktivieren (Spezialisierte Experten)

**Für komplexe Aufgaben**: Agents sind spezialisierte AI-Experten

1. **Agent-Datei öffnen**: z.B. `agents/data-engineer.agent.md`
2. **Im Chat referenzieren**: 
   ```
   @workspace Nutze den Data Engineer Agent aus agents/data-engineer.agent.md
   
   Hilf mir eine Medallion Architecture auf Azure Databricks zu designen
   ```

**Verfügbare Agents**:
- **Data Engineer**: ETL/ELT Pipelines, Data Modeling
- **GxP Compliance Expert**: Validation, 21 CFR Part 11
- **Azure Architect**: Cloud Infrastructure Design

---

## Praktische Beispiele

### Beispiel 1: SQL-Query optimieren

**Terminal öffnen in VS Code** → GitHub Copilot Chat öffnen:

```
#file:prompts/data-analytics/sql-optimization.prompt.md

Optimiere diese Query für Snowflake:

SELECT *
FROM large_table t1
JOIN other_table t2 ON t1.id = t2.id
WHERE t1.date > '2024-01-01'
```

**Copilot gibt Ihnen**:
- Optimierte Query mit Column Pruning
- Clustering Key Empfehlungen
- Performance-Tipps

---

### Beispiel 2: GxP-Validierung erstellen

```
#file:prompts/compliance/gxp-validation-documentation.prompt.md

Erstelle einen Validation Plan für unser Python-basiertes Data Analytics System 
zur klinischen Datenauswertung. System ist GAMP Category 5.
```

**Copilot erstellt**:
- Validation Master Plan
- Risk Assessment (FMEA)
- IQ/OQ/PQ Test Protocols
- Traceability Matrix

---

### Beispiel 3: SAS Code migrieren

```
#file:prompts/legacy-migration/sas-to-python.prompt.md

Konvertiere diesen SAS Code zu Python:

PROC MEANS DATA=sales N MEAN STD;
    VAR revenue;
    CLASS region;
RUN;
```

**Copilot liefert**:
- Python Pandas Equivalent
- Validation Script zum Vergleich
- Performance-Optimierungen

---

## Power-Tipp: Collections nutzen

**Collections kombinieren mehrere Prompts für komplette Workflows**:

1. Öffnen Sie `collections/hms-starter-pack.collection.yml`
2. Sehen Sie, welche Prompts zusammen funktionieren
3. Nutzen Sie alle referenzierten Dateien als Kontext:

```
#file:prompts/data-analytics/sql-optimization.prompt.md
#file:prompts/compliance/gxp-validation-documentation.prompt.md
#file:agents/data-engineer.agent.md

Hilf mir eine GxP-konforme Data Pipeline auf Azure zu bauen mit:
- Snowflake als Data Warehouse
- Azure Data Factory für Orchestration
- Validierte Transformations-Logik
```

---

## Weitere Nutzungsmöglichkeiten

### Copy-Paste (Quick & Dirty)
- Öffnen Sie einen Prompt
- Kopieren Sie den Inhalt
- Fügen Sie ihn direkt in GitHub Copilot Chat ein

### Snippet Library (VS Code)
- Erstellen Sie VS Code Snippets aus häufig genutzten Prompts
- Shortcut definieren (z.B. `hms-sql-opt`)
- Schneller Zugriff ohne Datei öffnen

### Team-Integration
- Teilen Sie relevante Instructions im `.github/` Ordner via Git
- Alle Teammitglieder nutzen automatisch die gleichen Standards
- Konsistenter Code Quality im gesamten Team

---

## Repository klonen (Optional)

Wenn Sie lokal arbeiten möchten:

```bash
git clone https://github.com/GianniMG/shared-prompts.git
cd shared-prompts
```

**Aber**: Sie können die Prompts auch direkt via GitHub Web UI nutzen!

## Repository-Struktur

```plaintext
├── prompts/              # Task-spezifische Prompts (.prompt.md)
│   ├── data-analytics/   # SQL, ETL, BI-Prompts
│   ├── ml-ai/            # Machine Learning Prompts
│   ├── cloud/            # Cloud Engineering Prompts
│   └── compliance/       # GxP & Validation Prompts
├── instructions/         # Coding Standards (.instructions.md)
│   ├── python/           # Python Best Practices
│   ├── sql/              # SQL Guidelines
│   └── testing/          # Testing Standards
├── agents/               # Spezialisierte AI-Agents (.agent.md)
│   ├── data-engineer/    # Data Engineering Expert
│   ├── compliance/       # GxP Compliance Expert
│   └── cloud-architect/  # Cloud Architecture Expert
└── collections/          # Kuratierte Collections (.collection.yml)
```

## Use Case Workflows

### Use Case 1: Predictive Maintenance für Manufacturing

**Szenario**: Maschinenausfälle vorhersagen mit IoT Sensordaten

```
#file:prompts/ml-ai/predictive-maintenance.prompt.md
#file:agents/data-engineer.agent.md

Ich habe Vibrationssensor-Daten von Produktionsmaschinen.
Erstelle mir ein komplettes ML-Pipeline für Predictive Maintenance:
- Feature Engineering
- Model Training (XGBoost)
- Deployment auf Azure ML
```

---

### Use Case 2: SAS zu Python Migration (GxP-konform)

**Szenario**: Legacy SAS-Code modernisieren mit Validation

```
#file:prompts/legacy-migration/sas-to-python.prompt.md
#file:prompts/compliance/gxp-validation-documentation.prompt.md

Konvertiere diesen SAS PROC zu Python und erstelle:
1. Python Pandas Equivalent
2. Unit Tests mit pytest
3. OQ Test Protocol für Validation
4. Output-Vergleich Script

[SAS Code hier einfügen]
```

---

### Use Case 3: Cloud Data Pipeline auf Azure (Validiert)

**Szenario**: ETL Pipeline mit GxP Compliance

```
#file:agents/data-engineer.agent.md
#file:agents/gxp-compliance-expert.agent.md
#file:prompts/data-analytics/sql-optimization.prompt.md

Designe eine validierte Data Pipeline auf Azure:
- Source: Clinical Trial Data (CSV, Azure Blob Storage)
- Transform: Azure Data Factory
- Target: Snowflake (optimiert für Analytics)
- Compliance: GxP Audit Trail, ALCOA+
```

## Featured Collections

Entdecken Sie unsere kuratierten Collections für spezifische Workflows:

| Name | Beschreibung | Items | Tags |
| ---- | ----------- | ----- | ---- |
| **Life Sciences** | GxP-konforme Entwicklung für regulierte Umgebungen | Coming Soon | life-sciences, gxp, compliance |
| **Data Engineering** | End-to-End Data Pipeline Development | Coming Soon | data, etl, cloud |
| **ML Engineering** | Machine Learning Model Development & Deployment | Coming Soon | ml, ai, predictive |
| **Cloud Native** | Cloud-first Architecture auf Azure/AWS | Coming Soon | cloud, azure, aws |

## Beitragen

Wir freuen uns über Beiträge! Siehe [CONTRIBUTING.md](CONTRIBUTING.md) für Details.

### Quick Contribution Guide

1. Folgen Sie den Namenskonventionen
2. Testen Sie Ihre Prompts gründlich
3. Aktualisieren Sie relevante Dokumentation
4. Erstellen Sie einen Pull Request


**Bereit, Ihre Entwicklungsproduktivität zu steigern?** Erkunden Sie unsere Prompts, Instructions und Agents!

## Zusätzliche Ressourcen

- [VS Code Copilot Customization](https://code.visualstudio.com/docs/copilot/copilot-customization)
- [GitHub Copilot Chat Documentation](https://code.visualstudio.com/docs/copilot/chat/copilot-chat)
- [GxP Compliance Guidelines](https://www.ema.europa.eu/en/human-regulatory/research-development/compliance/good-manufacturing-practice)

## Trademarks

This project may contain trademarks or logos for projects, products, or services. Authorized use of Microsoft trademarks or logos is subject to and must follow [Microsoft's Trademark & Brand Guidelines](https://www.microsoft.com/en-us/legal/intellectualproperty/trademarks/usage/general).
