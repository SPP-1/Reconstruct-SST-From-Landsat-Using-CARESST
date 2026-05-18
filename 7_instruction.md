# Script Overview

This script is used to download OSTIA Level-4 global sea surface temperature (SST) data from NASA Earthdata / PO.DAAC for a specified AOI and date range.

The script searches OSTIA data, opens each cloud-hosted netCDF file virtually, crops it to the target bounding box, converts SST from Kelvin to Celsius, and saves each daily subset as a GeoTIFF.

## Workflow

### The workflow automatically:

1. Authenticates with NASA Earthdata
2. Searches OSTIA L4 SST granules from PO.DAAC
3. Uses a user-defined bounding box to subset the dataset
4. Handles longitude convention differences, such as `-180 to 180` vs. `0 to 360`
5. Opens each OSTIA netCDF file virtually without fully downloading the raw file first
6. Extracts `analysed_sst`
7. Converts SST from Kelvin to Celsius
8. Applies a basic valid SST range filter
9. Saves each daily SST subset as a GeoTIFF

## Required Libraries

Install required Python libraries:

```
pip install earthaccess xarray h5netcdf rasterio tqdm numpy
```
## Main Inputs

The main user settings are located in the ```USER CONFIG``` section.

Key inputs include:
|Variable|Description|
|-------|--------|
|START_DATE|Start date for OSTIA search|
|END_DATE|End date for OSTIA search|
|bbox|Search and crop bounding box in EPSG:4326|
|SHORT_NAME|NASA Earthdata short name for OSTIA|
|OUT_ROOT|Output root directory|
|NODATA|NoData value written to GeoTIFF|

### Bounding Box

The AOI is defined using:
```
bbox = (-160.5, 19.0, -155.5, 24.0)
```
The format is:
```
(west, south, east, north)
```
### Output Directory

GeoTIFF outputs are saved under:
```
OUT_ROOT = Path("Y:/") / "Mingyue" / "Oahu" / "L4_OSTIA_Oahu"
```
The output directory structure is:
```
Y:/Mingyue/Oahu/L4_OSTIA_Oahu/
└── geotiff/
    ├── 2020/
    │   ├── 01/
    │   │   ├── SST_L4_OSTIA_20200101.tif
    │   │   ├── SST_L4_OSTIA_20200102.tif
    │   │   └── ...
    │   ├── 02/
    │   └── ...
    ├── 2021/
    └── ...
```
## Main Outputs 
|Output|Description|
|-------|--------|
|geotiff/YYYY/MM/SST_L4_OSTIA_YYYYMMDD.tif|Daily cropped OSTIA SST GeoTIFF|
|Console logs|Search progress, processing progress, and warning messages|

## GeoTIFF Details

Each output GeoTIFF is written with:
|Property|Value|
|-------|--------|
|CRS|EPSG:4326|
|Data type|float32|
|Unit|Celsius|
|NoData|-9999.0|
|Compression|deflate|
