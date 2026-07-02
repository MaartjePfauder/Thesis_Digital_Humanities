# Data README

This folder contains the datasets used for the thesis **“The Geography of News Values in Dutch Digital Journalism: Mapping Newsworthiness in NU.nl Coverage.”**

The data is organized into derived article and classification files, GIS-ready files, validation outputs, diagnostics, and pilot/smoke-test outputs.

> **Important:** some files may contain full or partial NU.nl article text. These files should only be included in a **private repository** unless redacted versions are created. See `../docs/copyright_and_data_availability.md`.

## Folder Structure

```text
data/
│
├── derived/
│   ├── articles_final.csv
│   ├── articles_for_classification.csv
│   ├── articles_for_classification_with_locations_only.csv
│   ├── article_id_mapping.csv
│   ├── article_news_values.csv
│   ├── article_news_values_raw_outputs.csv
│   ├── article_news_values_calibrated_to_harcup_oneill.csv
│   ├── corpus_score_summary_calibrated_and_raw.csv
│   └── news_value_calibration_summary.csv
│
├── gis/
│   ├── locations_final.csv
│   ├── location_mentions_for_gis.csv
│   ├── location_mentions_with_calibrated_news_values_for_gis.csv
│   ├── location_news_value_summary_by_location_calibrated.csv
│   └── osm.json
│
├── validation/
│   ├── manual_validation_raw_vs_calibrated_metrics.csv
│   ├── manual_validation_raw_vs_calibrated_comparison.csv
│   ├── manual_validation_confusion_matrices_raw_vs_calibrated.csv
│   ├── manual_validation_article_level_disagreements.csv
│   ├── manual_fewshot_examples.csv
│   └── manual_validation_sample.csv
│
├── diagnostics/
│   ├── diagnostic_duplicate_article_id_rows.csv
│   ├── diagnostic_duplicate_article_location_rows.csv
│   └── diagnostic_excluded_non_netherlands_location_rows.csv
│
├── pilot/
│   ├── pilot_article_news_values.csv
│   ├── pilot_article_news_values_calibrated_to_harcup_oneill.csv
│   ├── pilot_news_value_calibration_summary.csv
│   ├── smoke_test_article_news_values_calibrated_to_harcup_oneill.csv
│   └── smoke_test_news_value_calibration_summary.csv
│
└── README_data.md
```

## Data Workflow

The data workflow follows these main steps:

1. NU.nl articles were exported from Nexis.
2. Article metadata and text were converted into structured CSV files.
3. Dutch location mentions were extracted and matched to OpenStreetMap-derived coordinates.
4. Location-linked articles were classified for six news values using Qwen2.
5. Raw model scores were calibrated using benchmark prevalence targets.
6. A manual validation sample was used to compare raw and calibrated scores.
7. Calibrated article-level scores were merged with geocoded location mentions.
8. GIS-ready files and location-level summary files were exported for QGIS mapping.

## News Values

The classification workflow uses six news values:

- `entertainment`
- `bad_news`
- `magnitude`
- `good_news`
- `celebrity`
- `power_elite`

Each news value is scored using the following scale:

| Score | Meaning |
|---:|---|
| `0` | Not meaningfully present |
| `50` | Present, but secondary |
| `100` | Strongly present / central |

## `derived/`

This folder contains the main article-level and classification datasets. It includes both intermediate article files and derived classification outputs.

Some files in this folder may contain full or partial article text. Treat these files carefully if the repository is public.

### `articles_final.csv`

Structured article table created from the original Nexis exports.

Possible contents include:

- article ID
- file name
- title
- date
- subjects
- lead text
- body text
- extracted metadata

This file is useful for local reproduction but should not be made public if it contains full article text.

### `articles_for_classification.csv`

Article-level file prepared as input for the LLM-assisted news value classification workflow.

Possible contents include:

- article ID
- title
- date
- subjects
- body text
- classification text
- character count fields

This file may contain the article text passed to the model.

### `articles_for_classification_with_locations_only.csv`

Subset of `articles_for_classification.csv` containing only articles with matched Dutch location mentions.

This file was used to focus the classification workflow on articles relevant for the spatial analysis.

### `article_id_mapping.csv`

Maps original article identifiers to stable numeric article IDs.

Used to keep article references consistent across:

- classification outputs
- validation files
- GIS-ready datasets
- location-level summaries

### `article_news_values.csv`

Raw article-level news value classification output.

This file contains the model scores before benchmark calibration.

Typical columns include:

- `article_id`
- `title`
- `date`
- `entertainment_score`
- `bad_news_score`
- `magnitude_score`
- `good_news_score`
- `celebrity_score`
- `power_elite_score`
- `dominant_news_values`
- `short_reason`
- `parse_ok`
- `parse_error`
- `model_name`
- `prompt_type`

### `article_news_values_raw_outputs.csv`

Raw model response file.

This file is useful for checking:

- original model responses
- JSON parsing success
- parsing errors
- prompt/model settings

### `article_news_values_calibrated_to_harcup_oneill.csv`

Main calibrated article-level news value dataset.

This file is used as the main analytical version for the thesis. It contains calibrated scores in the standard score columns and preserves original model scores in `raw_*` columns.

Typical columns include:

- `article_id`
- `title`
- `date`
- `entertainment_score`
- `bad_news_score`
- `magnitude_score`
- `good_news_score`
- `celebrity_score`
- `power_elite_score`
- `raw_entertainment_score`
- `raw_bad_news_score`
- `raw_magnitude_score`
- `raw_good_news_score`
- `raw_celebrity_score`
- `raw_power_elite_score`

### `corpus_score_summary_calibrated_and_raw.csv`

Corpus-level comparison of raw and calibrated score distributions.

This file summarizes:

- mean scores
- present counts
- present shares
- strong counts
- strong shares
- counts for score `0`, `50`, and `100`

### `news_value_calibration_summary.csv`

Summary of the benchmark calibration process.

This file records:

- benchmark target shares
- target number of present cases
- raw present counts
- calibrated present counts
- calibrated present shares

## `gis/`

This folder contains geocoded files and GIS-ready datasets.

### `locations_final.csv`

Geocoded article-location table before the final merge with calibrated news value scores.

Each row represents a location mention linked to an article.

Typical columns include:

- `article_id`
- `file_name`
- `title`
- `date`
- `subjects`
- `location`
- `lat`
- `lon`

### `location_mentions_for_gis.csv`

GIS-ready article-location mention table.

Each row represents one article-location combination. This file can be imported directly into QGIS as a point layer using latitude and longitude.

### `location_mentions_with_calibrated_news_values_for_gis.csv`

Main GIS-ready file used for the spatial news value analysis.

Each row represents one article-location combination with calibrated news value scores attached.

This file is the recommended starting point for reproducing or inspecting the GIS analysis.

Typical columns include:

- `article_id`
- `title`
- `date`
- `subjects`
- `location`
- `lat`
- `lon`
- calibrated news value score columns
- raw news value score columns
- dominant news value fields
- parsing and model metadata

### `location_news_value_summary_by_location_calibrated.csv`

Location-level summary table.

Each row represents one unique geocoded location and summarizes news value visibility at that location.

Typical columns include:

- `location`
- `lat`
- `lon`
- `mention_count`
- mean score columns
- present-share columns
- strong-share columns
- sum-score columns
- `dominant_news_values`
- `dominant_map_category`

### `osm.json`

OpenStreetMap-derived location lookup file used for matching Dutch place names to coordinates.

This file contains OSM elements and should be used according to OpenStreetMap’s license terms.

Recommended attribution:

```text
Contains information from OpenStreetMap, which is made available under the Open Database License.
```

## `validation/`

This folder contains the manual validation files and outputs.

Some files in this folder may contain article text or article excerpts. Treat these files carefully if the repository is public.

### `manual_validation_raw_vs_calibrated_metrics.csv`

Validation metrics comparing manual scores with raw and calibrated model scores.

Metrics include:

- exact score accuracy
- mean absolute error
- Cohen’s kappa
- present/absent accuracy
- present/absent precision
- present/absent recall
- present/absent F1
- strong/not-strong accuracy
- strong/not-strong precision
- strong/not-strong recall
- strong/not-strong F1

### `manual_validation_raw_vs_calibrated_comparison.csv`

Side-by-side comparison of raw and calibrated model performance.

This file is useful for quickly checking whether calibration improved or worsened performance by news value.

### `manual_validation_confusion_matrices_raw_vs_calibrated.csv`

Long-format confusion matrix data for raw and calibrated scores.

Typical columns include:

- `score_set`
- `news_value`
- `manual_score`
- `predicted_score`
- `count`

### `manual_validation_article_level_disagreements.csv`

Article-level file showing where manual scores and model scores differed.

This file may contain article text or classification input text. For a public repository, create a redacted version before uploading.

A public version should remove columns such as:

```text
body_text
lead_text
classification_text
```

### `manual_fewshot_examples.csv`

Manually selected examples used in the few-shot prompting setup for the LLM-assisted classification step.

This file may contain article text or article excerpts and should be treated as private unless redacted.

### `manual_validation_sample.csv`

Manual validation sample used to compare human-coded news value scores with raw and calibrated model scores.

This file may contain article text or classification input text. For public sharing, create a redacted version without full article text.

## `diagnostics/`

This folder contains files used to inspect possible data-quality issues.

### `diagnostic_duplicate_article_id_rows.csv`

Rows flagged during duplicate article ID checks.

### `diagnostic_duplicate_article_location_rows.csv`

Rows flagged during duplicate article-location-coordinate checks.

### `diagnostic_excluded_non_netherlands_location_rows.csv`

Rows excluded because their coordinates fell outside the European Netherlands bounding box.

## `pilot/`

This folder contains smaller test outputs created before running the full workflow.

### `smoke_test_article_news_values_calibrated_to_harcup_oneill.csv`

Small smoke-test output used to check whether the model, prompt, parsing, and calibration logic worked.

### `smoke_test_news_value_calibration_summary.csv`

Calibration summary for the smoke test.

### `pilot_article_news_values.csv`

Raw pilot classification output.

### `pilot_article_news_values_calibrated_to_harcup_oneill.csv`

Calibrated pilot classification output.

### `pilot_news_value_calibration_summary.csv`

Calibration summary for the pilot run.

## Public vs Private Data

For a **private repository**, the full data folder can be included for reproducibility.

For a **public repository**, the following files should be removed or replaced with redacted versions if they contain full or partial article text:

```text
data/derived/articles_final.csv
data/derived/articles_for_classification.csv
data/derived/articles_for_classification_with_locations_only.csv
data/validation/manual_fewshot_examples.csv
data/validation/manual_validation_sample.csv
data/validation/manual_validation_article_level_disagreements.csv
```

Before uploading publicly, check for columns such as:

```text
body_text
lead_text
classification_text
raw article text
long article excerpts
```

## Recommended Public Data

The safest files to include in a public repository are derived, aggregated, or diagnostic outputs that do not contain full article text.

Recommended public files include:

```text
data/derived/article_id_mapping.csv
data/derived/article_news_values.csv
data/derived/article_news_values_calibrated_to_harcup_oneill.csv
data/derived/corpus_score_summary_calibrated_and_raw.csv
data/derived/news_value_calibration_summary.csv
data/gis/location_mentions_with_calibrated_news_values_for_gis.csv
data/gis/location_news_value_summary_by_location_calibrated.csv
data/validation/manual_validation_raw_vs_calibrated_metrics.csv
data/validation/manual_validation_raw_vs_calibrated_comparison.csv
data/validation/manual_validation_confusion_matrices_raw_vs_calibrated.csv
data/diagnostics/
data/pilot/
```

Still, all files should be checked before public release.

## File Naming Notes

Before uploading to GitHub, remove duplicate download suffixes such as:

```text
(1)
(2)
(3)
(4)
```

For example, rename:

```text
articles_for_classification_with_locations_only (1).csv
```

to:

```text
articles_for_classification_with_locations_only.csv
```

Use lowercase, stable filenames where possible.

## Recommended Starting Points

For inspecting article-level calibrated scores:

```text
data/derived/article_news_values_calibrated_to_harcup_oneill.csv
```

For GIS mapping:

```text
data/gis/location_mentions_with_calibrated_news_values_for_gis.csv
data/gis/location_news_value_summary_by_location_calibrated.csv
```

For validation results:

```text
data/validation/manual_validation_raw_vs_calibrated_metrics.csv
data/validation/manual_validation_raw_vs_calibrated_comparison.csv
```

For understanding the full workflow:

```text
notebooks/news_reader_for_news_values.ipynb
notebooks/qwen2_news_value_assignment.ipynb
notebooks/qwen2_validation.ipynb
```
