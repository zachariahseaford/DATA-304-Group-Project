# DATA-304-Group-Project
Team: Zach Seaford (Data Analyst/Data Manager), Harrison William Bohls (Data Quality Specialist/Data Manager)
# What are we trying to do?
We want to combine SpaceX Falcon Heavy launch information with historical weather data to make one clean dataset. Each row should represent one launch and show what the weather was like near the launch time and location. This is mainly a data wrangling project, not a machine-learning prediction project. 

# Questions
1. How can we clean launch records and weather records that come in different formats?
2. Can we match launches to the closest hourly weather record using UTC timestamps?
3. What weather conditions were recorded during the Falcon Heavy launches in our dataset?
4. How complete and accurate is the merged dataset?
# Data sources
- Falcon Heavy launches: Wikipedia Falcon Heavy launch table (HTML; manually transcribed first three launches in the starter sample CSV; next step is building the HTML scraper).
- Historical weather: Open-Meteo Historical Weather API (JSON; temperature, humidity, precipitation, cloud cover, winds). Weather is modeled/reanalysis weather, not direct observations from NOAA stations.
- Possible future source: NOAA Local Climatological Data (station-based observations). This is in our original proposal but not yet included in the starter pipeline.
** Scope of first prototype: Falcon Heavy launches from Kennedy Space Center LC-39A, Florida. The sample has three historical launches to test our pipeline; it is not the complete launch history. LC-39A coordinates used in the demo are approximately 28.6084, -80.6042, based on NASA records. **

# Wrangling methods from DATA 304
Class skill	Where we use it
CSV / pandas	Load launch data and save cleaned results
HTML tables	Explore scraping the Falcon Heavy Wikipedia table
JSON	Turn weather API results into a DataFrame
String cleaning	Standardize names and locations
Missing data / duplicates	Remove invalid launch records and check missing weather
Timestamps	pd.to_datetime(..., utc=True) for both sources
Data integration	pd.merge_asof joins each launch to the nearest hourly observation
Validation	Check missing values, duplicate flight numbers, time differences
GitHub	Branches, issues, commits, pull requests


# Cleaning process
1. Load and launch CSV, strip extra spaces from column names and text fields.
2. Parse launch time into a timezone-aware UTC timestamp.
3. Drop launches missing a timestamp and remove duplicate flight numbers.
4. Request weather for the launch date and pad coordinates using Open-Meteo's historical endpoint, specifying timezone=UTC.
5. Flatten the hourly JSON arrays into a DataFrame, parse the weather timestamps, and remove duplicate hours.
6. Use pd.merge_asof to select the nearest hourly weather record within 30 minutes of launch. If no reading is close enough, leave the weather missing and report it instead of pretending a match exists.
7. Validate timestamps, missing observations, and duplicates; export CSV.
Time-matching limitation: Historical weather is hourly. A nearest-hour match can differ by as much as 30 minutes; hourly precipitation describes the preceding hour and should not be presented as an instantaneous reading.
Expected columns
flight_number, launch_utc, launch_site, payload, launch_outcome, weather_utc, temperature_2m, relative_humidity_2m, precipitation, cloud_cover, wind_speed_10m, wind_gusts_10m, minutes_apart.

The presentation also proposes booster details, payload mass, orbit, visibility, air quality, and NOAA station matching. Those are future additions, not promised output of this starter code.
# Team roles
- Zach — Data Analyst: Build launch + weather wrangling code, timestamp matching, export cleaned CSV, create exploratory charts.
- Harrison — Data Quality Specialist: Check missing values, duplicates, timestamps, unmatched launches, and validate output.

# GitHub task outline
- [ ] #1 Set up repo and folders (in progress)
- [ ] #2 Decide on Falcon Heavy data scope and confirm source links
- [ ] #3 Collect launch records from Wikipedia (HTML extraction)
- [ ] #4 Retrieve historical weather (API JSON)
- [ ] #5 Clean source data (strings, types, missing values, duplicates)
- [ ] #6 Match launches to nearest weather hour and export CSV
- [ ] #7 Validate dataset and document unmatched rows
- [ ] #8 Exploratory analysis and charts
- [ ] #9 Finish README, presentation, and limitations

# Planned analysis
- Wind speed, precipitation, temperature, cloud cover at launch time.
- Compare weather by year or season (where sample size permits).
- Report percent matched and missing values.
Important limitations: Falcon Heavy has relatively few launches, many are successful, and the sample file has only three. We are not building a meaningful success/failure prediction model or claim to identify the best launch site from this limited dataset. We are also not using NOAA station data yet. For more launches we might expand to Falcon 9, or add launch delays/scrubs from another source.
# Project status
Starter code and sample CSV are provided. Offline tests passed. Live API requests and Wikipedia scraping need to be run and checked in Codespaces.
