# Contributing to HMS Copilot Prompt Library

Vielen Dank für Ihr Interesse, zur HMS Copilot Prompt Library beizutragen! Wir freuen uns über Beiträge, die unsere Sammlung von Prompts, Instructions und Agents für Data Analytics, KI und regulierte Umgebungen erweitern.

## Fokus der Bibliothek

Diese Prompt-Bibliothek ist spezialisiert auf:

- **Data Analytics & BI**: SQL-Optimierung, ETL/ELT, Reporting
- **Machine Learning & AI**: Predictive Maintenance, Feature Engineering, Model Training
- **GxP Compliance**: Validierung, Audit Trails, 21 CFR Part 11
- **Cloud Engineering**: Azure, AWS, Snowflake
- **Legacy Modernization**: SAS-Migration, Database-Migration
- **Software Engineering**: Testing, CI/CD, Code Quality

## Wie Sie beitragen können

### Prompts hinzufügen

Prompts sind fertige Templates für spezifische Entwicklungsaufgaben.

1. **Erstellen Sie Ihre Prompt-Datei**: Fügen Sie eine neue `.prompt.md` Datei im entsprechenden `prompts/` Unterordner hinzu:
   - `prompts/data-analytics/` - SQL, ETL, BI
   - `prompts/ml-ai/` - Machine Learning, Predictive Analytics
   - `prompts/compliance/` - GxP, Validation
   - `prompts/legacy-migration/` - SAS Migration
   - `prompts/cloud/` - Cloud Infrastructure, IaC

2. **Namenskonvention**: Verwenden Sie beschreibende, kleingeschriebene Dateinamen mit Bindestrichen (z.B. `sql-optimization-snowflake.prompt.md`)

3. **Frontmatter einbinden**:
```markdown
---
agent: 'ask'
description: 'Kurze Beschreibung des Prompts'
---
```

4. **Strukturieren Sie Ihren Prompt**: Klarer Kontext und spezifische Anweisungen

#### Beispiel Prompt-Format

```markdown
---
agent: 'ask'
description: 'Optimiert SQL-Queries für Snowflake Data Warehouse'
---

# SQL Query Optimization für Snowflake

Sie sind ein Experte für Performance-Optimierung von SQL-Queries.

## Ziel

[Beschreiben Sie das Ziel des Prompts]

## Optimierungsstrategien

[Konkrete Strategien und Best Practices]

## Beispiele

[Code-Beispiele]

## Best Practices

[Checkliste]
```

### Instructions hinzufügen

Instructions passen GitHub Copilot's Verhalten für spezifische Technologien an.

1. **Erstellen Sie Ihre Instruction-Datei**: Fügen Sie eine neue `.instructions.md` Datei im `instructions/` Ordner hinzu
2. **Namenskonvention**: Beschreibende, kleingeschriebene Dateinamen mit Bindestrichen (z.B. `python-pandas-gxp.instructions.md`)
3. **Frontmatter**:
```markdown
---
description: 'Python Pandas Best Practices für GxP-validierte Umgebungen'
applyTo: '**.py'
---
```

### Agents hinzufügen

Agents sind spezialisierte AI-Personas für bestimmte Domänen.

1. **Erstellen Sie Ihre Agent-Datei**: Fügen Sie eine neue `.agent.md` Datei im `agents/` Ordner hinzu
2. **Namenskonvention**: z.B. `cloud-architect.agent.md`
3. **Frontmatter**:
```markdown
---
description: 'Cloud Architecture Expert für Azure/AWS'
tools: ['codebase']
---
```

4. **Definieren Sie die Persona**: Expertise, Ansätze, Kommunikationsstil

#### Beispiel Agent-Format

```markdown
---
description: 'Data Engineering Expert für Cloud Analytics'
tools: ['codebase']
---

# Data Engineering Expert

Sie sind ein erfahrener Data Engineer...

## Ihre Expertise

- Cloud Platforms (Azure, AWS, Snowflake)
- ETL/ELT Pipelines
- Data Modeling

## Ihre Ansätze

[Beschreiben Sie wie der Agent arbeitet]

## Typische Aufgaben

[Welche Aufgaben der Agent unterstützt]
```

### Collections erstellen

Collections gruppieren verwandte Prompts für spezifische Workflows.

1. **Collection Manifest**: Erstellen Sie eine `.collection.yml` Datei im `collections/` Ordner
2. **Referenzieren Sie existierende Items**: Nur Dateien die bereits im Repository existieren

```yaml
id: life-sciences-gxp
name: Life Sciences GxP Compliance
description: Vollständige Toolkit für GxP-konforme Software-Entwicklung
tags: [life-sciences, gxp, compliance, validation]
items:
  - path: prompts/compliance/gxp-validation-documentation.prompt.md
    kind: prompt
  - path: agents/gxp-compliance-expert.agent.md
    kind: agent
display:
  ordering: alpha
  show_badge: true
```

## Beitrag einreichen

1. **Forken Sie das Repository**
2. **Erstellen Sie einen Branch** für Ihren Beitrag
3. **Fügen Sie Ihre Dateien hinzu** nach den obigen Guidelines
4. **Testen Sie Ihre Inhalte** mit GitHub Copilot
5. **Submiten Sie einen Pull Request** mit:
   - Klarem Titel
   - Beschreibung des Beitrags
   - Relevanter Kontext

## Qualitätskriterien

- **Spezifisch**: Konkrete, umsetzbare Anleitungen
- **Getestet**: Funktioniert mit GitHub Copilot
- **Fokussiert**: Jede Datei adressiert ein spezifisches Thema
- **Best Practices**: Industriestandards folgen (besonders bei GxP)
- **Dokumentiert**: Klare, präzise Sprache

## HMS-spezifische Anforderungen

### Für Compliance-bezogene Prompts

- **Regulatory References**: Verweise auf FDA, EU Annex 11, GAMP 5
- **ALCOA+ Prinzipien**: Data Integrity berücksichtigen
- **Audit Trail**: Dokumentationsanforderungen klar machen
- **Validation**: IQ/OQ/PQ Templates wo relevant

### Für Data Analytics Prompts

- **Cloud-native**: Azure/AWS/Snowflake Best Practices
- **Performance**: Optimierung und Cost-Awareness
- **Scalability**: Berücksichtigung großer Datenmengen
- **Data Quality**: Great Expectations, dbt Tests

### Für ML/AI Prompts

- **Reproducibility**: Seed-Setting, Versionierung
- **Explainability**: SHAP, Feature Importance
- **Monitoring**: Drift Detection, Performance Tracking
- **Validation**: Train/Test Splits, Cross-Validation

