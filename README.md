# Interactive-Mapping

Walking Distance on Campus

I don't know how to take a screenshot, I'm sorry. 

This map is used to calculate walking and travelling distances between classes at UBC Vancouver on Campus. It allows the user to click to add points at the location of their classes or walking routes and the map will calulate the total distacne walked. This could be useful for personal inquiry, or for UBC administration or faculties as they could easily collect data for walking distances that could be used for various purposes. For example, walking times could be considered for faculty classes too far away or spacing out exam rooms based on faculty. It could also be used for personal health data to quantify walking distances and calories burned, etc.

The base of the map is from mapbox interactive tutorials. I have customized it to be the most user friendly for UBC students by changing style and locations that the map is based on. I edited my code on the p5.js editor. I also have no idea how to host a web page or what that is, other than the very vague example we tried in class about a month ago, which I can't find. Thus, I have copied my html code below. I think this is the readme file but I'm not 100% sure. 

How I would like to improve my map: If I were able to program my map the way I would like, there would be a number of changes I would make. For one, I would find a style that clearly indicates campus buildings, either by labelling or perhaps mouseover. Another issue with the map is that because the user selects each point, to measure from class to class is a direct line, as opposed to a more accurate walking route. To improve this the user must click ever point of direction change along their walking route, this would not be efficient. It would be useful for the map to automatically route from class to class based on walking paths or roads as is done by google or apple maps in directions. The most difficult part of this assignment, in my opinion, is actually figuring out how to hand it in.

https://editor.p5js.org/stevors/sketches/5hunDAlEv

<!DOCTYPE html>
<html>
<head>
<meta charset='utf-8' />
<title>Measure distances</title>
<meta name='viewport' content='initial-scale=1,maximum-scale=1,user-scalable=no' />
<script src='https://api.tiles.mapbox.com/mapbox-gl-js/v0.53.1/mapbox-gl.js'></script>
<link href='https://api.tiles.mapbox.com/mapbox-gl-js/v0.53.1/mapbox-gl.css' rel='stylesheet' />
<style>
body { margin:0; padding:0; }
#map { position:absolute; top:0; bottom:0; width:100%; }
</style>
</head>
<body>
 
<style>
.distance-container {
position: absolute;
top: 10px;
left: 10px;
z-index: 1;
}
 
.distance-container > * {
background-color: rgba(0, 0, 0, 0.5);
color: #fff;
font-size: 11px;
line-height: 18px;
display: block;
margin: 0;
padding: 5px 10px;
border-radius: 3px;
}
</style>
 
<div id='map'></div>
<div id='distance' class='distance-container'></div>
 
  // creating a customized style and location of map
  
<script src='https://api.mapbox.com/mapbox.js/plugins/turf/v2.0.2/turf.min.js'></script>
<script>
mapboxgl.accessToken = 'pk.eyJ1Ijoic3Rldm9yczA3IiwiYSI6ImNqcnYxZ3NraTBlbmU0M2w1aXlmaDc1anYifQ.2rgpndd_MR45azpuCOzIIw';
var map = new mapboxgl.Map({
container: 'map',
style: 'mapbox://sprites/mapbox/streets-v9', 
center: [-123.25, 49.262], //UBC geo coordinates
zoom: 13.6
});
 
var distanceContainer = document.getElementById('distance');
 
var geojson = {
"type": "FeatureCollection",
"features": []
};
 
  //direct line from class to class
  
var linestring = {
"type": "Feature",
"geometry": {
"type": "LineString",
"coordinates": []
}
};
 
map.on('load', function() {
map.addSource('geojson', {
"type": "geojson",
"data": geojson
});
 
// visual properties of user selected point
  
map.addLayer({
id: 'measure-points',
type: 'circle',
source: 'geojson',
paint: {
'circle-radius': 5,
'circle-color': '#000'
},
filter: ['in', '$type', 'Point']
});
map.addLayer({
id: 'measure-lines',
type: 'line',
source: 'geojson',
layout: {
'line-cap': 'round',
'line-join': 'round'
},
paint: {
'line-color': '#000',
'line-width': 2.5
},
filter: ['in', '$type', 'LineString']
});
 
map.on('click', function(e) {
var features = map.queryRenderedFeatures(e.point, { layers: ['measure-points'] });
 

if (geojson.features.length > 1) geojson.features.pop();
 
distanceContainer.innerHTML = '';
 
if (features.length) {
var id = features[0].properties.id;
geojson.features = geojson.features.filter(function(point) {
return point.properties.id !== id;
});
} else {
var point = {
"type": "Feature",
"geometry": {
"type": "Point",
"coordinates": [
e.lngLat.lng,
e.lngLat.lat
]
},
"properties": {
"id": String(new Date().getTime())
}
};
 
geojson.features.push(point);
}
 
if (geojson.features.length > 1) {
linestring.geometry.coordinates = geojson.features.map(function(point) {
return point.geometry.coordinates;
});
 
geojson.features.push(linestring);
 
var value = document.createElement('pre');
value.textContent = 'Total distance: ' + turf.lineDistance(linestring).toLocaleString() + 'km';
distanceContainer.appendChild(value);
}
 
map.getSource('geojson').setData(geojson);
});
});
 
map.on('mousemove', function (e) {
var features = map.queryRenderedFeatures(e.point, { layers: ['measure-points'] });

  map.getCanvas().style.cursor = (features.length) ? 'pointer' : 'crosshair';
});
</script>
 
</body>
</html>


