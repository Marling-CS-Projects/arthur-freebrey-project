# 2.2.2 Cycle 10

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
</strong><strong>}
</strong></code></pre>

## Development

### Outcome

For the development in this cycle everything was relatively simple and all went according to plan with all needed to be done was initialising new images I had created as well as setting up a new scene environment to initialise.

{% tabs %}
{% tab title="mainMenu.ts" %}
```typescript
import Phaser, { Scene } from 'phaser'

export default class Game extends Phaser.Scene
{
	constructor()
	{
		super('mainMenu')
	}

    preload(){}

// adding all the images to the scene
    create()
    {
    const background = this.add.image(130, 0, 'backgroundtest').setOrigin(0)
    background.setScale(2.75)
    this.add.image(innerWidth / 2.8, innerHeight / 6.9, 'logo').setOrigin(0)
    const playbutton = this.add.image(innerWidth / 2.2, innerHeight / 2.8, 'playreal').setOrigin(0)
    this.add.image(innerWidth / 2.2, innerHeight / 1.2, 'settings').setOrigin(0)
    this.cameras.main.zoom = 0.59;

//allowing the play button to be interactive and allowing it to be pressed
    playbutton.setInteractive()
// after being clicked the play button should stop the main menu scene and start the game scene
    playbutton.on("pointerdown", ()=> {
        this.scene.stop()
        this.scene.start('game')
    })
}
```
{% endtab %}

{% tab title="PreLoader.ts" %}
```typescript
import Phaser from "phaser";

export default class Preloader extends Phaser.Scene
{
    constructor()
    {
        super('preloader')
    }

    preload()
    {
        this.load.image('tiles', 'tiles/Serene_Village_16x16_extruded.png');
        this.load.tilemapTiledJSON('mainmap', 'tiles/mainmaptest.json');
        this.load.image('tiles2', 'tiles/Serene_Village_16x16.png');
        this.load.tilemapTiledJSON('map2', 'tiles/map2.json');
        this.load.image('tiles3', 'tiles/4 BigSet.png');
        this.load.tilemapTiledJSON('house2', 'tiles/house.json');
        this.load.image('ui-heart-empty', 'ui/ui_heart_empty.png')
	this.load.image('ui-heart-full', 'ui/ui_heart_full.png')
        this.load.image('knife', 'weapons/weapon_knife.png')
        this.load.atlas('faune', 'character/faune.png', 'character/faune.json')
        this.load.atlas('lizard', 'enemies/lizard.png', 'enemies/lizard.json')
        this.load.image('adam', 'character/adam.png')
        this.load.image('background', 'ui/backgroundMenu.jpg')
        this.load.image('backgroundtest', 'ui/backgroundtest.png')
        this.load.image('logo', 'ui/logoreal.png')
        this.load.image('settings', 'ui/settingsreal.png')
        this.load.image('playreal', 'ui/playreal.png')
    } 
    
    create()
    {
        this.scene.start('mainMenu')
    }
}
```
{% endtab %}
{% endtabs %}

### Challenges

I did face small issues early on in trying to get the scene to correctly import using the PreLoader.ts file I had created but after small changes to the export in the scene to be compatible and match the rest of the scenes involved, most things from then went according to plan. \
\
One inconvenience I faced was getting everything to be centered due to the unique sizing of map I had chose early on in the game which then meant using `innerWidth / 2` was not enough to centre everything and I had to be more specific with my measurements.

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

| Test | Instructions           | What I expect                                                                   | What actually happens | Pass/Fail |
| ---- | ---------------------- | ------------------------------------------------------------------------------- | --------------------- | --------- |
| 1    | Run code               | Main menu scene should be presented on launch with background image and buttons | As expected           | Pass      |
| 2    | Click the play button  | Main menu should change for the main game scene after button clicked            | As expected           | Pass      |

{% embed url="https://youtu.be/urRZcaXCZ60" %}

The video above demonstrates the new main menu system and how the game can be initialised on click to the new play button with only small visual differences to previous cycles.
