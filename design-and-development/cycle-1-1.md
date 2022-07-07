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

export default class Preloader extends Phaser.Scene
{
    constructor()
    {
        super('preloader')
    }

    preload()
    {
        this.load.image('tiles', 'tiles/Serene_Village_16x16.png');
        this.load.tilemapTiledJSON('mainmap', 'tiles/mainmap.json');
        this.load.atlas('faune', 'character/faune.png', 'character/faune.json')
    } 

    create()
    {
        this.scene.start('game')
    }
}
```
{% endtab %}

{% tab title="main.ts" %}
```typescript
import Phaser, { Scale } from 'phaser'

import Preloader from './scenes/Preloader'
import Game from './scenes/game'

const config: Phaser.Types.Core.GameConfig = {
	type: Phaser.AUTO,
	width: innerWidth,
	height: innerHeight,
	physics: {
		default: 'arcade',
		arcade: {
			gravity: { y: 0 }
		}
	},
	scene: [Preloader, Game],
	scale: {
		zoom: 2
	}
}

export default new Phaser.Game(config)

```
{% endtab %}

{% tab title="game.ts" %}
```typescript
import Phaser from 'phaser'

export default class Game extends Phaser.Scene
{
    private cursors!: Phaser.Types.Input.Keyboard.CursorKeys
    private faune!: Phaser.Physics.Arcade.Sprite
	constructor()
	{
		super('game')
	}

	preload()
    {
        this.cursors = this.input.keyboard.createCursorKeys()
    }

    create()
    {
       const map = this.make.tilemap({ key: 'mainmap' })
       const tileset = map.addTilesetImage('Serene_Village_16x16', 'tiles', 16, 16, 1, 2)

       const water = map.createLayer('Water', tileset)
       const Island1 = map.createLayer('Island1', tileset)
       const Island2 = map.createLayer('Island2', tileset)
       const Rocks = map.createLayer('Rocks', tileset)

       Rocks.setCollisionByProperty({ collides: true })
       Island1.setCollisionByProperty({ collides: true })
       water.setCollisionByProperty({ collides: true})
       Island2.setCollisionByProperty({ collides: true})

    //    const debugGraphics = this.add.graphics().setAlpha(0.7)
    //    Island1.renderDebug(debugGraphics, {
    //     tileColor: null,
    //     collidingTileColor: new Phaser.Display.Color(243, 234, 48, 255),
    //     faceColor: new Phaser.Display.Color(40, 39, 37, 255)
    //    })

    this.faune = this.physics.add.sprite(480, 235, 'faune', 'walk-down-3.png')
    this.faune.body.setSize(this.faune.width * 0.5, this.faune.height * 0.8)

    this.anims.create({
        key: 'faune-idle-down',
        frames: [{ key: 'faune', frame: 'walk-down-3.png'}]
    })

    this.anims.create({
        key: 'faune-idle-up',
        frames: [{ key: 'faune', frame: 'walk-up-3.png'}]
    })

    this.anims.create({
        key: 'faune-idle-side',
        frames: [{ key: 'faune', frame: 'walk-side-3.png'}]
    })
    

    this.anims.create({ 
        key: 'faune-run-down',
        frames: this.anims.generateFrameNames('faune', {start: 1, end: 8, prefix: 'run-down-', suffix: '.png'}),
        repeat: -1,
        frameRate: 15

    })

    this.anims.create({ 
        key: 'faune-run-up',
        frames: this.anims.generateFrameNames('faune', {start: 1, end: 8, prefix: 'run-up-', suffix: '.png'}),
        repeat: -1,
        frameRate: 15

    })

    this.anims.create({ 
        key: 'faune-run-side',
        frames: this.anims.generateFrameNames('faune', {start: 1, end: 8, prefix: 'run-side-', suffix: '.png'}),
        repeat: -1,
        frameRate: 15

    })


    this.faune.anims.play('faune-idle-down')
    this.physics.add.collider(this.faune, Island1)
    this.physics.add.collider(this.faune, Rocks)
    this.physics.add.collider(this.faune, Island2)
    this.physics.add.collider(this.faune, water)

    this.cameras.main.startFollow(this.faune, true,)
    // this.cameras.main.centerOn(innerWidth, innerHeight)

    }
    

    update(t: number, dt: number){
        if (!this.cursors || !this.faune)
        {
            return
        }
        const speed = 100;
        if (this.cursors.left?.isDown)
        {
            this.faune.anims.play('faune-run-side', true)
            this.faune.setVelocity(-speed, 0)
            this.faune.scaleX = -1
            this.faune.body.offset.x = 24
        }
        else if (this.cursors.right?.isDown)
        {
            this.faune.anims.play('faune-run-side', true)
            this.faune.setVelocity(speed, 0)
            this.faune.scaleX = 1
            this.faune.body.offset.x = 8

        }
        else if (this.cursors.up?.isDown) {
            
            this.faune.anims.play('faune-run-up', true)
            this.faune.setVelocity(0, -speed)
        }

        else if (this.cursors.down?.isDown) {
            
            this.faune.anims.play('faune-run-down', true)
            this.faune.setVelocity(0, speed)
        }
        else
        {
            const parts = this.faune.anims.currentAnim.key.split('-')
            parts[1] = 'idle'
            this.faune.anims.play(parts.join('-'))
            this.faune.setVelocity(0, 0)
        }

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
| 1    | Run code                                                | Character should appear on screen on top of the map                          | Character texture not loaded with texture frame missing                                                       | Fail      |
| 2    | Press keys / WASD                                       | The player should respond by moving around the screen in the given direction | As expected                                                                                                   | Pass      |
| 3    | Press arrow keys                                        | Characters sprite to change to a different corresponding animation           | Character's sprite was half cut off implying the amount the sprite needs to change was too much or too little | Fail      |
| 4    | Move character into an object with collision properties | Character to stop moving and not get past the object or wall                 | As expected                                                                                                   | Pass      |

After this I went back to work out why the sprite issues weren't working and was due to the increments of how the image sprite stages were going up, to fix this I changed them from decimal increments to integer stages.

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
