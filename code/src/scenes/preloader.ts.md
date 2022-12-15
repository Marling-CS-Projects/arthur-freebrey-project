# PreLoader.ts

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
