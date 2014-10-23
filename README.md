# Esri Leaflet GP

Esri Leaflet GP is a small series of API helpers and UI controls to interact with geoprocessing services published with ArcGIS Server

**Currently Esri Leaflet GP is in development and should be thought of as a beta or preview**

Esri Leaflet GP relies on the minimal Esri Leaflet Core which handles abstraction for requests and authentication when neccessary. You can find out more about the Esri Leaflet Core on the [Esri Leaflet downloads page](http://esri.github.com/esri-leaflet/downloads).

## Example
Note that this plugin requires changes introduced in esri-leaflet 1.0.0 Release Candidate 2.

Take a look at this [elevation profile demo](https://jgravois.github.io/esri-leaflet-gp/index.html) to see it in action.

```html
<!DOCTYPE html>
<html>
<head>
  <meta charset=utf-8 />
  <title>gp drivetime</title>
  <meta name='viewport' content='initial-scale=1,maximum-scale=1,user-scalable=no' />

  <!-- Load Leaflet from CDN-->
  <link rel="stylesheet" href="http://cdn.leafletjs.com/leaflet-0.7.3/leaflet.css" />
  <script src="http://cdn.leafletjs.com/leaflet-0.7.3/leaflet.js"></script>

  <!-- Esri Leaflet Core -->
    <script src="http://cdn-geoweb.s3.amazonaws.com/esri-leaflet/0.0.1-beta.5/esri-leaflet-core.js"></script>

  <!-- Esri Leaflet GP -->
  <script src="http://cdn-geoweb.s3.amazonaws.com/esri-leaflet-geocoder/0.0.1-beta.3/esri-leaflet-gp.js"></script>

  <style>
    body {
      margin:0;
      padding:0;
    }

    #map {
      position: absolute;
      top:0;
      bottom:0;
      right:0;left:0;
    }

    #info-pane {
      position: absolute;
      top: 10px;
      right: 10px;
      z-index: 10;
      padding: 1em;
      background: white;
    }
  </style>
</head>
<body>

<div id="map"></div>
<div id="info-pane" class="leaflet-bar">
</div>

<script>
  document.getElementById('info-pane').innerHTML = 'click on the map to calculate 5 and 10 minute drivetimes';

  var map = L.map('map').setView([40, -74.2], 12);

  L.esri.basemapLayer('NationalGeographic').addTo(map);

  var gpTask = new L.esri.Tasks.Geoprocessing("http://sampleserver1.arcgisonline.com/ArcGIS/rest/services/Network/ESRI_DriveTime_US/GPServer/CreateDriveTimePolygons", {useCors:false});
  gpTask.gpString("Drive_Times", "5 10");

  var driveTimes = new L.FeatureGroup();
  map.addLayer(driveTimes);

  var mapClickGeoJson = { "type": "FeatureCollection", "features": []};
  mapClickGeoJson.features.push({ "type":"Feature", "geometry":{"type":"Point"}});

  map.on('click', function(evt){
    driveTimes.clearLayers();
    mapClickGeoJson.features[0].geometry.coordinates = [];
    mapClickGeoJson.features[0].geometry.coordinates.push(evt.latlng.lng, evt.latlng.lat);
    gpTask.gpGeoJson("Input_Location", mapClickGeoJson)
    gpTask.run(driveTimeCallback);
  });

  function driveTimeCallback(response, raw, error){
    driveTimes.addLayer(L.geoJson(response));
  }

</script>

</body>
</html>
```

## L.esri.Tasks.Geoprocessing

### Constructor

**Extends** [`L.esri.Tasks.Task`](http://esri.github.io/esri-leaflet/api-reference/tasks/task.html)

Constructor | Options | Description
--- | --- | ---
`new L.esri.Task.Geoprocessing(url)`<br>`L.esri.Tasks.Tasks(options)` | [`<GeoprocessingOptions>`](#options) | Creates a new Geoprocessing Task.

### Options

L.esri.Tasks.Geoprocessing accepts all L.esri.Tasks.Task options.

### Methods

Method | Returns | Description
--- | --- | ---
`gpString(<String> inputParamName, <String> value)` | `this` | Sets an input string parameter.
`gpNumber(<String> inputParamName, <Number> value)` | `this` | Sets an input number parameter.
`gpBoolean(<String> inputParamName, <Boolean> value)` | `this` | Sets an input boolean parameter.
`gpGeoJson(<String> inputParamName, <GeoJson> value)` | `this` | Converts a GeoJson geometry or FeatureCollection into GeoServices json before setting the input parameter.
`run(<Function> callback)` | `this` | Calls the corresponding Geoprocessing service, passing the previously supplied input parameters.
`gpAsyncResultParam(<String> resultParamName, <Object> value)` | `this` | Sets a result parameter for Asynchronous geoprocessing services that require it.

#### Result Object

A single result from the geoprocessing service. You should not rely on all these properties being present in every result object.

Property | Type | Description
--- | --- | ---
`features` | [`L.geoJson`] | An array of geoJson features.
`result` | `<object>`| A result object typically containing a link to the url of an output file written to disk on the server.

#### GP Results

Geoprocessing results conform to the following format

```json
[
  {
    features: [L.geoJson],
    result:{
      "paramName": "Output_File",
      "dataType": "GPDataFile",
      "value": {
        "url": "http://server/arcgis/rest/directories/arcgisoutput/./_ags_856aed6eb_.png"
      }
    }
  }
]
```

## Development Instructions

1. [Fork and clone Esri Leaflet GP](https://help.github.com/articles/fork-a-repo)
2. `cd` into the `esri-leaflet-gp` folder
5. Install the dependencies with `npm install`
5. The example at `/index.html` should work
6. Make your changes and create a [pull request](https://help.github.com/articles/creating-a-pull-request)

## Dependencies

Esri Leaflet GP relies on the minimal Esri Leaflet Core which handles abstraction for requests and authentication when neccessary. You can fine out more about teh Esri Leaflet Core on the [Esri Leaflet downloads page](http://esri.github.com/esri-leaflet/downloads).

## Resources

* [Geoprocessing Services Documentation](http://resources.arcgis.com/en/help/arcgis-rest-api/index.html#/GP_Service/02r3000000rq000000/)
* [ArcGIS for Developers](http://developers.arcgis.com)
* [ArcGIS REST Services](http://resources.arcgis.com/en/help/arcgis-rest-api/)
* [twitter@esri](http://twitter.com/esri)

## Issues

Find a bug or want to request a new feature?  Please let us know by submitting an issue.

## Contributing

Esri welcomes contributions from anyone and everyone. Please see our [guidelines for contributing](https://github.com/esri/contributing).

## Terms and Conditions

Signup for an [ArcGIS for Developers account](https://developers.arcgis.com/en/plans) or purchase an [ArcGIS Online Organizational Subscription](http://www.arcgis.com/features/plans/pricing.html).

1. Once you have an account you are good to go. Thats it!
2. If you use this library in a revenue generating application or for goverment use you must upgrade to a paid account. You are not allowed to generate revenue while on a free plan.

This information is from the [ArcGIS for Developers Terms of Use FAQ](https://developers.arcgis.com/en/terms/faq/)

## Licensing
Copyright 2014 Esri

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

> http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.

A copy of the license is available in the repository's [license.txt]( https://raw.github.com/Esri/esri-leaflet-geocoder/master/license.txt) file.

[](Esri Tags: ArcGIS Web Mapping Leaflet Geocoding)
[](Esri Language: JavaScript)
