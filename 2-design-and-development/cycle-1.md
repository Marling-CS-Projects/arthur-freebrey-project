# 2.2.1 Cycle 1

## Design

### Objectives

In this development cycle my aim is to create a HTML project with Typescript using parcel for building the page. Using the Phaser.js library I went on to create scenes and backgrounds from custom maps I have designed which I could then build on top of later on.\
\
However a lot of the code in this cycle may need to be rewritten later on and refactored but I believe that this is important into learning more about the libraries and me getting familiar in how to code effectively with them.

* [x] Set up an environment in VS Code with TypeScript support
* [x] Create the GitHub repository to store the code in.
* [x] Get the website to build and run on localhost
* [x] Create working scenes with Phaser.js
* [x] Import and use custom sprites and maps through JSON files

### Usability Features

### Key Variables

| Variable Name | Use                                                                                         |
| ------------- | ------------------------------------------------------------------------------------------- |
| create        | Initialises all the variables at the start of the game such as sprites.                     |
| update        | Updates the position of the character and other information every frame                     |
| mainMap       | Variable that stores the JSON information for the map and where each tile goes              |
| velocity(X,Y) | The speed assigned to the player when key inputs are pressed determining how fast they move |
| player        | Variable that stores all of the information and properties about the character.             |

### Pseudocode

```
class Preloader extends Phaser.Scene {
  constructor() {
    super("preloader");
  }
  preload() {
    image(tiles/main_tiles.png);
    tilemapTiledJSON(tiles/map-1.json);
  }

  create() {
    this.scene.start("game");
  }
}
```

## Development

### Outcome

The file game.ts is where lots of development will take place with importing separate files for individuals characters as enemies as well as preparing the location and map to take place. This is where I have imported different parts of my map that will be initialised upon loading by using the create function.

{% tabs %}
{% tab title="main.ts" %}
```typescript
import Phaser from "phaser";

import Preloader from "./scenes/Preloader";
import Game from "./scenes/game";

export default new Phaser.Game({
  type: Phaser.AUTO,
  width: 1120,
  height: 640,
  physics: {
    default: "arcade",
    arcade: {
      gravity: { y: 0 },
    },
  },
  scene: [Preloader, Game],
  scale: {
    zoom: 1.464,
  },
});

```
{% endtab %}

{% tab title="index.html" %}
```html
<html>
  <head>
    <title>testing</title>
  </head>
  <body>
    <script src="main.ts" type="module"></script>
    <style>
      body {
        padding: 0%;
        margin: 0%;
        padding-left: 7.5%;
      }
    </style>
  </body>
</html>

```
{% endtab %}

{% tab title="game.ts" %}
```typescript
import Phaser from "phaser";

export default class Game extends Phaser.Scene {
  constructor() {
    super("game");
  }

  preload() {}

  create() {
    const map = this.make.tilemap({ key: "map-1" });
    const tileset = map.addTilesetImage("Serene_Village_XP", "tiles");

    const island = map.createLayer("Island 1", tileset);
    const island2 = map.createLayer("Island 2", tileset);
    const pool = map.createLayer("Pool", tileset);
    const bushes = map.createLayer("bushes etc", tileset);
    const flowers = map.createLayer("flowers", tileset);
    const rocks = map.createLayer("Rocks", tileset);
    const house = map.createLayer("House", tileset);
    const house_decor = map.createLayer("House decor", tileset);
    const house_decor2 = map.createLayer("House decor 2", tileset);
    const tippity = map.createLayer("Tippity top", tileset);
    const inbetween = map.createLayer("inbetween", tileset);
    const trees5 = map.createLayer("Tree 5", tileset);
    const trees4 = map.createLayer("Tree 4", tileset);
    const trees3 = map.createLayer("Tree 3", tileset);
    const trees2 = map.createLayer("Trees 2", tileset);
    const trees = map.createLayer("Trees", tileset);
  }
}

```
{% endtab %}

{% tab title="Preloader.ts" %}
```typescript
import Phaser from "phaser";

export default class Preloader extends Phaser.Scene {
  constructor() {
    super("preloader");
  }
  preload() {
    this.load.image("tiles", "tiles/main_tiles.png");
    this.load.tilemapTiledJSON("map-1", "tiles/map-1.json");
  }

  create() {
    this.scene.start("game");
  }
}

```
{% endtab %}
{% endtabs %}

Using npm I will be able to easily install all packages with the **`npm install`** command which installs custom scripts too. Using these custom scripts, the command **`npm start`** will cause parcel to build and deploy the project to localhost:8000 where I can test my code and see updates in real time.

### Challenges

* Getting web page to load after install the Phaser.js library
* Exporting Tiled maps to the right format to use
* Loading tile layers in certain orders so everything is visible&#x20;

## Testing

Evidence for testing

### Tests

| Test | Instructions                         | What I expect                                                                                | What actually happens                                                          | Pass/Fail |
| ---- | ------------------------------------ | -------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------ | --------- |
| 1    | Run code                             | The code should build with the console presenting information about where it is being hosted | As expected                                                                    | Pass      |
| 2    | Connect to localhost:8000 on browser | Web page to load with all sections of the map present                                        | Parts of the map load in incorrect order causing visual glitches in appearance | Fail      |

After this I went back to work out why the visual glitches were appearing which is when I realised the order in which parts of the map were being loaded caused them to appear in the wrong order on top of each other. I went back to edit my game.ts file to reorder all my layers to an order where they are being created in the visual order I would like them to.

{% code title="game.ts" %}
```typescript
import Phaser from "phaser";

export default class Game extends Phaser.Scene {
  constructor() {
    super("game");
  }

  preload() {}

  create() {
    const map = this.make.tilemap({ key: "map-1" });
    const tileset = map.addTilesetImage("Serene_Village_XP", "tiles");

    const island = map.createLayer("Island 1", tileset);
    const island2 = map.createLayer("Island 2", tileset);
    const pool = map.createLayer("Pool", tileset);
    const trees5 = map.createLayer("Tree 5", tileset);
    const trees4 = map.createLayer("Tree 4", tileset);
    const trees3 = map.createLayer("Tree 3", tileset);
    const trees2 = map.createLayer("Trees 2", tileset);
    const trees = map.createLayer("Trees", tileset);
    const bushes = map.createLayer("bushes etc", tileset);
    const flowers = map.createLayer("flowers", tileset);
    const rocks = map.createLayer("Rocks", tileset);
    const house = map.createLayer("House", tileset);
    const house_decor = map.createLayer("House decor", tileset);
    const house_decor2 = map.createLayer("House decor 2", tileset);
    const tippity = map.createLayer("Tippity top", tileset);
    const inbetween = map.createLayer("inbetween", tileset);

    house.setCollisionByProperty({ collides: true });
    rocks.setCollisionByProperty({ collides: true });
    pool.setCollisionByProperty({ collides: true });
  }
}


```
{% endcode %}



| Test | Instructions                         | What I expect                                                                                | What actually happens | Pass/Fail |
| ---- | ------------------------------------ | -------------------------------------------------------------------------------------------- | --------------------- | --------- |
| 1    | Run code                             | The code should build with the console presenting information about where it is being hosted | As expected           | Pass      |
| 2    | Connect to localhost:8000 on browser | Web page to load with all sections of the map present                                        | As expected           | Pass      |

### Evidence

![](<../.gitbook/assets/image (12).png>)

This image shows the map I have created and designed where all the layers of the map have loaded in the correct order I specified in my game.ts file.
