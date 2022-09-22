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

Development in this cycle was coincided with a lot more refactoring too across the whole project instead of the majority of the code being placed in the game.ts file compared to previous cycles. I did this through creating individual files with characters, collisions and event centers.

{% tabs %}
{% tab title="Faune.ts" %}
```typescript
enum HealthState
{
	IDLE,
	DAMAGE,
   	DEAD
}

export default class Faune extends Phaser.Physics.Arcade.Sprite
{
    private healthState = HealthState.IDLE
    private damageTime = 0
    private _health = 3

handleDamage(dir: Phaser.Math.Vector2)
	{
		if (this._health <= 0)
		{
			// TODO: die
			this.healthState = HealthState.DEAD
			this.anims.play('faune-faint')
			this.setVelocity(0, 0)
		}
		else
		{
			this.setVelocity(dir.x, dir.y)
			this.setTint(0xff0000)
			this.healthState = HealthState.DAMAGE
			this.damageTime = 0
		}
	}
preUpdate(t: number, dt: number)
	{
		super.preUpdate(t, dt)

		switch (this.healthState)
		{
			case HealthState.IDLE:
				break

			case HealthState.DAMAGE:
				this.damageTime += dt
				if (this.damageTime >= 250)
				{
					this.healthState = HealthState.IDLE
					this.setTint(0xffffff)
					this.damageTime = 0
				}
				break
		}
	}
```
{% endtab %}

{% tab title="game.ts" %}
```typescript
this.playerLizardCollider = this.physics.add.collider(this.lizards, this.faune, this.handlePlayerLizardCollision, undefined, this)

private handlePlayerLizardCollision(obj1: Phaser.GameObjects.GameObject, obj2: Phaser.GameObjects.GameObject){
        
        const lizard = obj2 as Lizard
        const dx = this.faune.x - lizard.x
        const dy = this.faune.y - lizard.y

	const dir = new Phaser.Math.Vector2(dx, dy).normalize().scale(200)
        this.faune.handleDamage(dir)

        sceneEvents.emit('player-health-changed', this.faune.health)

        if (this.faune.health <= 0){
            this.playerLizardCollider?.destroy()
        }
    }
    

```
{% endtab %}
{% endtabs %}

### Challenges

This development cycle involved lots of tedious challenges in trying to obtain an inverse direction for the collision since this would only work if a player was facing certain directions due to phasers built in vector engine. Another issue I faced throughout my development was trying to&#x20;

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
