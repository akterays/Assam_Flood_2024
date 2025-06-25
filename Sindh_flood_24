# Sindh_Flood_2022
https://code.earthengine.google.com/9e888208ae6b00971ee45547a8c54358



// --------------------------------------
// // --------------------------------------
// Sindh, Pakistan Flood Mapping 2022 using your shapefile
// --------------------------------------

// 1. Load uploaded Sindh boundary from Assets
var sindh = ee.FeatureCollection("projects/ee-ayshaakteres/assets/Sindh");
var sindh_geom = sindh.geometry();

// 2. Load pre-flood Sentinel-1 image
var preFlood = ee.ImageCollection("COPERNICUS/S1_GRD")
  .filterBounds(sindh_geom)
  .filterDate("2022-06-01", "2022-07-10")
  .filter(ee.Filter.eq('instrumentMode', 'IW'))
  .filter(ee.Filter.listContains('transmitterReceiverPolarisation', 'VV'))
  .filter(ee.Filter.inList('orbitProperties_pass', ['ASCENDING','DESCENDING']))
  .select('VV')
  .median()
  .clip(sindh_geom);

// 3. Load post-flood Sentinel-1 image
var postFlood = ee.ImageCollection("COPERNICUS/S1_GRD")
  .filterBounds(sindh_geom)
  .filterDate("2022-07-20", "2022-09-10")
  .filter(ee.Filter.eq('instrumentMode', 'IW'))
  .filter(ee.Filter.listContains('transmitterReceiverPolarisation', 'VV'))
  .filter(ee.Filter.inList('orbitProperties_pass', ['ASCENDING','DESCENDING']))
  .select('VV')
  .median()
  .clip(sindh_geom);

// 4. Flood detection using threshold
var diff = preFlood.subtract(postFlood);
var flood = diff.gt(1.2).selfMask();  // You can adjust the threshold

// 5. Visualization
Map.centerObject(sindh_geom, 7);
Map.addLayer(preFlood, {min: -25, max: 0}, "Pre-Flood VV");
Map.addLayer(postFlood, {min: -25, max: 0}, "Post-Flood VV");
Map.addLayer(flood, {palette: ['blue']}, "Flood Map 2024");

// 6. Export flood map to Google Drive
Export.image.toDrive({
  image: flood,
  description: 'FloodMap_Sindh_2022',
  folder: 'S_Floods',
  fileNamePrefix: 'sindh_flood_2022',
  region: sindh_geom,
  scale: 30,
  maxPixels: 1e13
});

// 7. Add a legend for the flood map
var legend = ui.Panel({
  style: {
    position: 'bottom-left',
    padding: '8px 15px'
  }
});

var legendTitle = ui.Label({
  value: 'Legend',
  style: {
    fontWeight: 'bold',
    fontSize: '16px',
    margin: '0 0 4px 0',
    padding: '0'
  }
});

var makeLegendRow = function(color, name) {
  var colorBox = ui.Label({
    style: {
      backgroundColor: color,
      padding: '8px',
      margin: '0 0 4px 0'
    }
  });
  
  var description = ui.Label({
    value: name,
    style: {margin: '0 0 4px 6px'}
  });
  
  return ui.Panel({
    widgets: [colorBox, description],
    layout: ui.Panel.Layout.Flow('horizontal')
  });
};

legend.add(legendTitle);
legend.add(makeLegendRow('blue', 'Flooded Area'));
Map.add(legend);
