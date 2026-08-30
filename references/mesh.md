# SU2 native mesh

An ASCII `.su2` mesh is ordered as dimension, points, interior elements, then marker blocks:

```text
NDIME= <2|3>
NPOIN= <count>
<coordinates>
NELEM= <count>
<interior elements>
NMARK= <count>
<marker blocks>
```

- Point indices are zero-based and follow the `NPOIN` order. Points are `x y` in 2D and `x y z` in 3D; legacy files may append a redundant index.
- Element records are `<VTK type> <point index> ...`.

| VTK type | Element | Points |
|---:|---|---:|
| `5` | triangle | 3 |
| `9` | quadrilateral | 4 |
| `10` | tetrahedron | 4 |
| `12` | hexahedron | 8 |
| `13` | prism | 6 |
| `14` | pyramid | 5 |

## Marker block

```text
MARKER_TAG= <name>
MARKER_ELEMS= <count>
<boundary elements>
```

`MARKER_TAG` is the exact, case-sensitive name used by `.cfg` boundary conditions. Boundary elements use the same record form and global point indices: normally lines (`3`) in 2D and triangles (`5`) or quadrilaterals (`9`) in 3D.
