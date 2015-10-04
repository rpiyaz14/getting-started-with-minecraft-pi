# Getting Started with Minecraft Pi

Minecraft is a popular sandbox open-world building game. A free version of Minecraft is available for the Raspberry Pi; it also comes with a programming interface. This means you can write commands and scripts in Python code to build things in the game automatically. It's a great way to learn Python!

![Minecraft Pi banner](images/minecraft-pi-banner.png)

## Run Minecraft

To run Minecraft, double click the desktop icon or enter `minecraft-pi` in the terminal.

![](images/mcpi-start.png)

When Minecraft Pi has loaded, click on **Start Game**, followed by **Create new**. You'll notice that the containing window is offset slightly. This means to drag the window around you have to grab the title bar behind the Minecraft window.

![](images/mcpi-game.png)

You are now in a game of Minecraft! Go walk around, hack things, and build things!

Use the mouse to look around and use the following keys on the keyboard:

| Key          | Action               |
| :---:        | :-----:              |
| W            | Forward              |
| A            | Left                 |
| S            | Backward             |
| D            | Right                |
| E            | Inventory            |
| Space        | Jump                 |
| Double Space | Fly / Fall           |
| Esc          | Pause / Game menu    |
| Tab          | Release mouse cursor |

You can select an item from the quick draw panel with the mouse's scroll wheel (or use the numbers on your keyboard), or press `E` and select something from the inventory.

![](images/mcpi-inventory.png)

You can also double tap the space bar to fly into the air. You'll stop flying when you release the space bar, and if you double tap it again you'll fall back to the ground.

![](images/mcpi-flying.png)

With the sword in your hand, you can click on blocks in front of you to remove them (or to dig). With a block in your hand, you can use right click to place that block in front of you, or left click to remove a block.

## Use the Python programming interface

With Minecraft running, and the world created, bring your focus away from the game by pressing the `Tab` key, which will free your mouse. Open Python 3 from the application menu and move the windows so they're side-by-side.

You can either type commands directly into the Python window or create a file so you can save your code and run it again another time.

If you want create a file, go to `File > New window` and `File > Save`. You'll probably want to save this in your home folder or a new project folder.

Start by importing the Minecraft library, creating a connection to the game and testing it by posting the message "Hello world" to the screen:

```python
from mcpi.minecraft import Minecraft

mc = Minecraft.create()

mc.postToChat("Hello world")
```

If you're entering commands directly into the Python window, just hit `Enter` after each line. If it's a file, save with `Ctrl + S` and run with `F5`. When your code runs, you should see your message on screen in the game.

![](images/mcpi-idle.png)

### Find your location

To find your location, type:

```python
pos = mc.player.getPos()
```

`pos` now contains your location; access each part of the set of coordinates with `pos.x`, `pos.y` and `pos.z`.

Alternatively, a nice way to get the coordinates into separate variables is to use Python's unpacking technique:

```python
x, y, z = mc.player.getPos()
```

Now `x`, `y`, and `z` contain each part of your position coordinates. `x` and `z` are the walking directions (forward/back and left/right) and `y` is up/down.

Note that `getPos()` returns the location of the player at the time, and if you move position you have to call the function again or use the stored location.

### Teleport

As well as finding out your current location you can specify a particular location to teleport to.

```python
x, y, z = mc.player.getPos()
mc.player.setPos(x, y+100, z)
```

This will transport your player to 100 spaces in the air. This will mean you'll teleport to the middle of the sky and fall straight back down to where you started.

Try teleporting to somewhere else!

### Set block

You can place a single block at a given set of coordinates with `mc.setBlock()`:

```python
x, y, z = mc.player.getPos()
mc.setBlock(x+1, y, z, 1)
```

Now a stone block should appear beside where you're standing. If it's not immediately in front of you it may be beside or behind you. Return to the Minecraft window and use the mouse to spin around on the spot until you see a grey block directly in front of you.

![](images/mcpi-setblock.png)

The arguments passed to `set block` are `x`, `y`, `z` and `id`. The `(x, y, z)` refers to the position in the world (we specified one block away from where the player is standing with `x + 1`) and the `id` refers to the type of block we'd like to place. `1` is stone.

Other blocks you can try:

```
Air:   0
Grass: 2
Dirt:  3
```

Now with the block in sight, try changing it to something else:

```python
mc.setBlock(x+1, y, z, 2)
```

You should see the grey stone block change in front of your eyes!

![](images/mcpi-setblock2.png)

#### Blocks as variables

You can use a variable to store an ID to make the code more readable. The IDs are retrievable through `block`:

```python
from mcpi import block

dirt = block.DIRT.id
mc.setBlock(x, y, z, dirt)
```

Or if you know the ID, you can just set it directly:

```python
dirt = 3
mc.setBlock(x, y, z, dirt)
```

### Special blocks

There are some blocks which have extra properties, such as Wool which has an extra setting you can specify the colour. To set this use the optional fourth parameter in `setBlock`:

```python
wool = 35
mc.setBlock(x, y, z, wool, 1)
```

Here the fourth parameter `1` sets the wool colour to orange. Without the fourth parameter it is set to the default (`0`) which is white. Some more colours are:

```
2: Magenta
3: Light Blue
4: Yellow
```

Try some more numbers and watch the block change!

Other blocks which have extra properties are wood (`17`): oak, spruce, birch, etc; tall grass (`31`): shrub, grass, fern; torch (`50`): pointing east, west, north, south; and more. See the [API reference](http://www.stuffaboutcode.com/p/minecraft-api-reference.html) for full details.

### Set multiple blocks

As well as setting a single block with `setBlock` you can fill in a volume of space in one go with `setBlocks`:

```python
stone = 1
x, y, z = mc.player.getPos()
mc.setBlocks(x+1, y+1, z+1, x+11, y+11, z+11, stone)
```

This will fill in a 10 x 10 x 10 cube of solid stone.

![](images/mcpi-setblocks.png)

You can create bigger volumes with the `setBlocks` function but it may take longer to generate!

## Dropping blocks as you walk

Now you know how to drop blocks, let's use our moving location to drop blocks when you walk.

The following code will drop a flower behind you wherever you walk:

```python
from mcpi.minecraft import Minecraft
from time import sleep

mc = Minecraft.create()

flower = 38

while True:
    x, y, z = mc.player.getPos()
    mc.setBlock(x, y, z, flower)
    sleep(0.1)
```

Now walk forward for a while and turn around to see the flowers you have left behind you.

![](images/mcpi-flowers.png)

Since we used a `while True` loop this will go on forever. To stop it, hit `Ctrl + C` in the Python window.

Try flying through the air and see the flowers you leave in the sky:

![](images/mcpi-flowers-sky.png)

What if we only wanted to drop flowers when the player walks on grass? We can use `getBlock` to find out what type a block is:

```python
x, y, z = mc.player.getPos()  # player position (x, y, z)
this_block = mc.getBlock(x, y, z)  # block ID
print(this_block)
```

This tells you the location of the block you're standing *in* (this will be `0` - an air block). We want to know what type of block we're standing *on*. For this we subtract 1 from the `y` value and use `getBlock()` to determine what type of block we're standing on:

```python
x, y, z = mc.player.getpos()  # player position (x, y, z)
block_beneath = mc.getBlock(x, y-1, z)  # block ID
print(block_beneath)
```

This tells us the ID of the block the player is standing on.

Test this out by running a loop to print the block ID of whatever you're currently standing on:

```python
while True:
    x, y, z = mc.player.getPos()
    block_beneath = mc.getBlock(x, y-1, z)
    print(block_beneath)
```

![](images/mcpi-block-test.png)

We can use an `if` statement to choose whether or not we plant a flower:

```python
grass = 2
flower = 38

while True:
    x, y, z = mc.player.getPos()  # player position (x, y, z)
    block_beneath = mc.getBlock(x, y-1, z)  # block ID

    if block_beneath == grass:
        mc.setBlock(x, y, z, flower)
    sleep(0.1)
```

Perhaps next we could turn the tile we're standing on into grass if it isn't grass already:

```python
if block_beneath == grass:
    mc.setBlock(x, y, z, flower)
else:
    mc.setBlock(x, y-1, z, grass)
```

Now we can walk forward and if we walk on grass, we'll leave a flower behind. If the next block is not grass, it turns into grass. When we turn around and walk back, we now leave a flower behind us.

![](images/mcpi-flowers-grass.png)

## Playing with TNT blocks

Another interesting block is TNT! To place a normal TNT block use:

```python
tnt = 46
mc.setBlock(x, y, z, tnt)
```

![](images/mcpi-tnt.png)

However, this TNT block is fairly boring. Try applying `data` as `1`:

```python
tnt = 46
mc.setBlock(x, y, z, tnt, 1)
```

Now use your sword and left click the TNT block: it will be activated and will explode in a matter of seconds!

Now try making a big cube of TNT blocks!

```python
tnt = 46
mc.setBlocks(x+1, y+1, z+1, x+11, y+11, z+11, tnt, 1)
```

![](images/mcpi-tnt-blocks.png)

Now you'll see a big cube full of TNT blocks. Go and activate one of the blocks and then run away to watch the show! It'll be really slow to render the graphics as so many things are changing at once.

![](images/mcpi-tnt-explode.png)

## Building castles
As much fun as blowing stuff up is , now is time to start building something! We could build a house, but that's a bit boring. What if we build an entire castle every time you place a wool block?

Start by opening up a new window (`File > New window` and `File > Save`) and import the minecraft library
```python
from mcpi.minecraft import Minecraft

mc = Minecraft.create()
```
Because we want to build a castle EVERY time we place a wool block, we need to use a function. Functions allow you to seperate up your code into smaller chunks and make it reusable. Most functions look like a bit like this:

>def functionName(variable1,variable2):
    doSomethingWith(Variable2)

We can "pass" the function a variable each time we use it. To build the castle we will give our functions the players position so it can always know where they are.Functions are all about splitting code up, so we will start with creating a function for the towers.

```python
def towers(x,y,z,block):
```
The x,y,z variables are the player's position and the block is which block the castle will be made of -this means that later we can make the castle out of any block (even lava!). next we need to write the code to make the towers closest to us.
```python
def towers(x,y,z,block):
    mc.setBlocks(x+5, y-1, z+10, x+9, y+8, z+14, block)
    mc.setBlocks(x+6, y-1, z+11, x+8, y+8, z+13, 0)

    mc.setBlocks(x-5, y-1, z+10, x-9, y+8, z+14, block)
    mc.setBlocks(x-6, y-1, z+11, x-8, y+8, z+13, 0)
```
Each tower is made by making one large cuboid of stone, and then making a smaller cuboid of air inside. The Last two towers are very similar, instead this time we need to move them further away from the player by increasing their z coordinates.
```python
    mc.setBlocks(x+5, y-1, z+24, x+9, y+8, z+28, block)
    mc.setBlocks(x+6, y-1, z+25, x+8, y+8, z+27, 0)


    mc.setBlocks(x-5, y-1, z+24, x-9, y+8, z+28, block)
    mc.setBlocks(x-6, y-1, z+25, x-8, y+8, z+27, 0)
```
Now we have the towers set up, lets give ourselves some protection,and build some walls which are even easier to do.
```python
def walls(x,y,z,block):
  mc.setBlocks(x+4, y-1, z+12, x-4, y+4, z+12, block)

  mc.setBlocks(x+7, y-1, z+15, x+7, y+4, z+23, block)

  mc.setBlocks(x-7, y-1, z+15, x-7, y+4, z+23, block)

  mc.setBlocks(x+4, y-1, z+26, x-4, y+4, z+26, block)

```
Almost there! now we need to add the most important features of the castle: a floor to stand on and a door so we can actually get into the castle.
```python
def door(x,y,z):
    mc.setBlocks(x+1, y-1, z+15, x-1, y+2, z+12, 0)
def floor(x,y,z,block):
    mc.setBlocks(x-9, y-1, z+10, x+9, y-1, z+28, block)
```
Thats all the setup finished. We now need to write a way for the computer to know when and where to build our castle. We are going to use the player's position and what block they are standing on for this. First we need to use a while loop so the computer is constantly checking where the player is and what block they are standing on.
```python
while True:
    x, y, z = mc.player.getPos()
    blockBelow=mc.getBlock(x, y-1, z)

```
For this program we are going to check using an IF statement if the user is standing on orange wool (which has an ID of 35) and if they are we call our functions from earlier and change the block we are standing on to something other than orange wool (or it will keep trying to build a castle untill the player moves).
```python
  if blockBelow == 35:
          clearSpace(x,y,z)
          towers(x,y,z,stone)
          walls(x,y,z,stone)
          floor(x,y,z,stone)
          door(x,y,z)
          mc.setBlock(x, y-1, z,stone)
```
You're done! Save and run the program and change to minecraft. An an orange wool block and on the floor and then step on it. BOOM! Instant castle.
What if we could link real life with minecraft? What if we could press a button and have the castle built for us? OR press another button and build another castle made out of LAVA? Let's find out!   

## Building castles with the Explorer HAT
The explorer hat comes with four capacitive touch buttons which work the same way a smartphone's screen does. We are going to assign each button a block, so that every time we press the button a castle made of that block will be built. First go to where we put:
```python
 from mcpi import minecraft
 ```  
and add:
```python
import explorerhat as eh
pressed=0
```
This allows us to use the explorer hat's library, and declares a variable for if a button is being pressed. Next go to our while loop, and add before it a new function:

```python
def buttonPress(channel, event):
    block1=1
    block2=3
    block3=5
    block4=20
    global block
    global pressed
    if channel > 4
        return
    if event =='press':
        pressed=1
        if channel=1:
            block=block1           
        if channel=2:
            block=block2   
        if channel=3:
            block=block3
        if channel=4:
            block=block4
```
This function changes the block type that our castle will be made of, depending on which button has been pressed. The 'channel' variable is the button, and the event is whether it has been pressed or not. The block variables are what block the house will be made from. You can change them to be anything else, but for now we'll leave them as 1, 3, 5 and 20 which will build the castle out of stone, dirt, wood planks or glass.
The last thing we need to do is change the While loop so it is checking for a button press. First find our
```python
  blockBelow=mc.getBlock(x, y-1, z)
```
and change it to.
```python
    eh.touch.pressed(buttonPress)
```
This tells our function from earlier if a button has been pressed, and which one. Next find our IF statement and change it so it reads:
```python
  if pressed== 1:
```
Now our IF statement checks if a button has been pressed,and it has runs our castle building code. the last thing we need to do is set our pressed variable to 0 otherwise the program will continue to build castles forever. Because we don't need to change the block under us any more find
```python
    mc.setBlock(x, y-1, z,stone)
```
and change it to
```python
  pressed=0
```
And we're done! Jump back into minecraft and every time you press one of the explorer HAT's buttons, a castle will appear! 
