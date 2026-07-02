# Reproducibility Notes

This document explains how to inspect and, where possible, reproduce the workflow for the thesis **“The geography of news values in Dutch digital journalism: Mapping newsworthiness in NU.nl coverage.”**

## Important Limitation

The full workflow cannot be completely reproduced from the public repository alone because the original NU.nl article texts are copyrighted and are not redistributed.

The public repository is intended to make the workflow transparent by sharing:

- code notebooks
- derived article-level classification outputs
- calibration summaries
- validation outputs
- diagnostic files
- GIS-ready article-location datasets
- location-level summary tables

To fully rerun the raw extraction and classification process, you need local access to the original Nexis-exported NU.nl files.

## Recommended Environment

The notebooks were developed in Google Colab. A GPU runtime is recommended for the LLM-assisted classification notebook.

Main software and libraries:

- Python
- pandas
- NumPy
- spaCy
- `nl_core_news_lg`
- python-docx
- PyTorch
- Hugging Face Transformers
- Qwen/Qwen2-1.5B-Instruct
- tqdm
- Google Colab
- QGIS

Install the Python dependencies with:

```bash
pip install -r requirements.txt
```

The Dutch spaCy model is included in `requirements.txt`. If installation fails, it can also be installed manually with:

```bash
python -m spacy download nl_core_news_lg
```

## Workflow Overview

The workflow consists of four main stages.

## 1. Article Extraction and Location Matching

Notebook:

```text
notebooks/news_reader_for_news_values.ipynb
```

Purpose:

- read Nexis-exported NU.nl article files
- extract metadata such as title, date, and subjects
- extract article body and lead text
- detect Dutch place names using spaCy
- match locations to an OpenStreetMap-derived lookup table
- export article-level and article-location datasets
- create diagnostic files

Typical local inputs:

```text
data/raw/
nexis_exports/
original_nexis_exports/
```

These raw files are not included in the public repository.

Typical outputs:

```text
data/derived/article_id_mapping.csv
data/gis/locations_final.csv
data/gis/location_mentions_for_gis.csv
data/diagnostics/diagnostic_duplicate_article_id_rows.csv
data/diagnostics/diagnostic_duplicate_article_location_rows.csv
data/diagnostics/diagnostic_excluded_non_netherlands_location_rows.csv
```

## 2. LLM-Assisted News Value Classification

Notebook:

```text
notebooks/qwen2_news_value_assignment.ipynb
```

Purpose:

- load the article subset with Dutch location mentions
- classify articles for six news values
- parse structured model output
- save raw model responses
- create article-level score files
- apply benchmark prevalence calibration

Model:

```text
Qwen/Qwen2-1.5B-Instruct
```

The model classifies the following news values:

- entertainment
- bad news
- magnitude
- good news
- celebrity
- power elite

Each value is scored as:

| Score | Meaning |
|---:|---|
| `0` | Not meaningfully present |
| `50` | Present, but secondary |
| `100` | Strongly present or central |

Typical outputs:

```text
data/derived/article_news_values.csv
data/derived/article_news_values_raw_outputs.csv
data/derived/article_news_values_calibrated_to_harcup_oneill.csv
data/derived/news_value_calibration_summary.csv
data/derived/corpus_score_summary_calibrated_and_raw.csv
```

## 3. Manual Validation and Evaluation

Notebook:

```text
notebooks/qwen2_validation.ipynb
```

Purpose:

- compare raw model scores with manual validation scores
- compare calibrated model scores with manual validation scores
- calculate exact score accuracy
- calculate mean absolute error
- calculate Cohen’s kappa
- calculate present/absent precision, recall, and F1
- calculate strong/not-strong precision, recall, and F1
- create confusion matrix data
- identify article-level disagreements
- prepare GIS-ready data with calibrated scores

Typical outputs:

```text
data/validation/manual_validation_raw_vs_calibrated_metrics.csv
data/validation/manual_validation_raw_vs_calibrated_comparison.csv
data/validation/manual_validation_confusion_matrices_raw_vs_calibrated.csv
data/validation/manual_validation_article_level_disagreements_redacted.csv
data/gis/location_mentions_with_calibrated_news_values_for_gis.csv
data/gis/location_news_value_summary_by_location_calibrated.csv
```

The public disagreement file should be redacted before upload. Do not include `classification_text`, full article text, or long article excerpts.

## 4. GIS Mapping

Software:

```text
QGIS
```

Main GIS input:

```text
data/gis/location_mentions_with_calibrated_news_values_for_gis.csv
data/gis/location_news_value_summary_by_location_calibrated.csv
```

Coordinate reference systems:

| Use | CRS |
|---|---|
| Imported latitude/longitude points | WGS 84 |
| Dutch spatial analysis and mapping | Amersfoort / RD New, EPSG:28992 |

The QGIS workflow includes:

- overall location mention heatmaps
- mention-count maps
- dominant news value maps
- present-share maps
- mean-score maps
- contrast maps
- overrepresentation maps
- municipality-level aggregation
- province-level aggregation

Recommended QGIS outputs:

```text
qgis/project/thesis_news_values_maps.qgz
qgis/exports/
```

## Reusing Saved Outputs

If the original article texts are unavailable, the project can still be inspected using the saved derived CSV files.

Recommended analysis starting points:

```text
data/derived/article_news_values_calibrated_to_harcup_oneill.csv
data/gis/location_mentions_with_calibrated_news_values_for_gis.csv
data/gis/location_news_value_summary_by_location_calibrated.csv
data/validation/manual_validation_raw_vs_calibrated_metrics.csv
```

These files allow readers to inspect:

- article-level calibrated news value scores
- raw versus calibrated scores
- model and prompt settings
- location-level spatial summaries
- validation performance
- GIS-ready article-location rows

## Sources of Non-Determinism

Some parts of the workflow may not reproduce exactly unless the same environment, package versions, model version, prompt, runtime settings, and random sampling steps are used.

Potential sources of variation:

- language model version or backend changes
- GPU/runtime differences in Google Colab
- sampling of manual validation cases
- parsing behavior for irregular model outputs
- updates to OpenStreetMap data
- changes in external GIS boundary layers

For stable replication of the thesis results, use the saved CSV outputs included in the repository.

## Recommended Order for Running Notebooks

If you have access to the raw Nexis exports, run:

1. `news_reader_for_news_values.ipynb`
2. `qwen2_news_value_assignment.ipynb`
3. `qwen2_validation.ipynb`

If you do not have access to the raw article texts, start from the derived CSV files and use the notebooks only to inspect the later analysis logic.
original Nexis exports
```

Use redacted versions for public release.
