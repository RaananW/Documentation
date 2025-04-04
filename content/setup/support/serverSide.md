---
title: Running Babylon.js On A Server
image:
description: Learn how to run Babylon.js on a server.
keywords: babylon.js, advanced, server
further-reading:
video-overview:
video-content:
---

Starting with Babylon.js v3.1, we introduced the NullEngine which is a version of the main Babylon.js engine but with no need for a WebGL capable device.

The NullEngine will not produce any rendering and thus can be used in a Node.js / server side environment.

It can be used to:

- Run tests
- Run a server side version of your application / game
- Use specific Babylon.js services (like glTF loaders for instance)

## General

To use a headless version of Babylon.js, just run:

```javascript
var engine = new BABYLON.NullEngine();
var scene = new BABYLON.Scene(engine);
```

If you want to use it in a Node.js environment, you may need to add some additional resources (like support for xhr):

First install babylonjs and babylonjs-loaders: `npm install babylonjs babylonjs-loaders`

Then use this code:

```javascript
var BABYLON = require("babylonjs");
var LOADERS = require("babylonjs-loaders");
global.XMLHttpRequest = require("xhr2").XMLHttpRequest;

var engine = new BABYLON.NullEngine();
var scene = new BABYLON.Scene(engine);

var light = new BABYLON.PointLight("Omni", new BABYLON.Vector3(20, 20, 100), scene);

var camera = new BABYLON.ArcRotateCamera("Camera", 0, 0.8, 100, BABYLON.Vector3.Zero(), scene);

BABYLON.ImportMeshAsync("https://playground.babylonjs.com/scenes/skull.babylon", scene).then(function ({ meshes }) {
  camera.target = meshes[0];

  console.log("Meshes loaded from babylon file: " + meshes.length);
  for (let index = 0; index < meshes.length; index++) {
    console.log(meshes[index].toString());
  }

  BABYLON.ImportMeshAsync("https://www.babylonjs.com/assets/DamagedHelmet/glTF/DamagedHelmet.gltf", scene).then(function ({ meshes }) {
    console.log("Meshes loaded from gltf file: " + meshes.length);
    for (let index = 0; index < meshes.length; index++) {
      console.log(meshes[index].toString());
    }
  });

  console.log("render started");
  engine.runRenderLoop(function () {
    scene.render();
  });
});
```

## Configuration

You can specifiy some options in the `NullEngine` constructor:

```javascript
const engine = new BABYLON.NullEngine({
    renderWidth: 512,
    renderHeight: 256,
    textureSize: 512
});
```

With `renderWidth` and `renderHeight` you can define the size of the virtual canvas used to simulate the render surface.
With `textureSize`, you can define the size of all textures (which will be all black) used by the NullEngine.

## Troubleshooting

While the goal of the NullEngine is to completely replace the Engine class, some features cannot be used server side:

- `camera.attachControl` cannot be used as it requires an HTML element
- `DynamicTexture` cannot be used as it relies on HTML canvas

Furthermore as we already mentioned earlier, you need to provide an implementation of the `XMLHttpRequest` class.
