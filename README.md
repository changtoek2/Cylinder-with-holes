# Cylinder-with-holes
import FreeCAD as App
import Part

# Create a new document
doc = App.newDocument("CylinderWithHoles")

# Cylinder parameters
radius_outer = 20  # Outer radius
radius_inner = 18  # Inner radius (for hollow effect)
height = 50        # Cylinder height
hole_radius = 3    # Radius of the holes
num_holes = 6      # Number of holes
hole_spacing = 60  # Spacing around the cylinder (in degrees)

# Create outer and inner cylinders
outer_cylinder = Part.makeCylinder(radius_outer, height)
inner_cylinder = Part.makeCylinder(radius_inner, height)

# Subtract inner from outer to make it hollow
hollow_cylinder = outer_cylinder.cut(inner_cylinder)

# Create holes
holes = []
for i in range(num_holes):
    angle = i * hole_spacing  # Calculate angle
    x = radius_outer * App.cos(App.Units.Quantity(f"{angle} deg"))  # X position
    y = radius_outer * App.sin(App.Units.Quantity(f"{angle} deg"))  # Y position
    
    hole = Part.makeCylinder(hole_radius, height + 10, App.Vector(x, y, -5), App.Vector(0, 0, 1))
    holes.append(hole)

# Cut the holes from the hollow cylinder
for hole in holes:
    hollow_cylinder = hollow_cylinder.cut(hole)

# Add final part to the document
part_obj = doc.addObject("Part::Feature", "OpenCylinderWithHoles")
part_obj.Shape = hollow_cylinder

# Recompute and refresh view
doc.recompute()
