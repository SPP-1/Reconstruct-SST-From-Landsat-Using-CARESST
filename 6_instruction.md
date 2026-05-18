# Script Overview

This script is used to bulk download NOAA/STAR GHRSST Level-3S VIIRS PM SST netCDF data from NASA Earthdata / PO.DAAC for a specified AOI and date range.

The script searches data month by month, downloads all matching VIIRS L3S PM files, and saves the raw `.nc` files into a structured output directory.

## Workflow

### The workflow automatically:

1. Builds a square AOI bounding box from user-defined lon/lat points
2. Adds a small geographic buffer around the AOI
3. Authenticates with NASA Earthdata
4. Searches VIIRS L3S PM SST data from PO.DAAC
5. Loops through the full date range month by month
6. Downloads all matching raw netCDF files
7. Saves files into monthly folders

## Required Libraries

Install required Python libraries:

```
pip install earthaccess shapely
```
## NASA Earthdata Login
The script requires a NASA Earthdata account.
NASA Earthdata accounts are free and provide access to:

- PO.DAAC
- GHRSST
- MODIS
- Landsat
- OSTIA
- MUR 
- VIIRS
- Many other NASA Earth science datasets
Steps
1. Open the NASA Earthdata registration page:

[NASA Earthdata Registration Page
](https://urs.earthdata.nasa.gov/profile)

2. Click:
```
Register for a Profile
```
3. Fill in the required information:
- Username
- Password
- Email
- Country
- Affiliation
- Organization (optional)

4. Submit registration
5. Check your email inbox

NASA will send an activation email.

6. Click the activation link
7. Log into Earthdata:

Earthdata Login Page

After activation, your account is ready to use.
### Useful links:

<img width="1024" height="131" alt="image" src="https://github.com/user-attachments/assets/c62a2419-f888-420c-ab73-b54fb546b8ca" />

https://github.com/nasa/gesdisc-tutorials/blob/main/notebooks/How_to_Generate_Earthdata_Prerequisite_Files.ipynb
https://urs.earthdata.nasa.gov/documentation/for_users/data_access/python_download_script
https://urs.earthdata.nasa.gov/documentation/for_users

## Main Inputs

The main user settings are located in the ```USER CONFIG``` section.

Key inputs include:
