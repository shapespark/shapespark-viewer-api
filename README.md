# JavaScript API for interacting with the Shapespark 3D scene.

## Introduction

Shapespark 3D scene viewer exposes a JavaScript API that can be used
to built custom functionality on top of the viewer.

The API can be called either by extending the scene `index.html` file,
or from a web site that embeds the viewer in an iframe.

## Extending the scene `index.html` file to call the API.

Shapespark allows to inject a user provided HTML code into the scene
`index.html` file. To do it, place a file `body-end.html` in your
local directory with the Shapespark scene:
`Documents\Shapespark\YOUR_SCENE_NAME\`. The content of this file will
be automatically injected just before the closing `</body>` tag of the
scene `index.html`.

You can also use `head-end.html` file to put custom code just before
the `</head>` tag in the `index.html`.


Shapespark uses global namespace `WEBWALK` to expose the API functions.


