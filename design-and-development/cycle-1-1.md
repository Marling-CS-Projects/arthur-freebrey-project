# 2.2.2 Cycle 2

## Design

### Objectives

In this development cycle my main aim is to create characters that move and interact with keyboard inputs as well as giving them collision properties to interact with the existing map from [Cycle 1](../2-design-and-development/cycle-1.md).  I aim for the characters also to have sprites that change depending on the user input and how they respond to the movement.

* [x] Create collision properties for certain parts of the map
* [x] Create a character
* [ ] Make the character move on keyboard inputs
* [ ] Assign the character a sprite that interacts with movement
* [ ] Allow the character to respond to the collision properties in the map

### Usability Features

### Key Variables

| Variable Name         | Use                                                                                               |
| --------------------- | ------------------------------------------------------------------------------------------------- |
| velocity(X,Y)         | The speed assigned to the player when key inputs are pressed determining how fast they move       |
| player                | Variable that stores all of the information and properties about the character.                   |
| left, right, up, down | Stores the information about keyboard inputs allowing them to be assigned to player movement      |
| config                | Stores information about the game resolution, physics and other data to start the game correctly. |

### Pseudocode

```
// create config and information about the games properties
config
    height: 1120
    width: 640
    physics: no gravity
    scene: preloader, game
end object

// initialise variables and attributes for the game
procedure create
    player = generatePlayer
    camera.follow(player)
    player.collideWorldBounds = true
    
    
    // create the variables for the keyboard input values
    left = key bind for left arrow key
    right = key bind for right arrow key
    up = key bind for arrow key
    down = key bind for down key
    
    // what the character should do upon given keyboard inputs
    if (input = up) {
        velocity.x = 0
        velocity.y = -velocity
        this.player.animations.play('up');
    
    if (input = down) {
        velocity.x = 0
        velocity.y = -velocity
        this.player.animations.play('down');
    
    if (input = left) {
        velocity.x = -velocity
        velocity.y = 0
        this.player.animations.play('left');
        
    if (input = right) {
        velocity.x = -velocity
        velocity.y = 0
        this.player.animations.play('right');
        
    // stationary    
    if (input = none) {
        velocity.x = 0
        velocity.y = 0
        this.player.animations.play('standing');
    
end procedure
```

## Development

### Outcome

Lots of the parts of the code such as the index.html file remain the same in this second cycle but most of the change will take place in the game.ts file since that contains most of the player and world attributes.

{% tabs %}
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
    this.load.atlas('faune', 'character/faune.png', 'character/faune.json');
  }

  create() {
    this.scene.start("game");
  }
}

```
{% endtab %}

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
    
    //house.setCollisionByProperty({ collides: true });
    //rocks.setCollisionByProperty({ collides: true });
    //pool.setCollisionByProperty({ collides: true });
    
    const faune = this.add.sprite(128, 128, 'faune', 'run-side-4.png')

  }
}
```
{% endtab %}
{% endtabs %}



### Challenges

* Getting web page to load after install the Phaser.js library
* Exporting Tiled maps to the right format to use
* Loading tile layers in certain orders so everything is visible&#x20;

## Testing

Evidence for testing

### Tests

| Test | Instructions                                            | What I expect                                                                | What actually happens                                                                                         | Pass/Fail |
| ---- | ------------------------------------------------------- | ---------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------- | --------- |
| 1    | Run code                                                | Character should appear on screen on top of the map                          | Character texture not loaded with texture frame missing                                                       |           |
| 2    | Press keys / WASD                                       | The player should respond by moving around the screen in the given direction | As expected                                                                                                   | Pass      |
| 3    | Press arrow keys                                        | Characters sprite to change to a different corresponding animation           | Character's sprite was half cut off implying the amount the sprite needs to change was too much or too little | Fail      |
| 4    | Move character into an object with collision properties | Character to stop moving and not get past the object or wall                 | As expected                                                                                                   | Pass      |

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
