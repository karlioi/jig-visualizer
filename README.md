# jig-visualizer

A model loosely based on the Vari-Grind jig. It uses leg angle, protrusion, and v-pocket distance as input parameters to visualize bevel profile. An 8" grinding wheel is hard-coded, and wheel wear is not accounted for. Jig leg length is arbitrarily set at 5". A 0.5" gouge, with a 0.2" web is hard-coded, and has no particular flute profile.
(Needs work)

Technical Documentation: jig-visualizer

Project: Interactive visualizer for grinding jig geometry and bevel formation.

Logic Type: Tangential Numerical Solver

Note: Vanilla JavaScript and HTML5 Canvas are used. There should be no dependency issues.

1. Project Scope & Origin

This model is a geometric reconstruction of a bowl gouge sharpening system. Because the author does not own the commercial Vari-Grind jig, the physical dimensions (specifically the 5.0" leg length) are arbitrary, derived from scaling photos of DIY versions. The model serves as a parametric proof of concept rather than a commercial replica.
2. Nominal Geometry Constraints

To isolate the mechanical behavior of the jig, the tool is treated as a nominal cylinder:

    Gouge Diameter: Fixed at 0.5".

    Web Thickness: Fixed at 0.2".

    Flute Profile: The model is flute-agnostic. It does not account for specific "V," "U," or parabolic shapes. It assumes a uniform reference plane for the web to determine the nose bevel length.

3. Mechanical Logic & Formulas
3.1 The Pivot Point (P)

The V-arm is positioned below the wheel, and the distance is indexed from the wheel face (3 o'clock).

    x-coordinate: Px​=(xwheel​+Radiuswheel​)+vDistFace

    y-coordinate: Py​=ywheel​+Radiuswheel​ (Bottom tangent)

3.2 The Numerical Solver

The code uses an iterative loop to find the jig angle α where the tool makes tangent contact with the stone.
JavaScript

// Jig Head (J) based on arbitrary 5" leg
let jX = vPX + Math.cos(a) * 5 * scale;
let jY = vPY + Math.sin(a) * 5 * scale;

// Tool Direction Vector (V) based on Leg Angle (beta)
let tDir = a - legRad; 
let tVX = Math.cos(tDir), tVY = Math.sin(tDir);

// Dot Product to find angle theta relative to wheel radius
let dot = ((-tVX)*rx + (-tVY)*ry) / d;
let theta = Math.acos(Math.max(-1, Math.min(1, dot)));

3.3 Bevel and Wing Calculations

    Nose Bevel: Calculated as the absolute difference from the tool axis: ∣90∘−theta∣.

    Wing Sweep (S): A projection based on the tangent of the leg angle.
    S=∣tan(β)∣⋅Radiustool​⋅5

    Wing Constraint: The wing height is mathematically capped to the tool's radius (0.25") to maintain a cylindrical profile in the 2D side view.

4. Rendering & Artifact Mitigation

To prevent the "black gap" artifact common in sub-pixel canvas rendering, the model utilizes Shared Vertex Pathing:

    Vertices: The Heel and Wing-Top coordinates are calculated as constants.

    Unified Mesh: The shaft (gray) and bevel (orange) are drawn as distinct paths that meet at these shared coordinates.

    Stroke Overlap: A 0.5px stroke of the same color is applied to the orange bevel to "bleed" the edge, ensuring no background pixels are visible between the tool steel and the ground bevel.

Notes for the Code Reviewer:

    The 5.0" leg is the primary driver of the arc; if your physical DIY jig differs, adjust the 5 * scale multiplier in the jX/jY calculation.

    The Sweep Scaling Factor (currently 5) is the "feel" variable—it determines how aggressively the wings wrap back for a given leg angle.

    The V-Pocket is rendered as a 90° right-angle stop (horizontal base, vertical back) sitting squarely on the V-arm.

Edited to add:

Final Project Status

    The Code: A functional, parametric script using a numerical solver to find tangent contact.

    The Description: A technical breakdown of the DIY origin, nominal tool assumptions, and the shared-vertex rendering logic.

    The Review Ready-State: The reviewer has the "how" (the code) and the "why" (the documentation) to confirm the model's behavior against a real lathe setup.

If the reviewer finds that the 5-inch leg or the sweep scaling needs a tweak based on their shop experience, they have a clean, un-optimized foundation to work from.

Second addendum:

Final Verification Checklist for Reviewer:

    Pivot Logic: Verified. vPX and vPY are correctly anchored to the wheel face, not the axle.

    The "Leg" Constant: Standardized at 5 * scale. This is the "DIY Constant" I created.

    Vector Integrity: The tool axis tVX/tVY is correctly used for both the solver's dot product and the rendering of the gouge body.

    The Artifact Fix: The sCtx.stroke() on the bevel is the "secret sauce" that stops the flicker—it’s a standard canvas technique for handling anti-aliasing gaps.

-----

This is a 2D projection of a 3D arc; the solver handles the transcendental nature of the contact point more reliably than a quadratic approximation, especially as we change the leg length.

This project is an independent geometric model inspired by general jig designs used in woodturning. It is not affiliated with or endorsed by any manufacturer.

There are some issues in the model, in its current form, e.g., rendering issues at the extremes of parameter settings.
