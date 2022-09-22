# 2.2.2 Cycle 4

## Design

### Objectives

This development cycle was focused on creating simple combat mechanics  such as collisions for later levels and basic enemies will follow and attack the player. Since the player can now properly move as shown in [Cycle 2](cycle-1-1.md) I would like to add suiting combat mechanics and animations to use for the character.

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
Another challenge I faced throughout my development was getting proper collisions to work with the player character and the lizard enemy. The problem arose since Phaser has unique ways of handling collisions with things such as players and tiles which I was trying to replicate with the player. Whilst the solution ended up being quite simple with only having to make the Lizard code external to the create function allowing me to use normal phaser colliders with the player character, getting this to work and figuring it out proved to be challenging.

## Testing

Evidence for testing

### Tests

| Test | Instructions                                | What I expect                                                                                            | What actually happens                                             | Pass/Fail |
| ---- | ------------------------------------------- | -------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------- | --------- |
| 1    | Run code                                    | Lizard should appear on screen on the map                                                                | As expected                                                       | Pass      |
| 2    | Wait and watch enemy                        | Lizard should move around in random directions and collide with walls prompting another direction change | As expected                                                       | Pass      |
| 3    | Move player character to collide with enemy | Lizard either to keep pushing against the character and stop or change direction                         | Lizard passes through the player character and carries on moving. | Fail      |

After this I went back to work out why the collision issues were not working and this was due to over complication since I changed my code from checking to overlap of the lizard and player to instead giving the lizard its own collision properties.

{% tabs %}
{% tab title="Game.ts" %}
```typescript
createLizardAnims(this.anims)

const lizards = this.physics.add.group({
        classType: Lizard,
        createCallback: (go) => {
            const lizGo = go as Lizard
            lizGo.body.onCollide = true
        }
    })

lizards.get(500, 300, 'lizard')

this.physics.add.collider(lizards, Island1)
this.physics.add.collider(lizards, water)
this.physics.add.collider(lizards, this.faune)Tests
```
{% endtab %}

{% tab title="Lizard.ts" %}
```typescript
enum Direction 
{
    UP,
    DOWN,
    LEFT,
    RIGHT
}

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

    destroy(fromScene?: boolean){
        this.moveEvent.destroy()
        super.destroy(fromScene)
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

{% tab title="EnemyAnims.ts" %}
```typescript
import Phaser from "phaser";

const createLizardAnims = (anims: Phaser.Animations.AnimationManager) => {
    anims.create({
        key: 'lizard-idle',
        frames: anims.generateFrameNames('lizard', {start: 0, end: 3, prefix: 'lizard_m_idle_anim_f', suffix: '.png' }),
        repeat: -1,
        frameRate: 10
    })

    anims.create({
        key: 'lizard-run',
        frames: anims.generateFrameNames('lizard', {start: 0, end: 3, prefix: 'lizard_m_run_anim_f', suffix: '.png' }),
        repeat: -1,
        frameRate: 10
    })
}

export {
    createLizardAnims
}

```
{% endtab %}
{% endtabs %}

### Tests

| Test | Instructions                                | What I expect                                                                                            | What actually happens                                     | Pass/Fail |
| ---- | ------------------------------------------- | -------------------------------------------------------------------------------------------------------- | --------------------------------------------------------- | --------- |
| 1    | Run code                                    | Lizard should appear on screen on the map                                                                | As expected                                               | Pass      |
| 2    | Wait and watch enemy                        | Lizard should move around in random directions and collide with walls prompting another direction change | As expected                                               | Pass      |
| 3    | Move player character to collide with enemy | Lizard either to keep pushing against the character and stop or change direction                         | As expected, Lizard resists the player and pushes against | Passem    |

{% embed url="https://youtu.be/NcZtR6avPNU" %}

This video demonstrates how the lizard movement operates in testing as well as showing the collisions and how this works with world borders as well as the player.
