# 2.2.2 Cycle 4

## Design

### Objectives

This development cycle was focused on creating simple combat mechanics for later levels and basic enemies will follow and attack the player. Since the player can now properly move as shown in [Cycle 2](cycle-1-1.md) I would like to add suiting combat mechanics and animations to use for the character.

* [x] Create enemies characters that move in random directions
* [x] Make enemy characters interact with the map and collide with walls
* [x] Enemy characters to collide with the player
* [x] Enemy character having movement animations&#x20;

## Usability Features

### Key Variables

| Variable Name         | Use                                                                                               |
| --------------------- | ------------------------------------------------------------------------------------------------- |
| enemy                 | Holds the information about the enemies such as hitbox information                                |
| player / faune        | Variable that stores all of the information and properties about the character.                   |
| left, right, up, down | Stores the information about keyboard inputs allowing them to be assigned to player movement      |
| config                | Stores information about the game resolution, physics and other data to start the game correctly. |
| randomDirection       | Variable that stores and calculates the new direction for the enemy character to randomly move to |

### Pseudocode

```
const lizards
    lizards.collideWorldBounds = true
    lizards.collidePlayer = true
    
Directions = (
    UP,
    DOWN,
    LEFT,
    RIGHT
)
    
procedure randomDirections = {
    newDirection = random.math(0,3)
    while (newDirection = exclude)
    {
        newDirection = random.math(0, 3)
    }
    return newDirection
end procedure

const movement = {
    delay = 1500
    direction = newDirection

lizard.move(movement, loop)
```

## Development

### Outcome

Lots of the parts of the code such as the index.html file remain the same in this second cycle but most of the change will take place in the game.ts file since that contains most of the player and world attributes.

{% tabs %}
{% tab title="game.ts" %}
```typescript
const lizards = this.physics.add.group({
        classType: Lizard,
        createCallback: (go) => {
            const lizGo = go as Lizard
            lizGo.body.onCollide = true
        }
    })

    lizards.get(500, 300, 'lizard')
    this.physics.add.collider(lizards, Island1)
    this.physics.add.collider(lizards, Rocks)
    this.physics.add.collider(lizards, Island2)
    this.physics.add.collider(lizards, water)
    this.physics.add.collider(lizards, House)
    this.physics.add.collider(lizards, Housedecor)
    this.physics.add.collider(lizards, Tree1)
    this.physics.add.collider(lizards, Tree2)
    this.physics.add.collider(lizards, Tree3)
    this.physics.add.collider(lizards, Tree4)
    this.physics.add.collider(lizards, Houseontop)
    this.physics.add.collider(lizards, this.faune)

    // const lizard = this.physics.add.sprite(500, 300, 'lizard', 'lizard_m_idle_anim_f0.png')

    // lizard.anims.play('lizard-run')
```
{% endtab %}

{% tab title="Lizard.ts" %}
```typescript
const randomDirection = (exclude: Direction) => {
    let newDirection = Phaser.Math.Between(0, 3)
    while (newDirection ===  exclude)
    {
        newDirection = Phaser.Math.Between(0, 3)
    }
    return newDirection

}

export default class Lizard extends Phaser.Physics.Arcade.Sprite
{
    private direction = Direction.RIGHT
    private moveEvent: Phaser.Time.TimerEvent
    constructor(scene: Phaser.Scene, x: number, y: number, texture: string, frame?: string){
        super(scene, x, y, texture, frame)

        this.anims.play('lizard-idle')

        scene.physics.world.on(Phaser.Physics.Arcade.Events.TILE_COLLIDE, this.handleTileCollisions, this)

        this.moveEvent = scene.time.addEvent({
            delay: 2000,
            callback: () => {
                this.direction = randomDirection(this.direction)   
            },
            loop: true
        })
    }

    private handleTileCollisions(go: Phaser.GameObjects.GameObject, tile: Phaser.Tilemaps.Tile){

        if (go!== this)
        {
            return
        }
        this.direction = randomDirection(this.direction)   

    }

    preUpdate(t: number, dt: number){
        super.preUpdate(t, dt)

        let speed = 50

        switch (this.direction)
        {
            case Direction.UP: 
            this.setVelocity(0, -speed)
            break

            case Direction.DOWN: 
            this.setVelocity(0, speed)
            break

            case Direction.LEFT: 
            this.setVelocity(-speed, 0)
            break

            case Direction.RIGHT: 
            this.setVelocity(speed, 0)
            break
        }
    }
}
```
{% endtab %}
{% endtabs %}

### Challenges

Throughout this development cycle I faced many challenges in trying to develop an enemy character such as getting the code abstracted into its own file for the enemy without having to bloat the main game.ts file. The reason this became an issue was due to multiple errors found in importing the code and using it inside my create function. How I tackled this was creating individual files for both the enemy character and the animations separately so I can import them both separately leading to less conflict errors. After this change I decided to do the same for the player character making cleaner and less bloated code.\
\
Another challenge I faced throughout my development&#x20;

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
       const WaterWalk = map.createLayer('Waterwalk', tileset)
       const IslandUnder = map.createLayer('Islandunder', tileset)
       const Island1 = map.createLayer('Island1', tileset)
       const Island2 = map.createLayer('Island2', tileset)
       const Rocks = map.createLayer('Rocks', tileset)
       const Decor = map.createLayer('Decor', tileset)
       const Bushes = map.createLayer('Bushes', tileset)
       const House_under = map.createLayer('House under', tileset)
       const Housedecor = map.createLayer('House decor',tileset)
       const House = map.createLayer('House', tileset)
       const Houseontop = map.createLayer('House Ontop', tileset)
       const Tree1 = map.createLayer('Tree1', tileset)
       const Tree2 = map.createLayer('Tree2', tileset)
       const Tree3 = map.createLayer('Tree3', tileset)
       const Tree4 = map.createLayer('Tree4', tileset)

       House.setCollisionByProperty({ collides: true})
       Housedecor.setCollisionByProperty({ collides: true })
       Houseontop.setCollisionByProperty({ collides : true })
       Bushes.setCollisionByProperty({collides: true})
       Rocks.setCollisionByProperty({ collides: true })
       Island1.setCollisionByProperty({ collides: true })
       water.setCollisionByProperty({ collides: true})
       Island2.setCollisionByProperty({ collides: true})
       Bushes.setCollisionByProperty({ collides: true })
       Tree1.setCollisionByProperty({ collides: true })
       Tree2.setCollisionByProperty({ collides: true })
       Tree3.setCollisionByProperty({ collides: true })
       Tree4.setCollisionByProperty({ collides: true })


       const debugGraphics = this.add.graphics().setAlpha(0.7)
       water.renderDebug(debugGraphics, {
        tileColor: null,
        collidingTileColor: new Phaser.Display.Color(243, 234, 48, 255),
        faceColor: new Phaser.Display.Color(40, 39, 37, 255)
       })

    this.faune = this.physics.add.sprite(480, 235, 'faune', 'walk-down-3.png')
    this.faune.body.setSize(this.faune.width * 0.5, this.faune.height * 0.7)

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
    this.physics.add.collider(this.faune, House)
    this.physics.add.collider(this.faune, Housedecor)
    this.physics.add.collider(this.faune, Tree1)
    this.physics.add.collider(this.faune, Tree2)
    this.physics.add.collider(this.faune, Tree3)
    this.physics.add.collider(this.faune, Tree4)
    this.physics.add.collider(this.faune, Houseontop)

    this.cameras.main.startFollow(this.faune, true,)
    this.cameras.main.setBounds(-436, -200.5, 1833, 887, true)
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
       const WaterWalk = map.createLayer('Waterwalk', tileset)
       const IslandUnder = map.createLayer('Islandunder', tileset)
       const Island1 = map.createLayer('Island1', tileset)
       const Island2 = map.createLayer('Island2', tileset)
       const Rocks = map.createLayer('Rocks', tileset)
       const Decor = map.createLayer('Decor', tileset)
       const Bushes = map.createLayer('Bushes', tileset)
       const House_under = map.createLayer('House under', tileset)
       const Housedecor = map.createLayer('House decor',tileset)
       const House = map.createLayer('House', tileset)
       const Houseontop = map.createLayer('House Ontop', tileset)
       const Tree1 = map.createLayer('Tree1', tileset)
       const Tree2 = map.createLayer('Tree2', tileset)
       const Tree3 = map.createLayer('Tree3', tileset)
       const Tree4 = map.createLayer('Tree4', tileset)

       House.setCollisionByProperty({ collides: true})
       Housedecor.setCollisionByProperty({ collides: true })
       Houseontop.setCollisionByProperty({ collides : true })
       Bushes.setCollisionByProperty({collides: true})
       Rocks.setCollisionByProperty({ collides: true })
       Island1.setCollisionByProperty({ collides: true })
       water.setCollisionByProperty({ collides: true})
       Island2.setCollisionByProperty({ collides: true})
       Bushes.setCollisionByProperty({ collides: true })
       Tree1.setCollisionByProperty({ collides: true })
       Tree2.setCollisionByProperty({ collides: true })
       Tree3.setCollisionByProperty({ collides: true })
       Tree4.setCollisionByProperty({ collides: true })


       const debugGraphics = this.add.graphics().setAlpha(0.7)
       water.renderDebug(debugGraphics, {
        tileColor: null,
        collidingTileColor: new Phaser.Display.Color(243, 234, 48, 255),
        faceColor: new Phaser.Display.Color(40, 39, 37, 255)
       })

    this.faune = this.physics.add.sprite(480, 235, 'faune', 'walk-down-3.png')
    this.faune.body.setSize(this.faune.width * 0.5, this.faune.height * 0.7)

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
    this.physics.add.collider(this.faune, House)
    this.physics.add.collider(this.faune, Housedecor)
    this.physics.add.collider(this.faune, Tree1)
    this.physics.add.collider(this.faune, Tree2)
    this.physics.add.collider(this.faune, Tree3)
    this.physics.add.collider(this.faune, Tree4)
    this.physics.add.collider(this.faune, Houseontop)

    this.cameras.main.startFollow(this.faune, true,)
    this.cameras.main.setBounds(-436, -200.5, 1833, 887, true)
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
{% endcode %}

### Tests

| Test | Instructions                                            | What I expect                                                                | What actually happens | Pass/Fail |
| ---- | ------------------------------------------------------- | ---------------------------------------------------------------------------- | --------------------- | --------- |
| 1    | Run code                                                | Character should appear on screen on top of the map                          | As expected           | Pass      |
| 2    | Press keys / WASD                                       | The player should respond by moving around the screen in the given direction | As expected           | Pass      |
| 3    | Press arrow keys                                        | Characters sprite to change to a different corresponding animation           | As expected           | Pass      |
| 4    | Move character into an object with collision properties | Character to stop moving and not get past the object or wall                 | As expected           | Pass      |

{% embed url="https://youtu.be/xtesQxUoG_Q" %}

Here is a video of me demonstrating working character movements on a map made for testing and demonstrating working collisions too through debugging the player hitbox to make it more visual.
