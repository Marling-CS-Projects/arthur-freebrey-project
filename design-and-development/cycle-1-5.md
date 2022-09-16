# 2.2.2 Cycle 6

## Design

### Objectives

This development cycle was focused on user accessibility and controls for the player character allowing multiple inputs and removing a hierarchy of input controls to make sure the experience is as smooth as possible.&#x20;

* [x] Create multiple inputs for player movement such as the W,A,S,D controls as well as still having the ability to use the arrow keys if desired.
* [x] Remove the hierarchy of inputs so that whatever key is pressed most recently is what maintains priority in movement.

## Usability Features

### Key Variables

| Variable Name  | Use                                                                                                     |
| -------------- | ------------------------------------------------------------------------------------------------------- |
| W, A, S, D     | Variables used to store keycode information about W,A,S,D inputs to make it easier to put into the code |
| player / faune | Variable that stores all of the information and properties about the character.                         |
| currentKey     | Holds information about the current key being                                                           |

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

| Test | Instructions                                                   | What I expect                                                                    | What actually happens                                                       | Pass/Fail |
| ---- | -------------------------------------------------------------- | -------------------------------------------------------------------------------- | --------------------------------------------------------------------------- | --------- |
| 1    | Run code                                                       | Player and map should still load on the original map                             | As expected                                                                 | Pass      |
| 2    | Try iniate the game with the 'secondmap' property              | The game to start loading the secondmap and load with that tilemap and info      | As expected                                                                 | Pass      |
| 3    | Run into the corner of the map where 'Next1' tiles are located | The main map scene to stop and the character to seamlessly go into the secondmap | On collision with Next1 nothing happens and the secondmap is not initiated  | Fail      |

After this I decided to rework how the collision and initiation works with me now checking for overlap in position and location instead of using the built in Phaser collision engine due to problems with accessing the variable outside of the create function scope. Upon this change I also removed some code I had made for fading and a delay inbetween scenes since the speed at which the player moves across maps is so quick a fade is not needed to make the transition seamless. I kept the collisions inside the game.ts file due to it may being needed later on in development so to comment this out instead seemed more convenient.

{% tabs %}
{% tab title="Game.ts" %}
```typescript
 update(t: number, dt: number){       

        // console.log(this.faune.x, this.faune.y)
        if (this.faune.y > 450 && this.faune.x < 90){
            this.scene.stop()
            this.scene.start('secondmap')
        }
```
{% endtab %}
{% endtabs %}

### Tests

| Test | Instructions                                                   | What I expect                                                                    | What actually happens | Pass/Fail |
| ---- | -------------------------------------------------------------- | -------------------------------------------------------------------------------- | --------------------- | --------- |
| 1    | Run code                                                       | Player and map should still load on the original map                             | As expected           | Pass      |
| 2    | Try iniate the game with the 'secondmap' property              | The game to start loading the secondmap and load with that tilemap and info      | As expected           | Pass      |
| 3    | Run into the corner of the map where 'Next1' tiles are located | The main map scene to stop and the character to seamlessly go into the secondmap | As expected           | Pass      |

{% embed url="https://youtu.be/-zB6Zxc0Btc" %}

This video above shows how the transition between scenes takes place when the character reaches a certain position without any transition or fading animations necessary&#x20;
