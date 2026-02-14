---
id: skill-manim
type: skill
name: manim
description: Comprehensive guide for Manim Community - Python framework for creating
  mathematical animations and educational videos with programmatic control
category: video
complexity: medium
keywords:
- github
- python
- test
capabilities: []
token_estimate: 674
has_references: true
reference_count: 4
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~674 -->
<!-- Includes Reference Materials -->

> **How to Use This Template**
>
> This template can be used with various AI coding assistants:
> 
> **GitHub Copilot:**
> - Add to `.github/copilot-instructions.md` in your repository
> - Reference in chat: `@workspace` to include in context
> - Add specific sections to your workspace instructions
> 
> **Augment Code:**
> - Load context: `aug context add <path-to-this-file>`
> - Reference in queries naturally
> - Use with specific commands
> 
> **Claude (Desktop/Web):**
> - Add to Project Knowledge in Claude Projects
> - Reference in custom instructions
> - Copy relevant sections as needed
> 
> **ChatGPT:**
> - Add to Custom GPT configuration
> - Include in conversation instructions
> - Use as system prompt
> 
> **Generic Usage:**
> Copy the content below and paste it into your AI assistant's context window
> or system instructions.

---




# manim

> Comprehensive guide for Manim Community - Python framework for creating mathematical animations and educational videos with programmatic control

# Manim Community - Mathematical Animation Engine

Comprehensive skill set for creating mathematical animations using Manim Community, a Python framework for creating explanatory math videos programmatically, popularized by 3Blue1Brown.

## When to use

Use this skill whenever you are dealing with Manim code to obtain domain-specific knowledge about:

- Creating mathematical animations and visualizations
- Building educational video content programmatically
- Working with geometric shapes and transformations
- Animating LaTeX equations and mathematical formulas
- Creating graphs, charts, and coordinate systems
- Implementing scene-based animation sequences
- Rendering high-quality mathematical diagrams
- Building explanatory visual content for teaching

## Core Concepts

Manim allows you to create animations using:
- **Scenes**: Canvas for your animations where you orchestrate mobjects
- **Mobjects**: Mathematical objects that can be displayed (shapes, text, equations)
- **Animations**: Transformations applied to mobjects (Write, Create, Transform, FadeIn)
- **Transforms**: Morphing between different states of mobjects
- **LaTeX Integration**: Native support for rendering mathematical notation
- **Python Simplicity**: Use Python to programmatically specify animation behavior

## Key Features

- Precise mathematical object positioning and transformations
- Native LaTeX rendering for equations and formulas
- Extensive shape library (circles, rectangles, arrows, polygons)
- Coordinate systems and function graphing
- Boolean operations on geometric shapes
- Camera controls and scene management
- High-quality video rendering
- IPython/Jupyter notebook integration
- VS Code extension with live preview

## How to use

Read individual rule files for detailed explanations and code examples:

### Core Concepts
- **[references/scenes.md](references/scenes.md)** - Creating scenes and organizing animations
- **[references/mobjects.md](references/mobjects.md)** - Understanding mathematical objects and shapes
- **[references/animations.md](references/animations.md)** - Core animation types and techniques
- **[references/latex.md](references/latex.md)** - Rendering LaTeX equations and formulas

For additional topics including transforms, timing, shapes, coordinate systems, 3D animations, camera movement, and advanced features, refer to the comprehensive [Manim Community documentation](https://docs.manim.community/).

## Quick Start Example

```python
from manim import *

class SquareToCircle(Scene):
    def construct(self):
        # Create a square
        square = Square()
        square.set_fill(BLUE, opacity=0.5)

        # Create a circle
        circle = Circle()
        circle.set_fill(RED, opacity=0.5)

        # Animate square creation
        self.play(Create(square))
        self.wait(1)

        # Transform square into circle
        self.play(Transform(square, circle))
        self.wait(1)

        # Fade out
        self.play(FadeOut(square))
```

Render with: `manim -pql script.py SquareToCircle`

## Best Practices

1. **Inherit from Scene** - All animations should be in a class inheriting from Scene
2. **Use construct() method** - Place all animation code inside the construct() method
3. **Think in layers** - Add mobjects to the scene before animating them
4. **Use self.play()** - Animate mobjects using self.play(Animation(...))
5. **Test with low quality** - Use `-ql` flag for faster preview renders
6. **Leverage LaTeX** - Use Tex() and MathTex() for mathematical notation
7. **Group related objects** - Use VGroup to manage multiple mobjects together
8. **Preview frequently** - Use `-p` flag to automatically open rendered videos

## Command Line Usage

```bash
# Preview at low quality (fast)
manim -pql script.py SceneName

# Render at high quality
manim -pqh script.py SceneName

# Save last frame as image
manim -s script.py SceneName

# Render multiple scenes
manim script.py Scene1 Scene2
```

## Resources

- **Documentation**: https://docs.manim.community/
- **Repository**: https://github.com/ManimCommunity/manim
- **Examples Gallery**: https://docs.manim.community/en/stable/examples.html
- **Discord Community**: https://www.manim.community/discord/
- **3Blue1Brown Channel**: https://www.youtube.com/c/3blue1brown
- **License**: MIT


---


## 📚 Reference Materials


### Animations

# Animations in Manim

Animations are transformations applied to mobjects over time. Manim provides a rich set of built-in animations for creating smooth, professional-looking transitions.

## Basic Animation Pattern

```python
from manim import *

class BasicAnimation(Scene):
    def construct(self):
        circle = Circle()

        # Play an animation
        self.play(Create(circle))
        self.wait(1)
```

## Creation Animations

Animations for introducing mobjects:

```python
class CreationAnimations(Scene):
    def construct(self):
        square = Square()
        circle = Circle()
        text = Text("Hello")

        # Create (draw stroke)
        self.play(Create(square))
        self.wait(0.5)

        # Fade in
        self.play(FadeIn(circle))
        self.wait(0.5)

        # Write (for text)
        self.play(Write(text))
        self.wait(0.5)

        # Grow from center
        triangle = Triangle()
        self.play(GrowFromCenter(triangle))
```

## Removal Animations

Animations for removing mobjects:

```python
class RemovalAnimations(Scene):
    def construct(self):
        shapes = VGroup(*[Circle() for _ in range(4)])
        shapes.arrange(RIGHT, buff=0.5)
        self.add(shapes)

        # Fade out
        self.play(FadeOut(shapes[0]))

        # Shrink to center
        self.play(ShrinkToCenter(shapes[1]))

        # Uncreate (reverse of Create)
        self.play(Uncreate(shapes[2]))

        # Unwrite (reverse of Write)
        text = Text("Bye")
        self.add(text)
        self.play(Unwrite(text))
```

## Transform Animations

Morph one mobject into another:

```python
class TransformAnimations(Scene):
    def construct(self):
        square = Square()
        circle = Circle()

        self.play(Create(square))
        self.wait(0.5)

        # Transform (original mobject becomes target)
        self.play(Transform(square, circle))
        self.wait(0.5)

        # ReplacementTransform (replace with new mobject)
        triangle = Triangle()
        self.play(ReplacementTransform(square, triangle))
```

## Movement Animations

```python
class MovementAnimations(Scene):
    def construct(self):
        circle = Circle()
        self.add(circle)

        # Shift (relative movement)
        self.play(circle.animate.shift(RIGHT * 2))

        # Move to (absolute position)
        self.play(circle.animate.move_to(UP * 2))

        # Rotate
        self.play(circle.animate.rotate(PI / 2))

        # Scale
        self.play(circle.animate.scale(2))
```

## The .animate Syntax

Use `.animate` to smoothly interpolate property changes:

```python
class AnimateSyntax(Scene):
    def construct(self):
        square = Square()
        self.add(square)

        # Animate multiple properties at once
        self.play(
            square.animate
            .shift(RIGHT * 2)
            .rotate(PI / 4)
            .scale(1.5)
            .set_fill(BLUE, opacity=0.7)
        )
```

## Animation Timing

```python
class AnimationTiming(Scene):
    def construct(self):
        circle = Circle()
        self.add(circle)

        # Set animation duration (default is 1 second)
        self.play(circle.animate.shift(RIGHT * 3), run_time=2)

        # Add delay before next animation
        self.wait(1.5)

        # Fast animation
        self.play(circle.animate.shift(LEFT * 3), run_time=0.5)
```

## Rate Functions (Easing)

Control animation speed curves:

```python
class RateFunctions(Scene):
    def construct(self):
        circles = VGroup(*[Circle() for _ in range(3)])
        circles.arrange(DOWN, buff=1)
        self.add(circles)

        # Linear (constant speed)
        self.play(
            circles[0].animate.shift(RIGHT * 3),
            rate_func=linear
        )

        # Smooth (ease in and out)
        self.play(
            circles[1].animate.shift(RIGHT * 3),
            rate_func=smooth
        )

        # Bounce
        self.play(
            circles[2].animate.shift(RIGHT * 3),
            rate_func=there_and_back
        )
```

## Simultaneous Animations

Animate multiple mobjects at once:

```python
class SimultaneousAnimations(Scene):
    def construct(self):
        circle = Circle()
        square = Square()

        circle.shift(LEFT * 2)
        square.shift(RIGHT * 2)

        # Both animations play simultaneously
        self.play(
            Create(circle),
            Create(square)
        )

        self.play(
            circle.animate.shift(UP),
            square.animate.shift(DOWN)
        )
```

## Sequential vs Simultaneous

```python
class SequentialVsSimultaneous(Scene):
    def construct(self):
        # Sequential (one after another)
        c1 = Circle().shift(LEFT * 3)
        c2 = Circle()
        c3 = Circle().shift(RIGHT * 3)

        self.play(Create(c1))  # First
        self.play(Create(c2))  # Second
        self.play(Create(c3))  # Third

        # Simultaneous (all at once)
        s1 = Square().shift(LEFT * 3 + DOWN * 2)
        s2 = Square().shift(DOWN * 2)
        s3 = Square().shift(RIGHT * 3 + DOWN * 2)

        self.play(Create(s1), Create(s2), Create(s3))  # All together
```

## Resources

- [Animation Reference](https://docs.manim.community/en/stable/reference/manim.animation.animation.Animation.html)
- [Animation Examples](https://docs.manim.community/en/stable/examples.html)




### Latex

# LaTeX in Manim

Manim has native support for rendering LaTeX, making it perfect for mathematical animations and educational content.

## MathTex - Mathematical Formulas

```python
from manim import *

class BasicMath(Scene):
    def construct(self):
        # Simple equation
        equation = MathTex(r"E = mc^2")

        self.play(Write(equation))
        self.wait(2)
```

## Tex - Mixed Text and Math

```python
class TexExample(Scene):
    def construct(self):
        # Mix text and math
        formula = Tex(r"The famous equation: $E = mc^2$")

        self.play(Write(formula))
        self.wait(2)
```

## Complex Equations

```python
class ComplexEquations(Scene):
    def construct(self):
        # Quadratic formula
        quadratic = MathTex(
            r"x = \frac{-b \pm \sqrt{b^2 - 4ac}}{2a}"
        )

        # Integral
        integral = MathTex(
            r"\int_0^{\infty} e^{-x^2} dx = \frac{\sqrt{\pi}}{2}"
        )

        # Matrix
        matrix = MathTex(
            r"\begin{bmatrix} a & b \\ c & d \end{bmatrix}"
        )

        formulas = VGroup(quadratic, integral, matrix)
        formulas.arrange(DOWN, buff=1)

        self.play(Write(formulas))
        self.wait(2)
```

## Colored Parts

Color specific parts of equations:

```python
class ColoredMath(Scene):
    def construct(self):
        # Split equation into parts
        equation = MathTex(
            r"a^2", "+", "b^2", "=", "c^2"
        )

        # Color individual parts
        equation[0].set_color(RED)    # a^2
        equation[2].set_color(BLUE)   # b^2
        equation[4].set_color(GREEN)  # c^2

        self.play(Write(equation))
        self.wait(2)
```

## Transform Between Equations

```python
class EquationTransform(Scene):
    def construct(self):
        # Start with one equation
        eq1 = MathTex(r"x^2 + y^2 = r^2")

        # Transform to another
        eq2 = MathTex(r"(x-h)^2 + (y-k)^2 = r^2")

        self.play(Write(eq1))
        self.wait(1)

        self.play(TransformMatchingShapes(eq1, eq2))
        self.wait(2)
```

## Step-by-Step Derivation

```python
class Derivation(Scene):
    def construct(self):
        # Create a series of equations
        line1 = MathTex(r"x + 5 = 10")
        line2 = MathTex(r"x + 5 - 5 = 10 - 5")
        line3 = MathTex(r"x = 5")

        # Position them
        equations = VGroup(line1, line2, line3)
        equations.arrange(DOWN, buff=0.8)

        # Show derivation step by step
        self.play(Write(line1))
        self.wait(1)

        self.play(TransformMatchingShapes(line1.copy(), line2))
        self.wait(1)

        self.play(TransformMatchingShapes(line2.copy(), line3))
        self.wait(2)
```

## Aligned Equations

```python
class AlignedEquations(Scene):
    def construct(self):
        # Use aligned environment
        aligned = MathTex(
            r"\begin{aligned}"
            r"x + 5 &= 10 \\"
            r"x &= 10 - 5 \\"
            r"x &= 5"
            r"\end{aligned}"
        )

        self.play(Write(aligned))
        self.wait(2)
```

## Greek Letters and Symbols

```python
class GreekSymbols(Scene):
    def construct(self):
        # Common Greek letters
        greeks = MathTex(
            r"\alpha, \beta, \gamma, \delta, \theta, \pi, \sigma, \omega"
        )

        # Common symbols
        symbols = MathTex(
            r"\sum_{i=1}^{n} i = \frac{n(n+1)}{2}"
        )

        # Limits
        limit = MathTex(
            r"\lim_{x \to \infty} \frac{1}{x} = 0"
        )

        group = VGroup(greeks, symbols, limit)
        group.arrange(DOWN, buff=1)

        self.play(Write(group))
        self.wait(2)
```

## Font Sizes

```python
class FontSizes(Scene):
    def construct(self):
        # Different font sizes
        small = MathTex(r"E = mc^2").scale(0.5)
        normal = MathTex(r"E = mc^2")
        large = MathTex(r"E = mc^2").scale(2)

        sizes = VGroup(small, normal, large)
        sizes.arrange(DOWN, buff=1)

        self.play(Write(sizes))
        self.wait(2)
```

## Highlighting Parts

```python
class HighlightParts(Scene):
    def construct(self):
        equation = MathTex(
            r"f(x) = ax^2 + bx + c"
        )

        # Create a box around the quadratic term
        box = SurroundingRectangle(equation[0][4:7], color=YELLOW)

        self.play(Write(equation))
        self.wait(1)

        self.play(Create(box))
        self.wait(2)
```

## Resources

- [MathTex Reference](https://docs.manim.community/en/stable/reference/manim.mobject.text.tex_mobject.MathTex.html)
- [LaTeX Symbols Guide](https://www.overleaf.com/learn/latex/List_of_Greek_letters_and_math_symbols)




### Mobjects

# Mobjects in Manim

Mobjects (Mathematical Objects) are the building blocks of Manim animations. They represent anything that can be displayed on screen.

## Basic Shapes

```python
from manim import *

class BasicShapes(Scene):
    def construct(self):
        # Circle
        circle = Circle(radius=1, color=BLUE)
        circle.set_fill(BLUE, opacity=0.5)

        # Square
        square = Square(side_length=2, color=RED)

        # Rectangle
        rectangle = Rectangle(width=3, height=1.5, color=GREEN)

        # Display them
        self.play(Create(circle))
        self.play(Create(square))
        self.play(Create(rectangle))
```

## Common Mobjects

### Geometric Shapes

```python
class GeometricShapes(Scene):
    def construct(self):
        # Basic shapes
        circle = Circle()
        square = Square()
        triangle = Triangle()
        pentagon = RegularPolygon(n=5)

        # Arrange in a row
        shapes = VGroup(circle, square, triangle, pentagon)
        shapes.arrange(RIGHT, buff=0.5)

        self.play(Create(shapes))
```

### Lines and Arrows

```python
class LinesAndArrows(Scene):
    def construct(self):
        # Line
        line = Line(start=LEFT, end=RIGHT, color=BLUE)

        # Arrow
        arrow = Arrow(start=LEFT, end=RIGHT, color=RED)

        # Double arrow
        double_arrow = DoubleArrow(start=LEFT * 2, end=RIGHT * 2)

        # Vector
        vector = Vector(direction=UP + RIGHT)

        arrows = VGroup(line, arrow, double_arrow, vector)
        arrows.arrange(DOWN, buff=0.5)

        self.play(Create(arrows))
```

## Mobject Properties

### Color and Opacity

```python
class ColorOpacity(Scene):
    def construct(self):
        circle = Circle()

        # Set stroke color
        circle.set_stroke(color=BLUE, width=5)

        # Set fill
        circle.set_fill(RED, opacity=0.7)

        self.play(Create(circle))
```

### Size and Position

```python
class SizePosition(Scene):
    def construct(self):
        square = Square()

        # Scale
        square.scale(2)

        # Move
        square.shift(UP * 2 + RIGHT * 3)

        # Rotate
        square.rotate(PI / 4)

        self.play(Create(square))
```

## Text and LaTeX

```python
class TextExample(Scene):
    def construct(self):
        # Regular text
        text = Text("Hello, Manim!")

        # LaTeX text
        formula = MathTex(r"e^{i\pi} + 1 = 0")

        # Equation
        equation = Tex(r"The famous equation: $E = mc^2$")

        group = VGroup(text, formula, equation)
        group.arrange(DOWN, buff=1)

        self.play(Write(text))
        self.play(Write(formula))
        self.play(Write(equation))
```

## Grouping Mobjects

```python
class GroupingExample(Scene):
    def construct(self):
        # Create multiple circles
        circles = VGroup(*[Circle(radius=0.5) for _ in range(5)])

        # Arrange them
        circles.arrange(RIGHT, buff=0.3)

        # Apply color gradient
        circles.set_color_by_gradient(BLUE, RED)

        # Animate all at once
        self.play(Create(circles))

        # Animate each individually
        self.play(*[circle.animate.shift(UP) for circle in circles])
```

## Custom Mobjects

```python
class CustomMobject(VMobject):
    def __init__(self, **kwargs):
        super().__init__(**kwargs)
        # Add custom shape construction
        self.set_points_as_corners([
            LEFT, UP, RIGHT, DOWN, LEFT
        ])
        self.set_fill(BLUE, opacity=0.5)

class CustomExample(Scene):
    def construct(self):
        custom = CustomMobject()
        self.play(Create(custom))
```

## Resources

- [Mobjects Reference](https://docs.manim.community/en/stable/reference/manim.mobject.mobject.Mobject.html)
- [Geometry Documentation](https://docs.manim.community/en/stable/reference/manim.mobject.geometry.html)




### Scenes

# Scenes in Manim

Scenes are the canvas for your animations. All Manim code is organized within Scene classes, and each scene represents a complete animation sequence.

## Basic Scene Structure

```python
from manim import *

class MyScene(Scene):
    def construct(self):
        # All animation code goes here
        circle = Circle()
        self.play(Create(circle))
        self.wait(1)
```

## Scene Types

Manim provides different scene types for different purposes:

### Standard Scene

```python
class BasicScene(Scene):
    def construct(self):
        text = Text("Hello, Manim!")
        self.play(Write(text))
        self.wait(2)
```

### Moving Camera Scene

For scenes with camera movement:

```python
class CameraScene(MovingCameraScene):
    def construct(self):
        circle = Circle()
        self.play(Create(circle))

        # Zoom in on the circle
        self.play(self.camera.frame.animate.scale(0.5))
        self.wait(1)
```

### 3D Scene

For three-dimensional animations:

```python
class ThreeDExample(ThreeDScene):
    def construct(self):
        axes = ThreeDAxes()
        sphere = Sphere()

        self.set_camera_orientation(phi=75 * DEGREES, theta=30 * DEGREES)
        self.play(Create(axes), Create(sphere))
        self.begin_ambient_camera_rotation(rate=0.1)
        self.wait(5)
```

## Adding and Removing Mobjects

```python
class AddRemoveScene(Scene):
    def construct(self):
        circle = Circle()
        square = Square()

        # Add without animation
        self.add(circle)
        self.wait(1)

        # Add with animation
        self.play(Create(square))
        self.wait(1)

        # Remove without animation
        self.remove(circle)
        self.wait(1)

        # Remove with animation
        self.play(FadeOut(square))
```

## Scene Methods

Common scene methods:

```python
class SceneMethods(Scene):
    def construct(self):
        circle = Circle()

        # Add mobject to scene
        self.add(circle)

        # Play animation
        self.play(circle.animate.shift(RIGHT * 2))

        # Wait (pause)
        self.wait(2)

        # Remove mobject
        self.remove(circle)

        # Clear all mobjects
        self.clear()
```

## Multiple Scenes in One File

```python
from manim import *

class Scene1(Scene):
    def construct(self):
        circle = Circle()
        self.play(Create(circle))

class Scene2(Scene):
    def construct(self):
        square = Square()
        self.play(Create(square))

# Render specific scene:
# manim script.py Scene1
# manim script.py Scene2
```

## Resources

- [Manim Scenes Documentation](https://docs.manim.community/en/stable/reference/manim.scene.scene.Scene.html)
- [Building Blocks Tutorial](https://docs.manim.community/en/stable/tutorials/building_blocks.html)




---

## 🚀 Usage

**Reference this template:** `@skill-manim.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
