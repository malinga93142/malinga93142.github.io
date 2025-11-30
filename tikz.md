# TikZ LaTeX Documentation Guide

## Table of Contents
1. [Introduction](#introduction)
2. [Basic Setup](#basic-setup)
3. [Drawing Basic Shapes](#drawing-basic-shapes)
4. [Text and Nodes](#text-and-nodes)
5. [Positioning: Absolute and Relative](#positioning-absolute-and-relative)
6. [Text Alignment with Anchors](#text-alignment-with-anchors)
7. [Boxes and Rectangles](#boxes-and-rectangles)
8. [Connecting Elements](#connecting-elements)
9. [Styling and Colors](#styling-and-colors)
10. [Advanced Examples](#advanced-examples)

---

## Introduction

TikZ is a powerful LaTeX package for creating graphics programmatically. It allows you to draw diagrams, plots, charts, and more directly in your LaTeX documents. TikZ stands for "TikZ ist kein Zeichenprogramm" (TikZ is not a drawing program).

**Key Features:**
- Works with both pdfLaTeX and traditional LaTeX
- Portable and consistent across platforms
- Highly customizable
- Extensive library support

---

## Basic Setup

### Loading TikZ

```latex
\documentclass{article}
\usepackage{tikz}
\begin{document}
% Your TikZ code here
\end{document}
```

### Loading Additional Libraries

```latex
\usetikzlibrary{positioning}  % For relative positioning
\usetikzlibrary{shapes}       % For special shapes
\usetikzlibrary{arrows}       % For arrow styles
\usetikzlibrary{calc}         % For coordinate calculations
\usetikzlibrary{fit}          % For fitting boxes around nodes
```

### Creating a TikZ Picture

```latex
\begin{tikzpicture}
  % Your drawing commands here
\end{tikzpicture}
```

**Important:** Every TikZ command must end with a semicolon (`;`).

---

## Drawing Basic Shapes

### Lines

```latex
\begin{tikzpicture}
  % Simple line
  \draw (0,0) -- (2,1);
  
  % Multiple connected lines
  \draw (0,0) -- (2,0) -- (2,2) -- (0,2) -- cycle;
\end{tikzpicture}
```

The `cycle` keyword closes the path by connecting back to the starting point.

### Rectangles

```latex
\begin{tikzpicture}
  % Rectangle from two corners
  \draw (0,0) rectangle (3,2);
  
  % Filled rectangle
  \filldraw[fill=blue!20, draw=black] (0,0) rectangle (3,2);
  
  % Rectangle with relative coordinates
  \draw (0,0) rectangle ++(2,1);  % ++(x,y) means relative offset
\end{tikzpicture}
```

### Circles

```latex
\begin{tikzpicture}
  % Circle with center and radius
  \draw (0,0) circle (1cm);
  
  % Filled circle
  \filldraw[fill=red!20, draw=red] (2,0) circle (0.5cm);
\end{tikzpicture}
```

### Ellipses

```latex
\begin{tikzpicture}
  % Ellipse with x-radius and y-radius
  \draw (0,0) ellipse (2cm and 1cm);
\end{tikzpicture}
```

### Curves

```latex
\begin{tikzpicture}
  % Bezier curve with control points
  \draw (0,0) .. controls (1,1) and (2,1) .. (3,0);
\end{tikzpicture}
```

---

## Text and Nodes

### Basic Nodes

A node is text placed at a coordinate. Nodes can have shapes around them.

```latex
\begin{tikzpicture}
  % Simple text node
  \node at (0,0) {Hello};
  
  % Node with name for later reference
  \node (mynode) at (2,1) {World};
  
  % Node with shape
  \node[circle, draw] at (4,0) {Circle};
  \node[rectangle, draw] at (6,0) {Box};
\end{tikzpicture}
```

### Node Shapes

```latex
\begin{tikzpicture}
  \node[circle, draw, fill=blue!20] at (0,0) {Circle};
  \node[rectangle, draw, fill=red!20] at (2,0) {Rectangle};
  \node[ellipse, draw, fill=green!20] at (4,0) {Ellipse};
\end{tikzpicture}
```

### Node Sizing

```latex
\begin{tikzpicture}
  % Minimum size
  \node[draw, minimum size=2cm] at (0,0) {Text};
  
  % Separate width and height
  \node[draw, minimum width=3cm, minimum height=1cm] at (3,0) {Wide};
  
  % Inner separation (padding)
  \node[draw, inner sep=10pt] at (6,0) {Padded};
\end{tikzpicture}
```

---

## Positioning: Absolute and Relative

### Absolute Positioning

Use exact coordinates to place elements.

```latex
\begin{tikzpicture}
  \node[draw] at (0,0) {Node 1};
  \node[draw] at (3,0) {Node 2};
  \node[draw] at (1.5,2) {Node 3};
\end{tikzpicture}
```

### Relative Positioning with the `positioning` Library

**Load the library first:**
```latex
\usetikzlibrary{positioning}
```

**Basic relative positioning:**

```latex
\begin{tikzpicture}
  \node[draw] (n1) {First};
  \node[draw, right=of n1] (n2) {Right};
  \node[draw, below=of n1] (n3) {Below};
  \node[draw, above=of n1] (n4) {Above};
  \node[draw, left=of n1] (n5) {Left};
\end{tikzpicture}
```

**With distances:**

```latex
\begin{tikzpicture}[node distance=2cm]
  \node[draw] (n1) {First};
  \node[draw, right=of n1] (n2) {2cm right};
  \node[draw, below=1cm of n1] (n3) {1cm below};
\end{tikzpicture}
```

**Diagonal positioning:**

```latex
\begin{tikzpicture}
  \node[draw] (n1) {Center};
  \node[draw, above right=of n1] (n2) {Above right};
  \node[draw, below left=of n1] (n3) {Below left};
\end{tikzpicture}
```

### Polar Coordinates

```latex
\begin{tikzpicture}
  \node[draw] (center) at (0,0) {Center};
  \node[draw] at (30:2cm) {30°};
  \node[draw] at (90:2cm) {90°};
  \node[draw] at (180:2cm) {180°};
\end{tikzpicture}
```

---

## Text Alignment with Anchors

### Understanding Anchors

Every node has multiple anchor points that can be used for positioning and alignment.

**Common anchors:**
- `center` - center of the node
- `north`, `south`, `east`, `west` - cardinal directions
- `north east`, `north west`, `south east`, `south west` - corners
- `base` - baseline of the text
- `base east`, `base west` - baseline at sides
- `mid` - middle height of lowercase letters
- `mid east`, `mid west` - middle height at sides

### Visualizing Anchors

```latex
\begin{tikzpicture}
  \node[draw, rectangle, minimum width=3cm, minimum height=2cm] (box) at (0,0) {Node};
  
  % Mark the anchors
  \foreach \anchor in {north, south, east, west, north east, north west, 
                       south east, south west, center}
    \draw[red] (box.\anchor) circle (2pt) node[above right, font=\tiny] {\anchor};
\end{tikzpicture}
```

### Using Anchors for Positioning

```latex
\begin{tikzpicture}
  % Position node with specific anchor
  \node[draw, anchor=south west] at (0,0) {SW at origin};
  \node[draw, anchor=center] at (3,0) {Center at (3,0)};
  \node[draw, anchor=north] at (6,0) {North at (6,0)};
\end{tikzpicture}
```

### Text Alignment with Anchors

**Left-aligned text (anchor=west):**

```latex
\begin{tikzpicture}
  \node[anchor=west] at (0,2) {Left aligned};
  \node[anchor=west] at (0,1) {Also left aligned};
  \node[anchor=west] at (0,0) {All aligned to west};
\end{tikzpicture}
```

**Right-aligned text (anchor=east):**

```latex
\begin{tikzpicture}
  \node[anchor=east] at (3,2) {Right aligned};
  \node[anchor=east] at (3,1) {Also right aligned};
  \node[anchor=east] at (3,0) {All aligned to east};
\end{tikzpicture}
```

**Baseline alignment:**

```latex
\begin{tikzpicture}
  \node[anchor=base] at (0,0) {Text};
  \node[anchor=base] at (2,0) {Aligned};
  \node[anchor=base] at (4,0) {by baseline};
\end{tikzpicture}
```

### Combining Anchors with Relative Positioning

```latex
\begin{tikzpicture}
  \node[draw] (n1) {First node};
  
  % Place below, but align left edges
  \node[draw, below=of n1.south west, anchor=north west] (n2) {Second node longer};
  
  % Place below, but align right edges
  \node[draw, below=of n2.south east, anchor=north east] (n3) {Third};
\end{tikzpicture}
```

---

## Boxes and Rectangles

### Drawing Rectangles Around Text

```latex
\begin{tikzpicture}
  % Node with rectangle border
  \node[draw, rectangle] at (0,0) {Text in box};
  
  % With styling
  \node[draw=blue, fill=blue!10, thick, rectangle] at (3,0) {Styled box};
\end{tikzpicture}
```

### Custom Box Sizes

```latex
\begin{tikzpicture}
  % Fixed width box
  \node[draw, text width=3cm, align=center] at (0,0) {
    This text wraps within the box
  };
  
  % Minimum size box
  \node[draw, minimum width=4cm, minimum height=2cm] at (5,0) {
    Large box
  };
\end{tikzpicture}
```

### Rounded Corners

```latex
\begin{tikzpicture}
  \node[draw, rounded corners] at (0,0) {Rounded};
  \node[draw, rounded corners=10pt] at (3,0) {Very rounded};
\end{tikzpicture}
```

### Fitting Boxes Around Multiple Nodes

**Load the fit library:**
```latex
\usetikzlibrary{fit}
```

**Usage:**

```latex
\begin{tikzpicture}
  % Create some nodes
  \node[draw] (a) at (0,0) {A};
  \node[draw] (b) at (2,1) {B};
  \node[draw] (c) at (1,-1) {C};
  
  % Draw box around them
  \node[draw, fit=(a)(b)(c), inner sep=5pt, dashed, red] {};
\end{tikzpicture}
```

### Drawing Direct Rectangles (Not Nodes)

```latex
\begin{tikzpicture}
  % Simple rectangle
  \draw (0,0) rectangle (2,1);
  
  % Filled rectangle
  \fill[blue!20] (3,0) rectangle (5,1);
  
  % Both draw and fill
  \filldraw[fill=yellow!30, draw=red, thick] (6,0) rectangle (8,1);
\end{tikzpicture}
```

---

## Connecting Elements

### Lines Between Nodes

```latex
\begin{tikzpicture}
  \node[draw, circle] (a) at (0,0) {A};
  \node[draw, circle] (b) at (3,2) {B};
  
  % Draw line between nodes
  \draw (a) -- (b);
  
  % Draw line between specific anchors
  \draw[red] (a.north) -- (b.south);
\end{tikzpicture}
```

### Arrows

```latex
\begin{tikzpicture}
  \node[draw] (a) at (0,0) {Start};
  \node[draw] (b) at (3,0) {End};
  
  % Arrow
  \draw[->] (a) -- (b);
  
  % Double arrow
  \draw[<->] (a) -- (b);
  
  % Thick arrow
  \draw[->, very thick] (a) -- (b);
\end{tikzpicture}
```

### Curved Connections

```latex
\begin{tikzpicture}
  \node[draw] (a) at (0,0) {A};
  \node[draw] (b) at (3,0) {B};
  
  % Curved line with bend
  \draw[->, bend right] (a) to (b);
  \draw[->, bend left] (a) to (b);
  
  % With specific angle
  \draw[->, out=45, in=135] (a) to (b);
\end{tikzpicture}
```

### Labels on Lines

```latex
\begin{tikzpicture}
  \node[draw] (a) at (0,0) {A};
  \node[draw] (b) at (4,0) {B};
  
  % Label above line
  \draw[->] (a) -- node[above] {label} (b);
  
  % Label at midpoint below
  \draw[->] (a) -- node[midway, below] {midway} (b);
  
  % Label near start
  \draw[->] (a) -- node[near start, above] {start} (b);
\end{tikzpicture}
```

---

## Styling and Colors

### Line Styles

```latex
\begin{tikzpicture}
  \draw (0,0) -- (2,0);                    % normal
  \draw[thick] (0,0.5) -- (2,0.5);        % thick
  \draw[very thick] (0,1) -- (2,1);       % very thick
  \draw[dashed] (0,1.5) -- (2,1.5);       % dashed
  \draw[dotted] (0,2) -- (2,2);           % dotted
  \draw[dash dot] (0,2.5) -- (2,2.5);     % dash dot
\end{tikzpicture}
```

### Colors

```latex
\begin{tikzpicture}
  % Basic colors
  \draw[red] (0,0) -- (1,0);
  \draw[blue] (0,0.5) -- (1,0.5);
  \draw[green] (0,1) -- (1,1);
  
  % Color mixing (red!30 = 30% red, 70% white)
  \draw[red!30] (2,0) -- (3,0);
  \draw[blue!50] (2,0.5) -- (3,0.5);
  
  % Mixing two colors (red!50!blue = 50% red, 50% blue)
  \draw[red!50!blue] (4,0) -- (5,0);
\end{tikzpicture}
```

### Fill Colors

```latex
\begin{tikzpicture}
  \filldraw[fill=yellow!30, draw=orange, thick] (0,0) rectangle (2,1);
  \filldraw[fill=blue!10, draw=blue!50, thick] (3,0) circle (0.5);
\end{tikzpicture}
```

### Defining Styles

```latex
\begin{tikzpicture}[
  mybox/.style={rectangle, draw=blue, fill=blue!10, thick, minimum size=1cm},
  myarrow/.style={->, thick, red}
]
  \node[mybox] (a) at (0,0) {Box 1};
  \node[mybox] (b) at (3,0) {Box 2};
  \draw[myarrow] (a) -- (b);
\end{tikzpicture}
```

---

## Advanced Examples

### Complete Diagram with Aligned Boxes

```latex
\documentclass{article}
\usepackage{tikz}
\usetikzlibrary{positioning}

\begin{document}
\begin{tikzpicture}[
  node distance=1cm and 2cm,
  box/.style={rectangle, draw, fill=blue!10, minimum width=2cm, minimum height=1cm, align=center}
]
  % Create nodes
  \node[box] (n1) {First Node};
  \node[box, right=of n1] (n2) {Second Node};
  \node[box, below=of n1] (n3) {Third Node};
  \node[box, below=of n2] (n4) {Fourth Node};
  
  % Connect with arrows
  \draw[->] (n1) -- (n2);
  \draw[->] (n1) -- (n3);
  \draw[->] (n2) -- (n4);
  \draw[->] (n3) -- (n4);
\end{tikzpicture}
\end{document}
```

### Flowchart Example

```latex
\begin{tikzpicture}[
  node distance=1.5cm,
  start/.style={rectangle, rounded corners, draw, fill=green!20, minimum width=2cm},
  process/.style={rectangle, draw, fill=blue!20, minimum width=2cm},
  decision/.style={diamond, draw, fill=yellow!20, aspect=2}
]
  \node[start] (start) {Start};
  \node[process, below=of start] (p1) {Process 1};
  \node[decision, below=of p1] (dec) {Decision?};
  \node[process, below left=of dec] (p2) {Process 2};
  \node[process, below right=of dec] (p3) {Process 3};
  \node[start, below=3cm of dec] (end) {End};
  
  \draw[->] (start) -- (p1);
  \draw[->] (p1) -- (dec);
  \draw[->] (dec) -- node[left] {Yes} (p2);
  \draw[->] (dec) -- node[right] {No} (p3);
  \draw[->] (p2) |- (end);
  \draw[->] (p3) |- (end);
\end{tikzpicture}
```

### Grid with Aligned Text

```latex
\begin{tikzpicture}
  % Draw grid
  \draw[help lines, step=1cm] (0,0) grid (5,3);
  
  % Place text nodes aligned to grid
  \foreach \x in {0,...,5} {
    \foreach \y in {0,...,3} {
      \node[anchor=center] at (\x,\y) {(\x,\y)};
    }
  }
\end{tikzpicture}
```

### Layered Architecture Diagram

```latex
\begin{tikzpicture}[
  layer/.style={rectangle, draw, fill=blue!20, minimum width=6cm, minimum height=1cm, anchor=north}
]
  \node[layer] (l1) at (0,0) {Presentation Layer};
  \node[layer, below=0.2cm of l1] (l2) {Business Logic Layer};
  \node[layer, below=0.2cm of l2] (l3) {Data Access Layer};
  \node[layer, below=0.2cm of l3] (l4) {Database Layer};
  
  % Add arrows
  \foreach \i/\j in {l1/l2, l2/l3, l3/l4} {
    \draw[<->, thick] (\i.south) -- (\j.north);
  }
\end{tikzpicture}
```

---

## Tips and Best Practices

1. **Always use semicolons** - Every TikZ command must end with `;`

2. **Name your nodes** - Give nodes meaningful names for easier reference:
   ```latex
   \node[draw] (mynode) at (0,0) {Text};
   ```

3. **Use styles** - Define reusable styles at the beginning:
   ```latex
   \begin{tikzpicture}[mystyle/.style={...}]
   ```

4. **Load needed libraries** - Only load what you need:
   ```latex
   \usetikzlibrary{positioning, shapes, arrows}
   ```

5. **Use relative positioning** - Makes diagrams easier to modify:
   ```latex
   \node[right=of other] (new) {Text};
   ```

6. **Anchor awareness** - Understanding anchors helps with precise alignment

7. **Comment your code** - TikZ code can get complex:
   ```latex
   % Draw the main box
   \node[draw] (main) {Main};
   ```

8. **Test incrementally** - Build complex diagrams step by step

---

## Common Patterns

### Left-aligned list

```latex
\begin{tikzpicture}
  \node[anchor=west] at (0,3) {Item 1};
  \node[anchor=west] at (0,2) {Item 2 - longer};
  \node[anchor=west] at (0,1) {Item 3};
\end{tikzpicture}
```

### Boxes in a row

```latex
\begin{tikzpicture}[box/.style={draw, minimum width=2cm, minimum height=1cm}]
  \node[box] (b1) at (0,0) {Box 1};
  \node[box] (b2) at (3,0) {Box 2};
  \node[box] (b3) at (6,0) {Box 3};
  \draw[->] (b1) -- (b2);
  \draw[->] (b2) -- (b3);
\end{tikzpicture}
```

### Centered title over diagram

```latex
\begin{tikzpicture}
  \node[font=\Large\bfseries] at (2,3) {Diagram Title};
  \draw (0,0) rectangle (4,2);
  % ... more elements
\end{tikzpicture}
```

---

## Additional Resources

- **Official PGF/TikZ Manual**: https://tikz.dev/ (1000+ pages)
- **Overleaf TikZ Documentation**: https://www.overleaf.com/learn/latex/TikZ_package
- **TikZ Examples**: http://www.texample.net/tikz/
- **Stack Exchange**: Questions tagged with tikz-pgf

---

This documentation covers the essential aspects of TikZ for creating diagrams with proper positioning, alignment, and styling. Experiment with the examples and combine different techniques to create your desired graphics!
