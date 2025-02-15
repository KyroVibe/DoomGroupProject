# Maze Group Project
This is a group project for CS464. Team is Hunter Barclay, Josh Martin, Jordan Casper.

## Usage

Following on-screen instructions in the top-left for controls.

### GitHub Pages
The project's `main` branch is hosted with GitHub pages [here](https://hunterbarclay.github.io/MazeGroupProject/).

### Self Hosting
This web app can be used with a simple http server and accessed by most if not all web browsers.

For http servers, theres a number of quick options for testing:
- NPM package `http-server`:
    ```
    $ npm install -g http-server
    $ cd /path/to/repo/
    $ http-server
    ```
- Golang package `claat`:
    ```
    $ go install github.com/googlecodelabs/tools/claat@latest
    $ cd /path/to/repo/
    $ claat serve
    ```
- Python `http.server`:
    ```
    $ cd /path/to/repo/
    $ python3 -m http.server
    ```

## Planning
[Trello Board](https://trello.com/b/FvLTHeI2/maze).

## Technologies Used
This project will use JavaScript within an html page. We will make use of WebGL for 3d graphics.

## The Code
### Core Components
#### Renderer
The renderer is mostly modeled after THREE.JS. We have 4 main components to the renderer:
- Shader Programs -> These handle compiled shaders as well as loading and binding uniforms and vertex attributes.
- Materials -> These interface with shader programs to specify values for said uniforms. The idea is we'll have one textured shader program, but two materials: one for the walls, and one for the ground.
- Geometry -> These manage the buffer objects and original mesh data
- Meshes -> These glue all the aforementioned objects together to make draw calls simpler.

We have two main types of Geometry: Basic Geometry which contains simple, single-instanced geometry, and BatchedGeometry, which allows us to more efficiently draw multiple of the same mesh data at different positions.

#### Maze Generation
##### Example
![maze example](/docs/maze-sample.png)

This is an example of a generate maze. The start is marked with an `S` and the end is marked with an `E`.

##### Algorithm
The maze generation starts with a grid graph, with each node connected to its vertical and horizontal neighbors. We then construct a second graph that starts with some random node in the former. Then if it has a neighbor not in the new graph, we randomly select one to add to the new graph and create an edge between the start node and the new node. We then recursively do this for the new node, then reevaluate and do this until all nodes in the original graph are in the new graph.

We store all leaf nodes of the new graph (except for the start node if it is a leaf node) and sort them in ascending order by distance from the starting node. We then use a difficulty factor to determine how close to the front or back of this list we pick the end point.

#### Main Game Loop
### Techniques of Note
#### Batch Rendering
For rendering the walls of the maze, we will be utilizing batch rendering to try and make rendering multiple of the same mesh faster.
Batch rendering is a technique used to efficiently render multiple instances of geometry in a single draw call.
The *loadBatchInstances* method handles the loading of batch instances into buffers meant for rendering.
It iterates through a subset of *batchInstances* based on the provided start index and the batch size.
For each batch instance, it calls *writeInstanceToBuffer* on the batch instance. This method writes the instance's vertex, normal, texture coordinate, and index data into the respective buffers. Incrementing offsets ensures that each instance's data is placed correctly in the allocated buffers without overwriting previously loaded data. The method returns the number of batch instances loaded during this iteration.
The *draw* method utilizes the loaded instances to perform draw calls efficiently. It renders batches in iterations, minimizing redundant calls and ensuring the rendering of all loaded instances.

#### Frustrum Culling
With making use of batch rendering, we'll be using frustrum culling to determine which parts of the maze can actually be seen by the user and only render those. This will cut down on the number of objects we need to render considerably.

## Contributing
### Non-JS Files
- [`index.html`](/index.html) -> Main HTML file that has all basic DOM elements.
- [`index.css`](/style/index.css) -> Main styling for the DOM elements.
- [Shaders](/shaders/) -> This folder contains all our GLSL shader files.
### Core Scripts
- [`app.mjs`](/js/app.mjs) is one of the only scripts that is added to the `index.html` file.
- [`game.mjs`](/js/game.mjs) contains all the main game glue and logic.
### Components
These scripts have classes that are multipurpose.
### Physics
These scripts have logic and classes ment for calculating and handling the limited about of physics.
### Renderer
These scripts are all used to interact with WebGL and draw to the canvas DOM element, as well as manage any render specific data.
### Util
These scripts serve as stateless utility classes used for calculation.
### Vendor
This folder contains any third party libraries we may use. Currently only contains NoiseJS *See Below*.

## Assets
### Meshes
The [Sphere Mesh](/assets/meshes/sphere.obj) was created in Blender, used for testing materials.
The [End Thing](/assets/meshes/endThing.obj) was created in Blender, used as the end zone marker.

### Textures
The following textures were all distributed under a [Creative Commons License](/assets/textures/CC_LICENSE.md) and were sourced from [3D Textures](https://3dtextures.me/):
- [Terracotta Tiles Textures](/assets/textures/style-brick2/)
- [Stylized Bricks Textures](/assets/textures/style-brick/)
- [Stylized Grass Textures](/assets/textures/style-grass/)
- [Stylized Dry Mud Textures](/assets/textures/style-dry-mud/)
- [Ground Dirt Textures](/assets/textures/ground-dirt/)

## Third Party JS and APIs
### [NoiseJS](https://github.com/josephg/noisejs)
NoiseJS is a simple perlin noise library. The minified version of their source can be found [here](/js/vendor/noisejs/), along with their [ISC License](js/vendor/noisejs/ISC_LICENSE.md).

## License
This repository is provided under the [MIT License](/LICENSE.md).

Read above to see which assets/files were sourced under different licenses.
