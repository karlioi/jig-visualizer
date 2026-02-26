# Changes 2026-2-25

## Reworked repo file structure:
1. Added models/, moved models to models/*
2. Added images/, moved screenshot to images/
3. Added CHANGELOG.md

## Edited index.com
1. Conforms to new repo structure.
2. Improved legibility with
 * better code blocks
 * better indentation

## 3D Model Improvements
1. Reduced Wing Sweep cap from 3.5" to 1.5"
* Realism: Aligns with "Irish/Ellsworth" profiles (max ≈ 2.4D for a 5/8" tool).
* Geometry Stability: Eliminated mesh artifacts and solver failures caused by:
    * Axis Crossing: The grinding path exceeding the tool's centerline, creating non-manifold 
       geoometry. 
    * Topological Errors: Prevented self-intersecting polygons and "degenerate triangles" occuring 
       where the bevel wrapped past the flute's physical limits.
    * Solver Conditioning: Avoided Jacobian singularities that occured at extreme acute angles between the wheel and the tool shaft.
2. Improved legibility with
 * better code blocks
 * better indentation

## Codeberg mirror set up and synced
1. Repository - https://codeberg.org/karlio/jig-visualizer
2. Live Site - https://karlio.codeberg.page/jig-visualizer/