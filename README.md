# FlexiGIS-Grid

The FlexiGIS-Grid scripts aim to perform a soft-coupling of the energy system models [**rivus**](https://github.com/tum-ens/rivus)
and the QGIS-Plugin of [**FlexiGIS**](https://github.com/FlexiGIS/FlexiGIS-plugin) is performed. 

**rivus** is a [mixed integer linear programming](https://en.wikipedia.org/wiki/Integer_programming) optimisation model 
for capacity planning of energy infrastructure networks. Its name, latin for stream or canal, stems from its origin as a 
companion model for [urbs](https://github.com/tum-ens/urbs), an optimisation model for urban energy systems. The model 
has been first published in the dissertation of [Johannes Dorfner](https://github.com/ojdo) ["Open Source Modelling 
and Optimisation of Energy Infrastructure at Urban Scale"](http://nbn-resolving.de/urn/resolver.pl?urn:nbn:de:bvb:91-diss-20161206-1285570-1-6).

**FlexiGIS-plugin** is a graphical user interface (GUI) of the FlexiGIS-model including FlexiGIS-light. It extracts the 
geo-datasets of the urban energy infrastructure and simulates the electricity consumption in the respective case study. 
This plugin can be directly installed from the QGIS Plugin Manager within the QGIS Desktop.

## Features:

  * **rivus** finds the minimum cost energy infrastructure networks to satisfy a given energy distribution for possibly multiple 
    commodities (e.g. electricity, heating, cooling, ...).
  * Tests have been performed on municipal districts.
  * Functionality of **rivus** as been coupled with Geoprocessing module of **FlexiGIS** to generate grids of any region  
    
    
  * Time is represented by a (small) set of weighted time steps that represent peak or typical loads. Both demands and technical 
    parameters can be edited in a spreadsheet.
  * Spatial input data is provided in form of shapefiles.
  * **rivus** itself is written using [Pyomo](https://software.sandia.gov/trac/coopr/wiki/Pyomo) and includes reporting 
    and plotting functionality. For more details use the [official documentation](http://rivus.readthedocs.io/en/latest/).

## Workflow:

The coupling of both tools was guided by a three step workflow (see Fig. 1). Network optimization in rivus requires
spatial and non-spatial input data. Non-spatial data contained in **data**.xlsx has been mainly adapted from the example 
provided in the original publication. 

> - **Input**:
> POLY-file from region of interest, 
> OSM-Protobuffer-File containing region, 
> location of substations/gas terminals
> - **Output**: cost-optimized multi-commodity energy infrastructure networks

![Workflow](doc/img/flexigis-rivus-workflow.png)
Fig. 1: Workflow describing the general order processing steps from pbf-files to rivus-plots. (Alhamwi et al., 2021, under submission) 

### 1. Geoprocessing with FlexiGIS:

Spatial data is processed using **FlexiGIS**. Download a super-regional data set (as an exchange for _niedersachsen-latest_.osm.pbf) 
from a provider like GeoFabrik.  Generate a POLY-File containing the shape of the region/district you are interested in. In Fig. 1,  with 

Open QGIS3-Desktop and launch the FlexiGIS-Plugin. 

**I- Extract urban datasets**: Load the super-regional data set, as well as the POLY-file 
so that FlexiGIS can generate the urban-infrastructure for the region of interest. For better 
maintainability, store the output under the POLY-files name (e.g. district name)

**II- Geoprocessing urban datasets**: Open the previously extracted dataset. Under *"Select urban element (OSM key)"*
you then select *"building"* and *"highway"*. This will generate csv-files that are referenced in Fig. 2. 

> **Note**: The FlexiGIS module generates the unfiltered data sets, as well as the filtered ones. Please copy agricultural.csv, 
commercial.csv, educational.csv, industrial.csv, residential.csv, highway_lines.csv and highway_squares.csv to the folder 
*data/flexigis-input/fg-wechloy-raw*. You may want to adjust the directory's name to suit your districts name.

To complete the pre-processing, follow the workflow presented in Fig. 2. It might be necessary to first adjust your input
(and output) directories to account for the location of your files. 
 
> **Note**: The location of sources required for *sources.csv* is approximated as the vertex which lies closest to the 
> actual substation or gas terminal. The capacity given is simply an upper limit in kW. 
> 
> To approximate the location use QGIS and open the network of edge.shp and vertex.shp. Activate vertex-labels and load
> the location of substations etc. 

### 2. Network optimization with rivus:

Open **runwechloy**.py. Change the *base_directory* to your input directory (i.e. where *edge_w_demands*.shp and 
*vertex_w_source*.shp lie). The results will be generated within this folder.

In line 33 under *scenarios* choose one (or a set) of scenarios, that should be run for you.

![Coupling interface](doc/img/flexigis-rivus-coupling-interface.png)
Fig. 2: Follow this workflow to convert FlexiGIS-processed **wechloy**.osm.pbf to rivus-compatible edge.shp and vertex.shp. 
This workflow defines conversion indicated through arrows in Fig. 1 at the border of Step 1 and Step 2. (Alhamwi et al., 2021, under submission)

### 3. Results:

The *rivus* package provides two different output types. Maps of peak demand and maximum transported power per edge are 
plotted and saved many different configurations. It is possible to plot with buildings as well.

A detailed overview on utilization and positioning of conversion processes, as well as extensive information on line
utilization is provided in **report**.xlsx.

![rivus-caps](doc/img/rivus-caps.png)
Fig. 3: Diagram shows grids that are generated for maximum transported power. Orange (Electricity), Red (Heat) and Brown
(Gas). (Dorfner, 2015)

## Installation:

### Mac & Linux

1. Install [**miniforge3**](https://github.com/conda-forge/miniforge/releases). Choose the 64-bit installer if possible.

2. Clone repository to your working directory
   
    ```
    (base)$ git clone https://github.com/FlexiGIS/FlexiGIS-Grid.git
    ```

3. Create **conda** environment from environment.yml file via terminal
    
    i) Navigate to your working directory
   
    ```
    (base)$ cd path/to/working_directory
    ```

    ii) Install **conda** virtual environment from file
    
    ```
    (base)$ conda env create -f environment.yml
    ```
   
    iii) Activate environment

    ```
    (base)$ conda activate flexigisnetz
    ```

4. Install [**Skeletron package**](https://github.com/ojdo/Skeletron.git) into **flexigisnetz** environment using **pip**
    
    i) Clone repository and navigate into directory

    ```
    (flexigisnetz)$ git clone https://github.com/ojdo/Skeletron.git
    ```      

    ii) Navigate into directory and install package using pip
    
    ```
    (flexigisnetz)$ pip install -e .
    ```


## <References>
