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
* magnitude moved closer to the manually observed prevalence
