# Data Dictionary

This document describes the main datasets included in the public repository for the thesis **“The geography of news values in Dutch digital journalism: Mapping newsworthiness in NU.nl coverage.”**

The public repository uses cleaned and derived data files. The original NU.nl/Nexis article texts are not redistributed. If local filenames contain suffixes such as `(1)`, `(2)`, or `(3)`, rename them before uploading to GitHub so that the repository uses stable filenames.

## Scoring System

The six classified news values are:

- `entertainment`
- `bad_news`
- `magnitude`
- `good_news`
- `celebrity`
- `power_elite`

Each news value is scored on a three-level scale:

| Score | Meaning |
|---:|---|
| `0` | Not meaningfully present |
| `50` | Present, but secondary |
| `100` | Strongly present or central |

Columns ending in `_score` contain the calibrated score unless the column name starts with `raw_`. Columns starting with `raw_` contain the original uncalibrated model output.

## Common Columns

These columns appear in several datasets.

| Column | Description |
|---|---|
| `article_id` | Stable numeric identifier assigned to each article for analysis. |
| `article_id_original` | Original article identifier used before numeric ID assignment, usually based on the article title or source filename. |
| `file` | Local file path used during the original processing workflow. This may be machine-specific and is not needed for most analysis. |
| `file_name` | Original source filename. In the raw workflow this often refers to a Nexis-exported DOCX file. |
| `title` | Article title. |
| `date` | Article publication date as exported from Nexis. |
| `subjects` | Subject metadata exported from Nexis. Multiple subjects may be separated by semicolons. |
| `location` | Dutch place name matched in the article. |
| `lat` | Latitude of the matched location. |
| `lon` | Longitude of the matched location. |
| `model_name` | Name of the language model used for classification. |
| `prompt_type` | Prompting strategy used for classification, for example `few-shot`. |
| `fewshot_examples_used` | Number of few-shot examples included in the prompt. |
| `body_char_limit` | Maximum number of article body characters passed to the model. |
| `parse_ok` | Indicates whether the model output was successfully parsed. |
| `parse_error` | Parsing error message, if parsing failed. |
| `dominant_news_values` | Dominant news value label or labels assigned to the article. |
| `dominant_news_values_json` | JSON version of the dominant news value labels. |
| `short_reason` | Short model-generated explanation for the classification. This should be checked for copyrighted article excerpts before public release. |
| `classification_missing` | Indicates whether calibrated classification information was missing after merging. |

## Article ID Mapping

### `data/derived/article_id_mapping.csv`

Maps the original article identifiers to stable numeric IDs.

| Column | Description |
|---|---|
| `article_id` | Stable numeric article identifier used across datasets. |
| `article_id_original` | Original identifier before numeric ID assignment. |
| `file_name` | Original DOCX filename from the local processing workflow. |
| `title` | Article title. |
| `date` | Publication date as exported from Nexis. |

## Article-Level News Value Files

### `data/derived/article_news_values.csv`

Raw parsed article-level news value classification output. This file contains the model scores before calibration.

| Column | Description |
|---|---|
| `entertainment_score` | Raw entertainment score. |
| `bad_news_score` | Raw bad news score. |
| `magnitude_score` | Raw magnitude score. |
| `good_news_score` | Raw good news score. |
| `celebrity_score` | Raw celebrity score. |
| `power_elite_score` | Raw power elite score. |
| `dominant_news_values` | Dominant news value label or labels selected from the raw output. |
| `dominant_news_values_json` | JSON representation of dominant news value labels. |
| `short_reason` | Short model-generated reason for the classification. |
| `parse_ok` | Whether the model output was parsed successfully. |
| `parse_error` | Parsing error message, if applicable. |
| `article_id` | Stable article identifier. |
| `title` | Article title. |
| `date` | Publication date. |
| `subjects` | Nexis subject metadata. |
| `classification_text_char_count` | Number of characters in the classification input text. |
| `body_char_limit` | Character limit applied to article body text before classification. |
| `model_name` | Model used for classification. |
| `prompt_type` | Prompting strategy. |
| `fewshot_examples_used` | Number of few-shot examples used. |

### `data/derived/article_news_values_calibrated_to_harcup_oneill.csv`

Main calibrated article-level classification file used for analysis. Calibrated scores are stored in the standard `*_score` columns. Original raw scores are preserved in `raw_*_score` columns.

Additional columns compared with `article_news_values.csv`:

| Column | Description |
|---|---|
| `raw_entertainment_score` | Original uncalibrated entertainment score. |
| `raw_bad_news_score` | Original uncalibrated bad news score. |
| `raw_magnitude_score` | Original uncalibrated magnitude score. |
| `raw_good_news_score` | Original uncalibrated good news score. |
| `raw_celebrity_score` | Original uncalibrated celebrity score. |
| `raw_power_elite_score` | Original uncalibrated power elite score. |

### `data/derived/article_news_values_raw_outputs.csv`

Stores the raw model response before parsing into score columns.

| Column | Description |
|---|---|
| `article_id` | Stable article identifier. |
| `model_name` | Model used for classification. |
| `body_char_limit` | Character limit applied to article body text. |
| `prompt_type` | Prompting strategy. |
| `fewshot_examples_used` | Number of few-shot examples used. |
| `raw_output` | Raw text output returned by the model. |
| `parse_ok` | Whether the raw output was parsed successfully. |
| `parse_error` | Parsing error message, if applicable. |

### `data/derived/corpus_score_summary_calibrated_and_raw.csv`

Corpus-level summary comparing raw and calibrated score distributions.

| Column | Description |
|---|---|
| `score_set` | Indicates whether the row describes raw or calibrated scores. |
| `news_value` | News value category. |
| `n_articles` | Number of articles included in the summary. |
| `mean_score` | Mean score for the news value. |
| `present_count` | Number of articles with score `50` or `100`. |
| `present_share` | Share of articles with score `50` or `100`. |
| `strong_count` | Number of articles with score `100`. |
| `strong_share` | Share of articles with score `100`. |
| `score_0_count` | Number of articles scored `0`. |
| `score_50_count` | Number of articles scored `50`. |
| `score_100_count` | Number of articles scored `100`. |

### `data/derived/news_value_calibration_summary.csv`

Summary of the benchmark prevalence calibration process.

| Column | Description |
|---|---|
| `news_value` | News value category. |
| `benchmark_count` | Count for the news value in the benchmark distribution. |
| `benchmark_n` | Total number of benchmark observations. |
| `target_share` | Target prevalence share derived from the benchmark. |
| `target_n_for_this_sample` | Target number of present cases for the thesis sample. |
| `raw_present_n` | Number of present cases in the raw model output. |
| `calibrated_present_n` | Number of present cases after calibration. |
| `calibrated_present_share` | Present-share after calibration. |
| `selected_from_raw_zero_n` | Number of cases selected from raw-zero scores during calibration. |

## GIS Files

### `data/gis/locations_final.csv`

Article-location rows before merging with calibrated news value scores.

| Column | Description |
|---|---|
| `article_id` | Stable article identifier. |
| `file` | Local source file path from the original workflow. |
| `file_name` | Original source filename. |
| `title` | Article title. |
| `date` | Publication date. |
| `subjects` | Nexis subject metadata. |
| `location` | Matched Dutch place name. |
| `lat` | Latitude of the matched location. |
| `lon` | Longitude of the matched location. |

### `data/gis/location_mentions_for_gis.csv`

GIS-ready article-location mention table. Each row represents one article-location combination.

This file uses the same columns as `locations_final.csv`.

### `data/gis/location_mentions_with_calibrated_news_values_for_gis.csv`

Main GIS-ready dataset used for spatial analysis. Each row represents one article-location combination with calibrated article-level news value scores attached.

| Column | Description |
|---|---|
| `article_id` | Stable article identifier. |
| `article_id_original` | Original article identifier. |
| `file` | Local source file path from the original workflow. |
| `file_name` | Original source filename. |
| `title` | Article title. |
| `date` | Publication date. |
| `subjects` | Nexis subject metadata. |
| `location` | Matched Dutch place name. |
| `lat` | Latitude of the matched location. |
| `lon` | Longitude of the matched location. |
| `entertainment_score` | Calibrated entertainment score. |
| `bad_news_score` | Calibrated bad news score. |
| `magnitude_score` | Calibrated magnitude score. |
| `good_news_score` | Calibrated good news score. |
| `celebrity_score` | Calibrated celebrity score. |
| `power_elite_score` | Calibrated power elite score. |
| `raw_entertainment_score` | Raw entertainment score before calibration. |
| `raw_bad_news_score` | Raw bad news score before calibration. |
| `raw_magnitude_score` | Raw magnitude score before calibration. |
| `raw_good_news_score` | Raw good news score before calibration. |
| `raw_celebrity_score` | Raw celebrity score before calibration. |
| `raw_power_elite_score` | Raw power elite score before calibration. |
| `dominant_news_values` | Dominant news value label or labels. |
| `dominant_news_values_json` | JSON version of dominant news value labels. |
| `short_reason` | Short model-generated reason for the classification. |
| `parse_ok` | Whether the model output was parsed successfully. |
| `parse_error` | Parsing error message, if applicable. |
| `model_name` | Model used for classification. |
| `body_char_limit` | Character limit applied before classification. |
| `prompt_type` | Prompting strategy. |
| `fewshot_examples_used` | Number of few-shot examples used. |
| `classification_missing` | Indicates whether classification values were missing after merging. |

### `data/gis/location_news_value_summary_by_location_calibrated.csv`

Location-level summary table for calibrated scores. Each row represents one unique geocoded location.

| Column | Description |
|---|---|
| `location` | Matched Dutch place name. |
| `lat` | Latitude of the location. |
| `lon` | Longitude of the location. |
| `mention_count` | Number of article-location mentions for this location. |
| `{news_value}_score_mean` | Mean calibrated score for a news value at this location. |
| `{news_value}_score_present_share` | Share of mentions where the news value score is `50` or `100`. |
| `{news_value}_score_strong_share` | Share of mentions where the news value score is `100`. |
| `{news_value}_score_sum` | Sum of calibrated scores for the news value at this location. |
| `dominant_news_values` | Dominant news value or values for the location. |
| `dominant_map_category` | Simplified dominant category used for mapping. |

The `{news_value}` placeholder refers to:

- `entertainment`
- `bad_news`
- `magnitude`
- `good_news`
- `celebrity`
- `power_elite`

### `data/gis/osm.json`

OpenStreetMap-derived location lookup used for matching Dutch place names to coordinates. This file contains OSM elements and should be treated according to OpenStreetMap’s data license terms.

## Validation Files

### `data/validation/manual_validation_raw_vs_calibrated_metrics.csv`

Validation metrics for raw and calibrated classifications.

| Column | Description |
|---|---|
| `score_set` | Indicates whether the row refers to raw or calibrated scores. |
| `news_value` | News value category. |
| `n` | Number of validation cases. |
| `exact_score_accuracy` | Share of exact matches on the 0/50/100 scale. |
| `mean_absolute_error` | Mean absolute difference between manual and predicted scores. |
| `cohen_kappa_3level` | Cohen’s kappa for three-level score agreement. |
| `manual_mean_score` | Mean manual score. |
| `predicted_mean_score` | Mean predicted score. |
| `manual_present_share` | Manual share with score `50` or `100`. |
| `predicted_present_share` | Predicted share with score `50` or `100`. |
| `manual_strong_share` | Manual share with score `100`. |
| `predicted_strong_share` | Predicted share with score `100`. |
| `present_accuracy` | Accuracy after converting scores to present/absent. |
| `present_precision` | Precision for present/absent classification. |
| `present_recall` | Recall for present/absent classification. |
| `present_f1` | F1 score for present/absent classification. |
| `present_tp` | True positives for present/absent classification. |
| `present_tn` | True negatives for present/absent classification. |
| `present_fp` | False positives for present/absent classification. |
| `present_fn` | False negatives for present/absent classification. |
| `strong_accuracy` | Accuracy after converting scores to strong/not strong. |
| `strong_precision` | Precision for strong/not strong classification. |
| `strong_recall` | Recall for strong/not strong classification. |
| `strong_f1` | F1 score for strong/not strong classification. |
| `strong_tp` | True positives for strong/not strong classification. |
| `strong_tn` | True negatives for strong/not strong classification. |
| `strong_fp` | False positives for strong/not strong classification. |
| `strong_fn` | False negatives for strong/not strong classification. |

### `data/validation/manual_validation_raw_vs_calibrated_comparison.csv`

Side-by-side comparison of raw and calibrated validation performance.

| Column | Description |
|---|---|
| `news_value` | News value category. |
| `exact_score_accuracy_calibrated` | Exact score accuracy for calibrated scores. |
| `exact_score_accuracy_raw_uncalibrated` | Exact score accuracy for raw scores. |
| `mean_absolute_error_calibrated` | Mean absolute error for calibrated scores. |
| `mean_absolute_error_raw_uncalibrated` | Mean absolute error for raw scores. |
| `cohen_kappa_3level_calibrated` | Cohen’s kappa for calibrated scores. |
| `cohen_kappa_3level_raw_uncalibrated` | Cohen’s kappa for raw scores. |
| `present_f1_calibrated` | Present/absent F1 for calibrated scores. |
| `present_f1_raw_uncalibrated` | Present/absent F1 for raw scores. |
| `present_precision_calibrated` | Present/absent precision for calibrated scores. |
| `present_precision_raw_uncalibrated` | Present/absent precision for raw scores. |
| `present_recall_calibrated` | Present/absent recall for calibrated scores. |
| `present_recall_raw_uncalibrated` | Present/absent recall for raw scores. |
| `manual_present_share_calibrated` | Manual present share used in the calibrated comparison. |
| `manual_present_share_raw_uncalibrated` | Manual present share used in the raw comparison. |
| `predicted_present_share_calibrated` | Predicted calibrated present share. |
| `predicted_present_share_raw_uncalibrated` | Predicted raw present share. |
| `exact_accuracy_difference_calibrated_minus_raw` | Difference in exact accuracy after calibration. |
| `present_f1_difference_calibrated_minus_raw` | Difference in present/absent F1 after calibration. |
| `mae_difference_calibrated_minus_raw` | Difference in mean absolute error after calibration. |

### `data/validation/manual_validation_confusion_matrices_raw_vs_calibrated.csv`

Long-format confusion matrix data.

| Column | Description |
|---|---|
| `score_set` | Raw or calibrated score set. |
| `news_value` | News value category. |
| `manual_score` | Manual validation score. |
| `predicted_score` | Predicted model score. |
| `count` | Number of cases with this manual/predicted score combination. |

### `data/validation/manual_validation_article_level_disagreements_redacted.csv`

Redacted article-level disagreement file. This file should not contain full article text or long article excerpts.

| Column | Description |
|---|---|
| `article_id` | Stable article identifier. |
| `manual_{news_value}_score` | Manual score for a given news value. |
| `{news_value}_score` | Calibrated model score for a given news value. |
| `raw_{news_value}_score` | Raw model score for a given news value. |
| `{news_value}_raw_matches_manual` | Whether the raw model score matches the manual score. |
| `{news_value}_calibrated_matches_manual` | Whether the calibrated model score matches the manual score. |
| `{news_value}_raw_absolute_error` | Absolute error between raw and manual scores. |
| `{news_value}_calibrated_absolute_error` | Absolute error between calibrated and manual scores. |
| `raw_total_absolute_error` | Total absolute error across news values using raw scores. |
| `calibrated_total_absolute_error` | Total absolute error across news values using calibrated scores. |
| `calibration_improved_total_error` | Indicates whether calibration improved total error. |
| `calibration_worsened_total_error` | Indicates whether calibration worsened total error. |

Do not include the `classification_text` column in the public redacted version.

## Diagnostic Files

### `data/diagnostics/diagnostic_duplicate_article_id_rows.csv`

Rows identified as duplicate article IDs during the workflow. A zero-row file means no duplicates were found.

### `data/diagnostics/diagnostic_duplicate_article_location_rows.csv`

Rows identified as duplicate article-location-coordinate combinations. A zero-row file means no duplicates were found.

### `data/diagnostics/diagnostic_excluded_non_netherlands_location_rows.csv`

Location rows excluded because their coordinates fell outside the European Netherlands bounding box.

## Pilot and Smoke Test Files

Pilot and smoke test files have the same structure as the corresponding full classification and calibration files, but they were produced on smaller samples to test the workflow.

| File | Description |
|---|---|
| `data/pilot/smoke_test_article_news_values_calibrated_to_harcup_oneill.csv` | Small smoke-test classification and calibration output. |
| `data/pilot/smoke_test_news_value_calibration_summary.csv` | Calibration summary for the smoke test. |
| `data/pilot/pilot_article_news_values.csv` | Raw pilot classification output. |
| `data/pilot/pilot_article_news_values_calibrated_to_harcup_oneill.csv` | Calibrated pilot classification output. |
| `data/pilot/pilot_news_value_calibration_summary.csv` | Calibration summary for the pilot run. |
