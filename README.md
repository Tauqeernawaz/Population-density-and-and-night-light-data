# Night Light and Population Density Analysis

This repository contains scripts for analyzing **night light** data and **population density** data to understand urbanization trends. The analysis uses the following datasets:
- **NOAA VIIRS Night Light** data for the years 2000-2022.
- **WorldPop Population Density** data for Pakistan (2022).

These datasets were processed using **Google Earth Engine (GEE)** and exported to **Google Drive** for further analysis.

## Datasets:
1. **NOAA VIIRS Night Light Data**: The **NOAA/VIIRS/DNB/MONTHLY_V1/VCMSLCFG** dataset is used to track the intensity of night lights, which is an indicator of urbanization and economic activity. The **avg_rad** band (average radiance) is used to visualize night lights.
   - **Source**: [Google Earth Engine - VIIRS](https://code.earthengine.google.com/)
   - **Time period**: January 2000 to July 2022.

2. **WorldPop Population Density Data**: The **WorldPop/GP/100m/pop** dataset provides the population density of Pakistan for the year 2022.
   - **Source**: [WorldPop](http://www.worldpop.org/)
   - **Time period**: January 2022 to December 2023.

## Methodology:
1. **Night Light Data (VIIRS)**:
   - The **NOAA VIIRS Night Light** dataset was filtered for the period from January 2000 to July 2022.
   - The **avg_rad** band, which represents average radiance, was extracted.
   - The data was clipped to the **region of interest (ROI)**, and the image was visualized and exported to Google Drive.

2. **Population Density Data (WorldPop)**:
   - The **WorldPop population dataset** was filtered for Pakistan and clipped to the **ROI**.
   - The **population density** data was visualized using a color palette.
   - The processed data was then exported to Google Drive.

## Data Processing in Google Earth Engine:
The following steps were executed in **Google Earth Engine** to analyze the datasets:
1. **Filter and Clip Night Light Data**: The VIIRS night light data was filtered by date and clipped to the specified ROI.
2. **Filter and Process Population Data**: The WorldPop population data was filtered for Pakistan and clipped to the same **ROI**.
3. **Export Processed Data**: Both datasets were exported to Google Drive for further analysis and visualization.

### Data Export:
- **Night Light Data (VIIRS)**: Exported at a scale of **10 meters**.
- **Population Data (WorldPop)**: Exported at a scale of **500 meters**.

## Geographical Coverage:
- **Region of Interest (ROI)**: The geographic region defined for analysis.
- **Latitude**: [31°10′N to 32°17′N]
- **Longitude**: [70°00′E to 71°40′E]

## License:
The data and code in this repository are made available under the **CC BY 4.0** (Creative Commons Attribution 4.0 International License), allowing reuse with proper attribution.

## How to Use:
1. **Download Data**: The **GeoTIFF** files for the night light and population density data are available for download.
2. **Run Scripts**: The provided **GEE script** processes the **VIIRS night light** and **WorldPop population** data.
   - **GEE Script**: The script runs in **Google Earth Engine** and can be accessed via: [Google Earth Engine](https://code.earthengine.google.com/).
3. **Visualize Data**: Once exported, the data can be visualized in **QGIS** or **ArcGIS**.

## Citation:
If you use this code or the data in your research, please cite the following:
Nawaz, T.,  [2025]. Night Light and Population Density Analysis for Urbanization Trends in Pakistan. Retrieved from (https://github.com/Tauqeernawaz).

## References:
- **NOAA VIIRS Night Light Data**: [VIIRS Data on Google Earth Engine](https://code.earthengine.google.com/)
- **WorldPop Population Density**: [WorldPop Data Portal](http://www.worldpop.org/)
// Step 1: Import the VIIRS Night Light Data from Google Earth Engine
var image = ee.ImageCollection("NOAA/VIIRS/DNB/MONTHLY_V1/VCMSLCFG")
    .filterDate("2000-01-01", "2022-07-30") // Filter data for the period 2000-2022
    .first(); // Get the first image in the collection

// Step 2: Clip the image to the Region of Interest (ROI)
var viirscollection1 = image.clipToCollection(roi);

// Step 3: Set visualization parameters for the VIIRS night light data
var visParams = {
  bands: ['avg_rad'],  // Use the avg_rad band for average radiance
  gain: 10  // Set the gain for radiance visualization
};

// Step 4: Center the map on the ROI and add the ROI boundary and VIIRS night light layer
Map.centerObject(roi);
Map.addLayer(roi, {color: 'FFFF00'}, 'roi');  // Add ROI boundary in yellow
Map.addLayer(viirscollection1, visParams, 'viirsnight1');  // Add night light data layer

// Step 5: Export the VIIRS night light image to Google Drive
Export.image.toDrive({
  image: viirscollection1.select(['avg_rad']),  // Select the avg_rad band for export
  description: 'viirscollection1',  // File name
  scale: 10,  // Set export scale to 10 meters
  region: roi,  // Set region of interest for export
  maxPixels: 1e13  // Set maximum number of pixels
});

// Step 6: Import WorldPop population density data
var pop = ee.ImageCollection("WorldPop/GP/100m/pop")
          .filterBounds(roi)  // Filter by region of interest
          .filter(ee.Filter.eq('country', 'Pak'))  // Filter for Pakistan
          .filterDate("2022-01-01", "2023-12-31")  // Filter for year 2022
          .select('population')  // Select the population band
          .mean().clip(roi);  // Calculate the mean population density and clip to ROI

// Step 7: Set visualization parameters for the population density layer
var visParam = {
  min: 0,
  max: 100,
  palette: '24126c, 1fff4f, d4ff50'  // Set color palette for population density
};

// Step 8: Add the population density layer to the map
Map.addLayer(pop, visParam, "POP");

// Step 9: Export the population density image to Google Drive
Export.image.toDrive({
   image: pop,  // Population density image
   description: 'POP',  // File name
   scale: 500,  // Set export scale to 500 meters
   region: roi,  // Set region of interest for export
   maxPixels: 1e13,  // Set maximum number of pixels
});

// Add styling for the boundary of the ROI to the map
var styling = {color: 'red', fillColor: '00000000'};
Map.centerObject(roi, 6);  // Center map on ROI
Map.addLayer(roi.style(styling), {}, 'Bound');  // Add ROI boundary in red
