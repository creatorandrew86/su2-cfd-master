# SU2 Native Mesh File Structure

An ASCII `.su2` file is an ordered sequence of dimension, point, interior-element, and boundary-marker sections:

```text
NDIME= <2 or 3>
NPOIN= <point count>
<point coordinates>
NELEM= <interior-element count>
<interior elements>
NMARK= <marker count>
<marker blocks>
```

All point indices are zero-based and assigned by their order in the `NPOIN` section. A 2D point has `x y`; a 3D point has `x y z`. Legacy files may append a redundant point index.

## Interior elements

After `NELEM=`, each record is:

```text
<VTK type> <point index> ...
```

Common VTK type identifiers are:

| ID | Element | Points |
|---:|---|---:|
| `5` | triangle | 3 |
| `9` | quadrilateral | 4 |
| `10` | tetrahedron | 4 |
| `12` | hexahedron | 8 |
| `13` | prism | 6 |
| `14` | pyramid | 5 |

## Boundary markers

`NMARK` gives the number of marker blocks. Each block has this structure:

```text
MARKER_TAG= <marker name>
MARKER_ELEMS= <boundary-element count>
<boundary element 0>
...
<boundary element count-1>
```

The `MARKER_TAG` value is the exact boundary-marker name used for boundary conditions. Marker names are case-sensitive. Boundary records use the same format as interior elements and reference the global point indices:

```text
<VTK type> <point index> ...
```

For 2D meshes, boundary elements are normally lines (`3`, two points). For 3D meshes, they are normally triangles (`5`, three points) or quadrilaterals (`9`, four points).

Example:

```text
NMARK= 2
MARKER_TAG= inlet
MARKER_ELEMS= 1
3 0 1
MARKER_TAG= wall
MARKER_ELEMS= 2
3 1 2
3 2 3
```
