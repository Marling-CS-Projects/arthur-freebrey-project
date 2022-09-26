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

The code involved for the making of the attack function in this game involved lots of coding across multiple different files to make the feature global to the character in any scene. This involved using the EventCenter.ts file I had set up earlier on in the project to emit scene variables allowing me to achieve this.

{% tabs %}
{% tab title="Faune.ts" %}
```typescript
private knives?: Phaser.Physics.Arcade.Group 

setKnives(knives: Phaser.Physics.Arcade.Group)
{
	this.knives = knives
}

private throwKnife()
{
	const knife = this.knives.get(this.x, this.y, 'knife') as Phaser.Physics.Arcade.Image
		if (!knife)
		{
			return
		}

	const parts = this.anims.currentAnim.key.split('-')
	const direction = parts[2]
	const vec = new Phaser.Math.Vector2(0, 0)

	switch (direction)
	{
		case 'up':
		vec.y = -1
		break

		case 'down':
		vec.y = 1
		break

		default:
		case 'side':
		if (this.scaleX < 0)
		{
			vec.x = -1
		}
		else
		{
			vec.x = 1
		}

		const angle = vec.angle()
		knife.setActive(true)
		knife.setVisible(true)
		knife.setRotation(angle)

		knife.x += vec.x * 16
		knife.y += vec.y * 16

		knife.setVelocity(vec.x * 300, vec.y * 300)
	}

update(){
if (Phaser.Input.Keyboard.JustDown(cursors.space!)){
			this.throwKnife()
			return
		}
	}
```
{% endtab %}

{% tab title="Game.ts" %}
```typescript
private knives!: Phaser.Physics.Arcade.Group

this.knives = this.physics.add.group({
        classType: Phaser.Physics.Arcade.Image,
        maxSize: 3
    })
    
this.physics.add.collider(this.knives, this.lizards, this.handleKnifeLizardCollision, undefined, this)
this.physics.add.collider(this.knives, Island1, this.handleKnifeWallCollision, undefined, this)

private handleKnifeLizardCollision(obj1: Phaser.GameObjects.GameObject, obj2: Phaser.GameObjects.GameObject)
	{
		this.knives.killAndHide(obj1)
		this.lizards.killAndHide(obj2)
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
