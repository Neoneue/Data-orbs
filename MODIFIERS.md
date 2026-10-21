# Using and adjusting Data Orbs

How each orb is built, what every setting does, and how to change them by hand or from Python. Numbers in the **Now** column are the values saved in `data-orbs.blend`. Lengths are in metres; each orb is about 1 m across the radius.

## Switch between the orbs

The scene holds both orbs, one per collection. In the **Outliner**, tick the checkbox next to the collection you want and untick the other:

- `Data Orb` (on when the file opens)
- `Sphere Lines`

The background colour and the camera focus follow whichever orb is on.

## Render

- **Engine:** Cycles, 1920 x 1080, 540 samples with denoising, frames 1 to 288.
- **Still:** press **F12**. **Animation:** **Ctrl+F12** (Cmd+F12 on macOS). Frames are written to `renders/frames/` next to the .blend file.
- **Depth of field:** the camera (`zz_cam`, 50 mm, f/1.4) focuses on the front of the active orb, so the near side is sharp and the back is slightly soft.

**Before your first render, two settings worth knowing:**

1. **Device.** The scene asks for the GPU. If no GPU is set up in **Edit > Preferences > System > Cycles Render Devices**, Cycles quietly uses the CPU. For a quick test, lower **Render Properties > Sampling > Render > Max Samples** (64 to 128 is plenty for a preview).
2. **Viewport.** Viewport sampling is also set to 540. If **Rendered** shading in the 3D viewport feels slow, lower **Sampling > Viewport > Max Samples**, or use **Material Preview** to look around.

## Where the settings live

| What | Where in Blender |
| --- | --- |
| Shape, layers, motion | Select the orb object > **Modifier** tab (wrench) > its Geometry Nodes modifier |
| Colours, glow, far side dim | Select the orb object > **Material** tab > **Surface** |
| Background colour | **Shader Editor** set to **World** > node `zz_orb_background` |
| Bloom and chromatic aberration | **Compositor** > nodes `Glare` and `Lens Distortion` (shared by both orbs) |
| Overall size | Object **Scale** (Data Orb is at 0.675, Sphere Lines at 1.0) |
| Camera focus | Empty `zz_dof_focus`, placed by a driver (see [Camera focus](#camera-focus)) |

| Orb | Object | Modifier | Material |
| --- | --- | --- | --- |
| Data Orb | `zz_data_orb` | `zz_data_orb` | `zz_data_orb_glow` |
| Sphere Lines | `zz_sphere_lines` | `Sphere Lines` | `zz_sphere_lines_glow` |

The heaviest settings, if the viewport slows down: Sphere Lines **Line Density** and **Steps**; Data Orb **Core Detail** and a small **Dot Spacing**.

---

## Data Orb

Stacked layers around a centre sphere, all made of glowing lines, dots and see-through panels. Each layer has its own **Show** switch, so you can solo or remove any of them.

### Whole orb

| Setting | Now | Range | What it does |
| --- | --- | --- | --- |
| Radius | 1.0 | 0.01 to 100 | Size of every layer together |
| Seed | 0 | any | New random layout for every layer; same look, different arrangement |
| Flow Speed | 0.05 | 0 to 10 | How fast the noise morphs the layers over time |
| Loop Seconds | 12 | 0.1 to 1000 | Length of the seamless loop. The spin is keyed separately (one Z turn over frames 1 to 289), so if you change this, change the frame range and that rotation key to match |
| Line Radius | 0.0007 | 0 to 1 | Thickness of every line (0.7 mm) |
| Dot Radius | 0.0024 | 0 to 1 | Size of the dots along dotted edges and rays |
| Vertex Dot Radius | 0.0045 | 0 to 1 | Size of the bigger dots on geodesic corners |
| Dot Spacing | 0.014 | 0.001 to 1 | Gap between dots on a dotted line. Smaller = denser and heavier |

### Geodesic belts

Tilted bands of dotted geodesic edges with corner dots, four shells at different sizes and angles.

| Setting | Now | Range | What it does |
| --- | --- | --- | --- |
| Show Geodesic Belts | on | | Layer on/off |
| Geodesic Holes | 0.25 | -0.4 to 0.4 | Higher = more gaps cut by noise; negative fills the bands in |
| Geodesic Radius | 0.76 | 0.2 to 2 | Size of the belts relative to the orb |

### Voronoi webs

Three shells of Voronoi cells, outlined, with faint see-through panel fills.

| Setting | Now | Range | What it does |
| --- | --- | --- | --- |
| Show Voronoi Webs | on | | Layer on/off |
| Web Holes | 0.05 | -0.4 to 0.4 | Higher = more missing cells |
| Web Spread | 0 | 0 to 4 | Pushes cells outward by noise, more toward one side of the orb; 0 keeps the shells clean |
| Web Panel Fill | 0.05 | 0 to 1 | Brightness of the see-through cell fills |
| Web Fill Patterns | 0.3 | 0 to 1 | Share of panels that get a hatch, dot or solid pattern |
| Web Pattern Size | 0.05 | 0.001 to 1 | Spacing of the hatch lines and dots |
| Web Pattern Brightness | 0.4 | 0 to 2 | Brightness of those patterns |

### Dotted rays

| Setting | Now | What it does |
| --- | --- | --- |
| Show Dotted Rays | on | Short dotted lines pointing out from the orb, on/off |

### Inner blobs

Wireframe blobs inside the orb, off by default.

| Setting | Now | Range | What it does |
| --- | --- | --- | --- |
| Show Inner Blobs | off | | Layer on/off |
| Blob Amount | 1.0 | 0 to 4 | How far the blobs bulge in and out |

### Centre sphere

Voronoi panels on a sphere in the middle: inset outlines, corner dots, fills and fill patterns.

| Setting | Now | Range | What it does |
| --- | --- | --- | --- |
| Show Centre Sphere | on | | Layer on/off |
| Core Radius | 0.6 | 0.2 to 1.2 | Size of the centre sphere |
| Core Detail | 3 | 1 to 8 | Number of panels: Detail squared x 30 (3 = 270 panels, 5 = 750) |
| Core Panels | 0.7 | 0 to 1 | Share of panels shown. 1 = a full sphere |
| Core Pattern Scale | 2.2 | 0.2 to 8 | Scale of the noise that picks which panels show. Higher = smaller, busier clusters |
| Core Randomness | 0.4 | 0 to 1 | 0 = shown panels form smooth clusters, 1 = scattered at random |
| Core Panel Gap | 0.06 | 0 to 0.4 | Inset of each panel's outline (the gap between neighbours) |
| Core Panel Fill | 0.05 | 0 to 1 | Brightness of the see-through panel fills |
| Core Fill Patterns | 0.3 | 0 to 1 | Share of panels with a hatch, dot or solid pattern |
| Core Pattern Size | 0.03 | 0.001 to 1 | Spacing of those patterns |
| Core Pattern Brightness | 0.4 | 0 to 2 | Brightness of those patterns |

### Dust

| Setting | Now | Range | What it does |
| --- | --- | --- | --- |
| Show Dust | on | | Floating particles around the orb, on/off |
| Dust Drift | 0.06 | 0 to 0.4 | How far each particle wanders |
| Dust Drift Speed | 0.35 | 0 to 3 | How fast they wander |

**Material** (last input): the material the modifier assigns. Leave it on `zz_data_orb_glow`.

---

## Sphere Lines

Flow lines traced across a noise field on a sphere, plus glitter points scattered from the lines.

### Lines

| Setting | Now | Range | What it does |
| --- | --- | --- | --- |
| Radius | 1.0 | 0.01 to 100 | Size of the sphere |
| Line Density | 20 | 0.1 to 500 | How many lines start per unit of surface. Heavy above about 40 |
| Steps | 220 | 2 to 2000 | Points per line. More = longer lines |
| Step Length | 0.014 | 0.0001 to 1 | Distance per step. Line length = Steps x Step Length |
| Line Radius | 0.0011 | 0 to 1 | Thickness of every line |

### Noise field (the shape of the swirls)

| Setting | Now | Range | What it does |
| --- | --- | --- | --- |
| Noise Scale | 1.1 | 0 to 50 | Higher = smaller, tighter swirls |
| Noise Detail | 1.5 | 0 to 15 | Extra layers of fine noise. Higher = wigglier lines |
| Warp Strength | 0.7 | 0 to 10 | How much a second noise bends the first. Higher = more marbled |
| Warp Scale | 0.8 | 0 to 50 | Size of that bending noise |
| Seed | 0 | any | New random field; same style, different pattern |
| Flow Speed | 0.04 | -10 to 10 | How fast the field morphs over the loop. Negative runs it the other way |

### Glitter

| Setting | Now | Range | What it does |
| --- | --- | --- | --- |
| Glitter Keep | 0.45 | 0 to 1 | Share of glitter points kept along the lines |
| Glitter Radius | 0.002 | 0 to 1 | Size of each point |
| Glitter Spill | 0.39 | 0 to 5 | How far points fly off the surface. Most stay close; a rare few go far |
| Glitter Drift | 0.05 | 0 or more | How far each point wanders |
| Glitter Drift Speed | 0.06 | 0 or more | How fast they wander |

**Material**: leave it on `zz_sphere_lines_glow`.

---

## Look (both orbs)

### Material: Surface

| Setting | Data Orb | Sphere Lines | What it does |
| --- | --- | --- | --- |
| Colour 1 | dark navy | deep blue | Darkest colour of the gradient |
| Colour 2 | blue | blue | Middle colour |
| Colour 3 | pale blue | pale blue | Brightest colour, on the hottest lines and dots |
| Glow | 2.0 (0 to 6) | 10.0 (0 to 30) | Emission strength. The two scales differ, so 2 on Data Orb looks about like 10 on Sphere Lines |
| Far Side Dim | 0.22 | 0.22 | How much the back half fades. 0 = no fade |

### World background

The node `zz_orb_background` mixes two colours and follows the collection checkboxes: input **A** is the Sphere Lines background (`#030b16`), input **B** the Data Orb background (`#000d16`). Change the colour on the one you want.

### Compositor

- **Glare** (Bloom): Strength 0.8, Threshold 1.0. Raise Strength for a softer, bigger glow.
- **Lens Distortion**: Dispersion 0.125 is the chromatic aberration (colour fringing at the edges). 0 turns it off.

### Camera focus

The camera (`zz_cam`, 50 mm, f/1.4) focuses on the empty `zz_dof_focus`. A driver moves it to Y = -1.0 when Sphere Lines is on (the front of a radius 1 sphere), or to Y = -0.405 when Data Orb is on (the front of its centre sphere: Core Radius 0.6 x object scale 0.675). If you change Radius, Core Radius or the object scale, update those two numbers in the driver on `zz_dof_focus` > Location Y, or the front of the orb goes soft.

---

## Starting points for new looks

These are suggestions to start from, not tested presets. Change one or two settings, look in **Material Preview**, then render.

- **Clean wireframe Data Orb:** Show Voronoi Webs off, Show Dust off, Core Panels 0.35, Core Panel Fill 0, Glow 1.5.
- **Dense tech Data Orb:** Core Detail 5, Core Panels 1.0, Core Fill Patterns 0.6, Web Fill Patterns 0.6, Web Holes -0.1.
- **Tighter Sphere Lines:** Noise Scale 2.0, Noise Detail 2.5, Line Density 30, Steps 160.
- **Calm Sphere Lines:** Warp Strength 0.2, Flow Speed 0.02, Glitter Keep 0.15.
- **New palette:** set Colour 1, 2, 3 dark to light in one hue (for example deep red, orange, pale yellow), then match the world background to a near-black version of Colour 1.

---

## For scripts and agents (Blender 5.2 Python)

In Blender 5.2, Geometry Nodes modifier inputs are read and written through `modifier.properties.inputs.<identifier>.value`. Old-style `modifier["Socket_2"]` access does not work on this file. Look inputs up by their visible name:

```python
import bpy

def set_input(obj_name, name, value):
    """Set a Geometry Nodes modifier input by its visible name, e.g. "Core Detail"."""
    obj = bpy.data.objects[obj_name]
    mod = next(m for m in obj.modifiers if m.type == "NODES")
    item = next(i for i in mod.node_group.interface.items_tree
                if getattr(i, "in_out", "") == "INPUT" and i.name == name)
    getattr(mod.properties.inputs, item.identifier).value = value
    obj.update_tag()

def set_look(obj_name, name, value):
    """Set a material input: "Colour 1".."Colour 3" (RGBA, linear), "Glow", "Far Side Dim"."""
    mat = bpy.data.objects[obj_name].material_slots[0].material
    group = next(n for n in mat.node_tree.nodes if n.type == "GROUP")
    group.inputs[name].default_value = value

set_input("zz_data_orb", "Core Detail", 4)
set_input("zz_data_orb", "Show Dust", False)
set_look("zz_data_orb", "Glow", 3.0)
set_look("zz_sphere_lines", "Colour 2", (1.0, 0.2, 0.05, 1.0))

# switch orbs: exclude one collection, include the other
for lc in bpy.context.view_layer.layer_collection.children:
    lc.exclude = lc.name != "Data Orb"

bpy.ops.wm.save_mainfile()
```

Notes for agents:

- Input names are exactly as in the tables above, case and spaces included.
- Colours in Python are linear RGBA, not hex. Convert sRGB hex first (channel / 255, then the sRGB-to-linear curve).
- Read an input back with `getattr(mod.properties.inputs, item.identifier).value`.
- Bool inputs take `True` / `False`, Core Detail takes an int, everything else a float.
- To check a change without rendering, evaluate the mesh: `obj.evaluated_get(bpy.context.evaluated_depsgraph_get()).to_mesh()`, then count `polygons`, and call `to_mesh_clear()` when done.
