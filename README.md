# TOPOJSON

[Work in Progress]. This repository describes the development a Python implementation of TopoJSON based on https://bost.ocks.org/mike/topology/ and https://github.com/topojson.

The currently implemented method deviates (till know) on at least four points from the JavaScript implementation by Mike Bostocks.
1. The extraction class stores all the different geometrical objects as shapely LineStrings in `'linestrings'` and keeps a record of these linestrings available under the key `'bookkeeping_geoms'`. In the JavaScript implementation there is a differentiation of the geometries between `'lines'`, `'rings'` and a seperate object containing all `'coordinates'`. Since the current approach adopts `shapely` for much of the heavy lifting this extraction is working against us (in the cut-process).
2. In the join class only the geometries that have shared paths are considered to have junctions. This means that the intersection of two crossing lines at a single coordinate is not considered as a junction. This also means that the two ends of a LineString are not automatically considered as being a junction. So if a segment starts or finish on another segment with that coordinate being the only coordinate in common it is not considered as a junction.
3. In the computation of a shared path, a junction can be created on a existing coordinate in one of the geometries. Where in the JavaScript implementation this only can be considered when both geometries contain the coordinate. This is maybe best explained given an example. Geometry 1 is a horizontal LineString from three coordinates [(0, 0), (3, 0) (5, 0)], geometry 2 is also a horizontal LineString of three coordinates [(0, 0), (3, 0), (10, 0)]. Coordinate (5, 0) is not included in geometry 2, but is considered a junction as it is computed as shared path.
4. In the process of cutting lines the rings are rotated in the JavaScript implementation to make sure they start at a junction. This reduces the number of cuts. This rotation is done before cutting. In the current Python implementation this is done differently. First the linestrings are cut using the junction coordinates and afterwards it is tried to apply a linemerge on the non-duplicate arcs of a geometry containing at least one shared arc.

Some points to consider:
In the JS-implementation a closed LineString ABCA contains a junction in A, but a closed Polygon ABCA contains no junction in A. In the current implementation in both situations there won't be a junction in A. Moreover Polygons and LineStrings are both considered as being LineStrings (Polygons are splitted in exterior and interiors LineStrings).

Installation of the python module `rtree` depends on the C++ library `libspatialindex`. If not installed, install by:

```bash
brew install spatialindex
```