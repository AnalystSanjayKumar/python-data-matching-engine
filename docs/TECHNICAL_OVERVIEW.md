# Technical Overview — Python Data Matching Engine

## Architecture at a glance

The documented application follows a data-matching workflow with these logical stages:

1. **Input layer** — accepts CSV, XLSX, XLS, and JSON files.
2. **Preparation layer** — inspects data types and normalizes values to reduce formatting-related mismatches.
3. **Matching layer** — compares selected entity fields using exact and approximate string-comparison methods.
4. **Scoring layer** — converts comparison results into similarity scores from 0 to 100.
5. **Classification and review layer** — groups exact and potential matches and exposes field-level reasons for review.
6. **Output layer** — provides CSV/Excel results and API-based access through FastAPI.

## Entity fields described

- Company / organization names
- Person names
- Postal addresses
- Email addresses
- Phone numbers
- Identifier-like values

## Similarity methods described

| Method | Typical comparison purpose |
|---|---|
| Levenshtein | Character insertion, deletion, and substitution differences |
| Damerau-Levenshtein | Edit distance including adjacent transpositions |
| Jaro / Jaro-Winkler | Similarity between short strings, including names |
| Token sort / token set | Word-order and token-overlap variations |
| Jaccard | Similarity based on shared tokens or n-grams |
| N-gram | Character or token sequence overlap |

The actual method selected, thresholds, preprocessing rules, and score aggregation should be confirmed from the implementation before quoting exact behavior.

## Review and data-governance considerations

- Treat fuzzy matches as candidates for review, not automatic proof of identity.
- Retain original values alongside normalized values where possible.
- Record the fields and methods contributing to each candidate match.
- Validate thresholds against representative data before operational use.
- Keep source datasets secure and avoid committing personal or confidential records.

## Verification checklist before publishing the runnable application

- [ ] Add the verified Python source files.
- [ ] Add the actual dependency list (requirements.txt).
- [ ] Add a small synthetic sample dataset with no personal information.
- [ ] Confirm the documented API routes and application entry point.
- [ ] Confirm matching thresholds and score calculation.
- [ ] Add screenshots or a short demo.
- [ ] Add the applicable license and attribution notices.
