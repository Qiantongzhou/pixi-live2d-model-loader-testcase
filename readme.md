This repository demonstrates a workaround for a common issue when integrating multiple Live2D models exported with different versions of the Cubism Core SDK. When placed on the same PIXI.js app.stage, one model will not animate properly. The solution is to isolate each model in its own iframe.


Problem Description
Live2D models exported from different versions of the Cubism Core SDK conflict when loaded into a single PIXI.js stage. This conflict typically results in one of the models failing to animate (i.e., it remains static). The underlying cause is that each Cubism Core version attaches itself to the global namespace, so when you try to load two different versions on the same stage, one overwrites the other’s functionality.


Reproduction
To reproduce the issue:

Export two Live2D models using different Cubism Core versions.
Load both models on the same PIXI.js stage:
js
Copy
const app = new PIXI.Application({ view: canvasElement, autoStart: true, resizeTo: window });
const modelA = await PIXI.live2d.Live2DModel.from("modelA.model3.json");
const modelB = await PIXI.live2d.Live2DModel.from("modelB.model3.json");
app.stage.addChild(modelA);
app.stage.addChild(modelB);
Notice that one model does not animate.

Solution: Using Iframes
The solution is to load each Live2D model in its own isolated iframe. This way, each model’s environment (including its specific Cubism Core version) is kept separate, ensuring both models animate correctly.

How It Works
Isolation: Each iframe creates its own global scope, so the Cubism Core libraries won’t conflict.
Integration: The iframes can be positioned to overlap each other, making it appear as though both models are part of one unified scene.
Example Implementation
Below is a sample HTML file (e.g., index.html) that loads two iframes on top of each other with transparent backgrounds:

html
Copy
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Overlapping Live2D Iframes</title>
  <style>
    html, body {
      margin: 0;
      padding: 0;
      height: 100%;
      overflow: hidden;
      background: transparent;
    }
    iframe {
      position: absolute;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      border: none;
      background: transparent;
    }
    /* Set stacking order: iframeB overlays iframeA */
    #iframeA {
      z-index: 1;
    }
    #iframeB {
      z-index: 2;
    }
  </style>
</head>
<body>
  <iframe id="iframeA" src="modelA.html" allowtransparency="true"></iframe>
  <iframe id="iframeB" src="modelB.html" allowtransparency="true"></iframe>
</body>
</html>
Each model page (e.g., modelA.html and modelB.html) should load its respective Cubism Core version and Live2D model with transparent backgrounds. For example, modelA.html might look like:

html
Copy
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Model A</title>
  <style>
    html, body {
      margin: 0;
      padding: 0;
      background: transparent;
      overflow: hidden;
    }
  </style>
  <!-- Load Cubism Core version specific to Model A -->
  <script src="https://example.com/cubismcore/v1/live2dcubismcore.min.js"></script>
  <script src="https://cdn.jsdelivr.net/npm/pixi.js@6.5.2/dist/browser/pixi.min.js"></script>
  <script src="https://cdn.jsdelivr.net/npm/pixi-live2d-display/dist/index.min.js"></script>
</head>
<body>
  <canvas id="canvasA"></canvas>
  <script>
    const modelAUrl = "https://example.com/models/modelA.model3.json";
    (async function() {
      const app = new PIXI.Application({
        view: document.getElementById("canvasA"),
        autoStart: true,
        resizeTo: window,
        backgroundAlpha: 0
      });
      const modelA = await PIXI.live2d.Live2DModel.from(modelAUrl);
      app.stage.addChild(modelA);
    })();
  </script>
</body>
</html>
Repeat a similar setup for modelB.html using its respective Cubism Core version.


Conclusion
By isolating each model in an iframe, you prevent conflicts between different versions of the Cubism Core SDK. This solution ensures that all models animate correctly, even when they originate from different SDK versions.

License
This project is licensed under the MIT License. See the LICENSE file for details.