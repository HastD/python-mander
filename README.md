# mander
Python package for calculating metrics related to district shapes.

# Installation

Requires [GDAL](http://www.gdal.org/) (`brew install gdal` on Mac OS X with Homebrew).

Then:
```
pip install mander
```

# Usage

```python
from mander.districts import District
from mander.metrics import calculatePolsbyPopper

# Load a district from a GeoJSON or SHP file path
district = District(path='CD_CA_9.geojson')

# Call a metrics function on the District class object
score = calculatePolsbyPopper(district)
```
## Assumptions
The package will accept any projection, but all districts are converted to [US National Atlas Equal Area](http://spatialreference.org/ref/epsg/2163/). Equal area projections are best for calculating compactness metrics. This ensures that when calculating area and perimeter measures, each district has a minimal amount of distortion, so districts in different parts of the country can be accurately compared to one another. Therefore, this shouldn't be used for districts outside of the U.S. Future versions will accept local projections.

## Compactness Metrics

### `metrics.calculatePolsbyPopper`
The Polsby-Popper measure is a ratio of the area of the district to the area of a circle whose circumference is equal to the perimeter of the district.

The formula for calculating the Polsby-Popper score is:  
![polsby popper formula](https://github.com/cicero-data/compactness-stats/raw/master/img/polsby-popper-formula.png)  
where A is the area of the district and p is the perimeter of the district.

---

### `metrics.calculateConvexHull`
The Area/Convex Hull score is a ratio of the area of the district to the area of the minimum convex polygon that can enclose the district's geometry.  

With convex hull polygons generated, the ratio can be calculated using the formula:
![convexhull formula](https://github.com/cicero-data/compactness-stats/raw/master/img/convexhull-formula.png)  
where A is the area of the district.

Note: this does not compute the calculation on each multipolygon feature in the case of districts containing islands. See [issue #9](https://github.com/gerrymandr/python-mander/issues/9).

---

### `metrics.calculateReock`
The Reock score is a measure of the ratio of the district to the area of the minimum bounding circle that encloses the district's geometry.  
![reock formula](https://github.com/cicero-data/compactness-stats/raw/master/img/reock-formula.png)  
where A is the area of the district.

Note: this does not compute the calculation on each multipolygon feature in the case of districts containing islands. See [issue #10](https://github.com/gerrymandr/python-mander/issues/10).

---

### `metrics.calculateSchwartzberg`
The Schwartzberg score is a ratio of the perimeter of the district to the circumference of a circle whose area is equal to the area of the district.

To generate the Schwartzberg score, first the circumference of a circle with an equal area of the district must be calculated. To do so, use the formula:

![schwartzberg formula 1](https://github.com/cicero-data/compactness-stats/raw/master/img/schwartzberg-formula-1.png)

Formula to generate circumference of a circle

where A is the area of the district and r is the radius. With the radius calculated, use the following formula to generate the circumference (perimeter):

![schwartzberg formula 2](https://github.com/cicero-data/compactness-stats/raw/master/img/schwartzberg-formula-2.png)

Finally, generate the Schwartzberg score using the following ratio:

![schwartzberg formula 3](https://github.com/cicero-data/compactness-stats/raw/master/img/schwartzberg-formula-3.png?raw=true)

where P is the perimeter of the district and C is the circumference (perimeter) of the circle with the same area.

## Add Compactness Scores to GeoJSON

### `metrics.scoresToGeojson(District, metricName, path_out=None)`

Add compactness scores as a property on GeoJSON features.

`metricName` should be one of:

- `'polsbypopper'`
- `'schwarzberg'`
- `'convex_hull'`
- `'reock'`

Return JSON object. Optionally, write to a GeoJSon file with `path_out`.

# Testing

To run the [metrics](test/metrics.py) test case:

```
python -m unittest test.metrics
```
