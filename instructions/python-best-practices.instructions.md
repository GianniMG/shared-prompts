---
description: 'Python Best Practices für Data Analytics und ML in regulierten Umgebungen'
applyTo: '**.py'
---

# Python Best Practices für HMS

## Code-Qualität

- Verwenden Sie Type Hints für alle Funktionen
- Docstrings nach Google/NumPy Style
- PEP 8 Konformität (Black Formatter)
- Maximum Line Length: 100 Zeichen

## Data Analytics

### Pandas Best Practices
```python
# Explizite dtypes verwenden
df = pd.read_csv('data.csv', dtype={'customer_id': str, 'amount': float})

# Vermeiden Sie Chained Assignment
df.loc[df['amount'] > 100, 'category'] = 'high'  # Gut
# df[df['amount'] > 100]['category'] = 'high'  # Schlecht

# Vectorized Operations bevorzugen
df['total'] = df['price'] * df['quantity']  # Gut
# for i, row in df.iterrows(): ...  # Vermeiden
```

### SQL-Interaktion
```python
# Parametrisierte Queries (SQL Injection vermeiden)
query = "SELECT * FROM customers WHERE customer_id = ?"
df = pd.read_sql(query, conn, params=[customer_id])
```

## Machine Learning

### Reproducibility
```python
import random
import numpy as np

# Seeds setzen für Reproducibility
random.seed(42)
np.random.seed(42)

# Sklearn
from sklearn.model_selection import train_test_split
X_train, X_test = train_test_split(X, y, random_state=42)
```

### Model Versioning
```python
import mlflow

# Tracking aktivieren
with mlflow.start_run(run_name="predictive_maintenance_v1"):
    mlflow.log_params({"n_estimators": 100, "max_depth": 5})
    mlflow.log_metrics({"accuracy": 0.95, "f1_score": 0.93})
    mlflow.sklearn.log_model(model, "model")
```

## GxP Compliance

### Logging & Audit Trail
```python
import logging

# Strukturiertes Logging
logger = logging.getLogger(__name__)
logger.info(f"Processing started by {user_id} at {timestamp}")

# Audit Trail für Datenänderungen
def audit_log(action, user_id, record_id, old_value, new_value):
    logger.info({
        'action': action,
        'user_id': user_id,
        'record_id': record_id,
        'old_value': old_value,
        'new_value': new_value,
        'timestamp': datetime.utcnow().isoformat()
    })
```

### Data Validation
```python
import great_expectations as ge

# Data Quality Checks
def validate_input_data(df):
    df_ge = ge.from_pandas(df)
    df_ge.expect_column_values_to_not_be_null("patient_id")
    df_ge.expect_column_values_to_be_between("age", 0, 120)
    return df_ge.validate()
```

## Testing

### Unit Tests (pytest)
```python
import pytest

def test_data_transformation():
    # Arrange
    input_data = pd.DataFrame({'value': [1, 2, 3]})

    # Act
    result = transform_data(input_data)

    # Assert
    assert result['value'].sum() == 6
    assert len(result) == 3
```

### Test Coverage
- Minimum 80% Code Coverage
- Kritische Berechnungen: 100% Coverage
- Integration Tests für Pipelines

## Error Handling

```python
# Spezifische Exceptions
try:
    result = process_data(df)
except ValueError as e:
    logger.error(f"Invalid data format: {e}")
    raise
except Exception as e:
    logger.error(f"Unexpected error: {e}")
    raise
```

## Performance

### Memory Management
```python
# Chunking für große Dateien
chunks = pd.read_csv('large_file.csv', chunksize=10000)
for chunk in chunks:
    process_chunk(chunk)

# Explizit Speicher freigeben
del large_df
import gc
gc.collect()
```

### Parallelisierung
```python
from multiprocessing import Pool

def parallel_process(data_list):
    with Pool(processes=4) as pool:
        results = pool.map(process_item, data_list)
    return results
```

## Cloud Best Practices

### Azure SDK
```python
from azure.storage.blob import BlobServiceClient
from azure.identity import DefaultAzureCredential

# Managed Identity verwenden
credential = DefaultAzureCredential()
blob_client = BlobServiceClient(account_url, credential=credential)
```

### Environment Variables
```python
import os
from dotenv import load_dotenv

load_dotenv()
connection_string = os.getenv('AZURE_STORAGE_CONNECTION_STRING')
```

## Dokumentation

- README.md mit Setup-Anleitung
- Requirements.txt mit pinned versions
- Docstrings für alle public Functions/Classes
- Inline-Kommentare für komplexe Logik
