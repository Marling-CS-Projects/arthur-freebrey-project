---
description: ar
---

# 2.2.2. Cycle 7

## Design

### Objectives

This development cycle was aimed on creating a damage animation and system for the player to get pushed away from the enemy experiencing knockback than can work with the health system I will implement later on.

* [x] Create a damage system to be built on later that decreases on collision with the enemy
* [x] Add a knockback effect temporarily immobilising the player and pushing them away on collision
* [x] Create a visual indicator for the player being hit such as a red flash

## Usability Features

### Key Variables

| Variable Name  | Use                                                                                             |
| -------------- | ----------------------------------------------------------------------------------------------- |
| healthState    | Stores the different cases for healthstate of either: DEAD, IDLE, DAMAGE                        |
| damageTime     | The number of milliseconds the player is immobilised for and until they stop sliding            |
| player / faune | Variable that stores all of the information and properties about the character.                 |
| dir            | Holds the inverse direction of the collision to make sure the player is eventually pushed away. |

### Pseudocode

```
procedure handlePlayerLizardCollision(lizard, faune){
    dirx = faune.x - lizard.x
    diry = faune.y - lizard.y

    const newDir = vector(dirx, diry)
    
    switch (healthState)
	{
	case HealthState.IDLE:
		break

	case HealthState.DAMAGE:
		damageTime += dt
		if (damageTime >= 250)
		{
			healthState = HealthState.IDLE
			this.setTint(red)
			this.damageTime = 0
		}
	}
    
    if health <= 0
	{
	healthState = HealthState.DEAD
	anims.play('faune-faint')
	setVelocity(0, 0)
	}
	else
	{
	setVelocity(dir.x, dir.y)
	setTint(normal)
	healthState = HealthState.DAMAGE
	this.damageTime = 0
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
