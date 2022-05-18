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

Shapespark exposes a global function `WALK.getViewer()` that
returns the `Viewer` object.  Call this function from a
`<script></script>` code block in the `body-end.html` and use the
returned object to interact with the viewer.

## Core 3D data types

Shapespark uses core 3D data types from [the Three.js
library](https://threejs.org/). Position is represented with
[`THREE.Vector3`](https://threejs.org/docs/#api/en/math/Vector3),
rotation with
[`THREE.Euler`](https://threejs.org/docs/#api/en/math/Euler), bounding
boxes with [`THREE.Box3`](https://threejs.org/docs/#api/en/math/Box3).

## Node hierarchy

Nodes of the scene, corresponding to *objects* in the user interface, are
represented with `Node` objects. Each node may have at most one mesh
attached to it - accessed with `.mesh` property, and any number of
children nodes - accessed with `.children` array property. A parent of the
node is accessed with `.parent` property.

Each node has a defined string type. Using the same type for multiple
nodes allows to perform API actions on all the nodes of the same type at
once. Node type is accessed with `.type` property.

## Scene autoplay, menu and help visibility

* `Viewer.play()` - starts to load the scene (corresponds to clicking the
  *Play* button)
* `Viewer.menuVisible` - a boolean property that controls the visibility
  of the viewer menu.
* `Viewer.helpVisible` - a boolean property that controls the visibility
  of the viewer help.

### Example
Plays the scene and opens the viewer help automatically without waiting
for the user to click the *Play* or *Help* buttons. The `#autoplay` and `#help`
hashes don't have to be included in the scene URL:

[`body-end.html`](examples/autoplay/body-end.html) and [live
scene](https://demo.shapespark.com/api-examples-autoplay/)

## Scene ready to display and scene load complete notifications

Many API operations can be performed only after the key scene assets
finish loading. The `Viewer` object exposes two functions to listen
for load events:


* `Viewer.onSceneReadyToDisplay(callback)` - the `callback` is called when
   key assets (geometry and materials) are loaded, the scene can be
   rendered and the user can start interacting with it. If the scene
   has progressive loader option enabled in the Shapespark editor, the
   `callback` passed to this function can be called before all the
   assets are downloaded (for example lightmaps and textures can still
   be loading).

* `Viewer.onSceneLoadComplete(callback)` - the `callback` is called when
   all the scene assets are loaded and the scene can be rendered in
   final quality.


### Example
Show a custom text banner that fades out after the scene is
ready to display:

[`body-end.html`](examples/loading-banner/body-end.html) and [live
scene](https://demo.shapespark.com/api-examples-banner/#autoplay)

## Using your own static asset files.

When extending the viewer you can use your own assets such as images,
JSONs, videos or audio files. Place such files in an
`Documents\Shapespark\YOUR_SCENE_NAME\extra-assets` folder. This
folder is uploaded together with a scene. To get a path to files in
this folder from your JavaScript code use a function:

* `WALK.getExtraAssetUrl(fileName)` - returns a path to a file stored
  in the scene `extra-assets` folder. Can be called only after page assets
  finish loading (so for example from the `window.addEventListener('load',
  handler);` handler.

### Example
Show a custom image banner that fades out after the scene is
ready to display:

[`body-end.html`](examples/loading-image-banner/body-end.html) and [live
scene](https://demo.shapespark.com/api-examples-image-banner/#autoplay)

## Displaying HTML pop-ups

To display an HTML pop-up use the following function:

* `Viewer.openPopup(content, options, onClose)` - opens a pop-up with
  content defined by an HTML markup string in the `content` argument.
  `options` is an optional object definining how the pop-up is laid out
  and `onClose` is an optional callback called when the pop-up is closed.

Options object has the following properties:

* `centerHorizontally` - if true, the pop-up is centered horizontally
  on the screen; defaults to false.
* `centerVertically` - if true, the pop-up is centered vertically
  on the screen; defaults to false.
* `padding` - if true, the pop-up has padding; defaults to false.

### Example
Show a custom welcome pop-up centered on a screen when the scene is ready
to display.

[`body-end.html`](examples/popup/body-end.html) and [live
scene](https://demo.shapespark.com/api-examples-popup/#autoplay)

## Node click notifications

To get a notification when the user clicks a node the following API
function can be used:

* `Viewer.onNodeTypeClicked([nodeTypeName], callback)` - the `callback` is
  called with three arguments:
  * the `Node` object that was clicked. In node hierarchy a click can be
    attributed to a child (eg. cushion) as well as to any of its ancestors
    (eg. sofa containing the cushion). The callback is given the
    lowest-level node (cushion in the above example).
  * the point of click in 3D world coordinates as a `THREE.Vector3` object,
  * the distance from the camera to the point of click in world units.
  If the optional `nodeTypeName` argument is given, the
  `callback` is called only for clicks in nodes of this type. Otherwise,
  the `callback` is called for clicks in all nodes.

### Example
Show:

* the types of the clicked node and all its ancestors in the node hierarchy,
* the distance from the camera to the clicked point.

[`body-end.html`](examples/node-click/body-end.html) and [live
scene](https://demo.shapespark.com/api-examples-node-click/#autoplay)

## View switch

`Viewer` exposes two functions that allow to get notification when the
user teleports to views.

* `Viewer.onViewSwitchStarted(callback)` - the `callback` is called with
  a string view name as an argument when the camera starts to teleport
  to a view.

* `Viewer.onViewSwitchDone(callback)` - the `callback` is called with
  a string view name as an argument when the camera finishes teleport
  to a view.

To switch to a view programmatically use:

* `Viewer.switchToView(viewName or viewObject, maxTime)` - `viewName`
  is a string that is visible in the Shapespark view menu and can be
  used to switch to a view that is already configured in the
  scene. `maxTime` is an optional integer that allows to override the
  default maximum time that it takes the camera to switch to a
  view, it can be set to 0 to switch instantaneously. `viewObject` is
  an instance of `WALK.View` and can be passed to switch to a view
  that is not defined in the scene. It has the following properties:

* `WALK.View.position`: [`THREE.Vector3`](https://threejs.org/docs/#api/en/math/Vector3)
that configures the camera destination position.
* `WALK.View.rotation`: [`THREE.Euler`](https://threejs.org/docs/#api/en/math/Euler)
that configures the camera destination rotation.


### Example

Show a text banner with a view name when the camera starts to telport
to a view, fade-out the banner after the telport finishes. Add a
custom view switch menu in the left corner:


[`body-end.html`](examples/view-switch/body-end.html) and [live
scene](https://demo.shapespark.com/api-examples-view-switch/#autoplay)

## Get the scene bounding box.

* `Viewer.getSceneBoundingBox()` - returns a
  [`THREE.Box3`](https://threejs.org/docs/#api/en/math/Box3) that
  encloses all the nodes in the scene.

## Get the camera position and rotation.

* `Viewer.getCameraPosition()` - returns a
  [`THREE.Vector3`](https://threejs.org/docs/#api/en/math/Vector3)
  with world coordinates of the camera.

* `Viewer.getCameraRotation()` - returns a
  [`THREE.Euler`](https://threejs.org/docs/#api/en/math/Euler) that
  represents rotation of the camera. You can use `.yaw` property to
  get the left and right camera rotation, `.pitch` - up and down
  rotation, `.roll` - tilt of the camera. Outside of the VR mode
  `.roll` is always 0.

### Example

Draw the camera path and the current camera rotation in a corner of
the viewer. The example uses `Viewer.getSceneBoundingBox()` to
determine the range in which the `x` and `y` coordinates of the camera
can change:

[`body-end.html`](examples/camera-tracking/body-end.html) and [live
scene](https://demo.shapespark.com/api-examples-camera-tracking/#autoplay)


## Capture screenshot.

* `Viewer.captureImage(options)` - captures a JPEG encoded screenshot
  at the current camera position. Depending on the `options`, the
  screenshot is either saved to the user disk or returned to the
  caller as a base64 encoded string.

Options object has the following properties:

* `isPanorama` - if true, 360 equirectangular panorama is captured.
* `width`, `height` - dimensions of the captured image. If missing the
  dimensions are set to the current window width and height, so the
  captured image matches exactly what the user sees on the screen.
* `toDataUrl` - if true, base64 encode image data is returned as
  string, otherwise the image is saved to the user disk (depending on
  the user browser settings, the browser will either open a save file
  dialog, or download the image automatically to the `Downloads`
  folder).

### Example

Show two buttons that save the image with the current camera view and
360 panorama to disk.

[`body-end.html`](examples/screenshot/body-end.html) and [live
scene](https://demo.shapespark.com/api-examples-screenshot/#autoplay)

## Replace textures using images or videos from external source.

Some applications, such as material configurators, need a custom UI
for switching textures that are used in the scene. In addition, if
the number of textures to choose from is large, it is convenient to
use external textures that are not part of the 3D model.

The following API functions create textures from images or videos that are
not embedded in the model:

* `Viewer.createTextureFromHTMLImage(image, hasAlpha=false)` - returns
a texture object created from the given <a
href="https://developer.mozilla.org/en-US/docs/Web/API/HTMLImageElement">HTMLImageElement</a>.
The image element can be defined in HTML using `<img>` tag or created in
JavaScript using the built-in <a
href="https://developer.mozilla.org/en-US/docs/Web/API/HTMLImageElement/Image">Image</a>
constructor. If the `hasAlpha` argument is `true`, the alpha channel of the
image is available in the texture.

* `Viewer.createTextureFromHTMLVideo(video)` - returns a texture
object created from the given <a
href="https://developer.mozilla.org/en-US/docs/Web/API/HTMLVideoElement">HTMLVideoElement</a>.
The video element can be defined in HTML using `<video>` tag or created in
JavaScript
with `document.createElement('video')` DOM API call. For the video to start
automatically set the `autoplay` attribute of the element, and if the video
has an audio track also set the `muted` attribute (videos with unmuted audio
cannot be started without a user interaction); for the video to loop set the
`loop` attribute.


To set the returned texture in one of the materials in the scene, the
material needs to be marked as editable. This needs to be done before
the scene is loaded. To improve performance, Shapespark scene loader
merges materials with identical properties, after such merging the
textures and other material properties can not be changed:

* `Viewer.setMaterialEditable(materialName)` - if `materialName` is a string
  it marks a single material with the given name as editable; if `materialName`
  is a regular expression it marks all the materials with name matching
  the regular expression as editable.
* `Viewer.setAllMaterialsEditable()` - marks all materials in the scene
  as editable.

Materials can be obtained in two ways:

* `Viewer.getEditableMaterials()` - returns an array of `Material` objects
  containing all the editable materials in the scene.
* `Viewer.findMaterial(materialName)` - returns a `Material` object
  for the editable material with the given name.

Returned `Material` objects have the following texture properties
that can be changed:

* `Material.baseColorTexture`
* `Material.roughnessTexture`
* `Material.metallicTexture`
* `Material.bumpTexture`

To save GPU and CPU resources Shapespark doesn't render a scene when
the camera is not moving. Because of this, after a texture is changed,
the following call is needed to force the scene re-render:

* `Viewer.requestFrame()` - forces the viewer to render a frame.


### Example

Show a grid of images and videos in a corner of the viewer, change a rug
texture when an image or video is clicked.

[`body-end.html`](examples/texture-picker/body-end.html) and [live
scene](https://demo.shapespark.com/api-examples-texture-picker/#autoplay)


## Minimap

Set of utilities for handling minimap state. Need to be called after the scene has loaded.

* `Viewer.isMinimapEnabled()` - returns `true` if minimap is enabled
* `Viewer.enableMinimap()` - enable minimap, attach to viewer
* `Viewer.disableMinimap()` - disable minimap, remove from viewer 
