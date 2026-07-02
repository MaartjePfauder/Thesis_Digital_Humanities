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
```

These files contain:
- full NU.nl article body text


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
