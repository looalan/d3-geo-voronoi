# d3-geo-voronoi

This module wraps d3 around Loren Petrich's [Spherical Delaunay triangulation library](http://lpetrich.org/Science/GeometryDemo/GeometryDemo_GMap.html), following as closely as possible the API of the [d3-voronoi](https://github.com/d3/d3-voronoi/) module.

Given a set of objects in spherical coordinates, it computes their Delaunay triangulation and its dual, the Voronoi diagram ([d3 issue #1820](https://github.com/d3/d3/issues/1820)).

In addition, it offers convenience methods to extract the convex hull, the Urquhart graph, the circumcenters of the Delaunay triangles, and to find the cell that contains any given point on the sphere.


## Installing

If you use NPM, `npm install d3-geo-voronoi`. Otherwise, download the [latest release](https://unpkg.com/d3-geo-voronoi).


## API Reference

<a href="#geo-voronoi" name="geo-voronoi">#</a> d3.<b>geoVoronoi</b>([data])
[<>](https://github.com/Fil/d3-geo-voronoi/blob/master/src/geoVoronoi.js "Source")

Creates a new *spherical* Voronoi layout. _data_ can be passed as an array of [lon, lat] coordinates, an array of GeoJSON features, or a GeoJSON FeatureCollection.

The following methods are similar to [d3-voronoi](https://github.com/d3/d3-voronoi/)'s methods:

<a href="#geo_voronoi_x" name="geo_voronoi_x">#</a> <i>voronoi</i>.<b>x</b>([<i>x</i>])

Sets or returns the _x_ accessor. The default _x_ and _y_ accessors are smart enough to recognize GeoJSON objects and return the geoCentroid of each feature.

<a href="#geo_voronoi_y" name="geo_voronoi_y">#</a> <i>voronoi</i>.<b>y</b>([<i>y</i>])

Sets or returns the _y_ accessor.

[![](img/geoVoronoiXY.png)](http://bl.ocks.org/Fil/74295d9ffe097ae4e3c93d7d00377d45)

<a href="#geo_voronoi_polygons" name="geo_voronoi_polygons">#</a> <i>voronoi</i>.<b>polygons</b>(<i>[data]</i>)

Returns the Voronoi tesselation of the data as a GeoJSON collection of polygons. (If there is only one data point, returns the Sphere). Each polygon exposes its datum in its properties.

[![](img/geoVoronoiPolygons.png)](http://bl.ocks.org/Fil/a9ba8d0d023752aa580bd95480b7de60)

<a href="#geo_voronoi_triangles" name="geo_voronoi_triangles">#</a> <i>voronoi</i>.<b>triangles</b>(<i>[data]</i>)

Returns the spherical Delaunay triangulation of the data as a GeoJSON collection of polygons. Each triangle exposes in its properties the three sites, its spherical area (in steradians), and its circumcenter.


[![](img/geoVoronoiTriangles.png)](http://bl.ocks.org/Fil/b1ef96e4bc991eb274f8d3a0a08932f9)

[![](img/geoVoronoiRadome.png)](http://bl.ocks.org/Fil/955da86d6a935b26d3599ca5e344fb38)


<a href="#geo_voronoi_links" name="geo_voronoi_links">#</a> <i>voronoi</i>.<b>links</b>(<i>[data]</i>)

Returns the Delaunay links of the data as a GeoJSON collection of lines. Each line exposes its source and target in its properties, but also its length (in radians), and a boolean flag for links that belong to the [Urquhart graph](https://en.wikipedia.org/wiki/Urquhart_graph).

[![](img/geoVoronoiUrquhart.png)](http://bl.ocks.org/Fil/1a78acf8b9b40fe8ecbae7b5035acf2b)


[![](img/geoVoronoiCircumcircles.png)](http://bl.ocks.org/Fil/79b9f17979c4070dee3cbba1c5283502)


[![](img/geoVoronoiMars.png)](http://bl.ocks.org/Fil/1c2f954201523af16280db018ddd90cc)


<i>voronoi</i>.<b>extent</b>(<i>[extent]</i>) and <i>voronoi</i>.<b>size</b>(<i>[size]</i>) are not implemented.

Indeed, defining the “paper extent” of the geoVoronoi polygons can be quite tricky, [as this block demonstrates](https://bl.ocks.org/Fil/6128aae082c04eef06422f953d0f593f).


The following new methods are introduced:

<a name="geo_voronoi_find" href="#geo_voronoi_find">#</a> <i>voronoi</i>.<b>find</b>(<i>x,y,[angle]</i>)

Finds the closest site to point *x,y*, i.e. the Voronoi polygon that contains it. Optionally, return null if the distance between the point and the site is larger than *angle* radians.

[![](img/geoVoronoiFind.png)](http://bl.ocks.org/Fil/e94fc45f5ed4dbcc989be1e52b797fdd)


<a name="geo_voronoi_hull" href="#geo_voronoi_hull">#</a> <i>voronoi</i>.<b>hull</b>(<i>data</i>)

Returns the spherical convex hull of the *data* array, as a GeoJSON polygon. Returns null if the dataset spans more than a hemisphere. Equivalent to:

```js
voronoi(data).hull();
```

[![](img/geoVoronoiHull.png)](http://bl.ocks.org/Fil/6a1ed09f6e5648a5451cb130f2b13d20)

_Note: there might be a better way to compute the geoHull, and this should probably be part of d3-geo. This method is experimental and may be removed from the API._


### Other projections

There is no reason to limit the display of Voronoi cells to the orthographic projection. The example below displays the Urquhart graph of top container ports on a Winkel tripel map.

[![](img/geoVoronoiPorts.png)](http://bl.ocks.org/Fil/24d5ee71f09ba72893323d803242c38a)



### Comparison with planar Voronoi Diagrams

- geoVoronoi is for points on the sphere, `d3-voronoi` is for points on a plane (or possibly a [torus](http://bl.ocks.org/Fil/c1b10942f61483d739dd601d09c30deb) or a [cylinder](http://bl.ocks.org/Fil/4639744e8be5428e7a8e7b3efd9a80dc)).    

- geoVoronoi uses a different algorithm (in `O(n^2)`, which is [much slower](https://github.com/Fil/d3-geo-voronoi/issues/1) as the number of sites grows past 1000) -- and its internal data structure is different. 

- geoVoronoi returns GeoJSON objects, which are often `FeatureCollections`. By consequence, you will have to change `.data(voronoi.polygons())` to `.data(geovoronoi.polygons().features)`, and so on.

- geoVoronoi offers methods to compute the [convex hull](#geo_voronoi_hull), the [Urquhart graph](#geo_voronoi_links). These can be achieved with the planar Voronoi ([hull](http://bl.ocks.org/mbostock/6f14f7b7f267a85f7cdc), [Urquhart](http://bl.ocks.org/Fil/df20827f817abd161c768fa18dcafcf5), but are not part of d3-voronoi.

