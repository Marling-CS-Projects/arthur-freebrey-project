# house.ts

```typescript
import Phaser, { Scene } from 'phaser'
import { createLizardAnims } from '~/anims/EnemyAnims'
import { createCharacterAnims } from '~/anims/CharacterAnims'
import Lizard from '../enemies/Lizard'
import secondmap from './secondmap'
import '../characters/Faune'
import Faune from '../characters/Faune'
import { sceneEvents } from '~/events/EventCenter'

function sleep(ms) {
    return new Promise(resolve => setTimeout(resolve, ms));
}

let times = 0;
let keyA;
let keyS;
let keyD;
let keyW;
let keyEnter;


export default class Game extends Phaser.Scene
{

    private cursors!: Phaser.Types.Input.Keyboard.CursorKeys
    private faune!: Faune
    private playerLizardCollider?: Phaser.Physics.Arcade.Collider
    private knives!: Phaser.Physics.Arcade.Group
    private lizards!: Phaser.Physics.Arcade.Group
    private _kills = 0

    get kills()
	{
		return this._kills
	}


	constructor()
	{
		super('house')
	}

	preload()
    {
        this.cursors = this.input.keyboard.createCursorKeys()
        this.load.audio('adamtalking', 'weapons/adam.mp3')
    }

    create()
    {
        this.scene.run('game-ui')
        const adamspeaking = this.sound.add("adamtalking", {loop: true})
        // adamspeaking.play()
        // this.scene.start('secondmap')

        createCharacterAnims(this.anims)
        createLizardAnims(this.anims)

       const map = this.make.tilemap({ key: 'house2' })
       const tileset = map.addTilesetImage('4Big', 'tiles3')

       const Floor = map.createLayer('Floor', tileset)
       const Walls = map.createLayer('Walls', tileset)
       const Carpet = map.createLayer('Carpet', tileset)
       const Decor = map.createLayer('Decor', tileset)
       const Decor2 = map.createLayer('Decor2', tileset)
       const OpenDoor = map.createLayer('Opendoor', tileset)

       keyA = this.input.keyboard.addKey(Phaser.Input.Keyboard.KeyCodes.A);
       keyS = this.input.keyboard.addKey(Phaser.Input.Keyboard.KeyCodes.S);
       keyD = this.input.keyboard.addKey(Phaser.Input.Keyboard.KeyCodes.D);
       keyW = this.input.keyboard.addKey(Phaser.Input.Keyboard.KeyCodes.W);
       keyEnter = this.input.keyboard.addKey(Phaser.Input.Keyboard.KeyCodes.ENTER);



       Walls.setCollisionByProperty({ collides: true })
       Decor.setCollisionByProperty({ collides: true })
       Decor2.setCollisionByProperty({ collides: true })


    //    const debugGraphics = this.add.graphics().setAlpha(0.7)
    //    Walls.renderDebug(debugGraphics, {
    //     tileColor: null,
    //     collidingTileColor: new Phaser.Display.Color(243, 234, 48, 255),
    //     faceColor: new Phaser.Display.Color(40, 39, 37, 255)
    //    })

    this.knives = this.physics.add.group({
        classType: Phaser.Physics.Arcade.Image,
        maxSize: 3
    })

    this.faune = this.add.faune(632, 400, 'faune')
    this.faune.anims.play("faune-idle-up")
    this.faune.setKnives(this.knives)
    const adam = this.add.image(664, 37, 'adam')
    this.physics.add.collider(this.faune, Walls)
    this.physics.add.collider(this.faune, Decor)
    this.physics.add.collider(this.faune, Decor2)




    this.cameras.main.startFollow(this.faune, true,)
    this.cameras.main.setBounds(-436, -218.5, 1833, 887, true)
    // this.cameras.main.centerOn(innerWidth, innerHeight)

    this.lizards = this.physics.add.group({
        classType: Lizard,
        createCallback: (go) => {
            const lizGo = go as Lizard
            lizGo.body.onCollide = true
        }
    })

    // this.lizards.get(500, 300, 'lizard')
    // this.lizards.get(800, 300, 'lizard')
    this.physics.add.collider(this.lizards, Walls)
    this.physics.add.collider(this.knives, this.lizards, this.handleKnifeLizardCollision, undefined, this)
    this.physics.add.collider(this.knives, Walls, this.handleKnifeWallCollision, undefined, this)
    this.playerLizardCollider = this.physics.add.collider(this.lizards, this.faune, this.handlePlayerLizardCollision, undefined, this)
    
    }

    // private handleKills(kills: number){
    //     kills += 1
    //     console.log(kills)
    // }

    private handleKnifeWallCollision(obj1: Phaser.GameObjects.GameObject, obj2: Phaser.GameObjects.GameObject)
	{
		this.knives.killAndHide(obj1)
	}

	private handleKnifeLizardCollision(obj1: Phaser.GameObjects.GameObject, obj2: Phaser.GameObjects.GameObject)
	{
		this.knives.killAndHide(obj1)
		this.lizards.killAndHide(obj2)
        // this.playerLizardCollider?.destroy()

	}

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


    update(t: number, dt: number){                
        // console.log(this._kills)
        console.log(this.faune.x, this.faune.y)
        if (this.faune.y > 418 && this.faune.x === 632){
            this.scene.stop()
            this.scene.start('game')
            times += 1;
        }


        if (this.faune.y < 60 && this.faune.x > 650 && this.faune.x < 670 && keyEnter.isDown && times > 0){
            const adamWin = this.add.text(678, 0, 'WWWWW', { fontFamily: 'Georgia, "Goudy Bookletter 1911", Times, serif' });
            async function destroyText(){
                await sleep(2000);
                adamWin.destroy()
            }
            destroyText()
        }
        // console.log(this.faune.x, this.faune.y)
        if (this.faune.y < 60 && this.faune.x > 650 && this.faune.x < 670 && keyEnter.isDown && times === 0){
            const adamtext = this.add.text(678, 0, 'Kill a lizard, then come back to talk to me', { fontFamily: 'Georgia, "Goudy Bookletter 1911", Times, serif' });
            async function destroyText(){
                await sleep(2500);
                adamtext.destroy()
            }
            destroyText()
        }

        if (this.faune)
        {
            this.faune.update(this.cursors)
        }
    }

}
```
