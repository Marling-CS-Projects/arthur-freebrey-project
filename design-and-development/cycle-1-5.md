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
speed = 100
        if (cursors.left.Down || keyA.isDown)
        {
            z-index: 0
            faune.anims.play('faune-run-side', true)
            faune.setVelocity(-speed, 0)
            currentkey = left
        }
        else if (cursors.right.Down || keyD.isDown)
        {
            z-index: 0
            faune.anims.play('faune-run-side', true)
            faune.setVelocity(speed, 0)
            faune.body.offset.x = 8
            currentkey = right
        }
        else if (cursors.up.Down || keyW.isDown)
        {
            z-index: 0
            faune.anims.play('faune-run-up', true)
            faune.setVelocity(0, -speed) 
            currentkey = up
        }
        else if (cursors.down.Down || keyS.isDown)
        {
            z-index: 0
            faune.anims.play('faune-run-down', true)
            faune.setVelocity(0, speed)
            currentkey = down
        }
        else
        {
            parts = 'idle'
            this.faune.anims.play()
            this.faune.setVelocity(0, 0)
        }
```

## Development

### Outcome

Through my development most of my development occurred in the game.ts file with declaration of variables with keymaps as well as movement code all remaining in the same file.

{% tabs %}
{% tab title="game.ts" %}
```typescript
 cycllet keyA;
let keyS;
let keyD;
let keyW;
keyA = this.input.keyboard.addKey(Phaser.Input.Keyboard.KeyCodes.A);
keyS = this.input.keyboard.addKey(Phaser.Input.Keyboard.KeyCodes.S);
keyD = this.input.keyboard.addKey(Phaser.Input.Keyboard.KeyCodes.D);
keyW = this.input.keyboard.addKey(Phaser.Input.Keyboard.KeyCodes.W);

    if (!this.cursors || !this.faune)
        {
            return
        }
        const speed = 100;
    if (this.cursors.left?.isDown || keyA.isDown)
        {
            this.faune.anims.play('faune-run-side', true)
            this.faune.setVelocity(-speed, 0)
            this.faune.scaleX = -1
            this.faune.body.offset.x = 24
        }
    else if (this.cursors.right?.isDown || keyD.isDown)
        {
            this.faune.anims.play('faune-run-side', true)
            this.faune.setVelocity(speed, 0)
            this.faune.scaleX = 1
            this.faune.body.offset.x = 8

        }
    else if (this.cursors.up?.isDown || keyW.isDown) {
            this.faune.anims.play('faune-run-up', true)
            this.faune.setVelocity(0, -speed)
        }

    else if (this.cursors.down?.isDown || keyS.isDown) {
            
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
```
{% endtab %}
{% endtabs %}

### Challenges

In this development cycle i faced multiple challenges around the key priority and making sure the most recent input is the correct one. I managed to figure out how to program the multiple inputs pretty quickly but making sure the movement corresponds to the most recent input was seen to be quite challenging. &#x20;

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
