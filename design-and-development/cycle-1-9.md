# Cycle 10

## Design

### Objectives

This development cycle was focused on just trying to develop a simple main menu presented on loading the game and buttons to start it.

* [x] Create background image
* [x] Create start button to load main scene
* [x] Start scene on click of play button
* [x] Create a simple settings menu for basic customisation

## Usability Features

### Key Variables

| Variable Name | Use                                                                                    |
| ------------- | -------------------------------------------------------------------------------------- |
| background    | Stores data surrounding the background such as coordinates scale and the image itself. |
| play          | Holds information about the play button image and a state of being clicked or idle     |
| settings      | Holds information about the settings scene and where the button is positioned.         |

### Pseudocode

<pre><code><strong>//Scene 'mainMenu'
</strong><strong>
</strong><strong>export Phaser Scene
</strong><strong>
</strong><strong>constructor(){
</strong><strong>    super('mainMenu')
</strong><strong>}
</strong><strong>
</strong><strong>preload(){
</strong><strong>}
</strong><strong>
</strong><strong>create(){
</strong><strong>    background = add.image(0,0 'background)
</strong><strong>    logo = add.image(innerwidth / 2, innerheight / 2, 'logo')
</strong><strong>    playbutton = add.image(innerwidth / 2, innnerheight / 2.5, 'playbutton')
</strong><strong>    settings = add.image(innerwidth / 2, innerheight / 3.5, 'settings')
</strong><strong>    
</strong><strong>    playbutton.interactive()
</strong><strong>    
</strong><strong>    playbutton.onclick(){
</strong><strong>        scene.stop()
</strong><strong>        scene.start('game')
</strong><strong>    }
</strong><strong>}</strong></code></pre>

## Development

### Outcome

The code involved for the making of the attack function in this game involved lots of coding across multiple different files to make the feature global to the character in any scene. This involved using the EventCenter.ts file I had set up earlier on in the project to emit scene variables allowing me to achieve this.\
\
&#x20;I have also had to use basic maths functions built into phaser such as the `Math.Vector` feature in order to make sure I am launching the weapon in the correct direction that the character is facing to make the gameplay easier.

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
		this.playerLizardCollider?.destroy()

	}
	
	
```
{% endtab %}
{% endtabs %}

### Challenges

Throughout this development cycle most of the code worked out seamlessly on the testing with only a few small select few bugs. One example of a bug found was the game freezing upon trying to destroy colliders since it was detecting multiple collisions and trying to remove a non existent element across all enemies.&#x20;

## Testing

Evidence for testing

### Tests

| Test | Instructions                                                  | What I expect                                                                  | What actually happens                                 | Pass/Fail |
| ---- | ------------------------------------------------------------- | ------------------------------------------------------------------------------ | ----------------------------------------------------- | --------- |
| 1    | Run code                                                      | Player and map should still load on the original map                           | As expected                                           | Pass      |
| 2    | Press the spacebar to throw the weapon                        | A weapon should be thrown away from the player in the direction they're facing | As expected                                           | Pass      |
| 3    | Hit a character with the weapon to see if they disappear      | The enemy disappears and so does the weapon                                    | The game freezes after detecting multiple collisions  | Fail      |
| 4    | Hit a wall / rock with weapon to see if the weapon disappears | Weapon should disappear and go back into the group upon collision              | As expected                                           | Pass      |

After encountering this through my testing I went through many different methods in an attempt to fix this issue for the specific enemy without having to freeze and break the whole game. I did eventually settle on having to remove the line that destroys the enemy collision now only hiding and immobilising the knife and lizard instead of removing their ability to interact with players.

```
private handleKnifeLizardCollision(obj1: Phaser.GameObjects.GameObject, obj2: Phaser.GameObjects.GameObject)
{
	this.knives.killAndHide(obj1)
	this.lizards.killAndHide(obj2)
        // this.playerLizardCollider?.destroy()
}
```

### Tests

| Test | Instructions                                                  | What I expect                                                                  | What actually happens | Pass/Fail |
| ---- | ------------------------------------------------------------- | ------------------------------------------------------------------------------ | --------------------- | --------- |
| 1    | Run code                                                      | Player and map should still load on the original map                           | As expected           | Pass      |
| 2    | Press the spacebar to throw the weapon                        | A weapon should be thrown away from the player in the direction they're facing | As expected           | Pass      |
| 3    | Hit a character with the weapon to see if they disappear      | The enemy disappears and so does the weapon                                    | As expected           | Pass      |
| 4    | Hit a wall / rock with weapon to see if the weapon disappears | Weapon should disappear and go back into the group upon collision              | As expected           | Pass      |

{% embed url="https://youtu.be/ZsE5NNBF4jw" %}

The game footage above shows how the combat system works on a given input and how the weapon interacts with the enemy characters for a basic combat system that can be fully integrated into a more suitable map later on.
