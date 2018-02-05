## GeoJSON-LDT
This proposed extension to the GeoJSON-LD format tries to accommodate the fact that many geographic features are "event-like" (e.g. crimes, tweets,  journeys) or otherwise inherently temporal (e.g. political boundaries, flows, or anything else that changes position or shape over time). In fact **__all__** geographic features have temporal attributes, whether or not we have that data or use them for a particular application. Likewise, many temporal things are inherently spatial (e.g. historical periods like _Bronze Age Britain_; see [the PeriodO project](http//perio.do), [Pleiades Period Vocabulary](http://pleiades.stoa.org/vocabularies/time-periods)). All events occur somewhere, with a spatial footprint we might like to map or analyze.

Like the [GeoJSON standard]() (RFC 7946) and its [GeoJSON-LD](http://geojson.org/geojson-ld/) cousin, GeoJSON-LDT represents collections of geographic features in a **FeatureCollection**, and its **geometry** element handles spatial attributes identically. Any software that interprests GeoJSON will interpret GeoJSON-LDT, simply ignoring certain of its elements.

But unlike GeoJSON, a **Feature** in GeoJSON-LDT can be either an essentially spatial thing (a place) or temporal thing (period or events of any complexity). This difference requires extending the GeoJSON format in a few ways:


### Adding time

A GeoJSON-LDT **Feature** can have optional temporal attributes, in one or more **when** elements

If a **Feature** has a single geometry, a single **when** temporal element can be added as a sibling to GeoJSON's standard **type**, **geometry**, and **properties** elements. Note that these key values now aliases to specified in the [GeoJSON-LD context file] (http://geojson.org/geojson-ld/) indicated.

<pre>{

"type": "FeatureCollection",
"@context": "http://geojson.org/geojson-ld/geojson-context.jsonld",
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

If a **Feature** had multiple locations or extents over time, its **geometry** will be of type **GeometryCollection** -- an array of one or more **geometry** objects. Each of these can have a single **when** temporal element, along with the standard **type** and **coordinates** elements 

<pre>"geometry": {
 "type": "GeometryCollection",
 "geometries": [
   {
    "type": "LineString",
    "coordinates": [[93.867,40.35],[108.9423,34.26]],
    <strong><span style="color: #ff0000;">"when": {}</span></strong>,
    "attributes": {}
   },
   {. . .}
 ]
}</pre>

Each **geometry** in a **GeometryCollection** can have an unlimited number of **attributes** (the term "properties" is reserved as a top-level element in GeoJSON). Although these are outside the GeoJSON specification, like **when** elements, they will be ignored by existing GeoJSON-compatible software.

### The structure of "when"
_For example_:

<pre><strong><span style="color: #ff0000;">"when"</span></strong>: {
  "timespans": [["-323-01-01 ","","","-101-12-31", "Hellenistic period"]],
  "duration": "1y",
  "periods": [
   {
    "name": "Hellenistic Period",
    "period_uri": " http://n2t.net/ark:/99152/p0mn2ndq6bv"
   }
  ],
  "follows": "feature_00123",
}</pre>


The **when** object in a GeoJSON-T **Feature** (or in a geometry object within a Feature's **GeometryCollection**) is comprised of:

- a set of one or more **timespans**, specified in one of two ways: 
	- by a 5-part array (as above), the positions of which correspond to [start, latest-start, earliest-end, end, label]. Values for the first four can be any valid ISO-8601 expression, e.g. YYYY-MM-DD, YYYY-MM or YYYY; by convention, years preceded by a minus (-) are interpreted as BCE.

	- by a [PeriodO-compatible timespans element](http://perio.do/) of the form

<pre>
	"timespans": [
		{"start": {
			"label": "480 B.C.",
			"in": {"year": "-0479"}
		},
		"end": {
		    "label": "around 300 B.C.",
		    "in": {
          		"latestYear": "-0290",
          		"earliestYear": "-0310"}
      	}}
	]
</pre>


- an optional **duration** property whose value is an integer followed by a single letter (d, m, y for day, month, years respectively) indicating the phenomena occurred or is valid for that duration _**some time during**_ the timespan; e.g. "4d" or "15y". Absence of a "duration" element will be interpreted as the phenomena occuring _**throughout**_ the timespan.

- an optional **follows** property, whose value can be an internal identifier for the preceding segment of an ordered set


An outstanding issue is whether to permit a **_circa_** operator for the four temporal expressions in timespans. For example: **~1635-01** could stand for 'circa January, 1653' -- with the question of how to represent that visually or in computation left to individual software applications.

## Please comment
The development of GeoJSON-LDT could evolve into a more formal process, but that should follow more informal discussion. Please comment as [an Issue in this repository](https://github.com/kgeographer/topotime/issues). GeoJSON-LDT will be implemented in the [World-Historical Gazetteer] (http://whgazetteer.org) project now under way.
