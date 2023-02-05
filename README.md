# Ocean_Flux_Project
This contains information on where data can be sourced and the user instructions for running this code.

***

## Data
The data for the relevant variables can be found at the following links (as of August 2021): <br>
Reanalysed SOCAT: https://doi.pangaea.de/10.1594/PANGAEA.939233 <br>
Sea Surface Temperature - https://cds.climate.copernicus.eu/cdsapp#!/dataset/satellite-sea-surface-temperature <br>
Chlorophyll-a Concentration - https://data.ceda.ac.uk/neodc/esacci/ocean_colour/data/v5.0-release/geographic/netcdf/chlor_a/monthly/v5.0 <br>
Sea Surface Salinity - https://resources.marine.copernicus.eu/product-detail/GLOBAL_MULTIYEAR_PHY_001_030/INFORMATION <br>
Mixed Layer Depth - http://apdrc.soest.hawaii.edu/erddap/search/index.html?page=1&itemsPerPage=1000&searchFor=ecco2 <br>
Dry Mixing Ratio of CO2 (xCO2) - https://gml.noaa.gov/ccgg/mbl/index.html <br>
Bathymetry (sub ice topo/bathy) - https://www.gebco.net/data_and_products/gridded_bathymetry_data/ <br>
Longhurst Province shapefile - https://www.marineregions.org/gazetteer.php?p=details&id=22538 <br>
Wind Speed (10m above surface) - https://cds.climate.copernicus.eu/cdsapp#!/dataset/reanalysis-era5-single-levels?tab=form <br>
Air Pressure (at sea level) - https://psl.noaa.gov/data/gridded/data.ncep.reanalysis.html <br>
Ice Coverage - https://www.metoffice.gov.uk/hadobs/hadisst/data/download.html <br>

***

## User Instructions
+ **YAML file** - for producing the conda environment needed to run this code.
The project code is broken up into 3 folders: <br>
+ **Project** - This is where raw data is collected/manipulated and put onto 1x1 degree grids. Also temporally and spatially matches data, along with producing a dataframe for training the neural network. <br>
 + **NN_train** - This is where the code for training the neural network is stored, along with the output scalers, models, and model performance. Also there is code to make gridded estimates of pCO2. <br>
+ **FE_RUN** - is the code to produce data files for use with FluxEngine, including auxiliary files, input files, and configuration files. Also stored is the FluxEngine outputs, along with code to view and analyse the results. <br>

More detailed instructions below. <br>

***

### Folder: Project
This code can mostly be run in any order, but the file *Copy_Data.ipynb* must be run before *NN_DATA* and both these **must** be run after all the other data collection files in this folder. <br>
+ #### BATHYMETRY <br>
   - Requires pre-downloaded data. Should run without issue.
+ #### CHL_DATA <br>
   - Should access data remotely. Error occurred during my download and there is code to fix this - can either be skipped or edited depending on if an error is incurred - and after the fix the climatology is added.
+ #### ICE_DATA <br>
   - Requires pre-downloaded data. Should run without problems. Code is broken into different years (believed due to changing file structures over time).
+ #### MLD_DATA <br>
   - Should access data remotely. I suffered with time-out issues and missing data, therefore if the first section (up to *Merging Separated Data*) doesn't work it's recommended to either edit this code to suit your needs or (probably better) write a new version from scratch. 
+ #### SLP_DATA <br>
   - Requires pre-downloaded data. Should run without issue.
+ #### SOCAT_DATA <br>
   - Requires pre-downloaded data. Should run without issue.
+ #### SSS_DATA <br>
   - Should be accessible remotely but **requires** username and password to Copernicus Climate Data Store (CDS). Also had to be run in separate batches due to time-out issues - follow same advice as for *MLD_DATA*.
+ #### SST_DATA <br>
   - Requires pre-downloaded data due to **very** large file size. Also requires user to point to directory where data is stored, and an additional directory to store monthly mean SST. 
+ #### WIND_DATA <br>
   - Requires pre-downloaded data. Should run without issue.
+ #### XCO2_DATA <br>
   - Requires pre-downloaded data. **Important Note** - this project was intended to use data up to 2021, but xCO2 data was not available. A climatology was produced to use for 2021- this was an *incorrect* process - however due to ease of code it was kept in and no data for 2021 was used for NN training or estimating of pCO2 values. Recommend next person to work on this project resolves this messy fix (sorry!)
+ #### Copy_Data.ipynb <br>
   - Must be run after data collecting code, by before NN_DATA code. Simply copies the produced gridded data into a *data* folder for ease of access.
+ #### NN_DATA <br>
   - Files in this folder must be run last. Files must be run in order of *Convert_to_netCDF.ipynb* -> *Matching_data.ipynb* -> *LHP_management.ipynb*. <br> 
   - *Convert_to_netCDF.ipynb* takes all gridded data files and converts them into a netCDF (*variables.nc*) for ease of use when matching the data. 
   - *Matching_data.ipynb* takes *variables.nc* and finds all variables are present both spatially and temportally, followed by creating a dataframe of these values. 
   - *LHP_management.ipynb* matches the values in the dataframe to their respective Lonhurst Province (requires pre-downloaded data), and then alters these to the next nearest province if the data counts is considered low for that province (produces training data *NN_data_reduced_regions.csv*. Also produces a grid of province values for use when estimating pCO2 values. <br>

***

### Folder: NN_train
This section training the neural networks - there are seperate folders for *with* and *without/no* chlorophyll-a (note: anyone editing this code may consider merging these into one function).<br>
+ *Neural_network.ipynb* and *Neural_network_noChl.ipynb* both train neural networks, but the latter does not include chlorophll-a in the training data. Both files output their respective scalers and models to appropriate folders - labelled *???_WC* or *???_NC* for with and without chlorophyll-a respectively. Also outputs .csv of both model estimates (incorrectly labelled as "Preds_WC(/NC).csv" for "predictions") and model errors. *Neural_network.ipynb* also produces a data set of interquartile ranges exported as *iqr_train.csv*.<br>
+ *pco2_estimation_WC.ipynb* and *pco2_estimation_NC.ipynb* calculate gridded pCO2 estimates without and without chlorophyll-a respectively. There is a line to make sure only data 1990-2020 is used and excludes 2021 for reasons previously mentioned. They produce a flattened .csv and full .nc files. **NOTE**: Run *pco2_estimation_WC.ipynb* first as it is required produce a mask for use in *pco2_estimation_NC.ipynb* (an intersecting data mask was not applied to both estimated data sets but should possibly be considered for anyone working on this).<br>

***

### Folder: FE_RUN
This seciton produces the data required for FluxEngine, and uses auxillary files that are predownloaded (see FluxEngine distribution files *onedeg_land.nc* and *World_Seas-final-complete_IGA.nc*)
- Run *FluxEngine_Prep.ipynb* first to produce netCDF files containing data needed for FluxEngine.
- May require setting up folders *project_withChl* and *project_noChl* wthinin the respective *With_Chlorophyll* and *No_Cholorphyll* directories for FluxEngine to store data in.
- Edit directory paths in *.conf* files for both with and without chlorophyll.
- Running FluxEngine should fill the created folders and produce a *withChl(/noChl)_global.txt* files.
- *project_results.ipynb* uses FluxEngine results and Neural Network training results to produce statistics and plot graphs.









