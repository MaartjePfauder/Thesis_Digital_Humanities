# Copyright and Data Availability

This document explains what data is included in the public repository and what data is excluded for copyright reasons.

## Summary

The original NU.nl article texts are copyrighted and are not redistributed in this repository.

The repository shares code, derived data, validation outputs, diagnostic files, and GIS-ready files so that the workflow can be inspected while respecting copyright restrictions.

This document is not legal advice. It describes the data handling choices made for this thesis repository.

## Data Included in the Public Repository

The public repository may include:

- thesis PDF
- code notebooks
- requirements file
- data dictionary
- reproducibility notes
- derived article-level classification scores
- raw and calibrated score summaries
- validation metrics
- confusion matrix data
- diagnostic files
- geocoded location mention tables
- location-level summary tables
- QGIS project files
- exported maps

These files are intended to document the analytical workflow and results without redistributing the full copyrighted article texts.

## Data Not Included in the Public Repository

The following materials should not be uploaded publicly:

```text
*.docx
original Nexis exports
articles_final.csv
articles_for_classification.csv
articles_for_classification_with_locations_only.csv
manual_fewshot_examples.csv
manual_validation_sample.csv
manual_validation_article_level_disagreements.csv
```

These files may contain:

- full NU.nl article body text
- article lead text
- long excerpts from articles
- classification input text copied from copyrighted articles
- local file paths to raw source documents

They can be stored locally for private reproducibility, but should not be included in the public GitHub repository.

## Redaction Checklist

Before uploading a CSV publicly, check whether it contains any of the following columns:

```text
body_text
lead_text
classification_text
raw article text
full article excerpt
large copied passage from NU.nl
```

If the file contains any of these fields, create a redacted version before upload.

Recommended public replacements:

| Private/local file | Public replacement |
|---|---|
| `articles_final.csv` | Do not upload, or create a metadata-only version without article text. |
| `articles_for_classification.csv` | Do not upload, or create a metadata-only version without article text. |
| `articles_for_classification_with_locations_only.csv` | Do not upload, or create a location-only version without article text. |
| `manual_validation_sample.csv` | Do not upload, or create a redacted validation sample without `classification_text`. |
| `manual_validation_article_level_disagreements.csv` | Upload only as `manual_validation_article_level_disagreements_redacted.csv`. |
| Original Nexis DOCX files | Do not upload. |

## Suggested Redaction Rules

A public CSV version should keep analytical metadata and remove copyrighted text.

Usually safe to keep:

- `article_id`
- `title`
- `date`
- `subjects`
- `location`
- `lat`
- `lon`
- score columns
- validation metrics
- parse diagnostics
- model settings
- aggregate counts and shares

Remove before public upload:

- `body_text`
- `lead_text`
- `classification_text`
- original DOCX contents
- long model explanations that quote or reproduce article text
- any column containing substantial article passages

Short titles and metadata are retained in this repository as analytical metadata. If stricter reuse conditions apply, remove titles as well and rely on `article_id`.

## NU.nl and Nexis Material

The article corpus was based on NU.nl articles exported through Nexis for academic research. The full texts remain copyrighted by their rights holders.

The public repository does not grant permission to reuse or redistribute NU.nl article texts. Anyone who wants to fully reproduce the extraction workflow needs their own lawful access to the source material.

## OpenStreetMap Data

The location lookup file `osm.json` is derived from OpenStreetMap data.

OpenStreetMap data is made available under the Open Database License. When redistributing or adapting OSM-derived data, follow the relevant attribution and license requirements.

Recommended attribution:

```text
Contains information from OpenStreetMap, which is made available under the Open Database License.
```

## GIS Boundary Data

If QGIS project files or exported maps use municipality or province boundary layers from PDOK/Kadaster or other external providers, check and follow the terms of those providers.

Recommended practice:

- mention the source of administrative boundary layers in the README or map captions
- keep source metadata with the QGIS project
- do not imply that third-party geographic data is owned by this repository

## Code License

The code notebooks can be licensed separately from the data.

A common option is to use the MIT License for code. If a license is added, make clear that it applies to the code and repository documentation, not to the original NU.nl article texts or third-party source data.

Example wording:

```text
The code in this repository is made available under the MIT License. The original NU.nl article texts are not included and are not covered by this license. OpenStreetMap and other third-party geographic data remain subject to their own licenses.
```

## Data Citation

When citing this repository or using the derived datasets, cite the thesis and repository together.

Suggested citation format:

```text
Pfauder, M. (2026). The Geography of News Values in Dutch Digital Journalism: Mapping Newsworthiness in NU.nl Coverage. Master’s thesis, University of Groningen.
```

If the GitHub repository is cited, include the repository URL and access date.
