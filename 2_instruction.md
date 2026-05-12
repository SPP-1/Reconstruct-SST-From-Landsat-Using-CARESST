# Script Overview
This script batch-processes extracted Landsat 8/9 Collection 2 Level-2 scenes and converts the thermal band ```ST_B10``` into sea surface temperature in Celsius. It also applies the Landsat ```QA_PIXEL``` cloud/shadow mask before saving the cleaned SST GeoTIFF outputs.

## The workflow automatically:
1. Finds all extracted Landsat scene folders.
2. Searches each scene folder for:
    - ```ST_B10```
    - ```QA_PIXEL```
    - optional ```ST_QA```
3. Converts ```ST_B10``` from scaled Kelvin to Celsius.
4. Uses ```QA_PIXEL``` bits to remove:
    - clouds
    - cloud shadows
    - cirrus
    - dilated clouds
5. Optionally applies ```ST_QA``` filtering.
6. Saves the masked SST raster as a GeoTIFF.
7. Writes a processing summary CSV.

## Main Inputs
|Variable|Description|
|-------|--------|
|SCENES_DIR|	Folder containing extracted Landsat scene folders |	
|OUT_SST_DIR| Folder where SST GeoTIFF outputs will be saved |	
|ST_B10.TIF|	Landsat surface temperature band |
|QA_PIXEL.TIF |	Quality band used to mask clouds, shadows, cirrus, and dilated cloud pixels|
|ST_QA.TIF |	Optional uncertainty/quality filter|	

## Main Outputs
The script saves one SST GeoTIFF per Landsat scene:
```
landsat_sst_outputs/
    LC08_..._SST_C.tif
    LC09_..._SST_C.tif
```
It also saves a summary CSV:
```
landsat_sst_outputs/sst_batch_summary.csv
```
The summary records processing status, valid-pixel percentage, SST statistics, and output paths.
