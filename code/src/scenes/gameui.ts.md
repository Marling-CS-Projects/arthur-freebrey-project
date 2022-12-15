# GameUI.ts

```typescript
import Phaser from "phaser";
import { sceneEvents } from "~/events/EventCenter";

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

        sceneEvents.on('playerKills', this.handleKills, this)
        this.events.on(Phaser.Scenes.Events.SHUTDOWN, () =>{
            sceneEvents.off('playerKills', this.handleKills, this)
        })
    }

    private handleKills(kills: number){
        kills = kills += 1
        console.log(kills)
        return kills
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
