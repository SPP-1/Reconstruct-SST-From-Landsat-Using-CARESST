# Script Overview

This notebook has 3 cells.

Cell 1 and Cell 2 are for processing and checking a **small local area**, smaller than the full Landsat scene.

Cell 3 is separate. It is only needed if we want to continue processing the **whole Landsat scene / full domain**.

## Cell 1: 

This cell is used to crop raw VIIRS L3S STAR netCDF files to a smaller local AOI (local AOI is smaller than landsat area).

It reads all raw VIIRS `.nc` files from a structured input directory, crops each file using a square AOI built from user-defined lon/lat points, and saves the cropped netCDF files while preserving the original monthly folder structure.

This is useful after downloading a large VIIRS area and before converting or using the data for CARE-SST / Landsat-scale workflows.

### Main Inputs

The main user settings are located in the SETTINGS section.

Key inputs include:

|Variable|Description|
|-------|--------|
|INPUT_ROOT|	Root directory containing raw VIIRS netCDF files|
|OUTPUT_ROOT|	Output directory for cropped VIIRS netCDF files|
|BUFFER_DEG|	Geographic buffer added around the AOI|
|points|	AOI control points in lon/lat, EPSG:4326|

## Cell 2:
This cell checks whether all cropped VIIRS netCDF files have the same spatial grid.

It compares every cropped VIIRS file against the first file as a reference and verifies that the longitude grid, latitude grid, and SST data shape are consistent across all dates.

This is useful after cropping raw VIIRS netCDF files to make sure the outputs are spatially consistent before converting them to GeoTIFF or using them in CARE-SST / Landsat-scale reconstruction workflows.


## Cell 3: Whole-Landsat Processing

Cell 3 is separate from Cell 1 and Cell 2.

Only run Cell 3 if we want to crop raw VIIRS L3S STAR netCDF files to the **whole Landsat scene footprint (+20 pixel buffer)** and keeps **daytime files only**.

It reads all downloaded raw VIIRS netCDF files, filters out nighttime files, crops each daytime file to the predefined Landsat scene region, and saves the cropped netCDF files while preserving the original folder structure.

This script is typically used after downloading raw VIIRS data and before using the data as guidance for full-domain CARE-SST reconstruction.

## Workflow

### The workflow automatically:

1. Reads all raw VIIRS netCDF files
2. Keeps only daytime (`_D-ACSPO_`) files  (from extracted landsat folder *_MTL.txt)
3. Uses predefined whole-Landsat scene bounds 
4. Reads longitude and latitude variables
5. Handles both:
   - `lon/lat`
   - `longitude/latitude` (+20px buffer from previous script result 8_*)
6. Converts longitude from `0–360` to `-180–180` if necessary
7. Supports:
   - regular 1D grids
   - curvilinear/swath 2D grids
8. Crops the VIIRS data to the Landsat scene bounds
9. Saves cropped netCDF files
10. Preserves original directory structure

### Required Libraries

Install required Python libraries:

```bash
pip install numpy xarray netcdf4 h5netcdf
```

### Main Inputs

The main settings are located in the `SETTINGS` section.

| Variable | Description |
|---|---|
| `INPUT_ROOT` | Raw VIIRS netCDF directory |
| `OUTPUT_ROOT` | Output cropped VIIRS directory |
| `KEEP_TAG` | Filter used to keep only daytime files |
| `square_min_lon` | West boundary |
| `square_min_lat` | South boundary |
| `square_max_lon` | East boundary |
| `square_max_lat` | North boundary |


### Whole Landsat Scene Bounds

Unlike the local-area crop workflow, this script uses a fixed full-scene crop region (+20px buffer from previous script result 8_*). 

Current bounds:

```python
square_min_lon = 124.13215391013588
square_min_lat = -9.743977465140459
square_max_lon = 126.23502593648494
square_max_lat = -7.614350046652487
```

These represent:

```text
Whole Landsat scene
+ 20 pixel buffer
```

The script prints:

```text
VIIRS crop box:
lon: 124.13215 126.23503
lat: -9.74398 -7.61435
```

### Daytime File Filter

The script only keeps files containing:

```python
KEEP_TAG = "_D-ACSPO_"
```

Example:

Included:

```text
20200113120000-STAR-L3S_GHRSST-SSTsubskin-LEO_PM_D-ACSPO_V2.81-v02.0-fv01.0.nc
```

Excluded:

```text
20200113120000-STAR-L3S_GHRSST-SSTsubskin-LEO_PM_N-ACSPO_V2.81-v02.0-fv01.0.nc
```

where:

| Tag | Meaning |
|---|---|
| `_D_` | Daytime |
| `_N_` | Nighttime |

### Input Directory

Expected input structure:

```text
../Timor_part1/L3S_STAR/raw/
├── 2020-01/
│   └── PM/
│       ├── 20200101....nc
│       ├── 20200102....nc
│       └── ...
├── 2020-02/
│   └── PM/
└── ...
```

### Output Directory

Output structure preserves the original monthly folders:

```text
../Timor_part1/L3S_STAR/cropped/
├── 2020-01/
│   └── PM/
│       ├── 20200101_D.nc
│       ├── 20200102_D.nc
│       └── ...
├── 2020-02/
│   └── PM/
└── ...
```

Output filenames become:

```text
YYYYMMDD_D.nc
```

Example:

```text
20200113_D.nc
```

### Main Outputs

| Output | Description |
|---|---|
| `cropped/YYYY-MM/PM/YYYYMMDD_D.nc` | Cropped daytime VIIRS netCDF |
| `[SAVED]` | File successfully saved |
| `[EXISTS]` | File already exists |
| `[SKIP]` | No overlap or no valid date |
| `[ERROR]` | File processing failed |

### Example Console Output

```text
VIIRS crop box:
lon: 124.13215 126.23503
lat: -9.74398 -7.61435

Keeping only files with:
_D-ACSPO_

Found total VIIRS files: 2192
Found daytime files (_D-ACSPO_): 1096

[SAVED] ../Timor_part1/L3S_STAR/cropped/2020-01/PM/20200113_D.nc

[SAVED] ../Timor_part1/L3S_STAR/cropped/2020-01/PM/20200114_D.nc

================================================================================
Done.

Saved: 1080
Skipped: 16
Failed: 0
```

