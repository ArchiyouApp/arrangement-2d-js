# arrangement-2d-js

[![build](https://github.com/LokiResearch/arrangement-2d-js/workflows/build/badge.svg)](https://github.com/LokiResearch/arrangement-2d-js/actions?query=workflow:"build")
[![GitHub release](https://img.shields.io/github/release/LokiResearch/three-mesh-halfedge?include_prereleases=&sort=semver&color=blue)](https://github.com/LokiResearch/three-mesh-halfedge/releases/)
[![Language grade: JavaScript](https://img.shields.io/lgtm/grade/javascript/g/LokiResearch/arrangement-2d-js.svg?logo=lgtm&logoWidth=18)](https://lgtm.com/projects/g/minitoine/arrangement-2d-js/context:javascript)
[![License](https://img.shields.io/badge/License-MIT-green)](#license)

## Fork notes

Quick fork to use with Webpack and a simple .wasm loader.

- Updated Boost library to 1.81 to avoid unary function reference error. Changed in CMakeLists and install_libraries.sh
- Emscripten build that includes .wasm (not as inline bytecode in .js)
- If some TS/webidl2ts error. Run npm install in directory to install local typescript
- Added .wasm modules in build


## Examples

- Arrangement demo [Link](https://LokiResearch.github.io/arrangement-2d-js/build-examples/ArrangementExample)

## Install

### From GitHub

```bash
npm i git+https://github.com/LokiResearch/arrangement-2d-js.git
```

## Build

##### Install and configure Emscripten
```bash
# Get the emscripten repo 
git clone git@github.com:emscripten-core/emsdk.git

# Enter folder
cd emsdk

# Download the SDK tool (latest or another version)
./emsdk install latest

# Activate the downloaded SDK
./emsdk activate latest

# Add Emscripten env to PATH
source ./emsdk_env.sh
```

##### Install and build arrangement-2d-js
```bash
# Get this repo
git clone git@github.com:LokiResearch/arrangement-2d-js.git

# Install CGAL and boost libraries
./install_libraries.sh

# Build
npm run build
```


## Use

**Note: This library is a WebAssembly module**

```js
import Arrangement2D from 'arrangement-2d-js';

// Init module, wait for the promise with the loaded module
Arrangement2D().then(Arr2D => {

    // Create a list of points
    const points = new Arr2D.PointList();

    // Add six points forming 3 strokes forming a triangle
    points.push_back(new Arr2D.Point(1, 0));
    points.push_back(new Arr2D.Point(1, 4));
    points.push_back(new Arr2D.Point(0, 1));
    points.push_back(new Arr2D.Point(4, 1));
    points.push_back(new Arr2D.Point(4, 0));
    points.push_back(new Arr2D.Point(0, 4));

    // Get the polygons in the arrangement
    const builder = new Arr2D.ArrangementBuilder();
    const polygonList = builder.getPolygons(points);

    // Get the contour points of the polygon
    const poly = polygonList.at(0);
    for (let i=0; i<poly.contour.size(); i++) {
        const point = poly.contour.at(i);
        console.log(point.x, point.y);
    }

    // Clean the initial points
    Arr2D.destroy(points);

    // Clean the polygons : i.e. call C++ Polygon class destructor,
    // and avoid memory leaks
    for (let i=0; i<polygonList.size(); i++) {
        Arr2D.destroy(polygonList.at(i));
    }
    Arr2D.destroy(polygonList);
    Arr2D.destroy(builder);
    
});

```

See examples code for a more complete use of the library.


## Documentation

```js
class ArrangementBuilder {
    constructor();
    getPolygons(points: PointList): PolygonList;
}

class Point {
    constructor();
    constructor(x: number, y: number);
    x: number;
    y: number;
}

class Polygon {
    constructor(contour: Contour, holes: ContourList);
    getInsidePoint(point: Point): boolean;
    contour: Contour;
    holes: ContourList;
}

class PointList {
    constructor();
    push_back(s: Point): void;
    size(): number;
    at(i: number): Point;
    clear(): void;
}

class PolygonList {
    constructor();
    push_back(s: Polygon): void;
    size(): number;
    at(i: number): Polygon;
    clear(): void;
}

class Contour {
    constructor();
    push_back(s: Point): void;
    size(): number;
    at(i: number): Point;
    clear(): void;
}

class ContourList {
    constructor();
    push_back(s: Contour): void;
    size(): number;
    at(i: number): Contour;
    clear(): void;
}
```
