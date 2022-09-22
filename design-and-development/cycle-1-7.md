# 2.2.2 Cycle 8

## Design

### Objectives

This development cycle I was focused on making sure I had a working health system in place with a visual UI on screen to represent the players health making the experience more interactive.

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
        })
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
