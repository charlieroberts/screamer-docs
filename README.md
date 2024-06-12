# Screamer

## Configuration

### background
Assign a RGB list of floats representing a color for the rendering background, e.g. `(1,0,0)` for red or `(0,0,.5)` for dark blue. This configuration cannot be animated over time (although it's on the list!).
<!-- tabs:start -->

#### **red**
```clike
background = (1,0,0)
sphere
```
#### **black**
```clike
background = (0,0,0)
sphere
```

<!-- tabs:end -->
### camera
 The xyz coordinates of the camera. Example: `camera = (0,0,3)`. Geometries are placed at the `0,0,0` coordinate by default, so you usally want your camera to be a bit back on the z-axis to properly view them (a z value of 5 is the default. This configuation *can* be animated. 

<!-- tabs:start -->

#### **close**
```clike
camera = (0,0,2)
sphere
```
#### **far**
```clike
camera = (0,0,10)
sphere
```
#### **animated**
```clike
camera = (0,0,time*-1)
sphere # 4
```
<!-- tabs:end -->

### fog
 A list containing four floats. The first is the amount of fog, while the last three present the RGB color of the fog. It often makes sense for the fog to be the same color as the `background`. Example: `fog = (.5, 0,0,0)`. This configuration *can* be animated over time, for example, to drive the fog with audio you could use: `fog = (high*2, .5*sin(low)*.5, 0, med )`. 

<!-- tabs:start -->
#### **black**
```clike
fog = (.25,0,0,0)
box # 4
```

#### **red**
```clike
background = (1,0,0)
fog = (sinn(time)*.5, 1, 0, 0)
sphere # 3
```

<!-- tabs:end -->

### fft
 This determines the size of the FFT window for audio analysis, which *must* be a power of two (e.g. 512, 1024, 2048, 4096 etc.) The default is 512. For smoother audio windows, consider using 4096 or 8192. This configuration cannot be animated. Example: `fft = 8192`

<!-- tabs:start -->
#### **slow**
```clike
fft = 8192
sphere( high )
```

#### **fast**
```clike
fft = 512
sphere( high )
```

<!-- tabs:end -->


### post
 A list of post-processing effects to apply. Available effects include `antialias`, `focus`, `edge`, `invert`, `bloom`, `godrays`, and `blur`. These effects cannot (currently) be animated over time.

<!-- tabs:start -->
#### **edgy**
```clike
post = ( antialias(2), edge, invert )
box'.25 # 1
```

#### **blurry**
```clike
post = ( blur(5,5) )
sphere
```

<!-- tabs:end -->

### render 
Assign a rendering preset to be used, the default is `med`. Options include `low`, `med`, `high`, `fractal.low`, `fractal.med`, `fractal.high`, `repeat.low`, `repeat.med`, `repeat.high`, `voxel.low`, `voxel.med`, and `voxel.high`. The `fractal` presets are best for viewing fractals close to the camera, white the `repeat` presets are best for geometries that are endlessly repeated over space. The `voxel` presets convert geometries to voxelized representations (think Minecraft). This configuration cannot be animated over time.

<!-- tabs:start -->
#### **bad (but efficient)**
```clike
render = low
box'.25 # 1
```
#### **better**
```clike
render = repeat.low
box '.25 # 1
```
#### **best**
```clike
render = repeat.high
box '.25 # 1
```
#### **voxels**
```clike
render = voxel.med
sphere # 3
```
<!-- tabs:end -->

## Geometries
Most of the geometries available in screamer are very simple; the fun comes in repeating, combining, and warping them in different ways. However, there is a also a selection of fractals that can create more complex scenes with very little code.

### sphere
A sphere that accepts a radius argument.
<!-- tabs:start -->
#### **little**
```clike
sphere(.1)
```

#### **big**
```clike
sphere(2)
```

<!-- tabs:end -->
### box
 A box that accepts a size argument, which can be either a single number (creates a cube) or a three-item list (different sizes for x,y, and z dimensions). Examples: `box(.25)` or `box( (1,.5,.1) )`
<!-- tabs:start -->
#### **cube**
```clike
box(1.5) @y time*20
```

#### **not cube**
```clike
box(( 1.5 .5 .1 )) @y time*20
```

<!-- tabs:end -->


### octahedron
Accepts a single radius argument. This is a fun shape for mirroring and repeating.
<!-- tabs:start -->
#### **big**
```clike
octahedron(1.5) @ time*20
```

#### **small**
```clike
octahedron(.5) @ time*20
```

<!-- tabs:end -->


### julia
A julia fractal that accepts a fold amount.
<!-- tabs:start -->
#### **folding**
```clike
post = ( antialias )
render = fractal.high
julia(time)'2
```

#### **stripey**
```clike
render = fractal.med
fog = (.125,0,0,0)
julia( 5+sin(time/3) ) '2 : red :: stripes
```

<!-- tabs:end -->

### mandelbox
This fractal is great for making weird futuristic worlds to explore. It accepts *fold*, *size*, and *iterations* arguments. The fold determines the amount of spherical fodling in the fractal, while size controls the scaling in the equation. Iterations controls the number of times the folding is run (default 5).  
<!-- tabs:start -->
#### **spacey**
```clike
render = fractal.med
camera = (1,0,time*-.25)
fog = (.1 0 0 0)
mandelbox(.25, 3, 6) # 4
```

#### **foldy**
```clike
render = fractal.high
camera = (0,0,4)
fog = (.1 0 0 0)
mandelbox(sinn(time), 3.5+sinn(time/3)*2, 8)
```

<!-- tabs:end -->

### mandelbulb
A mandelbulb fractal accepting a fold amount that can look strangely organic. Higher fold amounts yield more recursion and greater complexity. 

<!-- tabs:start -->
#### **folding**
```clike
post = ( antialias )
render = fractal.high
mandelbulb(time%10)'2
```

#### **mirror**
```clike
render = fractal.med
fog = (.125,0,0,0)
camera = (0 0 3)
(mandelbulb(3+sinn(time/3)*4) >.25 |xyz) @y time*10
```

<!-- tabs:end -->


### mandalay
A mandalay fractal with three arguments: size, minimum radius, and iterations. Higher iteration counts are more complex but also cost more computationally. Lower iteration counts can create satisfying, blocky shapes that are much easier on your graphics card. 

<!-- tabs:start -->
#### **folding**
```clike
render = fractal.high
camera = (0 0 6)
mandalay( 3+sinn(time/2)*3, .3 + sinn(time/3) * .2, 2 )
```

#### **use your mouse**
```clike
render = fractal.high
camera = (0 0 6)
fog = (.1,0,0,0)
(sphere(3) ** mandalay(3+mousex*3, mousey, 2)) @y time*5 
```

<!-- tabs:end -->


### cone
The tip of the cone appears at the origin (0,0,0), which might be a bit non-intuitive. It accepts three arguemnts, the last one is the height and the first two determine the radius of the base. 
<!-- tabs:start -->
#### **rotating**
```clike
post = (antialias)
cone((.25,.25,1.5)) @ time*10
```

<!-- tabs:end -->

### cylinder
 A cylinder accepts a size argument containing a radius and a height. For example, to create a long narrow cylinder: `cylinder((.1,2))`
<!-- tabs:start -->
#### **long**
```clike
cylinder((.1,2))
```

#### **ring of candy canes**
```clike
render = high
fog = (.35 .25 0 0)
background = (.125 0 0)
ring = (cylinder((.125,3)) ###(20 1))
ring:red::stripes(5 (0 time/6 0))
ring @x 90 @y time*20
```

<!-- tabs:end -->

### capsule
A capsule takes two XYZ positions for its start and end, and a radius.

<!-- tabs:start -->
#### **simple**
```clike
capsule((-1 0 0) (1 0 0) .25) @y time*60
```

<!-- tabs:end -->


### torus
A ring, where you can specify the radius and the thickness. By default the torus lies flat (it is rendered along the z-axis) so you need to rotate on the x-axis to see the full ring.

<!-- tabs:start -->
#### **just so**
```clike
torus((2,.125)) @x 90
```

#### **trinket**
```clike
render = high
((torus > .75 ) ||xz.125):red @x 90 @z time*30
```

<!-- tabs:end -->

## Combinators
Combinators are operators that are used to combine geometries (or multiple combinators). 

### `++` (union)
Adds two geometries together with no transition so that they can be transformed together 
<!-- tabs:start -->
#### **simple**
```clike
box ++ sphere(1.2)::dots
```

#### **transformed**
```clike
// note the scale, rotate, and material (red) operators
// are applied to both shapes... dots is not
(box ++ sphere(1.2)::dots) :red '1.5 @y time*30
```
<!-- tabs:end -->

### `+++` (round union)
Adds two geometries and creates a smooth transition between them, with an argument smoothing coefficient.

<!-- tabs:start -->
#### **simple**
```clike
(box +++ sphere(1.2)::dots) @y time*30
```

#### **kenny g smooth**
```clike
(box +++(.75) sphere(1.2)::dots) @y time*30
```
<!-- tabs:end -->

### `++++` (stairs union) 
Adds two geometries and creates a stepped transition, with argument transition size and number of steps. 
<!-- tabs:start -->
#### **simple**
```clike
box ++++(.35,6) sphere(1.2)
```

#### **lotsa steps**
```clike
box ++++(.5 10) sphere
```
<!-- tabs:end -->

### `--` (difference)
 Subtracts two geometries. Example: `box -- sphere(1.2)`
### `---` (round difference)
 Subtracts two geometries and creates a smooth transition between them, with an argument smoothing coefficient. Example:`box ---(.75) sphere(1.2)`.
### `----` (stairs difference)
 Subtracts two geometries and creates a stepped transition, with argument transition size and number of steps. Example:`box ----(.35,6) sphere(1.2)`
### `**` (intersection)
 Creates the intersection of two geometries. Example: `box *** sphere(1.2)`
### `***` (round intersection)
Intersects two geometries and creates a smooth transition between them, with an argument smoothing coefficient. Example:`box ***(.75) sphere(1.2)`.
### `****` (stairs intersection)
 Intersects two geometries and creates a stepped transition, with argument transition size and number of steps. Example:`box ****(.35,6) sphere(1.2)`

## Modifiers
Modifiers are (mostly) single-character operators to modify the geometry, combinator, or modifier to their left. The `@`Rotate, `>`Translate, `#`Repeat, `|`Mirror, and `||`SmoothMirror operators can be used with `xyz` *decorators* to specify which dimensions the operator will be applied to. For example, `box >x .5` will only translate on the x axis, while `sphere #yz 2` will repeat on the y and z axes. If no decorations are applied, the operation will be applied on all axes by default. 

### `'` (scale) 
A uniform scaling coefficient. For mathy reasons this can only be applied to all dimensions simultaneously. For pure geometries it is usually better to use their built-in controls for scaling, as the scale operator incurs some additional computational cost. 

<!-- tabs:start -->
#### **simple**
```clike
julia(time) ' 2
```

#### **animated**
```clike
sphere '1 + round( time*2 ) % 3
```
<!-- tabs:end -->

### `@` (rotate)
Rotate on all three axes by an argument amount, or use xyz decorators to specify axes.
<!-- tabs:start -->
#### **all axes**
```clike
box @ time*15
```

#### **one axis**
```clike
box @y time*15
```
<!-- tabs:end -->

### `@@` (rotate axis/angle)
Rotate an argument number of degrees around an argument axis. This operator enables you to change the axis of rotation over time. 
<!-- tabs:start -->
#### **one axis**
```clike
box @@( time*15, 1,0,0 )
```

#### **shifting axes**
```clike
box @@( time*15, sin(time), 0, cos(time) )
```
<!-- tabs:end -->

### `>` (translate)
Move along three axes. 
<!-- tabs:start -->
#### **one axis**
```clike
box'.15 >x 1.5
```

#### **mirror fun**
```clike
(box'.15 >sinn(time*4) |) > 1 |
```
<!-- tabs:end -->

### `#` (repeat) 
Repeat on all three axes. 
<!-- tabs:start -->
#### **simple**
```clike
render = repeat.med
fog = (.1 0 0 0)
sphere(.25) # 1
```

#### **two axes**
```clike
box(.1) #xy .25
```

### **subtracting repetition**
```clike
(sphere(2) ---- (box(.05) # .225)) @y time*10
```
<!-- tabs:end -->

Example: `sphere # 3`

### `##` (smooth repeat)
TODO needs fixing. SmoothRepeat. Repeat on all three axes, and smooth transitions. Example: `sphere ## 3 .25`

### `###` (polar repeat)
 Repeat in a circle *x* times with *y* separation.
<!-- tabs:start -->
#### **wheel**
```clike
render = med
camera = (0 0 3)
(box(.2) ###(12,.8)) @x 90 @y time*10
```
<!-- tabs:end -->

### `|`  (mirror)
Mirrors the geometry. Note that with symmetric geometries this will have no effect; you must rotate or translate such geometries to create assymetry before applying the mirror operator.
<!-- tabs:start -->
### **simple but fun**
```clike
camera = (0 0 2.5)
render = fractal.med
julia(time) |
```

### **what?**
```clike
// because boxes are symmetrical this mirror
// will have no effect
box(.25) |x
```

### **oh**
```clike
// if we move our box first now it is not 
// symmetrical along the origin (0 0 0) and
// we can see the mirroring
box( .25 ) >x .5 |
```
### **three axes**
```clike
post = ( antialias )
(box(.25) > .5 |) @time*20
```
<!-- tabs:end -->


### `||` (smooth mirror)
 Mirrors the geometry, but smooths the transition at the mirror axes by an argument smoothness amount.  Note that smoothness amounts that are high in comparison to the size of the geometries being mirrored can cause geometries to disappear. Currently you can only specify a single smoothness amount to use for all three axes.

<!-- tabs:start -->
### **simple but smooth**
```clike
// compare this to the previous julia
// example for "regular" mirror
camera = (0 0 2.5)
render = fractal.med
julia(time) || .05
```

<!-- tabs:end -->

### `~` (twist)
Twists a geometry on all three axes by a single argument amount. WARNING: this operator is a bit buggy at the moment. It works best if you place it directly after a shape / combinator, before any other modifiers.

<!-- tabs:start -->
### **plot twist**
```clike
box:red ~time%3
```

<!-- tabs:end -->

### `:` (color) 
Apply a color preset. Colors include `red`, `green`, `blue`, `cyan`, `magenta`, `yellow`, `white`, `black`, `grey`.

### `::` (texture) 
Apply texture preset. Textures include `rainbow`, `stripes`, `dots`, `truchet`, `noise`, `cellular`, `zigzag`, and `voronoi`. Example: `box::truchet`. All textures also have two optional parameters: *scale*, which determines the scaling of procedural textures, and *uv* which is a three-item list that specifies offsets to look up texture values. Example: `box::rainbow( 10, (sin(time),0,.5))`


## Variables

### time 
The time since the environment was loaded, measured in seconds.
<!-- tabs:start -->
### **pulse**
```clike
sphere(1 + sin(time*4) * .25)
```
<!-- tabs:end -->

### mousex
The x position of the mouse mapped from 0--1. 
The time since the environment was loaded, measured in seconds.
<!-- tabs:start -->
### **mouse me**
```clike
sphere( mousex )
```
<!-- tabs:end -->

### mousey
The y position of the mouse mapped from 0--1. 
<!-- tabs:start -->
### **mouse me**
```clike
sphere # mousey * 6
```
<!-- tabs:end -->

### low
Low frequency analysis of audio input mapped to 0--1. Example: `sphere(low)`
### mid
Mid frequency analysis of audio input mapped to 0--1. Example: `box(mid)`
### high
High frequency analysis of audio input mapped to 0--1. Example: `box@(high*90, low, 0, mid)`
### i 
A special variable only used in loops that equals the current loop iteration number, starting at 0. See [the section on loop](#loop) for more information.

## Math
Note: traditional parenthesis `()` do not currently work in math expressions, as they're currently used by the language to group combinators, geometries, and modifiers together... this is a todo to fix.

- `+` Addition. Adds two numbers/variables together.
- `-` Subtraction. Subtracts two numbers/variables together.
- `*` Multiply. Multiplies two numbers/variables together.
- `/` Divide. Divide two numbers / variables.
- `%` Modules. Calculates the remainder of dividing two numbers.
- `^` Exponent. Raises the left value to the right power.
- *sin*: Calculate the sin of the arugment. Example: `sphere( .5 + sin(time) * .35 )`
- *cos*: Calculate the cosine of the arguments. Example: `box | 3 + sin(time)`
- *random*: Generates a random number from 0--1. Example: `sphere( .5 + random() * .5)`
- *round*: Rounds a number.
- *floor*: Rounds down a number.
- *ceil*: Rounds up a number.
- *abs*: Gets the absolute value for a number.

## Misc
### Loop
This lets you apply a chain of modifications to a geometry multiple times, which is useful to quickly create fractal-like shapes when used with mirroring / repeating. You can use the special `i` variable in math expressions to represent the current loop iteration number. It takes the form `[geometry numberOfIterations modifiers]`

<!-- tabs:start -->

#### **fold me tight**
```clike
[octahedron(.125) 8 >(.25,.1,.05) @@(45,cos(i+time/3),0,1) | ]
```

#### **rubik's dream**
```clike
render = fractal.high
[octahedron(.1) 4 >.1+i*.125 | @sin(time/8)*20*i^i ||(.0002)] @time*30
```

<!-- tabs:end -->

