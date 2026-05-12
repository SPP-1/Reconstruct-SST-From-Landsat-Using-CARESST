# Script Overview

This script is used to bulk download Landsat 8/9 Collection 2 Level-2 (.tar) data from the USGS M2M API for a specified WRS Path/Row and date range in cell1. And then cell2 extracts all downloaded Landsat .tar files into individual scene folders.

## Cell1
### The workflow automatically:

1. Searches Landsat scenes from USGS
2. Filters scenes by WRS Path/Row
3. Requests download products
4. Polls the API until files are ready
5. Downloads .tar files in parallel
6. Automatically retries failed or missing downloads

### Required Libraries

Install required Python libraries:
```
pip install pandas requests tqdm
```
### USGS M2M Token Setup
The script requires a USGS M2M API token.

#### Steps
1. Log in to your USGS EROS account
2. Click your username (top-right corner)
3. Open User Profile
4. Go to the M2M Application Token tab
5. Click Generate New Token
6. Copy and save the token securely

You can either:

- paste the token manually when running the script
- or set it as an environment variable:
```
set USGS_M2M_TOKEN=YOUR_TOKEN
```
### Main Inputs - Cell1
The main user settings are located in the ```USER SETTINGS``` section.
Key inputs include:
|Variable|Description|
|-------|--------|
|SITE|	Project/site name |	
|START_DATE|	Start acquisition date |	
|END_DATE|	End acquisition date |
|WRS_PATH |	Landsat WRS path|
|WRS_ROW |	Landsat WRS row|	
|DATASET_NAME|	Landsat dataset|
|SEARCH_MBR|Bounding box used to reduce search size|
|YOUR_USGS_USERNAME| USGS username| 
|YOUR_M2M_TOKEN| USGS API token|
### Main Outputs - Cell1
Downloaded files are saved to:
```
landsat_c2_l2_tar/*.tar
```
The script also saves:
|Output|Description|
|-------|--------|
|manifest_*.json|	Saved scene list |	
|missing_entityIds_*.json|	Missing/failed downloads |	
|retry_report_*.json|	Retry summary report |

## Cell2
### What It Does
- Reads all ```.tar``` files from:
```
TAR_DIR = DOWNLOAD_DIR
```
- Extracts each scene into:
```
../SITE/landsat_c2_l2_extracted/
```
- Uses parallel processing (ThreadPoolExecutor) to speed up extraction.
- Automatically skips scenes that were already extracted.
- Optionally deletes ```.tar``` files after extraction.
