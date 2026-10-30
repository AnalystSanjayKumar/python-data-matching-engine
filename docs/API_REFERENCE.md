# REST API Reference — Python Data Matching Engine

The application exposes a high-performance RESTful API using FastAPI.

**Base URL**: `http://127.0.0.1:8000`  
**Interactive Swagger Docs**: `http://127.0.0.1:8000/docs`  
**ReDoc**: `http://127.0.0.1:8000/redoc`

---

## 1. Quick Match Endpoint

### `POST /api/quick-match`
Compare two individual strings on the fly with customizable matching configurations.

**Request Body (`application/json`):**
```json
{
  "str_a": "Google India Pvt Ltd",
  "str_b": "Google India Private Limited",
  "matching_type": "company",
  "normalization_mode": "standard",
  "case_sensitive": false,
  "remove_punctuation": true,
  "normalize_whitespace": true,
  "expand_abbreviations": true,
  "normalize_numbers": true
}
```

**Response (`200 OK`):**
```json
{
  "status": "success",
  "result": {
    "score": 98.5,
    "status": "Strong Match",
    "matching_type": "company",
    "str_a_raw": "Google India Pvt Ltd",
    "str_b_raw": "Google India Private Limited",
    "str_a_normalized": "google india private limited",
    "str_b_normalized": "google india private limited",
    "explanation": {
      "summary": "High similarity across company name tokens after standardizing legal suffix.",
      "component_scores": {
        "token_sort": 100.0,
        "token_set": 100.0,
        "levenshtein": 96.0,
        "jaro_winkler": 98.0
      },
      "matching_tokens": ["google", "india", "private", "limited"],
      "confidence": "High"
    }
  }
}
```

---

## 2. Ingestion & File Upload

### `POST /api/upload`
Upload a CSV, Excel (`.xlsx`, `.xls`), or JSON file for batch matching.

**Request (`multipart/form-data`):**
- `file`: Uploaded file binary

**Response (`200 OK`):**
```json
{
  "status": "success",
  "file_id": "a823f4c2-901b-4d7a-8f56-e91b61c74a12",
  "filename": "customers.csv",
  "rows": 1500,
  "columns": ["Customer_ID", "Address_Legacy", "Address_ERP"],
  "detected_types": {
    "Customer_ID": { "type": "id_code", "confidence": 0.95 },
    "Address_Legacy": { "type": "address", "confidence": 0.88 },
    "Address_ERP": { "type": "address", "confidence": 0.88 }
  },
  "preview": [ ... ]
}
```

---

## 3. Sample Dataset Loader

### `GET /api/sample/{sample_name}`
Load built-in demonstration data instantly.

**Parameters:**
- `sample_name`: `addresses`, `companies`, `names`, or `customers`

**Response (`200 OK`):**
Returns sample file metadata, detected column types, and suggested default comparison pairs.

---

## 4. Batch Matching Job

### `POST /api/match`
Execute matching across columns in an uploaded dataset. Supports asynchronous execution with progress polling.

**Request Body (`application/json`):**
```json
{
  "file_id": "a823f4c2-901b-4d7a-8f56-e91b61c74a12",
  "col_a": "Address_Legacy",
  "col_b": "Address_ERP",
  "matching_type": "address",
  "normalization_mode": "standard",
  "thresholds": {
    "strong": 85.0,
    "likely": 70.0,
    "possible": 40.0,
    "weak": 20.0
  }
}
```

**Response (`200 OK`):**
```json
{
  "status": "started",
  "job_id": "job_39b02d8f"
}
```

---

## 5. Job Progress & Results

### `GET /api/match/status/{job_id}`
Poll matching progress.
```json
{
  "job_id": "job_39b02d8f",
  "status": "completed",
  "processed": 1500,
  "total": 1500,
  "percentage": 100.0
}
```

### `GET /api/match/results/{job_id}`
Retrieve paginated matching results with filter options (`status`, `search`, `page`, `page_size`).

---

## 6. Exporting Results

### `GET /api/export/{job_id}/csv`
Stream matching results as a downloadable CSV file.

### `GET /api/export/{job_id}/excel`
Stream matching results as an formatted `.xlsx` workbook.

---

## 7. Abbreviation Dictionary Management

### `GET /api/dictionary`
Get all active expansion rules.

### `POST /api/dictionary`
Add a custom abbreviation expansion rule (`{"abbreviation": "st", "expansion": "street"}`).

### `DELETE /api/dictionary/{abbrev}`
Delete an abbreviation rule.
