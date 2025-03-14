# Handling Live2D Models with Different Cubism Core Versions

When integrating multiple Live2D models into a single application, you may encounter issues if the models are exported using different versions of the Cubism Core. Specifically, adding such models to the same `app.stage` can result in one or more models not animating correctly. This is due to conflicts arising from the differing runtime environments required by each model version.

## Problem Illustration

Consider an application where two models, **Model A** and **Model B**, are exported using different versions of the Cubism Core. Attempting to load both models into the same `app.stage` may lead to the following issue:

![Model Conflict Illustration](images/model_conflict.png)

*In the image above, Model B fails to animate due to version conflicts.*

## Solution: Isolating Models Using Iframes

To resolve this issue, you can isolate each model within its own iframe. This approach ensures that each model operates within a separate global namespace, preventing conflicts between different Cubism Core versions.

### Implementation Steps

1. **Create Separate HTML Files for Each Model**

   - **modelA.html**: Contains the setup for Model A, including the specific Cubism Core version it requires.
   - **modelB.html**: Contains the setup for Model B with its respective Cubism Core version.

2. **Embed Each Model in an Iframe**

   In your main HTML file, embed each model's HTML file within an iframe:

   ```html
   <!DOCTYPE html>
   <html lang="en">
   <head>
     <meta charset="UTF-8">
     <title>Live2D Models in Iframes</title>
     <style>
       /* Ensure iframes overlap and have no borders */
       iframe {
         position: absolute;
         top: 0;
         left: 0;
         width: 100%;
         height: 100%;
         border: none;
         background: transparent;
       }
       /* Control stacking order */
       #iframeA {
         z-index: 1;
       }
       #iframeB {
         z-index: 2;
       }
     </style>
   </head>
   <body>
     <!-- Embed Model A -->
     <iframe id="iframeA" src="modelA.html" allowtransparency="true"></iframe>
     <!-- Embed Model B -->
     <iframe id="iframeB" src="modelB.html" allowtransparency="true"></iframe>
   </body>
   </html>
