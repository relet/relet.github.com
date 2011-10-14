# configuring kivyMaps

The example application of kivyMaps displays some rather vanilla maps like the OpenStreetMap and Microsoft Bing tiles.

To display other maps and WMS services in your application, you will have to work with the python files that define the application. Background maps are defined as instances of the `TileServer` class, overlays are instances of `WMSOverlayServer`
In the following are some code examples in lieu of a proper documentation:

## The minimal configuration

The following is a simple app that shows only the kivyMaps widget, and OpenStreetMap as mapping provider:

    import kivy
    kivy.require('1.0.7')

    from kivy.app import App
    from kivy.uix.floatlayout import FloatLayout
    from MapViewer import MapViewer

    class KVMaps(App):
      def build(self):
        layout = FloatLayout()
        self.mv = MapViewer(maptype="Roadmap", provider="openstreetmap")
        layout.add_widget(self.mv)
    return layout
    
    if __name__ in ('__android__','__main__'):
      KVMaps().run()


## Background tile servers

Initialize the MapViewer widget with the provider and map type of choice. Implementations are provided for some common tile servers and WMS-T:

    MapViewer(maptype="Roadmap", provider="openstreetmap")

Custom WMS-T services can be defined. The following example defines a WMS-T with a custom bounding box and projection which defines the tiling scheme:  

    class OpenCacheTileServer(WMSTileServer):
        provider_name = 'opencache'
        provider_host = 'opencache.statkart.no'
        available_maptype = dict(topo2='topo2', 
                                 topo2graatone='topo2graatone', 
                                 toporaster2='toporaster2', 
                                 sjo_hovedkart2 = 'sjo_hovedkart2', 
                                 europa='europa')

        def __init__(self, **kwargs):
          WMSTileServer.__init__(self)
          self.initFromGetCapabilities('http://opencache.statkart.no', 
                                       '/gatekeeper/gk/gk.open', 
                                       srs='EPSG:32633', 
                                       layer = 'topo2')
          self.customBounds = True
          self.bounds       = (-2500000, 3500000, 3045984, 9045984)

## Overlay WMS services are defined using similar parameters:

    class NorgeIBilder(WMSOverlayServer):
        provider_name = 'geonorge'
        provider_host = 'wms.geonorge.no'

        def __init__(self, **kwargs):
          super(WMSOverlayServer, self).__init__()
          self.initFromGetCapabilities('http://wms.geonorge.no', 
                                       '/skwms1/wms.norgeibilder', 
                                       layer="SatelliteImage,OrtofotoAlle", 
                                       srs="EPSG:32633")
          self.customBounds = True
          self.bounds       = (-2500000, 3500000, 3045984, 9045984)
          self.max_alpha = 1    # range [0.0-1.0]
                                # defines the opacity of the overlay after fade-in
  
To enable or disable an overlay, append or remove the class to/from the list of overlays defined in `MapViewer.map.overlays`.

# parameters

## MapViewer

* `provider` - the identifier by which the tileserver was registered (using `Tileserver.register`)
* `maptype` - every tileserver can provide a dictionary of map types to display

* `close_on_idle` - Boolean - if enabled, the application will close after 300 seconds of inactivity.
* `status_cb` - Allows to provide a callback method to read currently displayed tile count and number of tiles in the loading queue.

## WMS*Server 

*  `provider_name` - identifier of the map provider, currently not in use
*  `provider_host` - the request URL will be assembled using this hostname

*  `initFromGetCapabilities` - this method allows to send a GetCapabilities request to the server. If only the URL parameters are given, 
    the capabilities will be logged to stdout and the first one chosen by default. If the parameters `layer` and `srs` are given, the 
    selected layer and SRS will be requested. A parameter `index` may be used if the name of the layer is unknown, but note that it is 
    not a reliable identifier.

*  `customBounds` / `bounds` - If enabled, the coordinate system will be mapped to these bounds. 
*  `max_alpha` - Overlays will be displayed with the given opacity
