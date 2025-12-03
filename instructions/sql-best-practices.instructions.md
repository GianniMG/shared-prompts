---
description: 'SQL Best Practices für Cloud Data Warehouses (Snowflake, Azure Synapse, Redshift)'
applyTo: '**.sql'
---

# SQL Best Practices für Cloud Data Warehouses

## Allgemeine Regeln

### Namenskonventionen
```sql
-- Tabellen: lowercase mit underscores
CREATE TABLE customer_orders (...);

-- Spalten: lowercase mit underscores
SELECT customer_id, order_date, total_amount FROM orders;

-- Views: vw_ Prefix
CREATE VIEW vw_sales_summary AS ...;

-- Materialized Views: mv_ Prefix
CREATE MATERIALIZED VIEW mv_monthly_aggregates AS ...;
```

### Kommentare
```sql
-- Kommentare für komplexe Logik
-- Berechnet 30-Tage Rolling Average für Umsatz
SELECT
    date,
    AVG(revenue) OVER (
        ORDER BY date
        ROWS BETWEEN 29 PRECEDING AND CURRENT ROW
    ) as rolling_avg_30d
FROM daily_sales;
```

## Performance Optimization

### Spalten explizit benennen
```sql
-- Gut
SELECT customer_id, name, email FROM customers;

-- Vermeiden
SELECT * FROM customers;
```

### WHERE vor JOIN
```sql
-- Gut: Filter früh anwenden
SELECT o.order_id, c.name
FROM orders o
JOIN (
    SELECT customer_id, name
    FROM customers
    WHERE country = 'Germany'
) c ON o.customer_id = c.customer_id;

-- Weniger effizient
SELECT o.order_id, c.name
FROM orders o
JOIN customers c ON o.customer_id = c.customer_id
WHERE c.country = 'Germany';
```

### UNION ALL statt UNION
```sql
-- Gut (wenn Duplikate ok)
SELECT customer_id FROM customers_2023
UNION ALL
SELECT customer_id FROM customers_2024;

-- Langsamer (dedupliziert)
SELECT customer_id FROM customers_2023
UNION
SELECT customer_id FROM customers_2024;
```

## Snowflake-spezifisch

### Clustering Keys nutzen
```sql
-- Table mit Clustering Key
CREATE TABLE sales (
    sale_date DATE,
    customer_id NUMBER,
    amount NUMBER
)
CLUSTER BY (sale_date, customer_id);
```

### Zero-Copy Cloning
```sql
-- Instant Dev/Test Environments
CREATE TABLE sales_dev CLONE sales;
CREATE SCHEMA analytics_dev CLONE analytics;
```

### Time Travel
```sql
-- Daten vor Änderung abrufen
SELECT * FROM customers AT (TIMESTAMP => '2024-12-01 10:00:00');

-- Letzte X Sekunden
SELECT * FROM customers AT (OFFSET => -3600); -- vor 1 Stunde
```

## Azure Synapse-spezifisch

### Distribution Strategy
```sql
-- Hash Distribution für große Fact Tables
CREATE TABLE fact_sales (
    sale_id INT,
    customer_id INT,
    amount DECIMAL(10,2)
)
WITH (
    DISTRIBUTION = HASH(customer_id),
    CLUSTERED COLUMNSTORE INDEX
);

-- Replicated für kleine Dimension Tables
CREATE TABLE dim_product (
    product_id INT,
    product_name VARCHAR(100)
)
WITH (DISTRIBUTION = REPLICATE);
```

### Statistics pflegen
```sql
-- Statistics erstellen
CREATE STATISTICS stat_customer_id ON sales(customer_id);

-- Statistics aktualisieren
UPDATE STATISTICS sales;
```

## Data Quality

### NULL Handling
```sql
-- Explizit NULL-Checks
SELECT
    customer_id,
    COALESCE(email, 'no-email@example.com') as email,
    COALESCE(phone, 'N/A') as phone
FROM customers;
```

### Data Validation
```sql
-- Constraints verwenden
CREATE TABLE customers (
    customer_id INT NOT NULL PRIMARY KEY,
    email VARCHAR(255) NOT NULL,
    created_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP(),
    CONSTRAINT valid_email CHECK (email LIKE '%@%.%')
);
```

## CTEs für Lesbarkeit

```sql
-- Common Table Expressions verwenden
WITH monthly_sales AS (
    SELECT
        DATE_TRUNC('month', sale_date) as month,
        SUM(amount) as total_sales
    FROM sales
    GROUP BY 1
),
monthly_growth AS (
    SELECT
        month,
        total_sales,
        LAG(total_sales) OVER (ORDER BY month) as prev_month_sales
    FROM monthly_sales
)
SELECT
    month,
    total_sales,
    ROUND((total_sales - prev_month_sales) / prev_month_sales * 100, 2) as growth_pct
FROM monthly_growth
WHERE prev_month_sales IS NOT NULL;
```

## Window Functions statt Self-Joins

```sql
-- Gut: Window Function
SELECT
    customer_id,
    order_date,
    amount,
    SUM(amount) OVER (
        PARTITION BY customer_id
        ORDER BY order_date
    ) as running_total
FROM orders;

-- Vermeiden: Self-Join (langsam)
SELECT
    o1.customer_id,
    o1.order_date,
    o1.amount,
    SUM(o2.amount) as running_total
FROM orders o1
JOIN orders o2 ON o1.customer_id = o2.customer_id
    AND o2.order_date <= o1.order_date
GROUP BY o1.customer_id, o1.order_date, o1.amount;
```

## Incremental Processing

```sql
-- MERGE für Upserts
MERGE INTO target_table t
USING source_table s
ON t.id = s.id
WHEN MATCHED THEN
    UPDATE SET t.value = s.value, t.updated_at = CURRENT_TIMESTAMP()
WHEN NOT MATCHED THEN
    INSERT (id, value, created_at) VALUES (s.id, s.value, CURRENT_TIMESTAMP());
```

## GxP Compliance

### Audit Columns
```sql
-- Standard Audit Columns in jeder Tabelle
CREATE TABLE clinical_data (
    record_id INT PRIMARY KEY,
    patient_id VARCHAR(50) NOT NULL,
    -- ... business columns ...
    created_by VARCHAR(100) NOT NULL,
    created_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP(),
    updated_by VARCHAR(100),
    updated_at TIMESTAMP,
    version INT NOT NULL DEFAULT 1
);
```

### Audit Trail Table
```sql
CREATE TABLE audit_trail (
    audit_id BIGINT AUTOINCREMENT PRIMARY KEY,
    table_name VARCHAR(100) NOT NULL,
    record_id VARCHAR(100) NOT NULL,
    action VARCHAR(10) NOT NULL, -- INSERT, UPDATE, DELETE
    user_id VARCHAR(100) NOT NULL,
    timestamp TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP(),
    old_values VARIANT, -- JSON
    new_values VARIANT  -- JSON
);
```

## Testing

### Test Data Setup
```sql
-- Test Data mit bekannten Ergebnissen
INSERT INTO test_sales VALUES
    (1, '2024-01-01', 100.00),
    (2, '2024-01-02', 200.00),
    (3, '2024-01-03', 150.00);

-- Assertion
SELECT
    CASE
        WHEN SUM(amount) = 450.00 THEN 'PASS'
        ELSE 'FAIL'
    END as test_result
FROM test_sales;
```

## Dokumentation

```sql
-- Inline-Kommentare für komplexe Queries
-- Tabellen-Kommentare
COMMENT ON TABLE sales IS 'Fact table containing all sales transactions. Updated nightly via ETL pipeline.';

-- Spalten-Kommentare
COMMENT ON COLUMN sales.amount IS 'Sale amount in EUR. Excludes VAT.';
```

## Cost Optimization

### Partitioning nutzen
```sql
-- Snowflake: Automatic Clustering
ALTER TABLE large_table CLUSTER BY (date_column);

-- Azure Synapse: Partitioned Table
CREATE TABLE sales_partitioned (
    sale_date DATE,
    amount DECIMAL(10,2)
)
WITH (
    DISTRIBUTION = HASH(customer_id),
    PARTITION (sale_date RANGE RIGHT FOR VALUES
        ('2024-01-01', '2024-02-01', '2024-03-01'))
);
```

### Materialized Views
```sql
-- Teure Aggregationen vorberechnen
CREATE MATERIALIZED VIEW mv_daily_summary AS
SELECT
    DATE(sale_date) as date,
    COUNT(*) as num_sales,
    SUM(amount) as total_amount
FROM sales
GROUP BY 1;
```
