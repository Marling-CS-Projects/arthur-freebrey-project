# 2.2.2 Cycle 8

## Design

### Objectives

This development cycle I was focused on making sure I had a working health system in place with a visual UI on screen to represent the players health making the experience more interactive. This will allow a visual representation to the player of how the health system will work throughout the whole game.

* [x] Create hearts in the corner of the screen to act as a health system
* [x] Health system to interact with collisions and decreasing hearts on collision&#x20;

## Usability Features

### Key Variables

| Variable Name  | Use                                                                                          |
| -------------- | -------------------------------------------------------------------------------------------- |
| health         | Holds the number of health points a character has that corresponds with the number of points |
| player / faune | Variable that stores all of the information and properties about the character.              |
| hearts         | Contains information about the hearts images and their status of full or empty               |

### Pseudocode

```
hearts = object {
x: 0
y: 0 
gap: 16
quantity: 3
}

sceneEvents.on('player-health-changed', handlePlayerHealthChanged())

procedure handlePlayerHealthChanged(health){
    hearts.children(idx)
    if (idx < health){
    heart.setTexture(heart-full)
    }
    else{
    heart.setTexture(heart-empty)
    }
```

## Development

### Outcome

Throughout this development cycle I decided to abstract my code and make sure all the necessary features are contained in a new file named GameUI.ts where any future ui features can also be contained.

{% tabs %}
{% tab title="GameUI.ts" %}
```typescript
export default class GameUI extends Phaser.Scene
{
    private hearts!: Phaser.GameObjects.Group
    constructor()
    {
        super({key: 'game-ui'})
    }

    create()
    {
        this.hearts = this.add.group({
            classType: Phaser.GameObjects.Image
        })

        this.hearts.createMultiple({
            key: 'ui-heart-full',
            setXY: {
                x: 460,
                y: 230,
                stepX: 16,
            },
            quantity: 3
        })
```
{% endtab %}
{% endtabs %}

This stage in the development was me programming the visual UI onto the screen and making sure it is part of the correct Phaser gameobject with appropriate positioning on the screen too. I achieved this through using correct class types of images that are inside of the hearts game object group.

{% tabs %}
{% tab title="GameUI.ts" %}
```typescript
sceneEvents.on('player-health-changed', this.handlePlayerHealthChanged, this)
        this.events.on(Phaser.Scenes.Events.SHUTDOWN, () =>{
            sceneEvents.off('player-health-changed', this.handlePlayerHealthChanged, this)
        })
    }

    private handlePlayerHealthChanged(health: number){
        this.hearts.children.each((go, idx) => {
            const heart = go as Phaser.GameObjects.Image
            if (idx < health)
            {
                heart.setTexture('ui-heart-full')
            }
            else 
            {
                heart.setTexture('ui-heart-empty')
            }
        } 
```
{% endtab %}
{% endtabs %}

This part of the code demonstrates how the sceneEvents file interacts with the collision by allowing the UI to change by changing which sprite is used. This is achieved through a built in phaser function called "sexTexture" when the idx is greater than the health.

### Challenges

I faced a few small challenges in this development cycle with an example being the index properties of the hearts taking multiple hearts away per collision or none at all. I had dealt with these small issues through unit testing and debugging to make sure that the right health integer is being passed which is how I caught the bug of the wrong parameter of hearts being passed as a number instead of the health needed.

## Testing

Evidence for testing

### Tests

| Test | Instructions                        | What I expect                                                      | What actually happens | Pass/Fail |
| ---- | ----------------------------------- | ------------------------------------------------------------------ | --------------------- | --------- |
| 1    | Run code                            | Player and map should still load on the original map               | As expected           | Pass      |
| 2    | Collide with enemy and watch hearts | Hearts should decrease one at a time per collision to appear empty | As expected           | Pass      |
| 3    | Get to 0 hearts                     | All hearts should appear empty with the character remaining dead   | As expected           | Pass      |

{% embed url="https://youtu.be/2UA0wYcPLbc" %}

The video shows how the heart UI in the top corner decrease per collision until they reach 0 and the player character then faints and becomes immobilised. This will be the representation of how the health system will operate throughout the whole game unless further change is necessary later on.
