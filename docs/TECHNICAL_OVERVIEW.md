# Technical Overview — Python Data Matching Engine

## Architecture at a glance

The application implements an enterprise-grade, high-performance entity resolution and data deduplication workflow with six logical stages:

1. **Input & Ingestion Layer**: Accepts CSV, Excel (`.xlsx`, `.xls`), and JSON files. Inspects schema, validates encoding, and provides instantaneous data previews.
2. **Semantic Type Detection Layer**: Uses heuristic pattern recognition, regex profiling, and header inspection to auto-detect entity columns (Address, Company Name, Person Name, Email, Phone Number, ID / Code, Generic Text).
3. **Normalization & Standardization Layer**: Cleanses raw text through customizable multi-tier pipelines (Standard, Conservative, Aggressive) including punctuation stripping, accent stripping, abbreviation expansion (e.g. `Pvt Ltd` → `private limited`, `Rd` → `road`), and numeral normalization.
4. **Specialized Matching Layer**: Dispatches pairs to domain-tailored matching engines combining Levenshtein, Damerau-Levenshtein, Jaro-Winkler, Token Sort Ratio, Token Set Ratio, Jaccard Token Overlap, and Character N-grams.
5. **Scoring & Weighting Layer**: Calculates composite similarity scores (0.0 to 100.0) based on domain-specific weight profiles with customizable threshold classifications (*Strong*, *Likely*, *Possible*, *Weak*, *No Match*).
6. **Explainability & Presentation Layer**: Produces deep audit summaries explaining matching rationale ("Why did these match?"), token alignment highlights, side-by-side comparisons, and interactive export options (CSV & Excel).

---

## Entity Types & Specialized Engines

| Entity Type | Specialized Matcher | Key Algorithms & Logic |
|---|---|---|
| **Addresses** | `AddressMatcher` | Component parsing (House/Flat, Street, Locality, City, State, PIN code), house number discrepancy penalties, directional expansion. |
| **Companies** | `CompanyMatcher` | Legal suffix stripping (`Pvt Ltd`, `LLC`, `Corp`), brand name core extraction, acronym comparison (`ABC` vs `A.B.C.`). |
| **Person Names** | `NameMatcher` | Inverted name order resolution (`Smith, John` vs `John Smith`), middle name omission, initial matching. |
| **Email Addresses** | `EmailMatcher` | Exact domain verification penalty, plus-addressing resolution, local-part edit distance. |
| **Phone Numbers** | `PhoneMatcher` | Country code normalization (`+91`, `+1`), leading zero stripping, punctuation removal, length validation. |
| **IDs & Codes** | `IdCodeMatcher` | Prefix matching, strict alphanumeric alignment, numerical sequence matching. |
| **Generic Text** | `GenericMatcher` | Weighted ensemble of Token Sort, Token Set, Jaro-Winkler, and Levenshtein. |

---

## Multi-Algorithm Similarity Suite

- **Levenshtein Distance & Ratio**: Measures minimum edit operations (insertions, deletions, substitutions).
- **Damerau-Levenshtein**: Enhances edit distance by detecting adjacent character transpositions.
- **Jaro & Jaro-Winkler**: Prefix-weighted similarity giving higher scores to matching initial sequences (ideal for names).
- **Token Sort Ratio**: Word-order invariant comparison by sorting tokens lexicographically before scoring.
- **Token Set Ratio**: Compares intersection and differences between token sets, resilient against extraneous words.
- **Jaccard Token Similarity**: Intersection over union of token sets.
- **N-Gram Overlap**: Character n-gram Jaccard coefficient for sub-word similarity.

---

## Performance & Optimization

- Accelerated with **RapidFuzz** (C++ vectorization) with pure Python fallback for universal compatibility.
- Streaming batch processing with real-time job progress callbacks.
- Memory-efficient processing utilizing Pandas vectorized operations and garbage collection on completion.
