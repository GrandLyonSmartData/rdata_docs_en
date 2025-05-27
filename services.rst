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
https://data.grandlyon.com/geoserver/wms/[name_of_the_service]

One generally uses its GetCapabilities operation to know its precise content (layers list) :

https://data.grandlyon.com/geoserver/wms?SERVICE=WMS&REQUEST=GetCapabilities&VERSION=1.3.0

sends back an XML document listing (among others) the layers published by the service, from which you'll get the content with the help of a GetMap requ├¬st of this type :

https://data.grandlyon.com/geoserver/wms?LAYERS=adr_voie_lieu.adrcommune&FORMAT=image%2Fpng&EXCEPTIONS=application%2Fxml&TRANSPARENT=TRUE&VERSION=1.1.1&SERVICE=WMS&REQUEST=GetMap&STYLES=&SRS=EPSG%3A4171&BBOX=4.7,45.6,5,45.9&WIDTH=720&HEIGHT=780

It can seem a bit complicated and tedious. But cartographic libraries are here to help. Leaflet and OpenLayers implement WMS classes which will do these operations for you, adapting the requests to the manipulations made on the map. You just have to specify the service base URL and the name of the layer you want to use as our examples illustrate.


WFS Service
-----------
The WFS Service allows you to retrieve the raw data as it is recorded in the database. It is a download service, even if this download (the action of retrieving the data) is not necessarily full, as one can only download a part of the territory, mainly for the biggest datasets.
It is accessible from the URL :
https://data.grandlyon.com/geoserver/wfs/[name_of_the_service]

One generally uses its GetCapabilities operation to know its precise content (layers list) :

https://data.grandlyon.com/geoserver/wfs?SERVICE=WFS&REQUEST=GetCapabilities&VERSION=1.1.0

sends back a XML document listing (among others) the available layers in the service, from which you'll get the content with the help of the GetFeatures operation :

https://data.grandlyon.com/geoserver/wfs?SERVICE=WFS&REQUEST=GetFeature&typename=pvo_patrimoine_voirie.pvotronconwebcriter&VERSION=1.1.0

But here again, be reassured, the cartographic libraries have all the necessary classes for a very simple usage of this type of service.
The generally used format in WFS is GML (which stands for Geographic Markup Language), an XML derivative. However, this format is not the easiest to use in the context of a web application. Thus, while using WFS, it is still possible to receive a GeoJSON formatted stream by adding the OUTPUTFORMAT=geojson parameter :

https://data.grandlyon.com/geoserver/wfs?SERVICE=WFS&REQUEST=GetFeature&typename=pvo_patrimoine_voirie.pvotronconwebcriter&VERSION=1.1.0&OUTPUTFORMAT=geojson

API Features Service
--------------------

The API Features is similar to WFS, but offers a more modern interface.

The root of the service is https://data.grandlyon.com/geoserver/ogc/features/v1/

An OpenAPI definition is available here : https://data.grandlyon.com/geoserver/ogc/features/v1/openapi?f=text%2Fhtml

The following request lists the available datasets : https://data.grandlyon.com/geoserver/ogc/features/v1/collections

The usual format returned by the API Features is GeoJSON, but it can be changed with the "f" parameter (for instance, f=application/gml+xml;version=3.2). Heres is an example URL to fetch a dataset :

https://data.grandlyon.com/geoserver/ogc/features/v1/collections/metropole-de-lyon:pvo_patrimoine_voirie.pvotronconwebcriter/items?f=application%2Fjson&limit=50

More information on the standard can be found here https://ogcapi.ogc.org/features/ and here https://opengeospatial.github.io/e-learning/ogcapi-features/text/basic-index.html

WCS Service
-----------
The WCS Services (Web Coverage Service) allow you to directly retrieve the raw data of raster layers (like orthophotographies or DEM). The word Coverage matches the raster dataset. So it is indeed a download service applied to image datasources in which it is possible to filter the data to retrieve for only a part of the territory :

It is accessible from the URL :

https://data.grandlyon.com/geoserver/wcs/[name_of_the_service]

As for WMS and WFS, one can use the GetCapabilities operation to know its content (available layers list) :

https://data.grandlyon.com/geoserver/wcs?service=WCS&request=GetCapabilities&version=1.1.0

sends back a XML document listing (among others) the available layers provided by the service, which you'll get the description of with the help of a DescribeCoverage operation :

https://data.grandlyon.com/geoserver/wcs?service=WCS&request=DescribeCoverage&version=1.1.0&identifiers=grandlyon:MNT2012_Altitude_10m_CC46

The returned information concerns only the specified layers by the identifier parameter (here MNT2012_Altitude_10m_CC46 ) and are a little bit more accurate than in the GetCapabilities.

Finally, to obtain the desired coverage, one uses a GetCoverage request of this type :

https://data.grandlyon.com/geoserver/wcs?SERVICE=WCS&VERSION=1.0.0&REQUEST=GetCoverage&FORMAT=GeoTIFF&COVERAGE=grandlyon:MNT2015_Ombrage_2m&BBOX=1836243.96544679999351501,5162352.9513221001252532,1842093.96544679999351501,5168132.9513221001252532&CRS=EPSG:3946&RESPONSE_CRS=EPSG:3946&WIDTH=585&HEIGHT=578

Once again, it is a standardized service for which the cartographic libraries provide all the necessary classes and operation for a sipmle and efficient usage.

REST Services (JSON)
-----------------------
The JSON services of our infrastructure allows an easy and fast navigation between the several datasets provided. 

The entry point of each service is built on the following pattern :

``https://data.grandlyon.com/fr/datapusher/ws/<service>/all.json``

Currently available services are "grandlyon" and "rdata" :

``https://data.grandlyon.com/fr/datapusher/ws/grandlyon/all.json``

and

``https://data.grandlyon.com/fr/datapusher/ws/rdata/all.json``

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

``https://data.grandlyon.com/fr/datapusher/ws/<service>/<table_schema>.<table_name>.json``

From link to link, you can then navigate towards the tables description.

For instance : https://data.grandlyon.com/fr/datapusher/ws/grandlyon/abr_arbres_alignement.abrarbre.json

::

   {
      
      requested_table: "abr_arbres_alignement.abrarbre",
      
      nb_records: 92216,
      
      database_href: "https://data.grandlyon.com/fr/datapusher/ws/grandlyon/all.json",
      
      nb_results: 26,
      
      results: [{
      
         is_pk: false,
         
         column_type: "varchar",
         
         precision: 50,
         
         is_nullable: "YES",
         
         href: "https://data.grandlyon.com/fr/datapusher/ws/grandlyon/abr_arbres_alignement.abrarbre/essencefrancais.json",
         
         column_name: "essencefrancais"
      
      },{
         
         is_pk: false,
         
         column_type: "int4",
         
         precision: 32,
         
         is_nullable: "YES",
         
         href: "https://data.grandlyon.com/fr/datapusher/ws/grandlyon/abr_arbres_alignement.abrarbre/circonference_cm.json",
         
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

For instance the type of trees in Greater Lyon : https://data.grandlyon.com/fr/datapusher/ws/grandlyon/abr_arbres_alignement.abrarbre/essencefrancais.json

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

https://data.grandlyon.com/fr/datapusher/ws/grandlyon/abr_arbres_alignement.abrarbre/essencefrancais.json?compact=false&maxfeatures=50&start=101

One can also reach the full content of a table (or paginate this content) using a URL such this one :

https://data.grandlyon.com/fr/datapusher/ws/rdata/jcd_jcdecaux.jcdvelov/all.json?compact=false

to consult the integrality of the records. 

In this all.json mode which dislays individual records, compact flag is always false. 

The default number of returned records is set to 1000 for performance reasons. You can override this setting using the maxfeatures parameter.

*Example* : 
https://data.grandlyon.com/fr/datapusher/ws/grandlyon/gip_proprete.gipdecheterie/all.json?maxfeatures=10
 
It is also possible to filter records on an attribute value using a URL such this one :
``https://data.grandlyon.com/fr/datapusher/ws/<service>/<table_schema>.<table_name>/all.json?field=<attribut>&value=<valeur>``

For instance : https://data.grandlyon.com/fr/datapusher/ws/grandlyon/abr_arbres_alignement.abrarbre/all.json?field=essencefrancais&value=Marronnier%20de%20Virginie

all.json also contains complementary information about pagination. It does include two links towards previous and next page, using the same maxfeature and adapting the start parameter from the current page. 

*Example* : 
https://data.grandlyon.com/fr/datapusher/ws/grandlyon/gip_proprete.gipdecheterie/all.json?maxfeatures=5&start=10

returns records 10 to 15 of gipdecheterie layer.

The REST-JSON services are thus particularly adapted to the construction of values lists, tables and paginated grids, inside datasets GUI.

Then, in a Django style, custom parameters in the form `fields__operator`, with field = target field name and operator to choose from eq, gt, gte, lt, lte, in

Example: https://data.grandlyon.com/fr/datapusher/ws/grandlyon/abr_arbres_aligner.abrarbre/all.json?codeinsee__eq=69116&commune__in=LIMONEST,BRON&dateplantation__gte=2009-03-01&gid__in=6795,6798

The operators:
* `eq` : (equal) equality
* `gt`: (greater than) strictly greater than
* `gte`: (greater than or equal)
* `lt`: (lesser than) strictly less than
* `lte`: (lesser than or equal) less than or equal
* `in`: (in) in the list, the elements of the list are separated by commas.


REST Services (CSV)
-------------------

*Example* :
https://data.grandlyon.com/fr/datapusher/ws/grandlyon/gip_proprete.gipdecheterie_3_0_0/all.csv?maxfeatures=5&start=10

In the same way that we request the JSON service, we can request a CSV extract by replacing the ".json" extension of the URL with ".csv".

The decimal separator can be replaced by adding 'ds=,' or 'ds=.' in the query.

The column separator can also be changed using the "separator=;" option for example.

An additional parameter "geometry=on" (off by default) adds a column "WKT" containing the geometry of the object in [WKT] format(https://fr.wikipedia.org/wiki/Well-known_text)

Shapefile export
----------------

Shapefile export is available through WFS service by using SHAPEFILE format (for exemple : https://data.grandlyon.com/geoserver/sytral/ows?SERVICE=WFS&VERSION=2.0.0&request=GetFeature&typename=sytral:tcl_sytral.tcllignebus_2_0_0&outputFormat=SHAPE-ZIP&SRSNAME=EPSG:4171&format_options=CHARSET:UTF-8&sortBy=gid). It returns a zip file containing the requested layer as a shapefile (SHP + SHX + DBF).

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

* https://imagerie.data.grandlyon.com/all/wmts?service=WMTS&request=getcapabilities
* https://imagerie.data.grandlyon.com/2154/wmts?service=WMTS&request=getcapabilities
* https://imagerie.data.grandlyon.com/3857/wmts?service=WMTS&request=getcapabilities
* https://imagerie.data.grandlyon.com/3946/wmts?service=WMTS&request=getcapabilities

These streams have a cache and are preferred over WMS streams available on https://data.grandlyon.com/geoserver/wms

OpenMapTiles Services
---------------------

This service offers OpenMaptiles tiles to use for basemaps

Demo Client: https://openmaptiles.data.grandlyon.com/data/v3/#8.37/45.796/4.592

These tiles are updated weekly using OpenStreetMap data.

https://openmaptiles.data.grandlyon.com/data/v3/1/1/0.pbf

These tiles can be used by the main web GIS frameworks (MaplibreGL, Leaflet,...) for example: https://openmaptiles.org/docs/website/maplibre-gl-js/


KML Services
------------
GrandLyon Data also publishes the data in KML format. Data for each service is accessible from the following URL : 
https://data.grandlyon.com/geoserver/ows?SERVICE=WFS&VERSION=2.0.0&request=GetFeature&typename=[organisation:][schema].[name]&outputFormat=kml&SRSNAME=EPSG:4171&sortBy=gid

Exemple* : https://data.grandlyon.com/geoserver/ows?SERVICE=WFS&VERSION=2.0.0&request=GetFeature&typename=sytral:tcl_sytral.tcllignebus_2_0_0&outputFormat=kml&SRSNAME=EPSG:4171&sortBy=gid

Services MVT
------------

Vector datasets are available in Mapbox Vector Tile (MVT) format by using WMTS service and  *application/vnd.mapbox-vector-tile* format (Mapbox Vector Tile)[https://docs.mapbox.com/vector-tiles/specification/]

https://data.grandlyon.com/geoserver/gwc/service/wmts?LAYERS=sytral:tcl_sytral.tcllignebus_2_0_0&SERVICE=WMTS&VERSION=1.0.0&REQUEST=GetTile&layer=sytral:tcl_sytral.tcllignebus_2_0_0&TILEMATRIX=EPSG:900913:10&TILEMATRIXSET=EPSG:900913&FORMAT=application/vnd.mapbox-vector-tile&TILECOL=525&TILEROW=365

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
`https://data.grandlyon.com/statistiques/dataset?start=2023-02-5&uuid=4d59a6fd-f99f-47af-a0d3-8f21082a45fa&layername=sytral:tcl_sytral.tcllignebus_2_0_0&granularity=month&end=2024-02-5`

::

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



The requested parameters (all case insensitive):

* UUID the uuid of the dataset
* layername of the dataset
* start: YYYY-MM-DD format the start date, if the granularity is the week or month and the date is "in" the week/month, I take the beginning of the week/month
* end: YYYY-MM-DD format the end date, if the granularity is the week or month and the date is "in" the week/month, I take the weekend/month
* granularity: day, week, year.

For the answer:

Dictionary table: one dictionary per granularity element (day, week, month). The dictionary contains:

* date: YYYY-MM-DD format the start date of the granularity element
* count: the number of times the resource has been viewed, regardless of the service (WMS/WFS...)
