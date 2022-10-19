Name: Logan Cho
Late Day: Used

# Procedural Murakami Flower Vine (Logan Cho)


<img src="myImages/finalImageFlat.png" width="1000">
<img src="myImages/finalImage2.png" width="1000">

# Description:

For this project, I wanted to try recreate Murakami's Flowers through procedural generation and an L-System. The project can be split into two parts, the first being my HDA Procedural Murakami Flower Generator, and the second being the LSystem itself. I learnt a ton through this project, both about Houdini in general as this is my first time ever using it, and also about tool development workflows. A really helpful resource for me throughout the project was Houdini Kitchen's article, https://www.houdinikitchen.net/2019/12/21/how-to-create-l-systems/, which had examples and definitions of the different utilities that Houdini's L-System primitive provides. 

### Reference:

<img src="reference.jpg" width="800">

# Project Overview:

### OBJ Level:

<img src="myImages/Process Screenshots/screenshotOBJ.png" width="1000">

### LSystemMurakami HDA:

Within my 'Flower' geom, I have an L-System Murakami Digital Asset Subnet as follows:

<img src="myImages/Process Screenshots/screenshotLSystemHDA.png" width="1000">

The LSystemHDA takes in # of generations as a parameter, and this would be useful if I ever wanted to spawn many of my vines at varying levels of generations!

#### (Aside)

I wanted to experiment with this idea more, but ran out of time to implement anything further. Will definitely do this in the future when I get the time.

<img src="myImages/screenshotForFunHoudini.png" width="1000">

#### (Back to the LSystemHDA Node)

<img src="myImages/Process Screenshots/screenshotLSystemHDAInside.png" width="1000">

The Lsystem node takes in two different leaves, the first being the flowers, and the second being a simple model of a leaf I modelled in Maya and imported as an FBX into the project (as observable in the OBJ Level screenshot.) 

### Murakami HDA:

<img src="myImages/Process Screenshots/screenshotMurakamiHDA.png" width="1000">

The Murakami HDA has two parameters, number of petals, and a float "id" received from the LSystem that is in the range of 0-1. Both are used in the generation process of a flower. I wrote stamp functions for both in order for the LSystem to be able to pass information upstream to the flowers from the rules!

# Procedural Murakami Flower Generator (The Murakami HDA Node): 

https://user-images.githubusercontent.com/72320867/196815779-042a96d9-5559-48f2-9dd6-bb5ff4aaf1d1.mov

Before working on my L-System, I needed to first figure out how I was going to be adding the flowers. One method would be for me to manually create an array of flowers with different features, and then select between when feeding them into the L-System. The more interesting approach that I wanted to take was to create an actual procedural generator which I could use to generate unique flowers on demand within the L-System via stamping parameters up-stream. 

### Process Explanation:

<img src="myImages/processScreenshots.jpg" width="1500">

As you can see, I replicate the petal shape of Murakami's Flowers through two main steps. 

1. Firstly, I modelled a petal through deforming a cube, and then used the subdivision node to make the shape smoother. I also apply rotation to the petal based on the node's parameters (id and petal_number), which controls how much the petals fold inwards/outwards in the final result. 

2. Secondly, I segment a primitve circle in order to obtain a regular polygon of points to which I can then copy the petals onto. In order to get the orientation correct and have the petals pointing inwards, I used the polyfram node to replace the tangents of the points with their normals!

Finally, through the copy-to-points node, I am able to get the final petal structure, which is then merged with the face of the flower which I modelled separately in Maya. 

Throughout the node, I also have several wrangles for grouping points into shared characteristics such as a mouth group, left eye group, etc. This is important for when I randomly color the different elements of the flower based on the digital asset's inputs received from the L-System. The geometry spreadsheet was incredibly useful for the grouping portion of this process, as I was able to see the different attributes of points at different parts of the pipeline and quickly debug any issues I was having.

The petals specifically have an interesting color scheme. For instance, if the number of petals is even, it is possible for the petal to have an alternating 2-color scheme, on top of other options such as a cosine color scheme gradient, as well as a uniform color.

# L-System:

### Rules:
```
  - A=[C[G]]Z(t)B
  - C=^(30 + 11 * rand(i+t))F(0.07 + rand(i+t)*0.16, 0.06 + rand(i+t)*0.03)P^(10 + 11 * rand(i+t))F(0.05+ rand(i+t)*0.16,0.05 + rand(i+t)*0.03)P^(5 + 11 * rand(i+t))F(0.03+ rand(i+t)*0.16, 0.04 + rand(i+t)*0.02)D
  - G=J(0.4*(rand(i+t) * 0.6)*(x*x + z*z) + 0.08, 0, 8 + 15 * rand(i / x * y / z + t), rand(i + t))
  - B:rand(i)>0.5=/F(0.005 + 0.03 * rand(i+t))///F(0.05 + 0.03 * rand(i))OA
  - B:rand(i)<=0.5=\\(30 * rand(t))F(0.003 + 0.02 * rand(i+t))\F(0.05 + 0.03 * rand(i))OA
  - D=^(0 + 5*rand(i+t))F(0.01 + 0.03*rand(i+t), 0.015 + rand(i+t)*0.03)E:0.7
  - E=^(0 + 5*rand(i+t))F(0.01 + 0.03*rand(i+t), 0.01 + rand(i+t)*0.03):0.5
  - Z(h)=/(1.0 *h*rand(i+t))^(5*rand(i+t)):0.25
  - Z(h)=\(1.0 *h*rand(i+t))&(5*rand(i+t)):0.25
  - Z(h)=/(1.0 *h*rand(i+t))&(5*rand(i+t)):0.25
  - Z(h)=\(1.0 *h*rand(i+t))^(5*rand(i+t)):0.25
  - X=//G:0.5
  - O=[/(15 * rand(i+t))^(60)-(20 + 20 * rand(i-t))K(0.02 + 0.03 * rand(i+t))][/(15 * rand(i+t) +180)^(60)-(20 + 20 * rand(i-t))K(0.02 + 0.03 * rand(i+t))]
  - P=[/(15 * rand(i+t))^(60)-(20 + 20 * rand(i-t))K(0.01 + 0.02 * rand(i+t))][/(15* rand(i+t) +180)^(60)-(20 + 20 * rand(i-t))K(0.01 + 0.02 * rand(i+t))]
```

Through my L-System rules, I wanted to create a vine that had subtle rotations over generations, little leaves across its main stem and branches, and finally, many diverse Murakami flowers branching off of it. 

One notable feature of the scale of the flowers is that the grow larger the further away from the main stem they are.

The most important aspect of the rules is the use of the rand() function, in order ot add some natural variation to the length of branches, distance between consecutive flowers, pass unique inputs to the flower generator via stamping, and more.

<img src="myImages/lsystemDiagram.png" width="1300">

# Conclusion:

I really enjoyed this project. Being new to Houdini made everything extra challenging (this is a huge understatement lol), but now that I've spent all of this time working with it, I've come to see just how great of a tool it can be. I think the final result matches up pretty similarly with the goal I'd had from the beginning, and I think I'm a lot more confident now with actuating my ideas into projects! : )

# Miscellaneous:

### Cool screenshot that I liked

<img src="myImages/cool.png" width="600">

### Modeling the face in Maya:

<img src="myImages/screenshot3DModelFace.png" width="700">

## Using Late Day

# Homework 4: L-systems

For this assignment, you will design a set of formal grammar rules to create
a plant life using an L-system program. Once again, you will work from a
TypeScript / WebGL 2.0 base code like the one you used in homework 0. You will
implement your own set of classes to handle the L-system grammar expansion and
drawing. You will rasterize your L-system using faceted geometry. Feel free
to use ray marching to generate an interesting background, but trying to
raymarch an entire L-system will take too long to render!

## Base Code
The provided code is very similar to that of homework 1, with the same camera and GUI layout. Additionally, we have provided you with a `Mesh` class that, given a filepath, will construct VBOs describing the vertex positions, normals, colors, uvs, and indices for any `.obj` file. The provided code also uses instanced rendering to draw a single square 10,000 times at different locations and with different colors; refer to the Assignment Requirements section for more details on instanced rendering. Farther down this README, we have also provided some example code snippets for setting up hash map structures in TypeScript.

## Assignment Requirements
- __(15 points)__ Create a collection of classes to represent an L-system. You should have at least the following components to make your L-system functional:
  - A `Turtle` class to represent the current drawing state of your L-System. It should at least keep track of its current position, current orientation, and recursion depth (how many `[` characters have been found while drawing before `]`s)
  - A stack of `Turtle`s to represent your `Turtle` history. Push a copy of your current `Turtle` onto this when you reach a `[` while drawing, and pop the top `Turtle` from the stack and make it your current `Turtle` when you encounter a `]`. Note that in TypeScript, `push()` and `pop()` operations can be done on regular arrays.
  - An expandable string of characters to represent your grammar as you iterate on it.
  - An `ExpansionRule` class to represent the result of mapping a particular character to a new set of characters during the grammar expansion phase of the L-System. By making a class to represent the expansion, you can have a single character expand to multiple possible strings depending on some probability by querying a `Map<string, ExpansionRule>`.
  - A `DrawingRule` class to represent the result of mapping a character to an L-System drawing operation (possibly with multiple outcomes depending on a probability).

- __(10 points)__ Set up the code in `main.ts` and `ShaderProgram.ts` to pass a collection of transformation data to the GPU to draw your L-System geometric components using __instanced rendering__. We will be using instanced rendering to draw our L-Systems because it is much more efficient to pass a single transformation for each object to be drawn rather than an entire collection of vertices. The provided base code has examples of passing a set of `vec3`s to offset the position of each instanced object, and a set of `vec4`s to change the color of each object. You should at least alter the following via instanced rendering (note that these can be accomplished with a single `mat4`):
  - Position
  - Orientation
  - Scaling

- __(55 points)__ Your L-System scene must have the following attributes:
  - Your plant must grow in 3D (branches must not just exist in one plane)
  - Your plant must have flowers, leaves, or some other branch decoration in addition to basic branch geometry
  - Organic variation (i.e. noise or randomness in grammar expansion and/or drawing operations)
  - The background should be a colorful backdrop to complement your plant, incorporating some procedural elements.
  - A flavorful twist. Don't just make a basic variation of the example F[+FX]-FX from the slides! Create a plant that is unique to you. Make an alien tentacle monster plant if you want to! Play around with drawing operations; don't feel compelled to always make your branches straight lines. Curved forms can look quite visually appealing too.

- __(10 points)__ Using dat.GUI, make at least three aspects of your L-System interactive, such as:
  - The probability thresholds in your grammar expansions
  - The angle of rotation in various drawing aspects
  - The size or color or material of the plant components
  - Anything else in your L-System expansion or drawing you'd like to make modifiable; it doesn't have to be these particular elements

- __(10 points)__ Following the specifications listed
[here](https://github.com/pjcozzi/Articles/blob/master/CIS565/GitHubRepo/README.md),
create your own README.md, renaming the file you are presently reading to
INSTRUCTIONS.md. Don't worry about discussing runtime optimization for this
project. Make sure your README contains the following information:
    - Your name and PennKey
    - Citation of any external resources you found helpful when implementing this
    assignment.
    - A link to your live github.io demo (refer to the pinned Piazza post on
      how to make a live demo through github.io)
    - An explanation of the techniques you used to generate your L-System features.
    Please be as detailed as you can; not only will this help you explain your work
    to recruiters, but it helps us understand your project when we grade it!

## Writing classes and functions in TypeScript
Example of a basic Turtle class in TypeScript (Turtle.ts)
```
import {vec3} from 'gl-matrix';

export default class Turtle {
  constructor(pos: vec3, orient: vec3) {
    this.position = pos;
    this.orientation = orient;
  }

  moveForward() {
    add(this.position, this.position, this.orientation * 10.0);
  }
}
```
Example of a hash map in TypeScript:
```
let expansionRules : Map<string, string> = new Map();
expansionRules.set('A', 'AB');
expansionRules.set('B', 'A');

console.log(expansionRules.get('A')); // Will print out 'AB'
console.log(expansionRules.get('C')); // Will print out 'undefined'
```
Using functions as map values in TypeScript:
```
function moveForward() {...}
function rotateLeft() {...}
let drawRules : Map<string, any> = new Map();
drawRules.set('F', moveForward);
drawRules.set('+', rotateLeft);

let func = drawRules.get('F');
if(func) { // Check that the map contains a value for this key
  func();
}
```
Note that in the above case, the code assumes that all functions stored in the `drawRules` map take in no arguments. If you want to store a class's functions as values in a map, you'll have to refer to a specific instance of a class, e.g.
```
let myTurtle: Turtle = new Turtle();
let drawRules: Map<string, any> = new Map();
drawRules.set('F', myTurtle.moveForward.bind(myTurtle));
let func = drawRules.get('F');
if(func) { // Check that the map contains a value for this key
  func();
}
```
TypeScript's `bind` operation sets the `this` variable inside the bound function to refer to the object inside `bind`. This ensures that the `Turtle` in question is the one on which `moveForward` is invoked when `func()` is called with no object.

## Examples from previous years (Click to go to live demo)

Andrea Lin:

[![](andreaLin.png)](http://andrea-lin.com/Project3-LSystems/)

Ishan Ranade:

[![](ishanRanade.png)](https://ishanranade.github.io/homework-4-l-systems-IshanRanade/)

Joe Klinger:

[![](joeKlinger.png)](https://klingerj.github.io/Project3-LSystems/)

Linshen Xiao:

[![](linshenXiao.png)](https://githublsx.github.io/homework-4-l-systems-githublsx/)

## Useful Resources
- [The Algorithmic Beauty of Plants](http://algorithmicbotany.org/papers/abop/abop-ch1.pdf)
- [OpenGL Instanced Rendering (Learn OpenGL)](https://learnopengl.com/Advanced-OpenGL/Instancing)
- [OpenGL Instanced Rendering (OpenGL-Tutorial)](http://www.opengl-tutorial.org/intermediate-tutorials/billboards-particles/particles-instancing/)

## Extra Credit (Up to 20 points)
- For bonus points, add functionality to your L-system drawing that ensures geometry will never overlap. In other words, make your plant behave like a real-life plant so that its branches and other components don't compete for the same space. The more complex you make your L-system self-interaction, the more
points you'll earn.
- Any additional visual polish you add to your L-System will count towards extra credit, at your grader's discretion. For example, you could add animation of the leaves or branches in your vertex shader, or add falling leaves or flower petals.
