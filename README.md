# Alembic Export

## Dealing with Packed Primitive

Export packed primitive can: 
- export transformation as xform node in alembic hierarchy
  - check `Packed transform` setting
  - `deform` baked transform into mesh, no xform data
  - `transform geometry` and `merge with parent transform` xform data is exported but the difference between the two is where in the hierarchy (path attribute) xform node get attached to
- instancing
- you can disable `Create Shape Nodes` to export only xform data without any mesh

## Export Additional uv attributes

- include them in `Addtional uv Attributes` setting on rop alembic output node

## Export for Maya

### Missing uv, normal

- sometimes happens when exporting fractured geometry
- when importing it into Maya you may got messages in script editor `Warning: normal vector scope does not match size of data, skipping normal`
- Fix:
  - vertex split all (disable limit by attributes. Same as unique point). Promote uv, N to vertex
  - another solution: on boolean node, set `Detriangulate` to "No Polygon"

### Export extra attributes as vertex color

- need to be vertex atribute of type color
- you can use attribute create node to set type info:
  - `Existing Name` replace existing attribute
  - `Type` float, color
  - `Size` 3
  - `Write Values` off
- or use vex
  ```c
  int  setattribtypeinfo(int geohandle, string attribclass, string name, string typeinfo)
  ```

### Export transformation data

- export packed primitives

# Working with Multiple UV sets

- Convert the attributes to float size 3 with type "Texture Coordinate" to make them show up on uv viewport
- You can choose which uv set is used for texture preview by switching to uv viewport and select uv. Or apply a material and use `OpenGL > Diffuse Texture Layers` (Edit Parameter Interface) to set uv set


# POP simulation

## Settings if plan to retime to slow down later

- negative jitter birth time
- disable reap at frame end

This way you have extra data just before particle birth and death so retiming does not flicker too much at birth and have no frozen points before death. 
Have to delete `age<0` and `dead==1` manually after retime
