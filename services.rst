Services provided by GrandLyon Data
=======================================

The GrandLyon data platform allows you to connect to several consultation services of Open Datasets. The services are of two kinds : view services making you display images (maps) and download services allowing you to directly retrieve the raw data, in various formats and ways.

When building up an online mapping application, one can not deal the same way with all the data. There is indeed data for background (base maps), data for adding contextual information (various points of interest such as stations, a city hall...) and finally data holding the important information, the one you really want to show up and emphasize in your application (bike availability, real time bus locations...)

When the base map is displayed as a non-interactive image format, and often obtained from a tiled cache system providing fast access to small tiles (256 x 256 pixels) drawn once for all, it will always be the lower layer in the application, the one below all the others, for obvious visibility reasons.

The important, critical information, bearing the application value-added will be the most interactive. So that a simple mouse-over opens a tooltip with the essential information, a single click opens a full notice, a dynamic change of style (size, color) underlines a selection, an association, a relation with another element of the map. In order to achieve that, one has to use a download service, to be able to transmit the raw data allowing the building of a vectorial layer in the mapping application, or to give access to all the associated attributes directly.
For the other data layers, arbitration is mandatory. Because we can't load all the data in vectorial mode for both readability  (too many points/lines blinking, bolding or shaking at the same time make the map useless) and performances reasons (every single coordinate is written in the page object model. Several thousands points are too heavy to be rendered smoothly by the browser). So we deal. We use view services (WMS, image format) for the datasets in which the location is the most important (for instance for disabled parking spaces, there is not much to say in a tooltip, one just has to know where it is, and displaying it is enough) or in which the spatial extent has a special point (park or restricted area border). And we choose the vectorial format for some spots closely related to the principal subject of the map, but having precious information associated to it (bike stands, events...)


WMS Service
-----------
The WMS service is the view service of reference. It is used to "see" the geographical data with a predefined styling (colors, symbols, sizes). It is the preferred service for the integration of a dataset into an image format.
It is accessible from the following URL :
https://download.data.grandlyon.com/wms/[name_of_the_service]

One generally uses its GetCapabilities operation to know its precise content (layers list) :

https://download.data.grandlyon.com/wms/grandlyon?SERVICE=WMS&REQUEST=GetCapabilities&VERSION=1.3.0

sends back an XML document listing (among others) the layers published by the service, from which you'll get the content with the help of a GetMap requ├¬st of this type :

https://download.data.grandlyon.com/wms/grandlyon?LAYERS=adr_voie_lieu.adrcommune&FORMAT=image%2Fpng&EXCEPTIONS=application%2Fxml&TRANSPARENT=TRUE&VERSION=1.1.1&SERVICE=WMS&REQUEST=GetMap&STYLES=&SRS=EPSG%3A4171&BBOX=4.7,45.6,5,45.9&WIDTH=720&HEIGHT=780

It can seem a bit complicated and tedious. But cartographic libraries are here to help. Leaflet and OpenLayers implement WMS classes which will do these operations for you, adapting the requests to the manipulations made on the map. You just have to specify the service base URL and the name of the layer you want to use as our examples illustrate.


WFS Service
-----------
The WFS Service allows you to retrieve the raw data as it is recorded in the database. It is a download service, even if this download (the action of retrieving the data) is not necessarily full, as one can only download a part of the territory, mainly for the biggest datasets.
It is accessible from the URL :
https://download.data.grandlyon.com/wfs/[name_of_the_service]

One generally uses its GetCapabilities operation to know its precise content (layers list) :

https://download.data.grandlyon.com/wfs/grandlyon?SERVICE=WFS&REQUEST=GetCapabilities&VERSION=1.1.0

sends back a XML document listing (among others) the available layers in the service, from which you'll get the content with the help of the GetFeatures operation :

https://download.data.grandlyon.com/wfs/grandlyon?SERVICE=WFS&REQUEST=GetFeature&typename=pvo_patrimoine_voirie.pvotronconwebcriter&VERSION=1.1.0

But here again, be reassured, the cartographic libraries have all the necessary classes for a very simple usage of this type of service.
The generally used format in WFS is GML (which stands for Geographic Markup Language), an XML derivative. However, this format is not the easiest to use in the context of a web application. Thus, while using WFS, it is still possible to receive a GeoJSON formatted stream by adding the OUTPUTFORMAT=geojson parameter :

https://download.data.grandlyon.com/wfs/grandlyon?SERVICE=WFS&REQUEST=GetFeature&typename=pvo_patrimoine_voirie.pvotronconwebcriter&VERSION=1.1.0&OUTPUTFORMAT=geojson

WCS Service
-----------
The WCS Services (Web Coverage Service) allow you to directly retrieve the raw data of raster layers (like orthophotographies or DEM). The word Coverage matches the raster dataset. So it is indeed a download service applied to image datasources in which it is possible to filter the data to retrieve for only a part of the territory :

It is accessible from the URL :
https://download.data.grandlyon.com/wcs/[name_of_the_service]

As for WMS and WFS, one can use the GetCapabilities operation to know its content (available layers list) :

https://download.data.grandlyon.com/wcs/grandlyon?service=WCS&request=GetCapabilities&version=1.1.0

sends back a XML document listing (among others) the available layers provided by the service, which you'll get the description of with the help of a DescribeCoverage operation :

https://download.data.grandlyon.com/wcs/grandlyon?service=WCS&request=DescribeCoverage&version=1.1.0&identifiers=MNT2015_Altitude_10m_WCS,road_ln_p

The returned information concerns only the specified layers by the identifier parameter (here MNT2015_Altitude_10m_WCS and road_ln_p) and are a little bit more accurate than in the GetCapabilities.

Finally, to obtain the desired coverage, one uses a GetCoverage request of this type :

https://download.data.grandlyon.com/wcs/grandlyon?SERVICE=WCS&VERSION=1.0.0&REQUEST=GetCoverage&FORMAT=GTiff&COVERAGE=road_ln_p&BBOX=1836243.96544679999351501,5162352.9513221001252532,1842093.96544679999351501,5168132.9513221001252532&CRS=EPSG:3946&RESPONSE_CRS=EPSG:3946&WIDTH=585&HEIGHT=578

Once again, it is a standardized service for which the cartographic libraries provide all the necessary classes and operation for a sipmle and efficient usage.

REST Services (JSON)
-----------------------
The JSON services of our infrastructure allows an easy and fast navigation between the several datasets provided. 

The entry point of each service is built on the following pattern :

``https://download.data.grandlyon.com/ws/<service>/all.json``

Currently available services are "grandlyon" and "rdata" :

``https://download.data.grandlyon.com/ws/grandlyon/all.json``

and

``https://download.data.grandlyon.com/ws/rdata/all.json``

The documents list all the available tables both in consultation and download. Some of them can have a restricted access depending on your rights.

:: 
  
  {
      
      results: [{
      
         table_schema: "abr_arbres_alignement",
         
         href: "https://download.data.grandlyon.com/ws/grandlyon/abr_arbres_alignement.abrarbre.json",
         
         table_name: "abrarbre"
      
      },{
         
         table_schema: "adr_voie_lieu",
         
         href: "https://download.data.grandlyon.com/ws/grandlyon/adr_voie_lieu.adradresse.json",
         
         table_name: "adradresse"

      },{
      
         ...
         
      }]

   }

Each table has an associated URL with the following pattern : 

``https://download.data.grandlyon.com/ws/<service>/<table_schema>.<table_name>.json``

From link to link, you can then navigate towards the tables description.

For instance : https://download.data.grandlyon.com/ws/grandlyon/abr_arbres_alignement.abrarbre.json

::

   {
      
      requested_table: "abr_arbres_alignement.abrarbre",
      
      nb_records: 92216,
      
      database_href: "https://download.data.grandlyon.com/ws/grandlyon/all.json",
      
      nb_results: 26,
      
      results: [{
      
         is_pk: false,
         
         column_type: "varchar",
         
         precision: 50,
         
         is_nullable: "YES",
         
         href: "https://download.data.grandlyon.com/ws/grandlyon/abr_arbres_alignement.abrarbre/essencefrancais.json",
         
         column_name: "essencefrancais"
      
      },{
         
         is_pk: false,
         
         column_type: "int4",
         
         precision: 32,
         
         is_nullable: "YES",
         
         href: "https://download.data.grandlyon.com/ws/grandlyon/abr_arbres_alignement.abrarbre/circonference_cm.json",
         
         column_name: "circonference_cm"
      
      },{
      
         ...
         
      }]

   }

List of displayed fields :

* **is_pk**: is the layer identifier ? 

* **column_type**: field type (numeric, text, etc)

* **precision**: field size

* **is_nullable**: is null value possible ?

* **href**: distinct values of the target attribute 

* **column_name**: field name

The URL in the href field provides access to the different predefined values used in a specific field.

For instance the type of trees in Greater Lyon : https://download.data.grandlyon.com/ws/grandlyon/abr_arbres_alignement.abrarbre/essencefrancais.json

::

   {
      
      fields: [
         
         "essencefrancais"
      
      ],
      
      nb_results: 401,
      
      values: [
         
         "Magnolia à grandes fleurs",
        
         "Erable rouge 'Schlesingeri'",
         
         "Arbre puant des Chinois",
         
         "Chène rouge d'Espagne",
         
         "Frêne d'Amérique",
         
         "Orme champêtre",
         
         "Chêne pédonculé fastigié, Chêne pyramidal",
         
         ...
      
      ]
   
   }

This last mode provides a few more options :

* **compact** : if false, gives a (key,value) result for all the records, else, only lists the different values found in the whole table. Default is True.

* **maxfeatures** : indicates the maximal number of records to be returned by the service. Default is 1000.

* **start** : indicates the start index, in order to paginate the results. Default is 1.

Thus, one can request the service for 50 kinds of trees from the 100th in the database (which can sound useless however):

https://download.data.grandlyon.com/ws/grandlyon/abr_arbres_alignement.abrarbre/essencefrancais.json?compact=false&maxfeatures=50&start=101

One can also reach the full content of a table (or paginate this content) using a URL such this one :

https://download.data.grandlyon.com/ws/rdata/jcd_jcdecaux.jcdvelov/all.json?compact=false

to consult the integrality of the records. 

In this all.json mode which dislays individual records, compact flag is always false. 

The default number of returned records is set to 1000 for performance reasons. You can override this setting using the maxfeatures parameter.

*Example* : 
https://download.data.grandlyon.com/ws/grandlyon/gip_proprete.gipdecheterie/all.json?maxfeatures=10
 
It is also possible to filter records on an attribute value using a URL such this one :
``https://download.data.grandlyon.com/ws/<service>/<table_schema>.<table_name>/all.json?field=<attribut>&value=<valeur>``

For instance : https://download.data.grandlyon.com/ws/grandlyon/abr_arbres_alignement.abrarbre/all.json?field=essencefrancais&value=Marronnier%20de%20Virginie

all.json also contains complementary information about pagination. It does include two links towards previous and next page, using the same maxfeature and adapting the start parameter from the current page. 

*Example* : 
https://download.data.grandlyon.com/ws/grandlyon/gip_proprete.gipdecheterie/all.json?maxfeatures=5&start=10

returns records 10 to 15 of gipdecheterie layer.

The REST-JSON services are thus particularly adapted to the construction of values lists, tables and paginated grids, inside datasets GUI.

REST Services (CSV)
-------------------

*Example* :
https://download.data.grandlyon.com/ws/grandlyon/gip_proprete.gipdecheterie/all.csv?maxfeatures=5&start=10

In the same way that we request the JSON service, we can request a CSV extract by replacing the ".json" extension of the URL with ".csv".

The decimal separator can be replaced by adding 'ds=,' or 'ds=.' in the query.

The column separator can also be changed using the "separator=;" option for example.

An additional parameter "geometry=on" (off by default) adds a column "WKT" containing the geometry of the object in [WKT] format(https://fr.wikipedia.org/wiki/Well-known_text)

Shapefile export
----------------

The shapefile export can be used from JSON services by using a .shp extension when requesting a layer (i.e. https://download.data.grandlyon.com/ws/rdata/pvo_heritage_family. pvotrafic.shp). This returns to the user a zip containing a shapefile (SHP + SHX + DBF) of the layer.

In addition, several parameters are available :

* srsname, which allows to specify the coordinate system for the export (using EPSG: XXXX, to choose between 4171, 3946, 2154, 4326 and 4258)

* a clipping third layer, which will be identified by a mask_layer attribute (name of the clipping layer), mask_field (fields on which to filter this layer) and finally mask_value (value of the field mask_field to generate the clipping polygon). This makes it possible to recover a geometrical entity that will serve as a filter for the requested main layer.

Example : https://download.data.grandlyon.com/ws/grandlyon/gic_collect.gicsiloverre.shp?mask_db=grandlyon&mask_layer=adr_circle_watch.adrcommune&mask_field=insee&mask_value=69034

The layer gicsiloverre will be cut to recover only the objects located in the polygon having the attribute insee at 69034 of the adr_voie_lieu.adrcommune layer.


WMTS Service
------------

The Data platform delivers a tiled mapping service respecting the WMTS standard. Two tilesets are provided, the 2015 Orthophotography of the Metropole, and an `OpenStreetMap <http://www.openstreetmap.fr>`_ cover of the Auvergne-Rhône-Alpes and Burgundy regions. The WMTS service is callable from the URL :

https://openstreetmap.data.grandlyon.com/wmts/

.. image:: https://openstreetmap.data.grandlyon.com/wmts/?SERVICE=WMTS&REQUEST=GetTile&VERSION=1.0.0&LAYER=osm_grandlyon&STYLE=default&TILEMATRIXSET=GoogleMapsCompatible&TILEMATRIX=16&TILEROW=23379&TILECOL=33653&FORMAT=image%2Fpng
   :alt: GrandLyon Data : OpenStreetMap WMTS Service
   :class: floatingflask

.. image:: https://openstreetmap.data.grandlyon.com/wmts/?SERVICE=WMTS&REQUEST=GetTile&VERSION=1.0.0&LAYER=ortho2015&STYLE=default&TILEMATRIXSET=GoogleMapsCompatible&TILEMATRIX=16&TILEROW=23378&TILECOL=33652&FORMAT=image%2Fjpeg
   :alt: GrandLyon Data : 2015 Orthophotography tileset
   :class: floatingflask

The name of the tilesets are respectively osm_grandlyon and ortho2015. These tilesets are available in Spherical Mercator projection system (EPSG:3857 et EPSG:900913)  and are therefore compatible with other services of the same kind, like GoogleMaps or French IGN API.
To use the WMTS service within QGIS, remember to set the service URL to the full GetCapabilities request URL :
https://openstreetmap.data.grandlyon.com/wmts/?REQUEST=GetCapabilities&SERVICE=WMTS

WMTS Service (Orthophotographs)
-------------------------------

Additional WMTS/WMS services exist broadcasting orthophotograph streams.

They can be accessed here:
* https://imagerie.data.grandlyon.com/all/wmts?service=WTMS&request=getcapabilities
* https://imagerie.data.grandlyon.com/2154/wmts?service=WTMS&request=getcapabilities
* https://imagerie.data.grandlyon.com/3857/wmts?service=WTMS&request=getcapabilities
* https://imagerie.data.grandlyon.com/3946/wmts?service=WTMS&request=getcapabilities

These streams have a cache and are preferred over WMS streams available on https://download.data.grandlyon.com/wms/grandlyon

OpenMapTiles Services
---------------------

This service offers OpenMaptiles tiles to use for basemaps

Demo Client: https://openmaptiles.data.grandlyon.com/data/v3/#8.37/45.796/4.592

These tiles are updated weekly using OpenStreetMap data.

https://openmaptiles.data.grandlyon.com/data/v3/1/1/0.pbf

These tiles can be used by the main web GIS frameworks (MaplibreGL, Leaflet,...) for example: https://openmaptiles.org/docs/website/maplibre-gl-js/



SOS Service (sensor data)
-------------------------

SOS is a standard web service interface for requesting, filtering, and retrieving observations and sensor system information. It is available in two versions: ``1.0.0`` et ``2.0.0``. SOS is part of the OGC *Sensor Web Enablement* (SWE) initiative.

The first one is related to noise sensors (1) located in the Lyon urban area. The second service publishes the availability of free bikes for hire in the city and surrounding area (2).

\(1\) https://download.data.grandlyon.com/sos/bruit?service=SOS&request=GetCapabilities

\(2\) https://download.data.grandlyon.com/sos/velov?service=SOS&request=GetCapabilities

For now, the above services are available in SOS ``1.0.0``.

Like any OGC services, *GetCapabilities* is used to request a capabilities document that describes the service's functionality. *DescribeSensor* returns metadata that describes the characteristics of an observation procedure as a XML-based Sensor Model Language (SensorML). Then the *GetObservation* operation is designed to query a service to retrieve observation data structured according to the Observation and Measurement specification.


Graph view
**********

Sensor data can be viewed on a simple time based graph. The viewer is implemented for both SOS services: temporal evolution of the noise level (1) and availability of Lyon city bike (2).

Graph service URL: ``http://demo.data.grandlyon.com/graph/<bruit|velov>?``

Which requires two query string parameters:

* ``offering``: The name of the sensor offering.
* ``procedure`` : The name of the sensor.

Offering and procedure values are given in the capacities document (from the *GetCapabilities* request) .

\(1\) Acoustic survey data:

* http://demo.data.grandlyon.com/graph/bruit/?offering=observatoire_acoustique_grandlyon&procedure=AF01
* http://demo.data.grandlyon.com/graph/bruit/?offering=observatoire_acoustique_grandlyon&procedure=AF02

\(2\) City bike avaibility:

* http://demo.data.grandlyon.com/graph/velov/?&procedure=velov-1001&offering=reseau_velov
* http://demo.data.grandlyon.com/graph/velov/?&procedure=velov-1002&offering=reseau_velov

Webmapping tool (WMS-Time)
**************************

Data shall be available by a WMS service which can provide temporal requests (with the ``TIME`` parameter).

https://download.data.grandlyon.com/wms/ldata?

* \(1\) Acoustic survey data: ``bruit.stations_observatoire_acoustique`` / ``bruit.stations_observatoire_acoustique_time``
* \(2\) City bike avaibility: ``velov.stations`` / ``velov.stations_time``

Also, sensor data can be viewed through online mapping tools which include the flexibility to go back in time using intuitive slider controls.

* \(1\) Acoustic survey data: http://demo.data.grandlyon.com/wmst/observatoire_acoustique_grandlyon.html
* \(2\) City bike avaibility: http://demo.data.grandlyon.com/wmst/reseau_velov.html

Map is clickable. It returns information data about the sensor station (hyperlink to the graph view, last update, etc.).


KML Services
------------
GrandLyon Data also publishes the data in KML format. Data for each service is accessible from the following URL : 
https://download.data.grandlyon.com/kml/[service_name]/?request=layer&typename=[schema].[name]

*Example* : https://download.data.grandlyon.com/kml/grandlyon/?request=layer&typename=pvo_patrimoine_voirie.pvostationvelov

This format is suitable for Google Earth and Maps Javascript API (Google)

For performance reasons, the KML service uses WMS flow instead of vector objects when the number of objects to display is too large (greater than 1000).

It is also possible to consult all the layers of a service in the same tree using the parameter ``request=list`` (instead of request=layer).
*Example* : https://download.data.grandlyon.com/kml/grandlyon?request=list

Services MVT
------------

Vector datasets are available in Mapbox Vector Tile (MVT) format[https://docs.mapbox.com/vector-tiles/specification/]

https://download.data.grandlyon.com/mvt/grandlyon?LAYERS=car_care.carencadrmtloyer_latest&map.imagetype=mvt&tilemode=gmap&tile=1052+730+11&mode=tile

This format is comparable to WFS but is tiled and geometrics are simplified. The goal is to be much faster than the WFS by allowing in addition to being cached. In output we obtain a tile encoded using the format (PBF)[https://fr.wikipedia.org/wiki/Protocol_Buffers] (protobuf) (more compact equivalent of JSON)

These tiles can be used by web clients like MapboxGL, MapLibre or OpenLayers.

QGis can also read these tiles using the "Vector Tiles Reader" plugin. QGIS 3.20 manages MVT.


Geocoder Photon
---------------

This service makes it possible to perform direct geocoding (conversion of a postal address or place name into geographical coordinates) and reverse (conversion of geographical coordinates into postal address or place name).

It is powered by the Open-Source tool Photon (see https://github.com/komoot/photon), powered by OpenStreetMap data relating to the former Rhône-Alpes region (see https://download.geofabrik.de/europe/france/rhone-alpes.html).

The official documentation for Photon's search API is populated on GitHub, https://github.com/komoot/photon#search-api.

The link to make a query is as follows (replace the.. with the place to geocode):
https://download.data.grandlyon.com/geocoding/photon/api?q=...
Examples:
https://download.data.grandlyon.com/geocoding/photon/api?q=lyon
https://download.data.grandlyon.com/geocoding/photon/api?q=%22Rue%20garibaldi%22


Dataset statistics
------------------

To query statistics, queries take the following form:
`https://download.data.grandlyon.com/statistiques/dataset?UUID=ad771ef2-7a40-11ea-ae2d-dbfa27ebc23c&start=2019-04-09&end=2020-04-09&granularity=week`

```json
[
  {"date": "2019-04-08", "service":"wms", "count":362},
  {"date": "2019-04-08", "service":"wfs", "count":123},
  {"date": "2019-04-08", "service":"ws", "count":12},
  {"date": "2019-04-08", "service":"kml", "count":2},
  {"date": "2019-04-15", "service":"wms", "count":364},
  {"date": "2019-04-15", "service":"wfs", "count":125},
  {"date": "2019-04-15", "service":"ws", "count":10},
  {"date": "2019-04-15", "service":"kml", "count":4},
  ...
  {"date": "2020-03-30", "service":"wms", "count":462},
  {"date": "2020-03-30", "service":"wfs", "count":223},
  {"date": "2020-03-30", "service":"ws", "count":22},
  {"date": "2020-03-30", "service":"kml", "count":50},
  {"date": "2020-04-06", "service":"wms", "count":202},
  {"date": "2020-04-06", "service":"wfs", "count":113},
  {"date": "2020-04-06", "service":"ws", "count":22},
  {"date": "2020-04-06", "service":"kml", "count":7},
]
```


The requested parameters (all case insensitive):

 * UUID the uuid of the dataset
 * start: YYYY-MM-DD format the start date, if the granularity is the week or month and the date is "in" the week/month, I take the beginning of the week/month
 * end: YYYY-MM-DD format the end date, if the granularity is the week or month and the date is "in" the week/month, I take the weekend/month
 * granularity: day, week, year.

Pour la réponse :

Dictionary table: one dictionary per granularity element (day, week, month)
The dictionary contains:
 * date: YYYY-MM-DD format the start date of the granularity element
 * count: the number of times the resource has been viewed, regardless of the service (WMS/WFS...)
