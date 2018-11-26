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

https://download.data.grandlyon.com/wcs/grandlyon?service=WCS&request=DescribeCoverage&version=1.1.0&identifiers=Ortho2009_vue_ensemble_16cm_CC46,1830_5155_16_CC46

The returned information concerns only the specified layers by the identifier parameter (here Ortho2009_vue_ensemble_16cm_CC46 and 1830_5155_16_CC46) and are a little bit more accurate than in the GetCapabilities.

Finally, to obtain the desired coverage, one uses a GetCoverage request of this type :

https://download.data.grandlyon.com/wcs/grandlyon?service=WCS&BBOX=1830000,5155000,1830100,5155100&request=GetCoverage&version=1.1.0&format=image/tiff&crs=EPSG::3946&identifiers=1830_5155_16_CC46

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

http://mapserver.middle.data.grandlyon.com/wms/ldata?

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
