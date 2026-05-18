# Script Overview [skip this step if there is no need for whole scene landsat process in the future]

This script checks whether a VIIRS L3S netCDF file spatially covers a Landsat SST scene, including optional pixel-based buffers around the Landsat footprint.

It is useful before cropping VIIRS data or using VIIRS as background guidance for Landsat / CARE-SST reconstruction.

## Workflow

### The workflow automatically:

1. Reads the Landsat SST GeoTIFF
2. Converts the Landsat bounds from its native CRS to EPSG:4326
3. Reads longitude and latitude variables from the VIIRS netCDF file
4. Converts VIIRS longitude from `0–360` to `-180–180` if needed
5. Computes the VIIRS spatial extent
6. Converts Landsat pixel buffers into longitude/latitude buffers
7. Checks whether the VIIRS file fully covers the Landsat footprint
8. Reports missing directions if coverage is incomplete

## Required Libraries

Install required Python libraries:

```
pip install numpy rasterio xarray
```
## Main Inputs

The main user settings are located near the top of the script.

Key inputs include:
|Variable|Description|
|-------|--------|
|LANDSAT_TIF|Landsat SST GeoTIFF used as the reference footprint|
|VIIRS_NC|VIIRS L3S netCDF file to check|
|BUFFER_LIST|List of pixel buffers to test around the Landsat footprint|

## Main Outputs

The script prints:
|Output|Description|
|-------|--------|
|Landsat bounds|Landsat footprint in EPSG:4326|
|VIIRS NC bounds|VIIRS netCDF spatial extent|
|Required bounds|Landsat footprint plus buffer|
|Coverage result|Whether VIIRS fully covers the required area|
|Missing directions|WEST / EAST / SOUTH / NORTH if coverage is incomplete|
### Example Console Output
```
LANDSAT bounds:
west : 124.13763656435658
south: -9.738524571993645
east : 126.22954328226425
north: -7.619802939799301
/home/mingyue/apps/miniforge3/envs/sst/lib/python3.12/site-packages/pyproj/network.py:59: UserWarning: pyproj unable to set PROJ database path.
  _set_context_ca_bundle_path(ca_bundle_path)

VIIRS NC bounds:
west : -179.99000549316406
south: -89.98999786376953
east : 179.99000549316406
north: 89.98999786376953

------------------------------------------------------------
BUFFER = 0px
Required:
124.13763656435658 -9.738524571993645 126.22954328226425 -7.619802939799301
Covered by VIIRS NC? True

------------------------------------------------------------
BUFFER = 10px
Required:
124.13489523724624 -9.741251018567052 126.23228460937459 -7.617076493225894
Covered by VIIRS NC? True

------------------------------------------------------------
BUFFER = 20px
Required:
124.13215391013588 -9.743977465140459 126.23502593648494 -7.614350046652487
Covered by VIIRS NC? True
```
## How to Interpret the Result
If the output says:
```
Covered by VIIRS NC? True
```
then the VIIRS file fully covers the Landsat scene plus the requested buffer.

If the output says:
```
Covered by VIIRS NC? False
Missing EAST
```
then the VIIRS file does not fully cover the requested Landsat area. You may need to:

- use a different VIIRS file,
- download a larger VIIRS crop,
- reduce the buffer,
- or check whether the VIIRS day/night file has different spatial coverage.
