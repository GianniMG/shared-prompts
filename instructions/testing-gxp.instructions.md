---
description: 'Testing Best Practices für GxP-validierte Software'
applyTo: '**test**.py, **Test**.cs'
---

# Testing Best Practices für GxP-Umgebungen

## Test-Strategie

### Validation Testing Levels
1. **Unit Tests**: Einzelne Funktionen/Methoden
2. **Integration Tests**: Zusammenspiel mehrerer Komponenten
3. **System Tests**: End-to-End Tests
4. **OQ Tests**: Operational Qualification
5. **PQ Tests**: Performance Qualification

## Unit Testing (Python/pytest)

### Struktur
```python
# tests/test_data_processor.py
import pytest
from src.data_processor import calculate_mean, validate_data

def test_calculate_mean_valid_data():
    """Test mean calculation with valid inputs"""
    # Arrange
    data = [1.0, 2.0, 3.0, 4.0, 5.0]
    expected = 3.0

    # Act
    result = calculate_mean(data)

    # Assert
    assert result == expected, f"Expected {expected}, got {result}"

def test_calculate_mean_empty_list():
    """Test mean calculation with empty list raises ValueError"""
    with pytest.raises(ValueError, match="Cannot calculate mean of empty list"):
        calculate_mean([])

def test_validate_data_missing_required_field():
    """Test data validation catches missing patient_id"""
    invalid_data = {"visit_date": "2024-01-01"}

    result = validate_data(invalid_data)

    assert result.is_valid == False
    assert "patient_id" in result.errors
```

### Fixtures
```python
@pytest.fixture
def sample_clinical_data():
    """Provides sample clinical trial data for testing"""
    return pd.DataFrame({
        'patient_id': ['PAT-001', 'PAT-002', 'PAT-003'],
        'visit_date': ['2024-01-01', '2024-01-02', '2024-01-03'],
        'blood_pressure': [120, 125, 118]
    })

def test_process_clinical_data(sample_clinical_data):
    result = process_data(sample_clinical_data)
    assert len(result) == 3
```

### Parameterized Tests
```python
@pytest.mark.parametrize("input_value,expected", [
    (100.0, "Normal"),
    (140.0, "High"),
    (90.0, "Low"),
])
def test_blood_pressure_classification(input_value, expected):
    result = classify_blood_pressure(input_value)
    assert result == expected
```

## Data Validation Testing

### Great Expectations Tests
```python
import great_expectations as ge

def test_data_quality_expectations():
    """Validates data quality rules per GxP requirements"""
    df = load_test_data()
    df_ge = ge.from_pandas(df)

    # Required fields not null
    result1 = df_ge.expect_column_values_to_not_be_null("patient_id")
    assert result1.success, "patient_id contains NULL values"

    # Value ranges
    result2 = df_ge.expect_column_values_to_be_between("age", 18, 100)
    assert result2.success, "Age values out of valid range"

    # Format validation
    result3 = df_ge.expect_column_values_to_match_regex(
        "patient_id",
        "^PAT-[0-9]{6}$"
    )
    assert result3.success, "patient_id format invalid"
```

## Integration Testing

### Database Integration
```python
@pytest.fixture(scope="session")
def test_database():
    """Creates test database and cleans up after tests"""
    # Setup
    conn = create_test_db()
    setup_schema(conn)

    yield conn

    # Teardown
    drop_test_db(conn)

def test_data_pipeline_integration(test_database):
    """Tests full ETL pipeline with test database"""
    # Arrange
    load_test_data(test_database)

    # Act
    run_etl_pipeline(test_database)

    # Assert
    result = query_result_table(test_database)
    assert len(result) == 100
    assert result['amount'].sum() == pytest.approx(12345.67, rel=1e-2)
```

## GxP-Specific Testing

### Audit Trail Testing
```python
def test_audit_trail_records_all_changes():
    """Validates audit trail captures all CRUD operations per 21 CFR Part 11"""
    # Arrange
    user_id = "test_user_001"
    record_id = create_test_record(user_id)

    # Act - Update
    update_record(record_id, {"value": "new_value"}, user_id)

    # Assert
    audit_entries = get_audit_trail(record_id)

    assert len(audit_entries) == 2, "Should have CREATE and UPDATE entries"

    update_entry = audit_entries[1]
    assert update_entry['action'] == 'UPDATE'
    assert update_entry['user_id'] == user_id
    assert update_entry['old_value'] == "old_value"
    assert update_entry['new_value'] == "new_value"
    assert update_entry['timestamp'] is not None
```

### Data Integrity Testing (ALCOA+)
```python
def test_data_integrity_alcoa_plus():
    """Validates ALCOA+ compliance for data storage"""
    record = create_clinical_record("PAT-123", "test_user")

    # Attributable
    assert record['created_by'] == "test_user"

    # Legible
    assert isinstance(record['data'], dict)  # Not binary

    # Contemporaneous
    time_diff = datetime.now() - record['created_at']
    assert time_diff.total_seconds() < 1, "Timestamp not contemporaneous"

    # Original
    assert record['version'] == 1

    # Accurate
    assert record['patient_id'] == "PAT-123"

    # Complete
    required_fields = ['patient_id', 'visit_date', 'created_by', 'created_at']
    assert all(field in record for field in required_fields)
```

### Calculation Validation
```python
def test_statistical_calculation_accuracy():
    """Validates statistical calculations meet validation criteria"""
    # Test data with known results
    test_data = [1.0, 2.0, 3.0, 4.0, 5.0]

    # Expected results (pre-calculated)
    expected_mean = 3.0
    expected_std = 1.5811388300841898

    # Calculate
    result_mean = calculate_mean(test_data)
    result_std = calculate_std(test_data)

    # Assert with tolerance (2 decimal places per validation protocol)
    assert result_mean == pytest.approx(expected_mean, abs=0.01)
    assert result_std == pytest.approx(expected_std, abs=0.01)
```

## Test Coverage

### Coverage Requirements
```python
# pytest.ini or pyproject.toml
[tool.pytest.ini_options]
addopts = """
    --cov=src
    --cov-report=html
    --cov-report=term
    --cov-fail-under=80
"""
```

### Critical Path Coverage
```python
# Mark critical tests that require 100% coverage
@pytest.mark.critical
def test_dosage_calculation():
    """Critical: Dosage calculation must be 100% tested"""
    # Test all branches and edge cases
    pass
```

## Performance Testing

### Load Testing
```python
import time

def test_query_performance():
    """Validates query meets performance requirements (< 2 seconds)"""
    start_time = time.time()

    result = execute_large_query()

    elapsed_time = time.time() - start_time

    assert elapsed_time < 2.0, f"Query took {elapsed_time}s, exceeds 2s SLA"
    assert len(result) > 0, "Query returned no results"
```

### Memory Testing
```python
import tracemalloc

def test_memory_usage_within_limits():
    """Validates memory usage stays within acceptable limits"""
    tracemalloc.start()

    process_large_dataset()

    current, peak = tracemalloc.get_traced_memory()
    tracemalloc.stop()

    max_memory_mb = 500
    peak_mb = peak / 1024 / 1024

    assert peak_mb < max_memory_mb, f"Peak memory {peak_mb}MB exceeds {max_memory_mb}MB"
```

## Test Documentation

### Test Protocol Format
```python
"""
Test Case: TC-OQ-001
Title: Data Import Functionality
Objective: Verify that CSV data import correctly loads and validates data

Prerequisites:
- Test data file 'validation_data.csv' available
- Database connection configured
- User has appropriate permissions

Test Steps:
1. Navigate to Data Import module
2. Select test file 'validation_data.csv'
3. Click 'Import' button
4. Verify import success message
5. Query database for imported records

Expected Results:
- Import completes without errors
- 100 records imported
- All data types correct
- No data truncation

Acceptance Criteria:
- All expected results met
- No warnings or errors in logs
"""
```

## Continuous Testing

### CI/CD Integration
```yaml
# .github/workflows/test.yml
name: GxP Validation Tests

on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Run Unit Tests
        run: pytest tests/unit --junitxml=results/unit-tests.xml

      - name: Run Integration Tests
        run: pytest tests/integration --junitxml=results/integration-tests.xml

      - name: Generate Coverage Report
        run: pytest --cov=src --cov-report=xml

      - name: Validate Test Results
        run: |
          if [ $? -ne 0 ]; then
            echo "Tests failed - deployment blocked"
            exit 1
          fi
```

## Test Data Management

### Synthetic Test Data
```python
from faker import Faker

def generate_test_patients(n=100):
    """Generates synthetic patient data for testing"""
    fake = Faker()

    patients = []
    for i in range(n):
        patients.append({
            'patient_id': f'PAT-{i:06d}',
            'name': fake.name(),
            'birth_date': fake.date_of_birth(minimum_age=18, maximum_age=80),
            'enrollment_date': fake.date_between(start_date='-1y', end_date='today')
        })

    return pd.DataFrame(patients)
```

## Best Practices Checklist

- [ ] Test-driven Development (TDD) wo möglich
- [ ] Minimum 80% Code Coverage
- [ ] 100% Coverage für kritische Berechnungen
- [ ] Parametrized Tests für verschiedene Input-Szenarien
- [ ] Fixtures für wiederverwendbare Test-Daten
- [ ] Mocking für externe Dependencies
- [ ] Performance Tests für SLA-kritische Funktionen
- [ ] Integration Tests für vollständige Workflows
- [ ] Test-Dokumentation im Code (Docstrings)
- [ ] CI/CD Pipeline führt alle Tests automatisch aus
