# 2.2.2 Cycle 5

## Design

### Objectives

This development cycle was focused on creating the ability to be able to change scenes and maps in game to make the gameplay experience more enjoyable by giving the players more rooms and areas to explore since this code can be reused later on for multiple areas and maps.

* [x] Create a second map for the player to explore
* [x] Program a new scene with all the same features as the first but working with the new tileset and map
* [x] Program the ability to change scenes mid game on call
* [x] Assign this changing map to the player interacting or reaching a certain area of the map to look continuous
* [ ] Add loading animations for the next map to make the playing experience more enjoyable

## Usability Features

### Key Variables

| Variable Name    | Use                                                                                                                             |
| ---------------- | ------------------------------------------------------------------------------------------------------------------------------- |
| Next1            | A tilemap layer that stores tiles that the player could interact with to trigger a map and scene change                         |
| player / faune   | Variable that stores all of the information and properties about the character.                                                 |
| secondmap        | Stores information such as tilemap about the second scene as well as controls for the players etc                               |
| faune.x, faune.y | Holds the player location and position on the map to determine whether there is a collision with the Next1 tile layer if needed |

### Pseudocode

```
const Next1 = tilelayer('Next', 'tileset')
Next1.collides = true
physics.add.collider('faune', 'Next1')

if physics.collide('faune', 'Next1') {
    this.scene.stop()
    this.scene.start('secondmap')
}
```

## Development

### Outcome

Despite the addition of all of the second map code in the new scene to seamlessly work with similar player controls and collision engines not much code needs to be added other than a simple function to detect collisions outside of the map to trigger a scene change.

{% tabs %}
{% tab title="game.ts" %}
```typescript
const Next1 = map.createLayer('Next', tileset)
Next1.setCollisionByProperty({ collides: true })
this.physics.add.collider(this.faune, Next1)

// this.physics.add.overlap(this.faune, Next1) {
    //     console.log("working");
    //     sleep(2000)
    //     this.scene.stop();
    //     this.scene.start('secondmap');
    // }
    
this.physics.world.collide(this.faune, Next1, ()=>{
    console.log("cool")
    this.scene.stop(),
    this.scene.start('secondmap');
 });
```
{% endtab %}
{% endtabs %}

### Challenges

Getting this simple function to swap the map proved to be quite challenging due to the inability to access tilemap properties across functions and outside the scope. How this was an issue was detecting collisions with the **Next1** variable in the update function. Since the detection for the collision needed to be constant, this had to be placed in the update function where the Next1 variable can not be accessed since it is outside of its own scope and globalising this leads to even bigger issues with Phasers engine overall.&#x20;

## Testing

Evidence for testing

### Tests

| Test | Instructions                                                   | What I expect                                                                    | What actually happens                            | Pass/Fail |
| ---- | -------------------------------------------------------------- | -------------------------------------------------------------------------------- | ------------------------------------------------ | --------- |
| 1    | Run code                                                       | Player and map should still load on the original map                             | As expected                                      | Pass      |
| 2    | Try iniate the game with the 'secondmap' property              | The game to start loading the secondmap and load with that tilemap and info      | As expected                                      | Pass      |
| 3    | Run into the corner of the map where 'Next1' tiles are located | The main map scene to stop and the character to seamlessly go into the secondmap | On collision with Next1 nothing happens and the  | Fail      |

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
