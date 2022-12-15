# mainMenu.ts

```typescript
import Phaser, { Scene } from 'phaser'

export default class Game extends Phaser.Scene
{

	constructor()
	{
		super('mainMenu')
	}

	preload()
    {
    }

    create()
    {
    const background = this.add.image(130, 0, 'backgroundtest').setOrigin(0)
    background.setScale(2.75)
    this.add.image(innerWidth / 2.8, innerHeight / 6.9, 'logo').setOrigin(0)
    const playbutton = this.add.image(innerWidth / 2.2, innerHeight / 2.8, 'playreal').setOrigin(0)
    this.add.image(innerWidth / 2.2, innerHeight / 1.2, 'settings').setOrigin(0)

    this.cameras.main.zoom = 0.59;

    playbutton.setInteractive()

    playbutton.on("pointerdown", ()=> {
        this.scene.stop()
        this.scene.start('game')
    })
 

    }


    update(t: number, dt: number){                

    }

}
```
