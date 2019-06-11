# JavaScript API for interacting with Shapespark 3D scenes.

Shapespark 3D scene viewer exposes a JavaScript API that you can use
to build custom functionality on top of the viewer.

## Extending the scene `index.html` file to call the API

You can inject your own HTML code into the scene `index.html` file. To
do it, place a file `body-end.html` in a local Windows directory with
your Shapespark scene: `Documents\Shapespark\YOUR_SCENE_NAME\`. The
content of this file is automatically injected just before the closing
`</body>` tag of the scene `index.html`.

You can also use `head-end.html` file to put custom code just before
the `</head>` tag in the `index.html`.

Shapespark exposes a global function `WEBWALK.getViewer()` that
returns the `Viewer` object.  Call this function from a
`<script></script>` code block in the `body-end.html` and use the
returned object to interact with the viewer.

## Scene ready to display and scene load complete notifications

Most API operations can be performed only after the key scene assets
finish loading. The `Viewer` object exposes two functions to listen
for load events:


* `Viewer.onSceneReadyToDisplay(callback)` - the callback is called when
   key assets (geometry and materials) are loaded, the scene can be
   rendered and the user can start interacting with it. If the scene
   has progressive loader option enabled in the Shapespark editor, the
   callback passed to this function can be called before all the
   assets are downloaded (for example lightmaps and textures can still
   be loading).

* `Viewer.onSceneLoadComplete(callback)` - the callback is called when
   all the scene assets are loaded and the scene can be rendered in
   final quality.


[See the `body-end.html` file](examples/loading-banner/body-end.html)
used by [this example
scene](https://demo.shapespark.com/api-examples-banner/) to show a
custom text banner that fades away after the scene is ready to display.