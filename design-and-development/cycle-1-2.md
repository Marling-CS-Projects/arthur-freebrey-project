# 2.2.2 Cycle 3

## Design

### Objectives

This cycle I focused on getting a working camera system set up to make the experience of playing more engaging with a top-down camera angle. I am to get the camera to actively track and follow the character created in[ **Cycle 2**](cycle-1-1.md) **** whilst also interacting with map barriers.

* [x] Create camera objects and properties inside using Phaser documentation&#x20;
* [x] Allow the camera to be centered onto the player and follow the player around
* [x] Make the camera interact with the map and not go beyond the map boundaries

### Usability Features

### Key Variables

| Variable Name  | Use                                                                                                                                                  |
| -------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------- |
| camera         | The camera class here has uses for deciding what properties the camera should operate on such as the viewport or information about the scroll values |
| player / faune | Variable that stores all of the information and properties about the character such as position                                                      |
| bounds         | Stores information about the map borders and where the camera should stop scrolling                                                                  |

### Pseudocode

```
// create config and information about the games properties
config
    height: 1120
    width: 640
    zoom: 2
    scene: preloader, game
end object

// initialise variables and attributes for the game
procedure create
    player = generatePlayer
    camera.follow(player)
    camera.setBounds(560, 320)
    
end procedure
```

## Development

### Outcome

Whilst the Phaser library contains documentation surrounding the use of the camera, lots of these resources were outdated creating for a process of trial and error to get the following aspects working properly. Most of the code took place in the Game.ts file since trying to create a separate file for simply camera controls created some errors regarding importing into the main scene.

{% tabs %}
{% tab title="game.ts" %}
```typescript
game.cameras.startFollow(faune, true,)
game.cameras.setBounds(-436, -200.5, 1833, 887, true)

//game.debug.cameraInfo(game.camera, 32, 32);
//game.debug.spriteCoords(player, 32, 500);
```
{% endtab %}
{% endtabs %}



### Challenges

This cycle whilst having a simple outcome and seeming relatively simple faced quite challenging regarding documentation and having to be very specific with measurements since many errors occurred in trying to be scalable.&#x20;

## Testing

| Test | Instructions                    | What I expect                                                                             | What actually happens                                                                   | Pass/Fail |
| ---- | ------------------------------- | ----------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------- | --------- |
| 1    | Run code                        | Everything should still be visible with the new camera property taking control            | Everything loads in correctly but positioning is not correct                            | Fail      |
| 2    | Attempt to move character       | Player moves around and the camera moves accordingly                                      | Camera moves accordingly but will not centre onto the character                         | Fail      |
| 3    | Move character to border of map | Camera should stop following the camera once it collides with the border to hide barriers | Camera does not stop when encountering borders and shows the black space beyond the map | Fail      |

After this failure in testing I had to make changes on how the camera was set up in the game.ts file which is where I had to make changes regarding measurements inside other files such as index.html and using **this.** to point to a specific object.&#x20;

One example of a change made to the index.html file was making sure that due to the size of the map players do not use the scroll bar to make sure the camera is uncentered and taking them out of the experience since it is a web based game. To account for this I hid the overflow scroll bar and went through processes of using specific measurements to centre the camera but decided using the inner width and inner height divided allowed the game to be more accessible and scalable across different device sizes.

Changes I had to make to the Game.ts involved having to give specific measurements for the game map borders since they were not registering when using estimate numbers. Another example was using **this.** to point to specific objects and creating the cameras.main making it usable across the project. This also allowed me to properly use this to interact with the character "faune" which has got its own separate folders and files across the project making the overall project readable but still operational.

{% tabs %}
{% tab title="Game.ts" %}
```typescript

// Creating a new camera object that follows the faune character with the boolean
// true to keep it constantly following and setting borders for the map
this.cameras.main.startFollow(this.faune, true,)
this.cameras.main.setBounds(-436, -200.5, 1833, 887, true)
// this.cameras.main.centerOn(innerWidth, innerHeight)

//game.cameras.startFollow(faune, true,)
//game.cameras.setBounds(-436, -200.5, 1833, 887, true)
//game.debug.cameraInfo(game.camera, 32, 32);
//game.debug.spriteCoords(player, 32, 500);
```
{% endtab %}

{% tab title="index.html" %}
```html
<html>
  <head>
    <title>testing</title>
  </head>
    <body>
	<script src="main.ts"></script>
	    <!-- <body onLoad="window.scroll(785, 375)"> -->
	    <body onLoad="window.scroll(innerWidth / 2, innerHeight / 2)">
	<style>
	  body {
	    padding: 0%;
	    margin: 0%;
            overflow: hidden;
	  }
	</style>
  </body>
</html>

```
{% endtab %}
{% endtabs %}

### Tests

| Test | Instructions                    | What I expect                                                                             | What actually happens | Pass/Fail |
| ---- | ------------------------------- | ----------------------------------------------------------------------------------------- | --------------------- | --------- |
| 1    | Run code                        | Everything should still be visible with the new camera property taking control            | As expected           | Pass      |
| 2    | Attempt to move character       | Player moves around and the camera moves accordingly                                      | As expected           | Pass      |
| 3    | Move character to border of map | Camera should stop following the camera once it collides with the border to hide barriers | As expected           | Pass      |

{% embed url="https://youtu.be/xtesQxUoG_Q" %}

This video demonstrates the character moving around the map with the camera following the character's movements until it reaches the world border and stops tracking the character until it is able to do so again.
