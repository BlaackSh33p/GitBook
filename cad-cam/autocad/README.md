# AutoCAD

## Floor Plan Creation (AutoCAD – Simple Steps)

#### 1. Set Units

* Command: `UN` → Enter
* Units → **Architectural**
* Precision → `1/4`
* Insertion scale → **Inches**

***

#### 2. Draw Outer Rectangle (House Boundary)

* Command: `RECTANGLE`
* Click anywhere, then type:\
  `@50',31'` → Enter

***

#### 3. Draw Interior Walls (using Line tool)

* Command: `LINE`
* Track from corners (ensure **Object Snap → Extension** is ON).
* Example steps:
  * Up: `15'`
  * Right: `12'`
  * Down (etc.) … until boundaries complete.

***

#### 4. Add Wall Thickness (Offset)

* Command: `OFFSET`
* For exterior walls: `10"` offset outward.
* For interior walls: `6"` offset both directions.

***

#### 5. Clean Geometry

* Command: `TRIM`
* Remove overlapping/extra parts at intersections.

***

#### 6. Create Layers

* Open **Layer Properties**
* Add layers:
  * `Door` → Magenta
  * `Window` → Orange (or Blue)
  * `Blocks` → Cyan
  * `Hatches` → Green
  * `Text` → Yellow
  * `Dimension` → Pink

***

#### 7. Make Door Block

* Switch to **Door layer**.
* Draw rectangle: `@3",3'`
* Small rectangle: `@3",6"` → move to edge.
* Add arc (swing).
* Copy rectangle to other side.
* Mirror for variation.

***

#### 8. Make Window Blocks

**Window 1**:

* Rectangle: `@5',6"`
* Offset: `1"` → up/down
* Copy line to midpoint
* Trim middle

**Window 2**:

* Rectangle: `@6',10"`
* Explode
* Offset: `2"` both sides
* Copy line to midpoint
* Trim

***

#### 9. Convert to Blocks

* Command: `BLOCK`
* Name blocks:
  * `Door1`, `Door2`, `Window1`, `Window2`
* Pick point → select objects → OK

## Floor Plan – Part 2 (Adding Blocks, Hatches, Text)

***

#### 1. Insert Door Blocks

* Use **Copy tool** → place door block at wall.
* Use **Mirror** to flip as needed.
* Use **Rotate** to align with wall.
* Delete extra/unneeded copies.

***

#### 2. Insert Window Blocks

* Copy window block → rotate to correct angle.
* Place inside walls (consider 10" wall thickness).
* Copy/move to multiple wall positions.
* Erase original unused blocks.

***

#### 3. Clean Geometry

* Use **Trim** to remove overlapping lines after inserting doors/windows.

***

#### 4. Save Drawing

* Save file → name it **floorplan.dwg** (or similar).

***

#### 5. Use Design Center for Interior Blocks

* Open with **Ctrl+2** (or command: `ADCENTER`).
* Navigate to: `en-us → Design Center`.
* Common categories:
  * **House Designer → Blocks**: bathtub, toilet, sink.
  * **Home Space Planner → Blocks**: bed, dining set, lamp, plant, sofa.
  * **Kitchens → Blocks**: oven, refrigerator, double sink.
* Drag & drop blocks into drawing.
* Change to **Blocks layer** (if wrong color).

***

#### 6. Place Furniture Blocks

* **Bedroom**: bed + lamps + table.
* **Toilet**: toilet, bathtub, sink (rotate/move into position).
* **Dining**: dining set (rotate 90° if needed).
* **Living Room**: sofa, table (with fillet corners), TV, plants.
* **Kitchen**: sink, oven, fridge, slab made with Line + Trim.

***

#### 7. Add Hatches (Wall Thickness)

* Switch to **Hatch layer**.
* Command: `HATCH` → choose **Solid** pattern.
* Click inside wall cavities (each closed area).
* Close hatch creation.

***

#### 8. Add Text (Room Labels)

* Change **Text Style**:
  * Command: `STYLE` → Font: `simplex.shx`
  * Height: leave blank (set when placing).
* Layer: **Text**.
* Command: `MTEXT` → Draw text box → type room name.
* Set text height = `1'`.
* Place and copy text to each room.
* Edit each copy to match room:
  * Bedroom 1, Bedroom 2, Toilet, Drawing Room, Dining, Kitchen, Air Vent, etc.
* Rotate/move text if needed for proper orientation.

✅ By this point, drawing includes **doors, windows, furniture blocks, hatches, and text labels**.\
Next step (in Part 3): add **dimensions** + plotting.



[https://www.youtube.com/watch?v=2n5AIkPvIjA](https://www.youtube.com/watch?v=2n5AIkPvIjA)



***

### Part 3 – Simplified Steps (Point-Form)

#### 1. Add Dimensions

* Switch to the **Dimension** layer (pink).
* Use the **DIMLINEAR** command for straight measurements.
* Apply dimensions to key elements like wall lengths, room widths, etc.
* Adjust arrow size, text placement, or style if needed.

#### 2. Set Up Layout and Viewports (Paper Space)

* Switch to a **Layout tab** (e.g., Layout1).
* Insert a **Viewport** (command: `MV` or `MVIEW`) to display your model space drawing.
* Adjust viewport scale to fit your floor plan (e.g., ¼″ = 1′-0″ or similar architectural scale).
* Lock the viewport to avoid accidental zoom/move inside it.

#### 3. Add Title Block & Annotations

* Insert or draw a **title block** frame in paper space (with project details, scale, sheet number).
* Add annotation text for title, scale, author, date, etc., using **Text** layer formatting.

#### 4. Plot/Print Settings

* Open **Plot dialog** (click Plot icon or `PLOT` command).
* Select your **printer/plotter** or choose **"DWG to PDF"** for digital output.
* Choose correct **paper size** (e.g., A3, A2 depending on layout).
* Set **plot scale** to 1:1 (paper space handles scale via viewport).
* Check **plot window**: ensure layout includes all content.
* Preview → print or save as PDF.

***

#### Why These Steps Matter

This final phase ties all previous work—walls, blocks, hatches, text—into a **presentable floor plan**, ready for actual use or sharing.

### ✅ Adding Dimensions

1. **Open Dimension Style Manager**
   * `Annotate → Dimensions → Dimension Style Manager`
   * Copy an existing style (e.g., ISO-25 → New → Name it “DIM”).
2. **Modify the Style**
   * **Symbols & Arrows** → set arrowhead to **Architectural Tick**.
   * **Text** → text placement → **Centered**.
   * **Primary Units** → Unit Format → **Architectural**.
   * **Fit** → Adjust overall scale (e.g., 5) if dimensions look too small.
3. **Place Dimensions**
   * Switch to **DIM layer**.
   * Use **Linear Dimension (DIMLINEAR)** → click two points → place dimension.
   * Hide hatches temporarily if they block snap points (`Lightbulb icon → HATCH off`).
   * Add only **essential dimensions** (room sizes, wall lengths, openings, etc.) – avoid redundancy.
4. **Add Notes**
   *   Use **Text tool** → e.g.,

       ```
       Exterior wall thickness = 10”
       Interior wall thickness = 6”
       ```
   * Place text on the **TEXT layer** (not DIM).

***

### ✅ Preparing the Layout (Paper Space)

1. **Switch to Layout tab** (Layout1).
2. **Delete default viewport** (if any).
3. **Page Setup**
   * Right-click Layout → Page Setup Manager → Modify.
   * Printer: **DWG to PDF**.
   * Paper size: **ISO A3 (landscape)**.
   * Plot area: **Layout**.
   * Save & Close.

***

### ✅ Creating the Viewport

1. **Layout tab → Rectangular Viewport** → draw inside dotted plot margin.
2. Double-click inside viewport → set **scale** (e.g., 1:100, 1/4"=1'-0").
3. Lock viewport to prevent accidental zoom/pan.

***

### ✅ Plotting (Printing)

1. **Plot command (PLOT)**
   * If you want **colored output** → Plot style = **None**.
   * If you want **black & white** → Plot style = **Monochrome.ctb**.
2. Preview → Save as PDF.
   * e.g., `floorplan_color.pdf` or `floorplan_bw.pdf`.

***

👉 At this point you have a **dimensioned + properly scaled floor plan** in PDF, both in color and monochrome.
