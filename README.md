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

### lighting
OK, this one is a bit confusing. Screamer has a default global lighting scheme that affects every object in the scene equally (you can use fog to decrease the lighting over distance). However, you can also use custom colors with the `:color(r,g,b)` operator with the `lighting` config to use a different lighting scheme (phong shading).

The parameters for the `light` function are:
- *list* xyz position
- *list* rgb color
- *float* attenuation, this controls how the light fades over distance 

<!-- tabs:start -->
#### **left red right blue**
```clike
lighting = ( light( (-2,0,0) (1 0 0) ), light( (2,0,0) (0 0 1) ) )
box:color( 1 1 1 )@time*20 ++ plane:color( 1 1 1)
```

#### **repeat no fog**
```clike
// note the different attentuations for left/right
lighting = ( light( (-2,0,0) (1 0 0) .1 ), light( (2,0,0) (1 1 1) 1.5 ) )
sphere(.1):color(0 1 0) # .5
```
<!-- tabs:end -->



### post
 A list of post-processing effects to apply. Available effects include `antialias`, `focus`, `edge`, `invert`, `bloom`, `godrays`, 'hue', 'brightness', 'contrast', 'glow', and `blur`. Some parameters of these effects can be animated over time.

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

### voxel 
Defaults to .1. When a voxel [render preset](#render) (`voxel.low`, `voxel.med`, or `voxel.high`) is used, this value determines the size of the voxels. The algorithm used to voxelize the geometries gets more expensive as this value decreases, so be careful not to choose values that are super small, for example, a value of .001 might grind your graphics card to a halt.

<!-- tabs:start -->
#### **big blocks**
```clike
render = voxel.low
voxel = .2
sphere::rainbow
```

#### **little blocks**
```clike
render = voxel.low
voxel = .01
fog = .5
sphere(.1)::rainbow #.4 
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
Apply a color preset or custom color. Colors include `red`, `green`, `blue`, `cyan`, `magenta`, `yellow`, `white`, `black`, `grey`. To create a custom color, use `:color(r,g,b)` where the rgb values are typically between 0-1 (you can go higher if you want). When custom colors are used, the object will be lit using a different lighting scheme than the regular global lighting.

<!-- tabs:start -->
### **red global**
```clike
box:red @time*20
```
### **red custom**
```clike
lighting = ( light( (2 0 5) ( 0 0 1) .1 ) light( ( -2, 0, -2) (1 1 1) .1 ) )
box:color(1 0 0) @time*20
```

<!-- tabs:end -->


### `::` (texture) 
Apply texture preset. Textures include `rainbow`, `stripes`, `dots`, `truchet`, `noise`, `cellular`, `zigzag`, `voronoi`, and `hydra`. Example: `box::truchet`. All textures also have two optional parameters: *scale*, which determines the scaling of procedural textures, and *uv offset* which is a three-item list that specifies offsets to look up texture values. Example: `box::rainbow( 10, (sin(time),0,.5))`

### `:::` (bump)
Apply a texture preset and also use it for bump mapping (more properly known as displacement mapping in this context). The same textures used with `::` can be applied here, however, the first argument for bump is the *amount* of displacement, while the second is *scale* and the third is *uv offset.* This operator can easily glitch out when high amounts are used, however, this is also somewhat dependent on the texture preset that is applied. Typically amount values lower than < .1 should be safe, but higher values can be used with certain presets and scalings.

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

### fade
A variable that linearly fades from one value to another over time. `fade` accepts three arguments: the number of frames of video the fade should occur over, a starting value for the fade, and a target ending value. All three arguments expect static (unchanging) numeric arugments.

<!-- tabs:start -->
### **fade in**
```clike
sphere( fade( 300, 0, 2 ) )
```

### **focusing**
```clike
post = ( blur( fade( 300, 50, 0 ) ) )
sphere( 1.5 )
```
<!-- tabs:end -->

## Math

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

### Hydra
This lets you define a texture using the [hydra live coding language](https://hydra.ojack.xyz). This texture can be live coded and changed without having to recompile the main shaders used by screamer; just re-run the hydra block of code and the texture will be updated automatically. You can use whitespace between the opening and closing hydra backticks however you like. All the code between the backticks is JavaScript.
 
<!-- tabs:start -->

#### **better open**
```clike
hydra`
  osc(15,.1,5).modulate( noise(50) ).out()
`

sphere(1.5)::hydra
```
<!-- tabs:end -->

## Post-Processing FX
Post-processing fx are (almost) always applied to the entire rendered scene. They can quickly and easily provide a lot of variation and visual interest. The effects are created using the [merge-pass](https://github.com/bandaloo/merge-pass) JavaScript library. These fx are added to the scene using the [`post`](#post) config.

### bloom
Brighten and blur pixels above a given threshold value. This effect is typically used to mimic the appearance of light reflecting directly into a camera, but can also be used for other artistic purposes. The input arguments are *brightness threshold*, *amount*, *horizontal blur*, and *vertical blur*.

<!-- tabs:start -->
#### **so shiny**

```clike
post = ( bloom( .5 1.3 3 3 ) )
mandelbox:white'.35 @y time*20
```
<!-- tabs:end -->

### blur
Blur the scene by an argument number of pixels.

<!-- tabs:start -->
#### **move left, move right**

```clike
post = ( blur( 50 * mousex ) )
sphere
```
<!-- tabs:end -->

### brightness
Brighten the scene by increasing the RGB values of every pixel.

<!-- tabs:start -->
#### **rainbow brite**

```clike
post = ( brightness( mousex ) )
box::rainbow(10) '1.25
```
<!-- tabs:end -->

### contrast
Adjust the contrast of the scene..

<!-- tabs:start -->
#### **move left, move right**

```clike
post = ( contrast( mousex * 3 ) )
mandelbox '.35
```
<!-- tabs:end -->

### edge 
Apply a sobel filter that highlights the edges in a 3D scene.

<!-- tabs:start -->
#### **edgy**

```clike
post = ( edge invert(1) )
box @yx time*20
```
<!-- tabs:end -->

### focus 
A depth-of-field filter that blurs objects that are not currently focused. It accepts two arguments: *depth* determines where on the z-axis from the camera the focus is, and *radius* determines how much depth is in focus..

<!-- tabs:start -->
#### **blurry boxes**

```clike
render = repeat.med
post = ( focus( sinn( time )*.5, .005 ) )
fog = .1
box'.25 #1
```
<!-- tabs:end -->

### glow 
Make the scene blurry and glowy.

<!-- tabs:start -->
#### **holy julia**

```clike
render = repeat.med
post = ( focus( sinn( time )*.5, .005 ) )
fog = .1
box'.25 #1
```
<!-- tabs:end -->

### godrays 
Volumetric lighting. Arguments:
- *decay*: How much the light decays over space. Slightly positive values (e.g. 1.01) create feedback that can create stronger beams. 
- *weight*: don't know how to describe this one.
- *density*: controls how far the beams extend.
- *threshold*: controls the starting point of the beams on the z-axis

<!-- tabs:start -->
#### **slits**

```clike
post = ( godrays(.975 .05 1 1 ) )
b = box((4 4 .05)) -- (box((.15 .5 .1)):white #x .8+sin(time)*.3)
b -- sphere(.5)
```

#### **neato**
```clike
post = ( godrays(.99 .0125 1 .75 ) )
fog = (.2 .125 0 .125)
background = (.125 0 .125)
render = fractal.med
 
s = sphere(2):purple 
s = s ** mandelbox(.3 + cos(time/2) * .1, 2.4+sin(time)*.15 ):red
s @time*20
```
<!-- tabs:end -->

### hue 
Shift the color spectrum of the scene. Arguments are: *shift* the amount to shift, from 0-1, and *threshold*, which determines where in the depth field the effect takes place. A value of `0` in threshold means that nothing will be affected, while a value of `.99` means that everything *except* the background will be affected. A value of `1` affects the background as well. Negative values may need to be used when using repeated field or other elements close to the camera.

<!-- tabs:start -->
#### **shifty rainbows**

```clike
post = ( hue( sin(time/3) sin(time) * .75 ) )
render = repeat.low
box::rainbow'.25 # 1
```
<!-- tabs:end -->

### invert 
Invert the color spectrum of the scene. The one argument is *threshold*, which determines where in the depth field the effect takes place. A value of `0` in threshold means that nothing will be affected, while a value of `.99` means that everything *except* the background will be affected. A value of `1` affects the background as well. Negative values may need to be used when using repeated field or other elements close to the camera. Tying the threshold to audio can create dramatic effects.

<!-- tabs:start -->
#### **audio flips**

```clike
post = ( invert( -.75 - (low *-.85) ) focus )
render = repeat.med
(box::rainbow'.25 # 1) @z time*5
```
<!-- tabs:end -->

### motionblur 
Create a blurred trail for moving objects. The effect accepts one argument, *amount*, which determines the strength of the blur and defaults to `.7`.
<!-- tabs:start -->
#### **harmonograph**

```clike
post = ( motionblur(.8) )
render = high
s = sphere(.5):white::dots(10) 
s >x sin(time*4)*2 
s >y cos(time*2)*2
```
<!-- tabs:end -->
