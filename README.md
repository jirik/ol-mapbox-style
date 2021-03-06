# ol-mapbox-style

Converts Mapbox Style objects for vector tile layers into OpenLayers style functions.

## Getting started

To use a standalone build of ol-mapbox-style, just include 'dist/olms.js' on your HTML page. Otherwise just require the ol-mapbox-style module, like in the snippet below.

The code below creates a Mapbox Streets v7 layer with the bright v9 style:

```js
var ol = require('openlayers');
var olms = require('ol-mapbox-style');

var key = 'Your Mapbox Access Token here';

var tilegrid = ol.tilegrid.createXYZ({tileSize: 512, maxZoom: 22});
var layer = new ol.layer.VectorTile({
  source: new ol.source.VectorTile({
    attributions: '© <a href="https://www.mapbox.com/map-feedback/">Mapbox</a> ' +
      '© <a href="http://www.openstreetmap.org/copyright">' +
      'OpenStreetMap contributors</a>',
    format: new ol.format.MVT(),
    tileGrid: tilegrid,
    tilePixelRatio: 8,
    url: 'http://{a-d}.tiles.mapbox.com/v4/mapbox.mapbox-streets-v7/' +
        '{z}/{x}/{y}.vector.pbf?access_token=' + key
  })
});

fetch('https://api.mapbox.com/styles/v1/mapbox/bright-v9?access_token=' + key).then(function(response) {
  response.json().then(function(glStyle) {
    glStyle.sprite = 'https://api.mapbox.com/styles/v1/mapbox/bright-v9/sprite?access_token=' + key;
    olms.applyStyle(layer, glStyle, 'mapbox').then(function() {
      map.addLayer(layer);
    });
  });
});
```

Note that it is the responsibility of the application to load web fonts used by the GL Style.

## API

### getStyleFunction

Creates a style function from the `glStyle` object for all layers that use
the specified `source`, which needs to be a `"type": "vector"`
source.

**Parameters**

-   `glStyle` **([string](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String) \| [Object](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object))** Mapbox GL style object.
-   `source` **[string](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String)** `source` key from the Mapbox GL style object.
-   `resolutions` **\[([Array](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array)&lt;[number](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Number)> | [undefined](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/undefined))]** Resolutions for mapping resolution to zoom level. For tile layers, this can
    be `layer.getSource().getTileGrid().getResolutions()`. (optional, default `[156543.03392804097,
    78271.51696402048,39135.75848201024,19567.87924100512,9783.93962050256,
    4891.96981025128,2445.98490512564,1222.99245256282,611.49622628141,
    305.748113140705,152.8740565703525,76.43702828517625,38.21851414258813,
    19.109257071294063,9.554628535647032,4.777314267823516,2.388657133911758,
    1.194328566955879,0.5971642834779395,0.29858214173896974,
    0.14929107086948487]`)
-   `onChange` **\[[Function](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/function)]** Callback which will be called when
    the style is ready to use for rendering, and every time a new resource (e.g.
    icon sprite or font) is ready to be applied. When the `glStyle` has no
    `sprite` and only standard fonts, the style will be ready to use
    immediately, and the callback can be omitted. (optional, default
    `function(){}`)

Returns **ol.style.StyleFunction** Style function for use in
`ol.layer.Vector` or `ol.layer.VectorTile`.

### applyStyle

Applies a style function to an `ol.layer.VectorTile` with an
`ol.source.VectorTile`. The style function will render all layers from the
`glStyle` object that use the specified `source`, which needs to be a
`"type": "vector"` source.

**Parameters**

-   `layer` **ol.layer.VectorTile** OpenLayers layer.
-   `glStyle` **([string](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String) \| [Object](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object))** Mapbox GL style object.
-   `source` **[string](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String)** `source` key from the Mapbox GL style object.

Returns **[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)** Promise which will be resolved when the style can be used
for rendering.

## Building the library

    npm install
    npm run dist

The resulting binary (`olms.js`) will be in the `dist/` folder. To see the library in action, navigate to `example/index.html`.
