# build_multiscale_graph_convid19

## Regions

| Name | Description |
| ------------- | ------------- |
| Region type | {Airport, Train Station, Ferry Port, City, State, Country, Continent} |
| Graph nodes | {City, State, Country, Continent} |

### Region Property
Centriods: Centriods are in form of (Latitude, Longitude):
- Every Airport has Centriods
- Every Train Station has Centriods
- Every Ferry Port has Centriods
- **Not** Every City has Centriods (1460 cities have no Centriods)
- **Not** Every State has Centriods (1820 states have no Centriods)
- **Not** Every Country has Centriods (58  countries have no Centriods)
-  **NONE** Continent has Centriods 

Population:
- Only City has Population so far 
- **Not** Every City has Population (1460 cities have no Population)

### Underlying Across-scale Region Connection
From high-resolution to coarse-resolution:
- Every Airport has parent City
- Every Train Station has parent City
- Every Ferry Port has parent City
- Every City has parent State
- Every State has parent Country
- **Not** Every Country has parent Continent (15 countries have no Continent)

From coarse-resolutio to high-resolution:
- **Not**  Every Continent has children Country ("Antarctica" has no Country)
- **Not** Every Country has children State (24 countries have no State)
- **Not** Every State has children City (612 states have no City)
- Every City has at least a child Airport
- **Not** Every City has a child Train (2879 cities have no Train)
- **Not** Every City has a child Ferry (3086 cities have no Ferry)

### Underlying Within-scale Region Connection
Train-scale:
- Disconnected (no train route information) 

Ferry-scale:
- Disconnected (no ferry route information) 

Airport-scale:
- Every arport is connected to another airport with direction (number of flights: 66133)

City-scale:
- Every city is connected to another city with direction (Specifically, city i connects to city j if there is a flight from city i to city j)

State-scale:
- *Is every state is connected to another state with direction?* (Specifically, state i connects to state j if there is a flight from state i to state j)

Country-scale:
- *Is every country is connected to another country with direction?* (Specifically, country i connects to country j if there is a flight from country i to country j)

Continent-scale:
- **NOT** Every continent is connected to another continent with direction (Specifically, continent i connects to continent j if there is a flight from continent i to continent j). "Antarctica" is isolated at the Continent-scale. *Is there any other continent isolated?*

## How Regions obtained
Final number of flights: 66133 

|  Region type | Description | Number |
| ------------- | ------------- | ------------- |
|  Airport | {all airports simultaneously have flights, city, state information} | 3227  |
|  Train | {all train stations that all cities that we consider have} |  478  |
|  Ferry | {all ferry ports that all cities that we consider have} |  34  |
|  City | {all cities have flights and have state information} |  3114  |
|  State | {states of all cities} + {all states in `JHU COVID-19 database`} |  1955  |
|  Countries | {countries of all states} + {all countries in `JHU COVID-19 database`}   |  231   |
|  Continent | {all countries' continent}. Allowed values are: "Africa" (AF), "Antarctica" (AN), "Asia" (AS), "Europe" (EU), "North America" (NA), "Oceania" (OC), "South America" (SA). |  7 |

Basically:
1. Find all cities have flights and have state information, according to `flight_routes_processed_v2.csv`, `regions.csv`, `airports.csv` and `airports.json` (because for every city in our graph, we have to know its state and its airports). Meanwhile find all airports.
2. Generate `flight.txt` based on filtered current all cities and `flight_routes_processed_v2.csv`. 
3. Find current all cities'  train stations, ferry ports respectively according to `airports-extended.dat`, `airports-extended.dat`. Meanwhile find all train stations, ferry ports.
4. Find all states = {states of current all cities} + {all states in `JHU COVID-19 database`} (source: `regions.csv`, `airports.json` and `UID_ISO_FIPS_LookUp_Table.csv`).
5. Find all countries = {countries of current all states} + {all countries in `JHU COVID-19 database`} (source: `regions.csv`, `airports.json` and `UID_ISO_FIPS_LookUp_Table.csv`).
6. Find all continents = {continents of current all countries} (source: `regions.csv` and `airports.json`).


## Data source

|  File name | Data source | Description |
| ------------- | ------------- | ------------- |
| `UID_ISO_FIPS_LookUp_Table.csv` |   [JHU COVID-19 database](https://github.com/CSSEGISandData/COVID-19)    | Mapping from Country name to iso2, iso3, Province. When province information is available, the latitude (lat) and longitude (lon) is the province's lat and lon; otherwise, is country's.  |
| `airports-extended.dat` |   [OpenFlights](https://openflights.org/data.html)    | Information for terminals (type value "airport" for air terminals, "station" for train stations, "port" for ferry terminals and "unknown" if not known): name, city, country, lat and lon, type.  |
| `airports.csv` |   [OurAirport](https://ourairports.com/data/)    | Information for airports: name, lat and lon, iso_country, iso_region (e.g. province, governorate), continent.   |
| `regions.csv` |   [OurAirport](https://ourairports.com/data/)    | Information for regions in `airports.csv`: iso_region, name, iso_country, continent.  |
| `countries.csv` |   [OurAirport](https://ourairports.com/data/)    | Information for countries in `airports.csv`:  iso_country, country_name, continent.  |
| `airports.json` |   [tdreyno GitHub](https://gist.github.com/tdreyno/4278655)    | Information for airports: name, lat and lon, city name, state name, country name, etc.   |
| `flight_routes_processed_v2.csv` |   ArcGIS    | Information for flight routes: From_Airport, From_Name, From_City, From_Country, From_Latitude, From_Longitude, To_Airport, To_Name, To_City, To_Country, To_Latitude, To_Longitude.   |
| `worldcities_processed.csv` |   ArcGIS    | Information for cities: City_Name, Latitude, Longitude, Country, Country_Code_ISO2, Country_Code_ISO3, Population.   |


## Preprocessed dataset 
./output/
├── all_train.pickle
├── all_ferry.pickle
├── all_airports.pickle
├── all_cities.pickle
├── all_states.pickle
├── all_countries.pickle
├── all_continents.pickle

├── train2centriods.pickle
├── train2city.pickle

├── ferry2centriods.pickle
├── ferry2city.pickle

├── airport2centriods.pickle
├── airport2city.pickle

├── city2centriods.pickle
├── city2population.pickle
├── city2state.pickle

├── state2centriods.pickle
├── state2country.pickle

├── country2centriods.pickle
├── country2continent.pickle

└── flight.txt

|  Data name | Data Type | Description |
| ------------- | ------------- | ------------- |
|  all_train.pickle | `set()` | A set of all train station names. |
|  all_ferry.pickle | `set()` | A set of all ferry port names. |
|  all_airports.pickle | `set()` | A set of all airport names. |
|  all_cities.pickle | `set()` | A set of all city names. |
|  all_states.pickle | `set()` | A set of all state names. |
|  all_countries.pickle | `set()` | A set of all country names. |
|  all_continents.pickle | `set()` | A set of all continent names. |
| train2centriods.pickle | `dictionary()` | Key: train station name; Value: centriods. |
| ferry2centriods.pickle | `dictionary()` | Key: ferry port name; Value: centriods. |
| airport2centriods.pickle | `dictionary()` | Key: airport name; Value: centriods. |
| city2centriods.pickle | `dictionary()` | Key: city name; Value: centriods (can be `'NULL'`). |
| state2centriods.pickle | `dictionary()` | Key: state name; Value: centriods (can be `'NULL'`). |
| country2centriods.pickle | `dictionary()` | Key: country name; Value: centriods (can be `'NULL'`). |
| train2city.pickle | `dictionary()` | Key: train station name; Value: city name. |
| ferry2city.pickle | `dictionary()` | Key: ferry port name; Value: city name. |
| airport2city.pickle | `dictionary()` | Key: airport name; Value: city name. |
| city2state.pickle | `dictionary()` | Key: city name; Value: state name. |
| state2country.pickle | `dictionary()` | Key: state name; Value: country name. |
| country2continent.pickle | `dictionary()` | Key: country name; Value: continent name  (can be `'NULL'`). |
| city2population.pickle | `dictionary()` | Key: city name; Value: population (can be `'NULL'`). |
| flight.txt | `edgelist` | Every line: '{}\t{}\n'.format(source_airport_name, target_airport_name) |

## Additional notes
To be consistent with `JHU COVID-19 database`, Country Name for "United States" is "US" in the above preprocessed dataset. 