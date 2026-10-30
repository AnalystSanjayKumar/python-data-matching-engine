# System Architecture — Python Data Matching Engine

## High-Level Architecture Diagram

```
                 +---------------------------------------------+
                 |            User Interface (Web UI)          |
                 |  (HTML5 / Tailwind CSS / Vanilla JS Engine) |
                 +----------------------+----------------------+
                                        | HTTP / JSON / Multipart
                                        v
                 +---------------------------------------------+
                 |            FastAPI Web / REST API           |
                 |              (app/web/routes.py)            |
                 +----------------------+----------------------+
                                        |
                 +----------------------+----------------------+
                 |          Master MatchingEngine              |
                 |        (app/matching/engine.py)             |
                 +----+-----------------+------------------+---+
                      |                 |                  |
                      v                 v                  v
          +--------------------+ +---------------+ +------------------+
          | Normalization      | | Type Detector | | Match Scorer     |
          | (app/normalization)| | (detector.py) | | (app/scoring)    |
          +--------------------+ +---------------+ +------------------+
                      |                                    ^
                      v                                    |
          +------------------------------------------------+---+
          |            Domain-Specific Matchers                |
          |  - AddressMatcher    - CompanyMatcher              |
          |  - NameMatcher       - EmailMatcher                |
          |  - PhoneMatcher      - IdCodeMatcher               |
          |  - GenericMatcher                                  |
          +----------------------+-----------------------------+
                                 |
                                 v
          +----------------------------------------------------+
          |             Similarity Algorithm Suite             |
          |  (Levenshtein, Jaro-Winkler, Token Sort/Set, etc.) |
          +----------------------------------------------------+
```

---

## Component Breakdown

### 1. Ingestion & File Processing (`app/utils/file_reader.py`)
- Reads `.csv`, `.xlsx`, `.xls`, and `.json`.
- Validates data encoding (UTF-8, Latin-1, CP1252 auto-detection).
- Generates truncated preview structures for the frontend.

### 2. Semantic Type Detector (`app/matching/detector.py`)
- Samples non-null entries from candidate columns.
- Runs regular expression matching and dictionary heuristics.
- Assigns column categories with confidence scores (0.0 to 1.0).

### 3. Normalization Pipeline (`app/normalization/`)
- **`cleaner.py`**: Manages case folding, whitespace collapsing, punctuation filtering, and mode presets (*Conservative*, *Standard*, *Aggressive*).
- **`abbreviations.py`**: Dictionary-based synonym expansion (`st` → `street`, `ltd` → `limited`, `mgr` → `manager`).
- **`numbers.py`**: Normalizes number prefixes (`No.`, `#`), written ordinals (`first` → `1st`), and floor designations.
- **`addresses.py`**: Segment parser extracting components into structured dictionaries.
- **`names.py`**: Resolves names into first name, middle name, last name, and initials.

### 4. Specialized Matchers (`app/matching/`)
Each matcher implements custom domain heuristics:
- **Address**: Checks street matching, locality overlap, and applies penalty when building/flat numbers differ despite identical street names.
- **Company**: Strips corporate entities to prevent false-positive matches across distinct companies sharing identical legal structures.
- **Person Name**: Inverts surname/given name orders and tests abbreviation combinations.
- **Email**: Separates domain from user handle, prioritizing exact domain equality before fuzzy matching username.
- **Phone**: Cleans international calling codes (`+`, dialing prefixes) and validates uniform digit lengths.

### 5. Scoring & Explainability (`app/scoring/`)
- Computes weighted score across algorithms based on entity type.
- Classifies candidate pairs into confidence buckets:
  - **Strong Match** (>= 85%)
  - **Likely Match** (>= 70%)
  - **Possible Match** (>= 40%)
  - **Weak Match** (>= 20%)
  - **No Match** (< 20%)
- Produces token-level explainability breakdown and natural language narratives.
