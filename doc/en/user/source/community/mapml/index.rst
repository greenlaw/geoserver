.. _mapml:

MapML 
=========

Map Markup Language (MapML) is a text-based format which allows map authors to encode map information as hypertext documents exchanged over the Uniform Interface of the Web. The format definition is still a work-in-progress by the Maps for HTML W3C Community Group, but various tools to work with the format already exist, including a Leaflet-based map viewer. For more information on MapML refer to the `Maps for HTML Community Group <https://maps4html.github.io/>`_.

The MapML module for GeoServer adds new MapML resources to access WMS and WFS services configured in Geoserver. The MapML modules includes support for styles, tiling, querying, sharding, and dimensions options for WMS layers, and also provides a MapML outputFormat for WMS GetFeatureInfo and WFS GetFeatures requests. See below for information on installing and configuring the MapML module.


Installation
--------------------

#. Download the MapML extension from the `nightly GeoServer community module builds <https://build.geoserver.org/geoserver/master/community-latest/>`_.

   .. warning:: Make sure to match the version of the extension to the version of the GeoServer instance.

#. Extract the contents of the archive into the ``WEB-INF/lib`` directory of the GeoServer installation.


Configuration
-------------

Configuration can be done using the Geoserver administrator GUI. The MapML configuration is accessible in the *MapML Settings* section under the *Publishing* tab of the Layer Configuration page for the layer being configured. Here is the MapML Settings section, with some example values filled in:

.. figure:: images/mapml_config_ui.png

License Title
  The License Title will be included as the value of ``title`` attribute of the ``license`` ``link`` element in the MapML header.

License Link
  The License Link will be included as the value of ``href`` attribute of the ``license`` ``link`` element in the MapML header, and should be a valid URL referencing the license document.
  
Use Tiles
  If the Use Tiles checkbox is checked, the output MapML will define a tile-based reference to the WMS server. Otherwise, an image-based reference will be used.
  
Enable Sharding
  If Enable Sharding is checked, and values are provided for the Shard List and Shard Server Pattern fields, then a hidden shard list input will be included in the MapML. The Sharding Config options are intended to allow for parallel access to tiles via different server names.
  
Shard List
  If Enable Sharding is checked, the Shard List should be populated with a comma-separated list of shard names which will be used to populate the shard data list element.
  
Shard Server Pattern
  The Shard Server Pattern should be a valid DNS name including the special placeholder string {s} which will be replace with the Shard Names from the Shard List in requests to the server. 


MapML Resources
---------------

MapML resources will be published for any published WMS layers. The MapML resources will be available at::

  http://{serverName}/geoserver/mapml/{layerName}/{projectionName}?style={styleName}
  

The ``{layerName}`` is the WMS layer name, and the ``{serverName}`` is the name or IP address of the server on which Geoserver is running. The ``{projectionName}`` must be one of the projections supported by MapML:

- OSMTILE
- CBMTILE
- APSTILE
- WGS84 

The ``style`` parameter is optional, but if provided the ``{styleName}`` must correspond to an available WMS style to use for the layer.


MapML Visualization
-------------------

The only tool which is presently able to display MapML is a Leaflet-based MapML client. This client can be imported into an HTML page with the appropriate ``<map>`` and ``<layer>`` elements to reference the MapML resources defined above. Here is a simple, self-contained example of such an HTML page: 

.. code-block:: html
    <html>
        <head>
            <title>MapML Test Map</title>
            <meta charset="utf-8" />
            <script src="http://geogratis.gc.ca/api/beta/mapml/client/bower_components/webcomponentsjs/webcomponents-lite.min.js"></script>
            <link rel="import" href="http://geogratis.gc.ca/api/beta/mapml/client/bower_components/web-map/web-map.html">
            <style>
                /* make the map fullscreen */
                html, body {
                    height: 100%;
                    width: 100%;
                    overflow: hidden;
                }
                body {
                    padding: 0;
                    margin: 0;
                }
                map {
                    height: 100%;
                    width: 100%;
                }
            </style>
        </head>
        <body>
            <map is="web-map" projection="{projectionName}" zoom="2" lat="61.209125" lon="-90.850837" controls>
                <layer- label="{layerName}" src="http://{serverName}/geoserver/mapml/{layerName}/{projectionName}?style={styleName}" checked hidden></layer->
            </map>
        </body>
    </html>
    
In the above example, the placeholders ``{layerName}``, ``{serverName}``, ``{projectionName}``, and ``{styleName}`` would need to be replaced with the appropriate values, and/or the ``style`` parameter could be removed entirely from the URL if not needed.