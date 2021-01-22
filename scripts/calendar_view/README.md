# Calendar view

## JRC Ispra Unit Food security

#### Main author(s): Csaba Wirnhardt

## 1. Introduction
The “**calendar_view**” python library provides a set of functions that allows one to download, process and display Sentinel-1 and Sentinel-2 data products. The library is designed to operate on “**parcels**” provided as input in a ESRI shape file. Sentinel-1 and -2 data are extracted for the specific parcels and are displayed in a “**calendar view**”, which as been designed and optimized to provide an immediate and intuitive access to both temporal and spatial dimensions of Sentinel-derived data.

While the scripts exploit the RESTful APIs developed by the JRC D5 unit to download raw Sentinel imagettes tailored to the extent of the parcels provided as input, the code is general and can be easily adapted to work, for example, on geotiff downloaded from other sources.

## 2. Dependencies
The *run_calendar_view.py* script depends on several standard python libraries that can be found in the *requirements.txt* file. The dependencies can be installed with:

  pip install -r requirements.txt

As well the gdal_merge.py module that can be download from [https://github.com/geobox-infrastructure/gbi-client/blob/master/app/geobox/lib/gdal_merge.py](https://github.com/geobox-infrastructure/gbi-client/blob/master/app/geobox/lib/gdal_merge.py)
and should be added to the utils folder.

### 2.1 JRC CbM Modules for the calendar_view package
The following custom libraries are called by the *run_calendar_view.py* script:

- **batch_utils**: provides general functions for the processing of parcels and Sentinel data. The set of functions provided in this library allows the selection of parcels, the determination of the list of imagettes to be downloaded on the basis of cloud mask criteria, the download of imagettes, processing of Sentinel data including image color stretching (lookup table stretch or LUT stretch)and computation of derived indexes such as NDVI and NDWI. For download operations, the library uses the functions developed under *download_utils*.
    
- **download_utils**: library for downloading and processing Sentinel-1 and 2 imagettes.
 
- **plot_utils**: functions for plotting Sentinel-1 and 2 imagettes in structured calendar views. Sentinel products are displayed in a coherent way, in order to provide an enhanced accessibility to both temporal and spatial dimensions of Sentinel-1 and 2 products. 

- **graph_utils**: library providing functions for displaying and plotting temporal profiles such as NDVI and NDWI. The library focuses on temporal (1-D) signals and complements the utilities in *plot_utils*, which focuses on displaying images (set of 2-D signals).   

- **extract_utils**: functions for the calculation of the NDVI and NDWI indexes. The functions in this library are called by *batch_utils*.

The call graph of the run_calendar_view script is provided in Figure 1.

![Figure 1: Call graph of the run_calendar_view script.](https://github.com/borioda/cbm/blob/main/media/img/calendar_view_fun.png)

## 3 Structure of the code
The *run_calendar_view* script is organized in two main parts: the initialization block and the main processing loop. The input parameters and processing settings are defined in the initialization block whereas the actual processing is performed in the main loop that performs the different tasks by considering individual parcels. The main processing loop is further divided in two parts: download and processing of Sentinel-2 data and download and processing of Sentinel-1 data. The overall structure of the script and the different tasks performed by the main processing loop are illustrated in Figure 2. 
The different parts of the script are better detailed in the following sections.

![Figure 2: Overall structure of the run_calendar_view script.](https://github.com/borioda/cbm/blob/main/media/img/run_calendar_view.png)

## 3.1 Initialization
The variables in the initialization part of the script allows one to define inputs and outputs and to customize the different operations performed by the script. 

### Authentication and data access
These scripts are using RESTful API for CbM. There are instructions provided on how to build a RESTful API for CbM at the wiki page [https://github.com/ec-jrc/cbm/wiki/Build-RESTful-API-with-Flask-for-CbM](https://github.com/ec-jrc/cbm/wiki/Build-RESTful-API-with-Flask-for-CbM). To access the RESTful services it is necessary to provide login information. In this respect, the username, password and the data url (url_base variable) have to be properly configured.
Alternatively JRC’s RESTful API for CbM provides limited sample open datasets and can be used for testing and demonstration purposes. A temporary account can be requested from JRC GTCAP.

### Cloud categories
Different SCL classes can be specified in the *cloud_categories* list. These classes, mainly specifying cloud types, are used to filter out Sentinel-2 images affected by cloud covers within the parcel.

### Parcel data
The parcel data including polygons, parcel IDs and crop types, are specified through the *vector_file_name variable*. This variable should point to the ESRI shape file containing all the needed information. The variables *parcel_id_column* and *crop_name_column* specify the columns in the shape file containing the parcel IDs and crop names. This information will be printed on the images generated by the script. In case crop name is not available add any other information in that column (can be even an empty string) you want to be printed on the outputs.

### Sentinel-2 imagettes
The bands and the maximum size of the Sentinel-2 imagettes are specified through the *bands* and *chipsize* variables. Default chip size is replaced by the maximum extent of the parcel plus the buffer size provided in *buffer_size_meter* variable.

### Date range
The date range for the data search can be set through the *search_window_start_date* and *search_window_end_date* variables. 

## 3.2 Main Processing Loop
The different operations performed in the main loop are listed in the second part of the “Overall structure of the run_calendar_view script” section above These operations are performed through the functions called in Figure 1. While the *run_calendar_view* script demonstrates most of the functionalities implemented in the different libraries, the main loop has a modular structure and functions can be commented if specific operations are not needed.  

