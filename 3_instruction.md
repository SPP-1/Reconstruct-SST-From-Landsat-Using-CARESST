# Script Overview
This script automatically searches historical Landsat scenes and finds the best “lag scenes” (reference scenes) based on ocean cloud coverage (```CC_OCEAN```) for later SST reconstruction, temporal augmentation, or cloud-filling workflows.
The workflow supports:

1. AOI-based processing (POLY)
2. Whole-scene processing (entire Landsat footprint)

The script uses:

- Google Earth Engine (GEE)
- Landsat Collection 2 TOA
- QA_PIXEL cloud masking
- Ocean masking
- Temporal ranking

## Purpose
The goal of this script is to automatically identify the best historical Landsat scenes that:

- have very low cloud coverage over the ocean,
- match the same WRS path/row,
- are temporally related to the target scene

## Input

The script requires:

# 1. Extracted Landsat Scene Folders #

Example:
```
SCENES_DIR = Path(
    r"Y:/Mingyue/Timor_part1/landsat_c2_l2_extracted"
)
```
Folder examples:
```
LC08_L2SP_110066_20200113_...
LC08_L2SP_110066_20200214_...
```
The script automatically parses:

    - sensor (LC08 / LC09)
    - WRS path/row
    - acquisition date
from the folder names.
# 2. Google Earth Engine #
You must authenticate Earth Engine first:
```
import ee
ee.Initialize()
```
## Two Processing Modes
### Option 1 — AOI-based Processing
Use this mode if you only care about a specific region.
The script then computes cloud coverage only inside the AOI.

This is useful when:

- studying a local SST region,
- ignoring clouds outside the study area,
- focusing on coastal waters.
### Option 2 — Whole Scene Processing
Use this mode if you want to process the entire Landsat scene.

## Cell2 - Batch Export Cloud / Shadow / Land Masks
This cell processes all Landsat scenes and exports a 3-class cloud-shadow-land mask to Google Drive using Google Earth Engine.
The workflow automatically:

1. Resolves each Landsat scene in GEE
2. Builds water/ocean masks
3. Adds historical lag scenes (```FIXED_LAGS```)
4. Detects clouds using multitemporal spectral differences
5. Detects cloud shadows
6. Combines results into a final 3-class mask
7. Exports masks to Google Drive
8. 
### Purpose

The goal of this workflow is to generate high-quality masks for:

    - cloud pixels
    - cloud shadow pixels
    - land pixels

for later SST reconstruction and CARE-SST workflows.

The final masks are used to:

    - isolate valid ocean SST pixels,
    - remove cloud contamination,
    - generate cloud-free SST products,
    - support temporal augmentation pipelines.
# Note / Current Limitation

One limitation of the current workflow is that the cloud and shadow masks may not fully remove:

    - cloud-edge contamination,
    - thin clouds,
    - haze,
    - semi-transparent cirrus,
    - or SST anomalies caused by nearby cloud influence.

As a result, some cloud-affected pixels may still remain in the final SST products, particularly around cloud boundaries. These contaminated regions can introduce unrealistic temperature artifacts and may affect later reconstruction or temporal augmentation steps.

# The generated masks and exported products will be downloaded from Google Drive using Colab after the Earth Engine export tasks are completed.
