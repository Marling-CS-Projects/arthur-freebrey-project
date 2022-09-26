# 2.2.2 Cycle 9

## Design

### Objectives

In this development cycle I was focused on creating a combat mechanic of throwing a weapon that can collide with the enemy and attack them to make the game more interactive

* [x] Add weapon sprites to be used for the main combat system
* [x] Have the weapon be thrown away from the player on given input
* [x] Weapon to collide and disappear on collision with enemy or wall
* [x] Enemy to die when being hit with the weapon
* [x] Weapon to move in the current facing direction of the character.

## Usability Features

### Key Variables

| Variable Name  | Use                                                                                                                                          |
| -------------- | -------------------------------------------------------------------------------------------------------------------------------------------- |
| knives         | Stores information about the knife such as the sprite, direction, how many are present in the group and more as well as detecting collisions |
| player / faune | Variable that stores all of the information and properties about the character.                                                              |
| angle          | Calculates and holds information about which direction the weapon will be thrown from on input.                                              |

### Pseudocode

<pre><code>knives = physics.group(){
class: image
maxsize: 3
}

function throwknife(){
    vec = math.vector(0, 0)
    
    if (scaleX &#x3C; 0)
	{
	    vec.x = -1
	}
	    else
	{
    	    vec.x = 1
	}
    
    angle = vec.angle()
    knife.setrotation(angle)

<strong>    knife.x += vec.x * 16
</strong>    knife.y += vec.y * 16
    knife.setVelocity(vec.x * 100, vec.y * 100)



if (keySpace.isDown){
    throwknife()
    return
}


add.collider(this.faune, knives, handleKnifeLizardCollision)
    
private handleKnifeLizardCollision(knives, lizard){
    knives.killAndHide()
    lizard.killAndHide()
}

</code></pre>

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

In this development cycle i faced multiple challenges around the key priority and making sure the most recent input is the correct one. I managed to figure out how to program the multiple inputs pretty quickly but making sure the movement corresponds to the most recent input was seen to be quite challenging.&#x20;

## Testing

Evidence for testing

### Tests

| Test | Instructions                                                                   | What I expect                                                                         | What actually happens                                     | Pass/Fail |
| ---- | ------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------- | --------------------------------------------------------- | --------- |
| 1    | Run code                                                                       | Player and map should still load on the original map                                  | As expected                                               | Pass      |
| 2    | Use the W,A,S,D keys for input as well as making sure arrow keys work properly | The game to allow multiple different inputs simultaneously for ease of play-ability   | As expected                                               | Pass      |
| 3    | Use multiple keys to see which ones are prioritised in movement                | Character moves in response to most recent input                                      | Movement inputs that are defined first remain prioritised | Fail      |

After this I tried to look through different documentation and for help online to try and fix my code in an attempt to make sure correct inputs are prioritised. I had tried to find equivalents to a z-index feature to make sure the right movements are being used. After a while I had realised that developing a feature like this was out of my ability and had chosen to settle with the multiple inputs but had to remain with the issue of input hierarchy.&#x20;

### Tests

| Test | Instructions                                                                   | What I expect                                                                         | What actually happens | Pass/Fail |
| ---- | ------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------- | --------------------- | --------- |
| 1    | Run code                                                                       | Player and map should still load on the original map                                  | As expected           | Pass      |
| 2    | Use the W,A,S,D keys for input as well as making sure arrow keys work properly | The game to allow multiple different inputs simultaneously for ease of play-ability   | As expected           | Pass      |

{% embed url="https://youtu.be/95xLwv9M7yk" %}

The video above shows the different key inputs and how they are used to both control player inputs in the game so that players can choose how to play for an easier and more enjoyable experience for better user access
