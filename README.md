<p align="center">
  <img src="./docs/screenshots/banner.jpg" alt="Monitoring Amazonas Deforestation banner" width="100%" />
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

## Overview

This repository contains a Jupyter Notebook developed for a Technical Support Engineer challenge. It shows a clear end-to-end workflow for vegetation-change screening:

- authenticate with the UP42 SDK
- search the catalog for Sentinel-2 scenes over an AOI
- filter results by date range and cloud coverage
- place and track orders for selected scenes
- inspect fulfilled outputs through STAC
- download and extract the original delivery
- compute NDVI for two timestamps
- generate an NDVI difference map

The notebook is designed as a compact, readable prototype rather than a production monitoring system.

## Why this project

The goal of the exercise was not only to produce a change map, but to demonstrate a realistic customer-support workflow around the UP42 ecosystem:

- data discovery
- order management
- STAC-based asset inspection
- raster-level processing
- clear troubleshooting logic

## Repository structure

```text
up42-amazonas-deforestation/
├── README.md
├── requirements.txt
├── .gitignore
├── .env.example
├── notebook/
│   └── amazonas_deforestation_up42.ipynb
├── data/
│   ├── aoi/
│   │   └── amazonas_aoi.geojson
│   └── downloads/
├── outputs/
│   └── figures/
└── docs/
    └── screenshots/
```

## Workflow summary

### 1. Authentication
The notebook authenticates with UP42 using credentials stored in a local `.env` file.

### 2. Catalog search
A small AOI in the Amazonas is loaded from GeoJSON, and the UP42 catalog is searched for Sentinel-2 Level-2A scenes within a defined date range and cloud-coverage threshold.

### 3. Scene selection
Two representative scenes are selected for downstream comparison.

### 4. Order placement and tracking
Orders are created through the UP42 SDK and tracked until fulfillment.

### 5. STAC inspection
Fulfilled outputs are located in storage through the UP42 STAC client, and item metadata and assets are inspected before download.

### 6. Raster processing
The original deliveries are downloaded, extracted locally, and the red and NIR bands are read with `rasterio`.

### 7. NDVI and change detection
NDVI is calculated for both timestamps, and an NDVI difference map is created to highlight potential vegetation change.

## Why Sentinel-2 and NDVI

This project uses Sentinel-2 Level-2A imagery because it is well suited for vegetation analysis and provides the red and near-infrared bands needed for NDVI.

```text
NDVI = (NIR - Red) / (NIR + Red)
```

The notebook also computes:

```text
NDVI Difference = NDVI(Date 2) - NDVI(Date 1)
```

This is used as a simple screening indicator for possible vegetation change.

## Why STAC

A key part of the notebook is the use of STAC to bridge the ordering and analysis steps. STAC is used to:

- locate fulfilled outputs in storage
- inspect item metadata
- inspect available assets
- retrieve the original delivery for local processing

## Setup

### 1. Install dependencies

```bash
pip install --upgrade pip
pip install -r requirements.txt
```

### 2. Create a local `.env` file

Create a file named `.env` in the project root:

```bash
UP42_USERNAME=your_up42_username_here
UP42_PASSWORD=your_up42_password_here
```

The repository includes `.env.example` as a safe template.

## Expected outputs

The notebook generates:

- a filtered scene metadata table
- STAC item and asset inspection output
- NDVI maps for two timestamps
- an NDVI difference map
- saved figures in `outputs/figures/`

Typical output files:

- `ndvi_date1.png`
- `ndvi_date2.png`
- `ndvi_difference.png`

## Notes

This repository is a prototype workflow. A production-ready monitoring pipeline would require additional steps such as:

- AOI-specific cloud masking
- more robust scene-quality checks
- denser temporal analysis
- stronger handling of seasonal effects and residual atmospheric differences

The notebook also notes a practical platform consideration: some orders may require prior EULA acceptance in the UP42 Console before order placement can succeed.

## Dependencies

Main packages used:

- `up42-py`
- `numpy`
- `pandas`
- `matplotlib`
- `rasterio`
- `geopandas`
- `geojson`
- `python-dotenv`

## Author

**Moein Mellat**  
GitHub: [moeinmmm70](https://github.com/moeinmmm70)
Email: [moein.mellat@gmail.com](mailto:moein.mellat@gmail.com)
