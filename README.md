# jig-visualizer

Stack: Vanilla JavaScript & HTML5 Canvas (No dependencies).

Quick Run: Open index.html in any modern browser or visit the Live Demo.

Interactive visualizer for grinding jig geometry and bevel formation. The model is loosely based on the Vari-Grind jig and uses leg angle, protrusion, and v-pocket distance as input parameters to visualize the resulting bevel profile.

## Goals:
    • Understand bevel geometry
    • Visualize the effect of jig-configuration on the bevel.
    • Explore the impact of jig-configuration changes

Logic Type: Tangential Numerical Solver

## 1. Nominal Geometry Constraints

    ┌────────────────┬───────┬──────────────────────────────┐
    │ Parameters     │ Value │ Note                         │
    ├────────────────┼───────┼──────────────────────────────┤
    | Grinding Wheel | 8"    | Hard-coded diameter;         |
    |                |       | no wear compensation         |
    ├────────────────┼───────┼──────────────────────────────┤ 
    | Jig Leg Length | 5"    | Arbitrary "DIY Constant"     |
    ├────────────────┼───────┼──────────────────────────────┤ 
    | Gouge Diameter | 0.5"  | Fixed cylindrical profile    |
    ├────────────────┼───────┼──────────────────────────────┤ 
    | Web Thickness  | 0.18" | Tool thickness between flute |
    |                |       | and gouge bottom             |
    ├────────────────┼───────┼──────────────────────────────┤ 
    | Flute Profile  | None  | Flute agnostic; assumes a    |
    |                |       | uniform reference plane      |
    └────────────────┴───────┴──────────────────────────────┘ 

## 2. Mechanical Logic

### 2.1 The Pivot Point (P)

The V-arm is positioned below the wheel, indexed from the wheel face (3 o'clock) and the bottom tangent of the wheel (6 o'clock).

    • x-coordinate: const vPX = (wheelX ​+ wRPx​) + (vDistFace * scale)
    • y-coordinate: const vPY)= wheelY ​+ wRPx​

### 2.2 The Numerical Solver

The code uses an iterative loop (0.001 radian increments) to find the exact jig angle α where the tool makes tangent contact with the stone.

JavaScript:

`// Calculate Tool Direction and Tip Position`\
`// tDir: Tool Direction Vector based on Leg Angle beta`\
`let tDir = a - legRad;`\
`let tVX = Math.cos(tDir), tVY = Math.sin(tDir);`

`// Calculate Bevel Angle using Dot Product`\
`let dot = ((-tVX)*rx + (-tVY)*ry) / d;`\
`let theta = Math.acos(Math.max(-1, Math.min(1, dot)));`\
`let bevelDeg = Math.abs(90 - (theta * 180 / Math.PI));`

### 2.3 Bevel and Wing Calculations

    * Nose Bevel: Calculated as the absolute difference from the tool axis: ∣90°−θ∣.

    * Wing Sweep: A projection based on the tangent of the leg angle:
      let sweepInches = Math.abs(Math.tan(legRad)) * (gDia/2) * 5

    * Wing Constraint: The wing sweep is  mathematically capped between 0.1" and 1.5" to maintain rendering stability.

## 3. Rendering & Artifact Mitigation

To prevent the "black gap" artifact common in sub-pixel canvas rendering, the model utilizes Shared Vertex Pathing:

    * Unified Mesh: The shaft (gray) and bevel (orange) are drawn as distinct paths that meet at shared coordinates (Heel and Wing-Top).

    * Stroke Overlap: A 0.5px stroke of the same color is applied to the orange bevel to "bleed" the edge, ensuring no background pixels are visible between the tool steel and the ground bevel.

## 4. Known Issues/Limitations:

    * Extreme Parameters: Rendering issues and calculation errors may occur at the extreme limits of the 
      input sliders.

    * 2D Projection: This is a 2D sideview projection of a 3D arc; wing "Sweep" is a "feel" variable rather than a literal 3D intersection.

## 5. Implementation Notes:

    * Pivot Logic: vPX and vPY are correctly anchored to the wheel face, not the axle.

    * Vector Integrity: The tool axis tVX/tVY is used consistently for both the solver's dot product and the rendering of the gouge body.

    * Transendental Accuracy: The numerical solver handles the nature of the contact point more reliably than a quadratic approximation, particularly when changing leg length.

    * Wing Sweep (sweepInches): capped at 1.5"