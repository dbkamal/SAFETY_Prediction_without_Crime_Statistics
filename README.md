# SAFETY Prediction without Crime Statistics

The project objective is to provide a visual way for a person to gauge the safety of an area within a large city even when specific crime data is not available for that city based on a model derived from another city with known crime data.

## Getting Started

These instructions will get you a copy of the project up and running on your local machine for development and testing purposes. (**NOTE: Due to GT Project restriction, few details will be removed**)

### Prerequisites

What things you need to install the software and how to install them

Software required to get the project up and running
```
Python 3.6 or higher
Jupyter
Python pacakges to install
  flatten_json
  geohash2
  geolib
  numpy
  pandas
  requests
  scikit-learn
  
Microsoft Excel
```
**Note: The CODE folder should be copied to your local drive and all commands should be run from the CODE folder.**

### Installing

A step by step series of instructions that tell you how to get a development env running

#### Create Virtual Enviroment
```
conda create --name myenv --file requirements.txt
```
geolib isn't in any conda channels for some OSes (e.g. macOS), so you may have to install it using pip.  If that is the case for you, do the following:

```
conda create --name myenv
source activate myenv
conda install pip
pip install -r requirements.txt
```
Make sure that pip is pointing to pip in your virtual env.  You may need to include the path i.e.:
```
/Users/<user_name>/anaconda/envs/myenv/bin/pip install -r requirements.txt
```

### Quick UI Run
All files needed to run the UI are included in the repo.  To run the UI without retraining/predicting:
1. cd to the ui directory `cd ui`
2. Open the command prompt and type ```python3 -m http.server 8000``` to start a local server
3. Go to http://localhost:8000/  in Firefox

### Quick Train, Predict, UI.
All files needed to train the model and run prediction are included in the repo aside from `data/Chicago_Crimes_2012_to_2017.csv` as it is too large to add to GitHub.
1. Download the Chicago Crimes data from (https://drive.google.com/file/d/XXXXXXX) and place into the ./data/crimes folder.
2. Download the Atlanta Crimes data from (https://drive.google.com/file/d/XXXXXXX) and place into the ./data/crimes folder.
3. Generate the json files used by the UI: `output/safetydata.pred.json`, `output/predictionstats_georgia.json` and `predictionstats_Illinois.json`

    ```python model/train_model_and_predict.py```

4. Copy these files into the `ui/json/` folder

    ```cp output/*.json ui/json/```

5. Either follow the instructions above for running the UI or refresh your current browser.
Note, you may need to clear your browser cache to get the new json files to load.

Instructions to run each module "from scratch" are below
#### Data collection and cleaning

##### A. Crime Data
1. Download the Chicago Crimes data from (https://drive.google.com/file/d/XXXXXXX) and place into the ./data/crimes folder.
2. Download the Atlanta Crimes data from (https://drive.google.com/file/d/XXXXXXX) and place into the ./data/crimes folder.

##### B. Places Data

1. Generate unique geohash level 7 for Chicago and Atlanta crime data

    `python data/scripts/generate_geohash7_crimetable.py`
    
    This command creates `data/geo_hash/atlanta_geohash7.csv` and `data/geo_hash/chicago_geohash7.csv`
    
2. Collect the places data for Chicago and Atlanta 
    
    `python data/scripts/google_placesearch.py`
    
    This command creates `data/google_place_search/atlanta_googlesearch.csv` and `data/google_place_search/chicago_googlesearch.csv`

##### C. Socio-economic Data

1. Download the socio-economic data from https://factfinder.census.gov/faces/nav/jsf/pages/searchresults.xhtml?refresh=t
  - Add the desired geographies by selecting "Block Group - 150", "Illinois", and "Cook County" for Chicago data and "Block Group - 150", "Atlanta", and "Fulton County" for Atlanta Data. These can both be selected to download data simultaneously.
  - Select desired socioeconomic features from the 2017 American Community Survey dataset: highest level of economic attainment, household income in past 12 months, median value of owner occupied housing, population, and poverty status. Download each as CSV.

##### D. Census block to lat long conversion

1. Obtain all valid lat long data points for the current city from appropriate data source (eg:- Crime data for Chicago)
2. Remove duplicates and save as a csv file with columns 'Latitude' and 'Longitude'
3. Run `python data/scripts/get_latlong_to_fipscode_mapping <file_name>` where <file_name> is the file created above
4. A new file out_<file_name> will be generated with an additinal column 'FIPS'
5. Open this output file in Excel and use Excel's vlookup function to update the lat long information in files where data is at Census block/FIPS code level. (Note: Excel could be showing the FIPS code as a numeric exponent value. Use the function =CONCATENATE(D2,"") to format it as string where D2 is the cell with FIPS code)

##### E. Socio Economic Data Cleanup
1. Perform Section C of **Data collection and cleaning** first as this is input for step 2
2. Run this python script to clean the socio data and split it into one file for Chicago and another for Atlanta
        ```python data/scripts/socio_data_cleanup.py```
3. Above script will generate ```Atlanta_Socio_Data.csv``` and ```Chicago_Socio_Data.csv```

#### Data Modelling and Prediction

1. Use the crime, socio-economic and popular places data in the above sections as input.
2. Perform data clean up
  - For data that is at census block level, use python script get_latlong_to_fipscode_mapping to get the FIPS code to lat long mapping.
   (For detailed instructions see section 'Census block to lat long conversion' below)
  - For cleaning and splitting the socio-economic data, use python script socio_data_cleanup.py which will generate the Atlana and Chicago socio-economic data for education, household income, median housing value, population and poverty
    (For detailed instructions see section **E. Socio Economic Data Cleanup** above)
3. Run the python script train_model_and_predict.py to generate the final json file <json_file> which includes the geohash, safety and predicted safety for Chicago and Atlanta

    ```python model/train_model_and_predict.py```


#### UI

1. Place the json files `output/safetydata.pred.json`, `output/predictionstats_georgia.json` and `predictionstats_Illinois.json` generated from "Data Modeling and Prediction>Step 3" into the ```ui/json``` folder
2. cd to the ui directory `cd ui`
3. Open the command prompt and type ```python3 -m http.server 8000``` to open the local server on the browser (use firefox)
4. Navigate to http://localhost:8000/ on the browser

## Installation / Execution Video
https://youtu.be/7A78k2_3iCY

## Built With

* [Bootstrap](https://getbootstrap.com/) - The web framework used
* [Google Maps JavaScript API](https://developers.google.com/maps/documentation/javascript/tutorial) - Mapping
* [gmaps.js](http://hpneo.github.com/gmaps/) - Mapping
* [Slide Reveal](https://github.com/nnattawat/slidereveal) - Used to display side panel


## Authors

Sean Browne, Kamal Debnath, Deepak Ranganathan, Matthew Resch, ​Safiyyah Saleem, Muhammad Basheer Thavannoor
