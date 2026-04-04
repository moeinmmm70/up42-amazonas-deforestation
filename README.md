<p align="center">
  <img src="./docs/screenshots/banner.webp" alt="Monitoring Amazonas Deforestation banner" width="100%" />
</p>

<h1 align="center">Monitoring Amazonas Deforestation with UP42 and Sentinel-2</h1>

<p align="center">
  A compact geospatial workflow for catalog search, STAC-based asset retrieval, and NDVI change detection in the Amazonas region.
</p>

<p align="center">
  <img src="https://img.shields.io/badge/Python-3.13-blue?style=for-the-badge&logo=python" alt="Python 3.13" />
  <img src="https://img.shields.io/badge/Geospatial-UP42-0A7B83?style=for-the-badge" alt="UP42" />
  <img src="https://img.shields.io/badge/Data-Sentinel--2-2E8B57?style=for-the-badge" alt="Sentinel-2" />
  <img src="https://img.shields.io/badge/Method-NDVI-556B2F?style=for-the-badge" alt="NDVI" />
  <img src="https://img.shields.io/badge/Status-Portfolio%20Project-8A2BE2?style=for-the-badge" alt="Portfolio Project" />
</p>

## Quick Navigation

- [Overview](#overview)
- [What is UP42](#what-is-up42)
- [Why Sentinel-2](#why-sentinel-2)
- [Why NDVI](#why-ndvi)
- [Why STAC](#why-stac)
- [Repository Structure](#repository-structure)
- [Workflow Summary](#workflow-summary)
- [Setup](#setup)
- [Run the Notebook](#run-the-notebook)
- [Expected Outputs](#expected-outputs)
- [Notes and Practical Considerations](#notes-and-practical-considerations)
- [Author](#author)

# Monitoring Amazonas Deforestation with UP42 and Sentinel-2 🌿🛰️

## Overview

This repository contains a Jupyter Notebook that demonstrates a repeatable geospatial workflow for monitoring potential deforestation in the Amazonas region using **Sentinel-2 imagery** through the **UP42 platform**.

The notebook was developed as part of a **Technical Support Engineer challenge** and is designed to reflect a realistic customer-support workflow:

1. authenticate with the UP42 API  
2. define an Area of Interest (AOI) in Amazonas  
3. search the UP42 catalog for Sentinel-2 imagery  
4. filter scenes by date range and cloud coverage  
5. place data orders for selected scenes  
6. inspect delivered outputs using **STAC**  
7. download and extract raster files  
8. compute **NDVI** for two timestamps  
9. generate an **NDVI difference map** to highlight potential vegetation change  

This project is intentionally compact and readable. The goal is not only to produce a result, but also to make the workflow easy to understand, review, and adapt.

---

## What is UP42?

[UP42](https://www.up42.com/) is a geospatial platform that provides programmatic access to satellite imagery, geospatial processing, and data management workflows through APIs and SDKs.

In this project, UP42 is used for:

- searching satellite imagery in the catalog
- ordering selected scenes
- accessing delivered outputs through STAC-compatible storage
- supporting a reproducible workflow from discovery to analysis

This notebook focuses on the customer-facing logic of how imagery is discovered, ordered, retrieved, and processed.

---

## Why Sentinel-2?

This project uses **Sentinel-2 Level-2A** imagery.

Sentinel-2 is a strong choice for vegetation monitoring because it provides:

- multispectral optical imagery
- global coverage
- frequent revisit times
- 10 m resolution bands for visible and near-infrared analysis
- a free and well-established dataset for environmental applications

For this notebook, the most important bands are:

- **B04** → Red
- **B08** → Near Infrared (NIR)

These two bands are used to compute **NDVI**.

---

## Why NDVI?

The **Normalized Difference Vegetation Index (NDVI)** is a simple and widely used indicator of vegetation condition.

It is calculated as:

```text
NDVI = (NIR - Red) / (NIR + Red)
```
In practice:

- higher NDVI values often indicate denser or healthier vegetation
- lower NDVI values may indicate sparse vegetation, bare ground, or disturbed land

To compare two points in time, the notebook also computes:

```text
NDVI Difference = NDVI(Date 2) - NDVI(Date 1)
```

This helps highlight areas where vegetation may have decreased or changed over time.

---

## Why STAC?

This repository also demonstrates the use of **STAC** (SpatioTemporal Asset Catalog), which is important for working with delivered geospatial assets in a structured way.

In the workflow, STAC is used to:

- find the storage outputs related to placed orders
- inspect item metadata
- inspect available assets
- retrieve the original delivery for local raster analysis

This is a key part of the project because it connects the catalog-ordering stage to the actual downloadable analysis files.

---

## Repository Structure

```text
up42-amazonas-deforestation/
├── README.md
├── requirements.txt
├── .gitignore
├── .env.example
├── notebook/
│   └── amazonas_deforestation_up42.ipynb
├── src/
│   └── helpers.py
├── data/
│   ├── aoi/
│   │   └── amazonas_aoi.geojson
│   └── downloads/
├── outputs/
│   ├── figures/
│   └── logs/
└── docs/
    └── screenshots/
```

### Main files

- `notebook/amazonas_deforestation_up42.ipynb`  
  Main notebook containing the complete workflow

- `data/aoi/amazonas_aoi.geojson`  
  The AOI polygon used for catalog search and ordering

- `outputs/figures/`  
  Saved figures such as NDVI maps and the NDVI difference map

- `.env.example`  
  Template showing how to define local UP42 credentials

- `requirements.txt`  
  Python dependencies for the project

---

## Workflow Summary

The notebook follows these main steps:

### 1. Authentication
The UP42 Python SDK is authenticated using credentials stored in a local `.env` file.

### 2. AOI Loading
A small AOI polygon in Amazonas is loaded from a GeoJSON file.

### 3. Product Discovery
The notebook inspects the UP42 product glossary, selects the **Sentinel-2** collection, and identifies the **sentinel-2-level-2a** data product.

### 4. Catalog Search
The workflow searches for scenes that intersect the AOI and match the selected:
- date range
- maximum cloud coverage threshold

### 5. Scene Selection
Two scenes are selected from the filtered search results for two-timestamp comparison.

### 6. Order Placement
An order is created for each selected scene using the UP42 SDK.

### 7. Order Tracking
The notebook waits until each order is fulfilled.

### 8. STAC Inspection
The delivered outputs are located in UP42 storage and inspected using STAC.

### 9. Download and Extraction
The original delivery files are downloaded and extracted locally.

### 10. Raster Processing
Band 4 (red) and Band 8 (NIR) are located and read using `rasterio`.

### 11. NDVI Calculation
NDVI is computed separately for each timestamp.

### 12. Change Detection
An NDVI difference map is created to highlight potential vegetation change.

### 13. Visualization
The notebook saves and displays:
- NDVI for Date 1
- NDVI for Date 2
- NDVI difference map

---

## Setup

### 1. Clone the repository

```bash
git clone https://github.com/YOUR_USERNAME/up42-amazonas-deforestation.git
cd up42-amazonas-deforestation
```

### 2. Create and activate a virtual environment

```bash
python3 -m venv .venv
source .venv/bin/activate
```

### 3. Install dependencies

```bash
pip install --upgrade pip
pip install -r requirements.txt
```

---

## Credentials with `.env` 🔐

This project uses a local `.env` file for UP42 credentials.

Create a file called `.env` in the project root:

```bash
touch .env
```

Then open it:

```bash
nano .env
```

Paste your credentials in this format:

```bash
UP42_USERNAME=your_up42_username_here
UP42_PASSWORD=your_up42_password_here
```

Save and exit.

### Important
- Do **not** commit your real `.env` file to GitHub
- The repository includes `.env.example` as a safe template
- Make sure `.env` is listed in `.gitignore`

---

## Run the Notebook

Start Jupyter Notebook from the project root:

```bash
jupyter notebook
```

Then open:

```bash
notebook/amazonas_deforestation_up42.ipynb
```

Run the notebook from top to bottom.

---

## Expected Outputs

The notebook produces:

- a clean table of filtered Sentinel-2 scenes
- placed and tracked UP42 orders
- STAC item and asset inspection output
- downloaded and extracted raster deliveries
- NDVI for two timestamps
- an NDVI difference map
- saved figure files in `outputs/figures/`

Typical figure outputs:

- `ndvi_date1.png`
- `ndvi_date2.png`
- `ndvi_difference.png`

---

## Notes and Practical Considerations

This repository is a **prototype workflow**, not a full production monitoring pipeline.

A few important limitations:

- scene-level cloud filtering does not guarantee cloud-free pixels inside the AOI
- two-date NDVI differencing is useful as a quick indicator, but changes may also reflect seasonality, water dynamics, or residual atmospheric effects
- production workflows should add cloud masking, scene-quality checks, and more robust alignment handling
- some UP42 orders may require prior EULA acceptance in the UP42 Console before order placement can succeed

These limitations are part of real-world geospatial support and are discussed in the notebook.

---

## Dependencies

Main Python packages used in this repository:

- `up42-py`
- `numpy`
- `pandas`
- `matplotlib`
- `rasterio`
- `geopandas`
- `geojson`
- `python-dotenv`

---

## Suggested Publishing Checklist ✅

Before publishing the repository:

- make sure the notebook runs from top to bottom
- confirm `.env` is not committed
- confirm `.env.example` is included
- confirm output figures are saved
- clean up overly noisy debug cells
- add a few screenshots to `docs/screenshots/` if helpful
- commit and push the final version

---

## License

This repository is shared for demonstration and portfolio purposes.  
Please review UP42 data usage terms and provider-specific conditions before reusing or extending the workflow with other datasets.

---

## Author

**Moein Mellat**  
GitHub: [moeinmmm70](https://github.com/moeinmmm70)  
Email: [moein.mellat@gmail.com](moein.mellat@gmail.com)

Prepared as part of a UP42 Technical Support Engineer challenge.
