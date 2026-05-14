# eg2d

> 日本語のREADMEはこちらです: [README.ja.md](README.ja.md)

A lightweight 2D game engine for modern web browsers, built on the powerful [Matter.js](https://brm.io/matter-js/) physics library.

## Features

-   **Full-Screen Canvas:** Automatically creates and manages a canvas that fills the browser window.
-   **Physics Simulation:** Leverages the robust Matter.js engine for realistic physics.
-   **Input Handling:** Built-in support for both touch and mouse interactions.
-   **Dynamic Gravity:** Integrates with device motion sensors to control gravity in real-time.

## Requirements

-   A modern web browser that supports ES Modules.

## Quick Start

1.  Create an `index.html` file and import `eg2d.js` as a module.

    ```html
    <!DOCTYPE html>
    <html>
      <head>
        <title>eg2d Demo</title>
        <style>
          body { margin: 0; overflow: hidden; }
        </style>
      </head>
      <body>
        <script type="module" src="app.js"></script>
      </body>
    </html>
    ```

2.  Create an `app.js` file to set up your world.

    ```javascript
    import { createWorld, Matter } from './eg2d.js';

    // 1. Create the world and define the drawing loop
    const world = createWorld(document.body, (g, engine) => {
      // This function is called on every frame to draw your objects
      g.lineWidth = 2;
      g.strokeStyle = "black";

      const bodies = Matter.Composite.allBodies(engine.world);
      for (const body of bodies) {
        g.beginPath();
        body.vertices.forEach((v, j) => {
          if (j === 0) g.moveTo(v.x, v.y);
          else g.lineTo(v.x, v.y);
        });
        g.closePath();
        g.stroke();
      }
    });

    // 2. Create physics bodies using the exported Matter object
    const ball = Matter.Bodies.circle(500, 100, 40, { restitution: 0.8 });
    const ground = Matter.Bodies.rectangle(500, 950, 800, 100, { isStatic: true });

    // 3. Add the bodies to the world
    world.add(ball);
    world.add(ground);

    // 4. (Optional) Enable UI events to add new balls on click/touch
    world.useUI();
    world.ontouch = (p) => {
      const newBall = Matter.Bodies.circle(p.x, p.y, 20 + Math.random() * 30, {
        restitution: 0.8,
      });
      world.add(newBall);
    };
    ```

## API Reference

### `createWorld(parentElement, renderCallback)`

Initializes the engine and creates a full-screen canvas inside the `parentElement`.

-   `parentElement` (HTMLElement): The DOM element to append the canvas to (e.g., `document.body`).
-   `renderCallback(g, engine)` (Function): A function called on each animation frame.
    -   `g`: A 2D canvas rendering context, extended with helper drawing functions.
    -   `engine`: The underlying Matter.js engine instance.

Returns a `world` object with the following properties and methods:

### World Object

#### Properties

-   `.width` / `.height` (Number): Get or set the logical dimensions of the physics world. Defaults to `1000x1000` and is scaled to fit the screen.
-   `.gravity` (Vector): Access the world's gravity vector (e.g., `world.gravity.y = 1;`).
-   `.engine` (Engine): The underlying Matter.js `Engine` instance for advanced control.
-   `.render` (Object): The internal rendering object, containing canvas, offset, and scale information.

#### Methods

-   `.add(body)`: Adds a Matter.js body or composite to the world.
-   `.remove(body)`: Removes a Matter.js body or composite from the world.
-   `.useRealGravity()`: Enables gravity based on the device's motion sensors. This may require user permission on some platforms (e.g., iOS).
-   `.useUI()`: Enables mouse and touch input. After calling, define an `ontouch` callback on the world object to handle events.
    -   `world.ontouch = (point) => { ... }`: A callback function that receives a `point` object (`{x, y}`) with coordinates mapped to the world's logical dimensions.

## License

MIT License — see [LICENSE](LICENSE).