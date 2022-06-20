# 2.1 Design Frame

![](<../.gitbook/assets/image (11).png>)

## Systems Diagram

This system diagram represents the stages of the game development by showing different parts of the game split up by category. Throughout my development I will choose a couple of the sections at a time to build a fully functioning game that is accessible in all the categories I have selected.

## Usability Features

Usability is very important in my game since I want everybody to have an enjoyable experience playing without having to worry about anything other than the gameplay. There are 5 keys areas of usability to make sure that the user experience is enjoyable.

### Effective

In my game users may not have clear levels or obstacles to complete but the player should still have a reward of completion through mini tasks throughout playing. These small mini objectives should keep the user engaged allowing them to have a sense of progress without directly aiming towards an end goal.

### Efficiency

Efficiency is the speed in which any user can complete a given goal or task which little difficulty. To help this, I will make sure that the UI is easy to navigate and does not take more than a few clicks to find any given setting or option.

### Engaging

The solution must be engaging for the user to use allowing them to carry on enjoying the game. To do this, I will have each area having hidden sections and areas that the player can explore further as they progress throughout the game. This is designed to keep the user playing for a prolonged period of time and not just trying to complete the game as quickly as possible.

### Error Tolerant

The solution should have little or no errors, in the place of an error it must not be critical to the gameplay. I will achieve this through constant unit testing of my project making sure that key sections and features are all functioning as intended whilst developing before they get published.&#x20;

### Easy To Learn

The solution should be easy to use and this is regarding all sections such as controls as well as game mechanics. I will make all the controls and game systems / mechanics simple since a complicated game that is hard to understand can make the whole experience unpleasant. However I also do not want the controls and mechanics too easy to allow them to still engaging.&#x20;

## Pseudocode for the Game

### Pseudocode for game

This is the basic layout of the object to store the details of the game. This will be what is rendered as it will inherit all important code for the scenes.

```
object Game
    type: Phaser
    parent: id of HTML element
    width: width
    height: height
    physics: set up for physics
    scenes: add all menus, levels and other scenes
end object

render Game to HTML web page
```

### Pseudocode for a level

This shows the basic layout of code for a Phaser scene. It shows where each task will be executed.

```
class Level extends Phaser Scene

    procedure preload
        load all sprites and music
    end procedure
    
    procedure create
        start music
        draw background
        create puzzle elements
        create enemies
        create obstacles
        create finishing position
        create character
        
    end procedure
    
    procedure update
        handle key presses
        move player
        move interactable objects
        update animations
        check if player at exit
    end procedure
    
end class
```
