# The geography of news values in Dutch digital journalism

**Mapping newsworthiness in NU.nl coverage**

Master’s thesis in Digital Humanities
University of Groningen
Maartje Pfauder, 2026

## Overview

This repository contains the computational workflow, derived and intermediate datasets, validation outputs, GIS-ready files, QGIS project, and thesis document for the master’s thesis **“The geography of news values in Dutch digital journalism: Mapping newsworthiness in NU.nl coverage.”**

The thesis examines how selected news values are geographically distributed in Dutch digital journalism through a case study of **NU.nl coverage published in 2025**. It combines computational text analysis, location extraction and geocoding, LLM-assisted classification, benchmark prevalence calibration, manual validation, and GIS-based spatial analysis.

The complete source corpus contains **55,772 Dutch-language NU.nl articles** retrieved for the period from 1 January to 31 December 2025. Of these, **12,225 location-linked articles** were classified for six selected news values:

* entertainment
* bad news
* magnitude
* good news
* celebrity
* power elite

The final GIS dataset contains **19,774 article-location rows** and **880 unique geocoded location-coordinate points**.

The project asks not only **where** NU.nl refers to Dutch places, but also **through which forms of newsworthiness those places become visible**.

NU.nl is treated as a case study of a major Dutch digital news platform. The thesis does not claim that the results represent Dutch journalism as a whole or every dimension of newsworthiness.

## Research Question

**How are news values geographically distributed in Dutch digital journalism?**

The question is examined through the geographical distribution of six selected news values in NU.nl’s 2025 location-linked coverage.

## Analytical Design

The workflow connects two units of analysis.

The **unit of classification is the article**. Each classified article receives a score for each of the six news values.

The **primary spatial unit of analysis is the article-location row**. After classification, the article-level news value scores are linked to every retained Dutch location associated with that article.

This means that the maps represent the **co-occurrence of Dutch locations with articles containing particular forms of newsworthiness**. They should not be interpreted as showing that every news value applies independently or equally to every place mentioned in an article. A secondary or incidental location can inherit a news value that primarily concerns another part of the article.

## Method

### 1. Article Extraction and Location Matching

The source material consisted of NU.nl articles exported from Nexis as DOCX files and organised by month.

The extraction workflow converted the articles into structured article-level data containing fields such as:

* article identifiers
* titles
* publication dates
* subject metadata
* body text
* lead text
* text-length indicators
* detected locations
* location counts

Dutch locations were identified in article body text using the Dutch spaCy model:

`nl_core_news_lg`

Candidate entities were retained when:

1. spaCy labelled the entity as a geopolitical entity (`GPE`), and
2. the entity matched a location name in an OpenStreetMap-derived lookup table.

The final location lookup contained **2,435 Dutch location names**.

A preprocessing step was used to reduce false-positive locations caused by football club names containing city names, including expressions such as PSV Eindhoven, Ajax Amsterdam, Feyenoord Rotterdam, FC Groningen, and ADO The Hague.

The location workflow initially produced **19,802 article-location rows**. A geographical bounding box for the European Netherlands was then applied:

* latitude: **50.6–53.7**
* longitude: **3.2–7.3**

This excluded **28 rows** with coordinates outside the defined scope.

After preprocessing and duplicate checks, the final GIS mention dataset contains:

* **19,774 article-location rows**
* **12,225 classified articles**
* **880 unique location-coordinate points**

No duplicate article-location-coordinate rows remained in the final GIS dataset.

### 2. LLM-Assisted News Value Classification

The 12,225 location-linked articles were classified using the open-weight instruction model:

`Qwen/Qwen2-1.5B-Instruct`

The model was run in Google Colab and used as a structured annotation tool rather than as a generative source of research conclusions.

Six news values were classified independently:

| News value    | Operational focus                                                                                                                                                                  |
| ------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Entertainment | Amusement, culture, media, leisure, sport spectacle, light human interest, animals, humour, or entertainment-oriented framing                                                      |
| Bad news      | Harm, loss, death, injury, danger, damage, crime, punishment, crisis, failure, threat, or serious negative consequences                                                            |
| Magnitude     | Scale, large numbers, major financial amounts, broad consequences, national or regional impact, records, or exceptional scale                                                      |
| Good news     | Success, victory, achievement, recovery, rescue, improvement, celebration, breakthrough, positive return, favourable outcome, or a problem being solved                            |
| Celebrity     | Famous or widely recognised individuals whose public profile contributes to the story                                                                                              |
| Power elite   | Powerful actors or institutions such as government, courts, police, ministries, municipalities, regulators, major companies, universities, hospitals, or other major organisations |

The classification was **multi-label**, meaning that several news values could be present in the same article.

Each news value was assigned one of three scores:

| Score | Meaning                                  |
| ----: | ---------------------------------------- |
|   `0` | Absent / not meaningfully present        |
|  `50` | Present but secondary                    |
| `100` | Central to why the article is newsworthy |

The values are numerical encodings of three ordinal categories rather than continuous measurements. A score of 50 does not mean that a news value constitutes 50% of an article.

#### Classification input

For each article, the model received a compact prompt containing:

* numeric article ID
* publication date
* title
* subject metadata
* detected Dutch locations
* text source
* article excerpt

The excerpt was constructed using the lead and body text:

* when the lead was sufficiently informative, it was preferred;
* when the lead was shorter than 600 characters and body text was available, lead and body text were combined;
* when the lead was missing or shorter than 80 characters, body text was used;
* the final classification excerpt was truncated to **750 characters**.

This design prioritised prominent and foregrounded forms of newsworthiness and reduced variation in model input length.

The final prompt contained **four manually coded few-shot examples** demonstrating the intended use of the codebook and the 0/50/100 scoring scheme.

The model returned structured JSON containing:

* one score for each news value
* dominant news value(s)
* a short classification reason

Invalid JSON or invalid score values triggered a retry.

The final classification achieved a **98.8% parse-success rate**. After repeated parsing or validation failures, fallback zero scores were stored so that failed observations remained in the workflow. This affected **147 articles**, corresponding to **186 article-location rows** in the GIS dataset. These cases are technical failures and should not be interpreted as substantive evidence that all six news values were absent.

### 3. Benchmark Prevalence Calibration

The raw Qwen2 output showed substantial under- or over-detection for some categories. Entertainment and celebrity were particularly under-detected, while magnitude was over-detected.

A benchmark prevalence calibration procedure was therefore applied after the raw classifications had been produced.

The benchmark was based on the distribution of selected news values reported by **Harcup and O’Neill (2017)** for newspaper page-lead stories.

The target presence shares were:

| News value    | Benchmark share |
| ------------- | --------------: |
| Bad news      |           62.2% |
| Entertainment |           46.7% |
| Power elite   |           30.4% |
| Magnitude     |           23.2% |
| Celebrity     |           20.4% |
| Good news     |           19.3% |

Calibration was performed separately for each news value.

For article (i) and news value (v), a ranking score was calculated from the original Qwen2 score and the number of predefined calibration keywords found in the article context. The raw Qwen2 score received much greater weight than the keyword count, so the model classification remained the main basis of the ranking.

The highest-ranked articles were selected until the benchmark target for each news value was reached.

Articles with a raw score of `50` or `100` retained that score when selected. An article with a raw score of `0` that entered the calibrated target set was assigned `50`. Calibration could therefore introduce a news value as a secondary feature, but could not create a new strongly present (`100`) case.

Articles falling outside the target set received a calibrated score of `0`.

The raw scores were retained separately so that the spatial effects of calibration could be compared directly with the original model output.

### Interpretation of Calibration

The benchmark is an **external calibration assumption**, not an estimate of the true distribution of news values on NU.nl.

Harcup and O’Neill’s benchmark concerns newspaper page-lead stories, whereas this thesis examines NU.nl articles from 2025. The calibrated prevalence values should therefore be understood as imposed reference targets used to correct clear imbalances in the raw model output.

The substantive analysis focuses on how raw and calibrated classifications compare in validation and on how the two versions produce different geographical patterns after spatial aggregation.

### 4. Manual Validation

A manually coded validation sample of **300 articles** was scored using the same 0/50/100 coding scheme.

Both the raw and calibrated model outputs were compared with the manual annotations.

Validation included:

* exact three-level accuracy
* mean absolute error (MAE)
* Cohen’s kappa
* binary precision
* binary recall
* binary F1 score

For the binary metrics, scores of `50` or `100` were treated as present and `0` as absent.

Across the six news values, the overall results were:

| Metric                 |   Raw | Calibrated |
| ---------------------- | ----: | ---------: |
| Mean exact accuracy    | 0.757 |  **0.764** |
| Mean absolute error    |  15.5 |   **13.4** |
| Mean Cohen’s kappa     | 0.347 |  **0.458** |
| Mean present/absent F1 | 0.517 |  **0.691** |

Calibration therefore slightly improved exact three-level accuracy, reduced average error, and substantially improved binary present/absent classification.

Performance differed between categories. In particular:

* entertainment improved strongly after calibration;
* celebrity improved substantially from severe raw under-detection;
* magnitude moved closer to the manually observed prevalence;
* bad news achieved very high calibrated recall but lower precision;
* good news remained the weakest calibrated category, with an F1 score of **0.532**, and should therefore be interpreted more cautiously.

Because the calibrated scores performed better overall against the manual sample, they are treated as the **preferred analytical representation**. The raw scores remain part of the analysis as a comparison condition.

### 5. GIS Preparation and Spatial Analysis

Article-level news value scores were merged with the geocoded article-location data.

The detailed GIS-ready layer contains one row per article-location combination and was used for:

* overall location-density analysis
* mention-weighted municipality aggregation
* mention-weighted province aggregation
* raw and calibrated present-share calculations
* raw and calibrated mean-score calculations

Parallel location-level summaries were also produced. These contain, for each unique location-coordinate combination:

* mention count
* mean score
* present share
* strong share
* sum score
* dominant news value

The point data were imported into QGIS using longitude and latitude in **WGS 84** and subsequently reprojected to:

**Amersfoort / RD New (`EPSG:28992`)**

Municipality and province boundary layers were obtained from the **PDOK/Kadaster Bestuurlijke Gebieden** service and used for spatial aggregation.

## GIS Map Set

The final thesis reports the following spatial visualisations.

### Baseline visibility

* overall heatmap of article-location rows
* mention count by unique geocoded location

These maps show where NU.nl’s retained Dutch location references are concentrated before news values are considered.

### Dominant news value by location

Location-level mean scores were used to identify the news value with the highest average score at each unique geocoded location.

Raw and calibrated location profiles were compared.

### Dominant news value by municipality

Municipal scores were calculated using **mention-weighted aggregation**.

Each article-location row contributes one observation. Locations that occur more frequently in NU.nl coverage therefore have proportionally greater influence on the municipal average.

Both raw and calibrated versions were analysed.

Municipal dominance maps were restricted to municipalities with at least **10 article-location observations**, producing **223 municipalities** in the comparison.

### Dominant news value by province

Province-level dominance was also calculated using **mention-weighted aggregation** for all twelve provinces.

Both raw and calibrated results were compared.

### No-bad-news dominance

Because bad news frequently dominates the full results, additional raw and calibrated dominance maps were produced in which bad news was excluded from the set of candidate dominant values.

These maps **do not remove articles containing bad news**. They only prevent bad news from being selected as the dominant category, allowing the secondary geography of the remaining five news values to become visible.

### Municipality-level present shares

Present-share maps were created independently for each of the six news values.

Scores of `50` or `100` were converted to present and scores of `0` to absent. The resulting municipal value represents the proportion of article-location observations in which that news value was present.

Both raw and calibrated present-share maps use the same minimum threshold of **10 article-location observations per municipality**.

## Results

### Uneven geographical visibility

NU.nl’s location-linked coverage is spatially uneven and concentrated in major urban and institutionally visible locations.

The ten most frequently represented locations in the final GIS dataset are:

| Location   | Article-location count |
| ---------- | ---------------------: |
| Amsterdam  |                  2,335 |
| Rotterdam  |                  1,482 |
| The Hague  |                  1,452 |
| Utrecht    |                    842 |
| Groningen  |                    651 |
| Eindhoven  |                    610 |
| Heerenveen |                    472 |
| Arnhem     |                    390 |
| Tilburg    |                    282 |
| Breda      |                    281 |

Amsterdam, Rotterdam, The Hague, Utrecht, Groningen, and Eindhoven are especially prominent in the national geography of NU.nl coverage.

### Raw and calibrated news values

The raw Qwen2 classifications produced a substantially different overall distribution from the calibrated version.

| News value    | Raw present share | Calibrated present share |
| ------------- | ----------------: | -----------------------: |
| Bad news      |             29.1% |                    62.2% |
| Entertainment |             12.6% |                    46.7% |
| Power elite   |             28.6% |                    30.4% |
| Magnitude     |             36.6% |                    23.2% |
| Celebrity     |              3.5% |                    20.4% |
| Good news     |             12.3% |                    19.3% |

The calibrated percentages correspond to the benchmark targets by construction. Their importance lies in how calibration changes which articles receive a positive classification and how those classifications subsequently alter the mapped spatial patterns.

### Bad news as the dominant geography

Bad news is the most common dominant category in the municipality-level results both before and after calibration, but calibration strengthens this pattern.

Among the **223 municipalities** included in the analysis:

| Condition                 | Bad news | Entertainment | Good news | Magnitude | Power elite | Celebrity |
| ------------------------- | -------: | ------------: | --------: | --------: | ----------: | --------: |
| Raw full dominance        |      164 |            20 |        16 |        22 |           1 |         0 |
| Calibrated full dominance |      188 |            27 |         6 |         2 |           0 |         0 |

The calibrated geography is therefore considerably more strongly dominated by bad news.

### Secondary geography changes after calibration

The effect of calibration becomes especially visible when bad news is excluded from the dominance calculation.

In the raw results:

* magnitude dominates **153 of 223 municipalities**;
* good news dominates 25;
* power elite dominates 23;
* entertainment dominates 20;
* celebrity dominates 2.

After calibration:

* entertainment becomes dominant in **111 municipalities**;
* power elite becomes dominant in 56;
* magnitude falls to 40;
* good news falls to 14;
* celebrity remains dominant in 2.

The dominant secondary geography therefore changes from being largely **magnitude-oriented** in the raw output to being primarily structured around **entertainment, power elite, and magnitude** after calibration.

### Province-level patterns

At province level, bad news dominates **11 of the 12 provinces** in both the raw and calibrated full-dominance results.

The exception changes:

* in the raw results, **Groningen** is magnitude dominant;
* after calibration, Groningen becomes bad-news dominant and **Fryslân** becomes entertainment dominant.

When bad news is excluded:

* magnitude dominates all **12 provinces** in the raw results;
* entertainment dominates **10 of 12 provinces** after calibration;
* Groningen and Zeeland remain magnitude dominant.

Province aggregation therefore produces more homogeneous patterns than municipality aggregation, while still showing a clear effect of calibration.

### Present-share patterns

The municipality-level present-share maps show that several news values coexist within the same places even when one category dominates the municipal mean.

After calibration:

* bad news has the strongest and most widespread geographical presence;
* entertainment becomes substantially more widespread;
* power elite remains comparatively stable;
* celebrity and good news become more visible but remain less prominent;
* magnitude becomes less geographically pronounced than in the raw results.

These comparisons show that calibration changes not only numerical prevalence but also the geographical patterns that emerge from the classification.

## Main Conclusion

The thesis finds that the geography of NU.nl coverage is uneven not only in **how much attention places receive**, but also in **the forms of newsworthiness through which they become visible**.

Bad news provides the strongest overall geography of visibility, while entertainment, magnitude, good news, celebrity, and power elite produce additional spatial patterns.

The project demonstrates how LLM-assisted annotation and GIS can be combined to analyse news values as spatially distributed features of published digital news. At the same time, the strong differences between raw and calibrated results demonstrate that spatial interpretations produced through computational classification are sensitive to methodological choices.

The maps should therefore be understood as analytical representations produced through a sequence of article extraction, classification, calibration, geocoding, aggregation, and visualisation decisions rather than as neutral representations of an underlying geography.

## Limitations

The main limitations of the workflow are:

### Article-level classification

News values were assigned at article level and then inherited by every retained location associated with the article. This can attach a news value to an incidental location even when the value primarily concerns another part of the article.

### Excerpt-based classification

The model classified excerpts rather than complete articles. News values appearing later in an article may therefore be missed or receive less emphasis.

### Benchmark calibration

Calibration introduces an external assumption about how common the six news values should be. The Harcup and O’Neill benchmark was derived from newspaper page-lead stories rather than NU.nl coverage and should not be interpreted as the true prevalence of these values on NU.nl.

### Classification uncertainty

Performance differs between news values. In particular, good news has weaker validation performance and should be interpreted more cautiously.

### Location extraction and geocoding

Place-name recognition and coordinate matching are imperfect. Locations may be missed, ambiguous names may be resolved incorrectly, and administrative areas may be represented by a single coordinate.

### Administrative names represented as points

A reference to an administrative area such as a province can be stored as a single coordinate and subsequently assigned to one municipality during a spatial join, even though the textual reference concerns a much larger area.

### Mention-weighted aggregation

Every article-location row contributes one observation to municipality and province aggregation. Frequently mentioned places consequently have more influence than locations appearing only occasionally.

### Dominance maps

Reducing six independent news-value scores to one dominant category simplifies multidimensional patterns, particularly where average scores are close.

### Municipality threshold

The main municipality-level dominance and present-share maps include only municipalities with at least 10 article-location observations. This improves stability and comparability but remains an analytical choice.

### Model-output parsing failures

The workflow contains 147 articles for which repeated parsing or validation failures resulted in fallback zero scores. These affect 186 article-location rows and should be treated as technical failures rather than genuine all-zero classifications.

### Restricted news value taxonomy

The project operationalises six of Harcup and O’Neill’s fifteen news values. Other categories, including conflict, relevance, follow-up, surprise, exclusivity, shareability, audio-visuals, drama, and the news organisation’s agenda, are outside the scope of the analysis.

The strongest conclusions therefore concern **aggregate spatial patterns**, rather than individual articles, individual locations, or the complete structure of journalistic newsworthiness.

## Repository Structure

```text
Thesis_Digital_Humanities/
│
├── README.md
│
├── thesis/
│   └── Pfauder-Maartje_S2882507_Thesis-Resit-Submission.pdf
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
│   └── pilot/
│       ├── pilot_article_news_values.csv
│       ├── pilot_article_news_values_calibrated_to_harcup_oneill.csv
│       ├── pilot_news_value_calibration_summary.csv
│       ├── smoke_test_article_news_values_calibrated_to_harcup_oneill.csv
│       └── smoke_test_news_value_calibration_summary.csv
│
└── qgis/
    ├── project/
    │   └── thesis_news_values_maps.qgz
    └── exports/
        └── exported thesis maps and GIS figures
```

## Files Included

### Thesis

`thesis/Pfauder-Maartje_S2882507_Thesis-Resit-Submission.pdf`

Final master’s thesis describing the theoretical background, methodology, validation procedure, GIS workflow, results, discussion, limitations, and conclusion.

### Notebooks

#### `notebooks/news_reader_for_news_values.ipynb`

Processes the Nexis-exported NU.nl material and constructs the article and location datasets.

The workflow includes:

* article extraction
* stable article-ID creation
* location recognition
* OpenStreetMap lookup matching
* coordinate preparation
* duplicate checks
* exclusion diagnostics
* creation of article-location data

#### `notebooks/qwen2_news_value_assignment.ipynb`

Runs the Qwen2 classification workflow.

The notebook includes:

* model loading
* prompt construction
* structured JSON output
* score validation
* retry logic
* smoke testing
* 150-article pilot classification
* full article classification
* benchmark prevalence calibration
* raw/calibrated comparison outputs

#### `notebooks/qwen2_validation.ipynb`

Evaluates the raw and calibrated classifications against the manually coded validation data and prepares downstream comparison and GIS files.

Outputs include:

* validation metrics
* confusion matrices
* raw/calibrated comparisons
* article-level disagreement diagnostics
* GIS-ready merged data
* summary tables

## Derived Data

### `data/derived/articles_final.csv`

Structured article-level dataset created from the Nexis exports. Depending on the repository version, this file may contain full or partial article text.

### `data/derived/articles_for_classification.csv`

Article-level data prepared for the classification workflow, including fields used to construct model inputs.

### `data/derived/articles_for_classification_with_locations_only.csv`

Classification subset containing articles with retained Dutch location references.

### `data/derived/article_id_mapping.csv`

Mapping between stable numeric article identifiers and the corresponding source metadata.

### `data/derived/article_news_values.csv`

Raw article-level Qwen2 news value classification output.

### `data/derived/article_news_values_raw_outputs.csv`

Raw model responses and parsing-related diagnostic information.

### `data/derived/article_news_values_calibrated_to_harcup_oneill.csv`

Article-level dataset containing the benchmark-calibrated news value scores used as the preferred analytical version.

### `data/derived/corpus_score_summary_calibrated_and_raw.csv`

Summary comparing raw and calibrated news value distributions.

### `data/derived/news_value_calibration_summary.csv`

Summary of benchmark targets and the resulting calibrated prevalence.

## GIS Data

### `data/gis/locations_final.csv`

Geocoded article-location data generated during the location-processing workflow.

### `data/gis/location_mentions_for_gis.csv`

GIS-ready article-location table containing article metadata, retained location names, and coordinates.

### `data/gis/location_mentions_with_calibrated_news_values_for_gis.csv`

Main GIS-ready article-location dataset combining location information with classified news value data.

### `data/gis/location_news_value_summary_by_location_calibrated.csv`

Calibrated location-level summary containing measures such as:

* mention count
* mean scores
* present shares
* strong shares
* sum scores
* dominant news value

### `data/gis/osm.json`

OpenStreetMap-derived Dutch location lookup used to match retained place names to coordinates.

## Validation Outputs

### `data/validation/manual_validation_raw_vs_calibrated_metrics.csv`

Validation metrics comparing raw and calibrated model scores with manual annotations.

### `data/validation/manual_validation_raw_vs_calibrated_comparison.csv`

Side-by-side comparison of raw and calibrated classification performance by news value.

### `data/validation/manual_validation_confusion_matrices_raw_vs_calibrated.csv`

Confusion-matrix data for the three-level manual, raw, and calibrated classifications.

### `data/validation/manual_validation_article_level_disagreements.csv`

Article-level comparison data for inspecting cases in which model output differs from manual annotation.

### `data/validation/manual_fewshot_examples.csv`

Manually coded examples used to construct the few-shot classification prompt. The final prompt uses four examples.

### `data/validation/manual_validation_sample.csv`

The manually coded 300-article validation sample used to evaluate the raw and calibrated classification outputs.

## Diagnostics

### `data/diagnostics/diagnostic_duplicate_article_id_rows.csv`

Diagnostic output used to inspect duplicate article identifiers.

### `data/diagnostics/diagnostic_duplicate_article_location_rows.csv`

Diagnostic output used to inspect duplicate article-location-coordinate rows.

### `data/diagnostics/diagnostic_excluded_non_netherlands_location_rows.csv`

The 28 article-location rows excluded because their coordinates fell outside the European Netherlands bounding box.

## Pilot and Smoke-Test Outputs

### `data/pilot/smoke_test_article_news_values_calibrated_to_harcup_oneill.csv`

Small-scale output used to verify model loading, prompt execution, structured parsing, and classification logic.

### `data/pilot/smoke_test_news_value_calibration_summary.csv`

Calibration summary for the smoke-test output.

### `data/pilot/pilot_article_news_values.csv`

Raw output from the **150-article pilot classification**.

### `data/pilot/pilot_article_news_values_calibrated_to_harcup_oneill.csv`

Calibrated version of the pilot classification.

### `data/pilot/pilot_news_value_calibration_summary.csv`

Calibration summary for the pilot run.

## Reproducing the Workflow

The workflow is organised in the same order as the thesis methodology.

1. Run `notebooks/news_reader_for_news_values.ipynb` to process the Nexis-exported NU.nl articles, extract Dutch locations, match coordinates, and construct the article-location datasets.

2. Run `notebooks/qwen2_news_value_assignment.ipynb` to classify the location-linked articles using Qwen2 and produce both raw and benchmark-calibrated news value scores.

3. Run `notebooks/qwen2_validation.ipynb` to compare raw and calibrated classifications with the manual validation sample and construct the final comparison and GIS-ready files.

4. Open `qgis/project/thesis_news_values_maps.qgz` or import the GIS-ready CSV files into QGIS to reproduce or inspect the spatial analysis.

Reproducing the complete extraction workflow requires access to the original Nexis-exported source material.

## Data and Copyright Note

Some intermediate, derived, or validation files may contain full or partial NU.nl article text or article excerpts because these fields were used during extraction, classification, validation, and diagnostic inspection.

Copyright in the original NU.nl articles remains with the relevant rights holders. Inclusion of research data or article excerpts in this workflow does not transfer rights to the underlying journalistic content.

Users of the repository are responsible for respecting the applicable terms governing the original source material.

## Use of ChatGPT

ChatGPT was used during the development of the project as **coding and writing support**.

It assisted with implementation tasks including:

* intermediate batch saving and resume-safe processing
* article-ID matching and join logic
* prompt construction
* JSON parsing and retry logic
* model-loading code
* benchmark calibration code
* GIS table rebuilding
* aggregation logic
* validation metric functions
* reshaping diagnostic comparison files
* revision of wording and structure in parts of the thesis text

ChatGPT was **not** used as the article-level news value classifier.

The article classifications analysed in the thesis were produced with **Qwen/Qwen2-1.5B-Instruct**.

Substantive decisions concerning the research design, operational definitions, manual validation, interpretation of the results, and final thesis text were checked and approved by the author.

## Citation

Pfauder, Maartje. 2026. *The geography of news values in Dutch digital journalism: Mapping newsworthiness in NU.nl coverage*. Master’s thesis, Digital Humanities, University of Groningen.
