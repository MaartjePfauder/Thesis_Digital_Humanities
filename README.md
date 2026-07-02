# The geography of news values in Dutch digital journalism

**Mapping newsworthiness in NU.nl coverage**

## Overview

This repository contains the code workflow, thesis document, source/intermediate datasets, derived datasets, validation outputs, and GIS-ready files for the master’s thesis **“The geography of news values in Dutch digital journalism: Mapping newsworthiness in NU.nl coverage.”**

The thesis investigates how news values are geographically distributed in Dutch digital journalism. Using NU.nl articles from 2025, the project combines computational text analysis, location extraction, LLM-assisted classification, benchmark calibration, manual validation, and GIS mapping to study how selected forms of newsworthiness become attached to Dutch places.

From a corpus of **55,772 NU.nl articles**, the project identifies articles containing Dutch location mentions and classifies **12,225 location-linked articles** for six news values:

- entertainment
- bad news
- magnitude
- good news
- celebrity
- power elite

The final GIS-ready dataset contains **19,774 article-location rows** and **880 unique geocoded location points**.

> **Important data note:** some files in this repository contain full or partial NU.nl article text. These files are included intentionally for thesis transparency and reproducibility. Some files contain full or partial NU.nl article text. The article texts remain copyrighted by NU.nl and should not be reused, redistributed, or republished outside the context of inspecting and reproducing this thesis workflow.

## Research Question

**How are news values geographically distributed in Dutch digital journalism?**

## Method

The project uses a mixed computational and interpretive workflow consisting of four main stages.

### 1. Article Extraction and Location Matching

NU.nl articles exported from Nexis were converted into structured article tables. Dutch location mentions were extracted from article text using the Dutch spaCy model `nl_core_news_lg`. Candidate locations were matched against an OpenStreetMap-derived lookup file and linked to latitude and longitude coordinates.

The output of this stage includes article-level metadata, location mentions, geocoded article-location rows, and diagnostic files for excluded or duplicate rows.

### 2. LLM-Assisted News Value Classification

Location-linked articles were classified for six selected news values using the open-weight instruction model `Qwen/Qwen2-1.5B-Instruct` in Google Colab.

Each article was classified on a three-level scale:

| Score | Meaning |
|---:|---|
| `0` | Not meaningfully present |
| `50` | Present, but secondary |
| `100` | Strongly present / central |

The model was prompted to return structured JSON output containing one score per news value, dominant news values, and a short explanation.

### 3. Calibration and Validation

Because the raw model output under- or over-detected some categories, a benchmark prevalence calibration step was applied using Harcup and O’Neill’s updated news value taxonomy as a reference point.

A manual validation sample of 300 articles was coded using the same 0/50/100 scoring scheme. The validation compares raw and calibrated model scores using exact accuracy, mean absolute error, Cohen’s kappa, precision, recall, and F1 score.

The calibrated scores are used as the main analytical version for GIS mapping.

### 4. GIS Preparation and Spatial Analysis

The calibrated article-level news value scores were merged with geocoded article-location rows. This created the main GIS-ready dataset, where each row represents one Dutch location mentioned in one classified article.

The GIS workflow was carried out in QGIS and included:

- overall location mention heatmaps
- mention-count maps by location
- dominant news value maps by point, municipality, and province
- present-share maps by municipality
- mean-score maps by municipality
- contrast maps
- overrepresentation maps

Municipality and province aggregations were created using both mention-weighted and location-weighted approaches.

## Results

The results show that NU.nl’s location-linked coverage is spatially uneven. Coverage is concentrated in major urban and institutionally visible places, especially:

- Amsterdam
- Rotterdam
- The Hague
- Utrecht
- Groningen
- Eindhoven

Bad news is the dominant form of spatial visibility across much of the dataset. However, secondary patterns also appear. Entertainment is associated with places connected to sport, culture, and events. Magnitude becomes visible in high-impact locations, while power elite is associated with places connected to government, courts, police, and other institutions. Good news appears more selectively and should be interpreted cautiously because it had weaker validation performance.

Overall, the thesis shows that GIS and LLM-assisted annotation can be combined to analyze not only where news refers to places, but also how those places become newsworthy.

## Repository Structure

```text
Thesis_Digital_Humanities/
│
├── README.md
├── requirements.txt
├── .gitignore
│
├── thesis/
│   └── Pfauder-Maartje_S2882507_Thesis-Submission.pdf
│
├── notebooks/
│   ├── news_reader_for_news_values.ipynb
│   ├── qwen2_news_value_assignment.ipynb
│   └── qwen2_validation.ipynb
│
├── data/
│   │
│   ├── derived/
│   │   ├── articles_final.csv
│   │   ├── articles_for_classification.csv
│   │   ├── articles_for_classification_with_locations_only.csv
│   │   ├── article_id_mapping.csv
│   │   ├── article_news_values.csv
│   │   ├── article_news_values_raw_outputs.csv
│   │   ├── article_news_values_calibrated_to_harcup_oneill.csv
│   │   ├── corpus_score_summary_calibrated_and_raw.csv
│   │   └── news_value_calibration_summary.csv
│   │
│   ├── gis/
│   │   ├── locations_final.csv
│   │   ├── location_mentions_for_gis.csv
│   │   ├── location_mentions_with_calibrated_news_values_for_gis.csv
│   │   ├── location_news_value_summary_by_location_calibrated.csv
│   │   └── osm.json
│   │
│   ├── validation/
│   │   ├── manual_validation_raw_vs_calibrated_metrics.csv
│   │   ├── manual_validation_raw_vs_calibrated_comparison.csv
│   │   ├── manual_validation_confusion_matrices_raw_vs_calibrated.csv
│   │   ├── manual_validation_article_level_disagreements.csv
│   │   ├── manual_fewshot_examples.csv
│   │   └── manual_validation_sample.csv
│   │
│   ├── diagnostics/
│   │   ├── diagnostic_duplicate_article_id_rows.csv
│   │   ├── diagnostic_duplicate_article_location_rows.csv
│   │   └── diagnostic_excluded_non_netherlands_location_rows.csv
│   │
│   ├── pilot/
│   │   ├── pilot_article_news_values.csv
│   │   ├── pilot_article_news_values_calibrated_to_harcup_oneill.csv
│   │   ├── pilot_news_value_calibration_summary.csv
│   │   ├── smoke_test_article_news_values_calibrated_to_harcup_oneill.csv
│   │   └── smoke_test_news_value_calibration_summary.csv
│   │
│   └── README_data.md
│
├── qgis/
│   ├── project/
│   │   └── thesis_news_values_maps.qgz
│   └── exports/
│       ├── Figure5.1.png
│       ├── Figure5.2.png
│       ├── Figure5.3.png
│       ├── Figure5.4.png
│       ├── Figure5.5.png
│       ├── Figure5.6.png
│       ├── Figure5.7.png
│       ├── Figure5.8.png
│       ├── AttachmentA1.png
│       ├── AttachmentB1.png
│       └── AttachmentC1.png
│
└── docs/
    ├── data_dictionary.md
    ├── reproducibility_notes.md
    └── copyright_and_data_availability.md
```

## Files Included

### Thesis

`thesis/Pfauder-Maartje_S2882507_Thesis-Submission.pdf`  
Full thesis submission describing the theoretical background, methodology, validation, GIS workflow, results, discussion, limitations, and conclusion.

### Notebooks

`notebooks/news_reader_for_news_values.ipynb`  
Converts Nexis-exported NU.nl files into structured article and location tables. Performs article extraction, location matching, geocoding preparation, and diagnostic checks.

`notebooks/qwen2_news_value_assignment.ipynb`  
Runs the Qwen2 news value classification workflow. Creates raw article-level news value scores, parses structured model output, performs smoke and pilot tests, and applies benchmark calibration.

`notebooks/qwen2_validation.ipynb`  
Validates calibrated and raw classification outputs against manual coding. Builds validation metrics, comparison tables, disagreement files, and GIS-ready export files.

### Derived Data

`data/derived/articles_final.csv`  
Structured article table created from the original Nexis exports. This file is used as a main intermediate article-level dataset and may contain full article text.

`data/derived/articles_for_classification.csv`  
Article-level dataset prepared as input for the news value classification workflow. This file may contain the text fields passed to the model.

`data/derived/articles_for_classification_with_locations_only.csv`  
Classification input subset containing only articles with matched Dutch location mentions.

`data/derived/article_id_mapping.csv`  
Mapping between stable numeric article IDs and original article/file metadata.

`data/derived/article_news_values.csv`  
Raw article-level news value classification output.

`data/derived/article_news_values_raw_outputs.csv`  
Raw model outputs and parsing diagnostics from the classification process.

`data/derived/article_news_values_calibrated_to_harcup_oneill.csv`  
Main calibrated article-level news value dataset used for analysis.

`data/derived/corpus_score_summary_calibrated_and_raw.csv`  
Corpus-level comparison of raw and calibrated news value score distributions.

`data/derived/news_value_calibration_summary.csv`  
Summary of benchmark calibration targets and resulting calibrated prevalence.

### GIS Data

`data/gis/locations_final.csv`  
Geocoded article-location table before final calibration merge.

`data/gis/location_mentions_for_gis.csv`  
GIS-ready article-location mention table containing article metadata, location names, and coordinates.

`data/gis/location_mentions_with_calibrated_news_values_for_gis.csv`  
Main GIS-ready dataset. Each row represents one article-location combination with calibrated news value scores and diagnostic classification information.

`data/gis/location_news_value_summary_by_location_calibrated.csv`  
Location-level summary table with mention counts, mean scores, present shares, strong shares, sum scores, and dominant news value categories.

`data/gis/osm.json`  
OpenStreetMap-derived location lookup used for matching Dutch place names to coordinates.

### Validation Outputs

`data/validation/manual_validation_raw_vs_calibrated_metrics.csv`  
Validation metrics for raw and calibrated model scores.

`data/validation/manual_validation_raw_vs_calibrated_comparison.csv`  
Side-by-side comparison of raw and calibrated model performance by news value.

`data/validation/manual_validation_confusion_matrices_raw_vs_calibrated.csv`  
Confusion matrix data comparing manual labels with raw and calibrated model scores.

`data/validation/manual_validation_article_level_disagreements.csv`  
Article-level disagreement file for inspecting where the model and manual validation differed. This file may contain article text or classification input text, so it should be checked before being made public.

`data/validation/manual_fewshot_examples.csv`  
Manually selected few-shot examples used in the prompt design for the LLM-assisted classification step.

`data/validation/manual_validation_sample.csv`  
Manual validation sample used to compare human-coded scores with raw and calibrated model scores.

### Diagnostics

`data/diagnostics/diagnostic_duplicate_article_id_rows.csv`  
Diagnostic output for duplicate article IDs.

`data/diagnostics/diagnostic_duplicate_article_location_rows.csv`  
Diagnostic output for duplicate article-location-coordinate rows.

`data/diagnostics/diagnostic_excluded_non_netherlands_location_rows.csv`  
Rows excluded because their coordinates fell outside the European Netherlands bounding box.

### Pilot and Smoke Test Outputs

`data/pilot/smoke_test_article_news_values_calibrated_to_harcup_oneill.csv`  
Small smoke-test output used to check model loading, prompt formatting, parsing, and calibration logic.

`data/pilot/smoke_test_news_value_calibration_summary.csv`  
Calibration summary for the smoke test.

`data/pilot/pilot_article_news_values.csv`  
Pilot classification output for 150 articles.

`data/pilot/pilot_article_news_values_calibrated_to_harcup_oneill.csv`  
Calibrated pilot classification output.

`data/pilot/pilot_news_value_calibration_summary.csv`  
Calibration summary for the pilot run.

## Installation

Install the required Python packages with:

```bash
pip install -r requirements.txt
```

The notebooks were developed in Google Colab. When running the Qwen2 classification notebook, a GPU runtime is recommended. The full Qwen2 inference step can also be skipped if the saved CSV outputs are reused.

## Reproducibility Notes

The full workflow can be inspected through the notebooks. Complete reproduction of the extraction and classification process requires access to the source/intermediate article files.

Recommended workflow order:

1. Run `notebooks/news_reader_for_news_values.ipynb` to process articles and extract locations.
2. Run `notebooks/qwen2_news_value_assignment.ipynb` to classify article news values and apply calibration.
3. Run `notebooks/qwen2_validation.ipynb` to validate raw and calibrated scores and prepare GIS-ready files.
4. Open the QGIS project or import the GIS-ready CSV files into QGIS for spatial analysis.
