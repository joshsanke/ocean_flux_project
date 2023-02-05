# Ocean_Flux_Project
This contains information on where data can be sourced and the user instructions for running this code.

***

## Data
The data for the relevant variables can be found at the following links (as of August 2021): <br>
Reanalysed SOCAT: https://doi.pangaea.de/10.1594/PANGAEA.939233 <br>
Sea Surface Temperature - https://cds.climate.copernicus.eu/cdsapp#!/dataset/satellite-sea-surface-temperature <br>
Chlorophyll-a Concentration - https://data.ceda.ac.uk/neodc/esacci/ocean_colour/data/v5.0-release/geographic/netcdf/chlor_a/monthly/v5.0 <br>
Sea Surface Salinity - https://resources.marine.copernicus.eu/product-detail/GLOBAL_MULTIYEAR_PHY_001_030/INFORMATION <br>
Mixed Layer Depth - http://apdrc.soest.hawaii.edu/erddap/search/index.html?page=1\&itemsPerPage=1000\&searchFor=ecco2 <br>
Dry Mixing Ratio of CO2 (xCO2) - https://gml.noaa.gov/ccgg/mbl/index.html <br>
Bathymetry (sub ice topo/bathy) - https://www.gebco.net/data_and_products/gridded_bathymetry_data/ <br>
Longhurst Province shapefile - https://www.marineregions.org/gazetteer.php?p=details\&id=22538 <br>
Wind Speed (10m above surface) - https://cds.climate.copernicus.eu/cdsapp#!/dataset/reanalysis-era5-single-levels?tab=form <br>
Air Pressure (at sea level) - https://psl.noaa.gov/data/gridded/data.ncep.reanalysis.html <br>
Ice Coverage - https://www.metoffice.gov.uk/hadobs/hadisst/data/download.html <br>

***

## User Instructions
The project code is broken up into 3 folders: <br>
+**Project** - This is where raw data is collected/manipulated. Also temporally and spatially matches data, along with producing a dataframe for training the neural network. <br>
+**NN_train** - This is where the code for training the neural network is stored, along with the output scalers, models, and model performance. Also there is code to make gridded estimates of pCO2. <br>
+**FE_RUN** - is the code to produce data files for use with FluxEngine, including auxiliary files, input files, and configuration files. Also stored is the FluxEngine outputs, along with code to view and analyse the results. <br>
