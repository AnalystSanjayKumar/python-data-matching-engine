# Python Data Matching Engine

A Python-based data quality and entity-resolution application designed to identify duplicate, similar, and potentially matching records across structured datasets.

**Technology focus:** Python · FastAPI · Pandas · Data Normalization · Fuzzy Matching · Entity Resolution · REST API

## Project overview

The Data Matching Engine helps compare records that may represent the same real-world entity even when values are formatted differently or contain spelling variations. It is designed for data-cleaning and validation workflows involving company, person, address, email, phone, and identifier fields.

The matching workflow combines data preparation, field-level comparison, similarity scoring, and review-friendly output so potential matches can be investigated rather than treated as unquestionable duplicates.

## Business problem

Business datasets often contain duplicate or near-duplicate records because of inconsistent naming, formatting differences, incomplete values, and variations in contact details. These issues can affect reporting, customer records, and downstream analysis.

This project demonstrates an approach to:
- Standardize values before comparison.
- Detect exact and approximate matches.
- Score similarity between candidate records.
- Explain which fields contributed to a match.
- Export results for review and further processing.

## Key capabilities

- **Multi-format ingestion:** CSV, Excel (.xlsx and .xls), and JSON inputs.
- **Data preparation:** data-type detection and value normalization before matching.
- **Entity matching:** company names, person names, addresses, email addresses, phone numbers, and ID-like fields.
- **Fuzzy comparison:** Levenshtein, Damerau-Levenshtein, Jaro, Jaro-Winkler, token-sort, token-set, Jaccard, and n-gram similarity methods.
- **Similarity scoring:** a 0–100 score to help rank candidate matches.
- **Multi-column matching:** comparisons across multiple fields with configurable weighting.
- **Match classification:** exact and potential matches for review.
- **Explainability:** field-level matching information to help inspect results.
- **Exports:** CSV and Excel outputs.
- **Interfaces:** a web interface and FastAPI-based REST API.

## Matching workflow

1. Upload or provide structured input files.
2. Detect fields and prepare values for comparison.
3. Normalize text and supported entity fields.
4. Compare records using exact and fuzzy matching methods.
5. Calculate similarity scores and classify candidate matches.
6. Review field-level match details.
7. Export results for validation or downstream use.

## Technology stack

| Area | Technology |
|---|---|
| Programming language | Python |
| Data processing | Pandas |
| API layer | FastAPI |
| Input formats | CSV, XLSX, XLS, JSON |
| Matching methods | String similarity, token-based and n-gram comparisons |
| Output formats | CSV, Excel |

## Example use cases

- Identifying duplicate company records in vendor or prospect lists.
- Finding customer records with spelling or formatting variations.
- Comparing addresses and contact information across datasets.
- Reviewing possible duplicate IDs or reference records.
- Preparing cleaner datasets for reporting and analytics.

## Repository contents

This repository currently contains project documentation. The runnable application source, verified dependency file, sample input files, and screenshots should be added after the implementation files and redistribution rights have been checked.

## Running the application

Executable setup instructions will be added alongside the verified application source and dependency file. Installation commands are not included yet because the exact dependency list and entry-point filenames have not been confirmed in this repository.

## Important note on matching results

A high similarity score indicates that records are similar according to the configured comparison methods; it does not independently prove that the records refer to the same entity. Review candidate matches against source data before merging or deleting records.

## Author

**Sanjaykumar D**  
GitHub: [AnalystSanjayKumar](https://github.com/AnalystSanjayKumar)

---

*Portfolio project documentation. Feature descriptions reflect the project specification shared for this portfolio; verify the implementation and configuration against the source code before presenting individual algorithms or capabilities as production-tested.*
