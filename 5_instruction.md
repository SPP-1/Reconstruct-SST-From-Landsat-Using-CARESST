# Script Overview

This script crops all Landsat SST GeoTIFF files to a fixed square AOI and saves the cropped outputs with simplified date-based filenames.

It is mainly used to prepare a smaller, consistent SST study region before later cleaning, reconstruction, validation, or CARE-SST processing.

## Input

The script reads all .tif files from:
```
INPUT_DIR = Path(r"../West_Fl_Shelf/landsat_sst_outputs")
```
These are the Landsat SST outputs generated from the ```2_calculate_sst.ipynb```

## Output

The cropped GeoTIFFs are saved to:
```
OUTPUT_DIR = INPUT_DIR.parent / "chopped_sst"
```
Each output file is renamed using the first date found in the original filename.

## Notes
The AOI points must be in ```[longitude, latitude]``` format.
West longitudes should be negative.
The script automatically handles CRS transformation.
Files without CRS are skipped.
Files without a detectable ```YYYYMMDD``` date in the filename are skipped.
The output keeps the original raster metadata, but updates the cropped size and transform.
