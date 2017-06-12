## GeoJSON-T
This proposed extension to the GeoJSON format standard tries to accommodate the fact that many geographic features are "event-like" (e.g. crimes, tweets, and journeys) or otherwise inherently temporal (e.g. political boundaries, flows, or anything else that changes position or shape over time). In fact **__all__** geographic features have temporal attributes, whether or not we have that data or use them for a particular application. Likewise, many temporal things are inherently spatial (e.g. historical periods like _Bronze Age Britain_; see [the PeriodO project](http//perio.do), [Pleiades Period Vocabulary](http://pleiades.stoa.org/vocabularies/time-periods)). All events occur somewhere, with a spatial footprint we might like to map or analyze.

Like GeoJSON and the emerging GeoJSON-LD, GeoJSON-T represents collections of geographic features in a **FeatureCollection**, and its **geometry** element handles spatial attributes identically.

But unlike GeoJSON, a **Feature** in GeoJSON-T can be either an essentially spatial thing (a place) or temporal thing (period or events of any complexity). This difference requires extending the GeoJSON format in a few ways:


### Adding time

A GeoJSON-T **Feature** can have optional temporal attributes, in one or more **when** elements

If a **Feature** has a single geometry, a single **when** temporal element can be added as a sibling to GeoJSON's standard **type**, **geometry**, and **properties** elements 

<pre>{
"type": "FeatureCollection",
"features": [
{
	 "type": "Feature",
	 "id": "",
	 "properties": {},
	 "geometry": {},
	 <strong><span style="color: #ff0000;">"when": {}</span></strong>
	 }
]
}</pre>

If a **Feature** had multiple positions or extents over time, its **geometry** will be of type **GeometryCollection** -- an array of one or more **geometry** objects. Each of these can have a single **when** temporal element, along with the standard **type** and **coordinates** elements 

<pre>"geometry": {
 "type": "GeometryCollection",
 "geometries": [
   {
    "type": "LineString",
    "coordinates": [[93.867,40.35],[108.9423,34.26]],
    <strong><span style="color: #ff0000;">"when": {}</span></strong>,
    "properties": {}
   }
 ]
}</pre>

Each **geometry** in a **GeometryCollection** can have an unlimited number of **properties**. Although these are outside the GeoJSON specification, like **when** elements, they will be ignored by existing GeoJSON-compatible software.

### The structure of "when"
_For example_:

<pre>"when": {
  "timespans": [["-323-01-01 ","","","-101-12-31",
     "Hellenistic period"]],
  "duration": "?",
  "periods": [
   {
    "name": "Hellenistic Period",
    "period_uri": " http://n2t.net/ark:/99152/p0mn2ndq6bv"
   }
  ],
  "follows": "feature_00123",
}</pre>


The **when** object in a GeoJSON-T **Feature** (or in a geometry object within a Feature's **GeometryCollection**) is comprised of:

- a set of one or more **timespans**, specified by a 5-part array, the positions of which correspond to [start, latest-start, earliest-end, end, label]. Values for the first four can be any valid ISO-8601 expression, e.g. YYYY-MM-DD, YYYY-MM or YYYY; by convention, years preceded by a minus (-) are interpreted as BCE.

- a **duration** property whose value can either be
	- null (indicating the phenomena occurred or was valid _**throughout**_ the timespan(s); or
	- an integer followed by a single letter (d, m, y for day, month, years respectively) indicating the phenomena occurred for that duration _**some time during**_ the timespan
	- a "?" indicating the phenomena occurred for an unknown duration _**some time during**_ the timespan

- an optional **follows** property, whose value can be an internal identifier for the preceding segment of an ordered set


An outstanding issue is whether to permit a **_circa_** operator for the four temporal expressions in timespans. For example: **~1635-01** could stand for 'circa January, 1653' -- with the question of how to represent that visually or in computation left to individual software applications.

## Please comment
I'd like to move the development of GeoJSON-T into a more formal process, but perhaps that should follow more informal discussion. Please comment as [an Issue in this repository](https://github.com/kgeographer/topotime/issues).
