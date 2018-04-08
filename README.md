## GeoJSON-T
This proposed extension to the GeoJSON format tries to accommodate the fact that many geographic features are "event-like" (e.g. journeys, crimes, tweets) or otherwise inherently temporal (e.g. political boundaries, flows, or anything else that changes position or shape over time). In fact **__all__** geographic features have temporal attributes, whether or not we have that data or use them for a particular application. Likewise, many temporal things are inherently spatial (e.g. historical periods like _Bronze Age Britain_; see [the PeriodO project](http//perio.do), [Pleiades Period Vocabulary](http://pleiades.stoa.org/vocabularies/time-periods)). All events occur somewhere, with a spatial footprint we might like to map or analyze.

Like the [GeoJSON standard]() (RFC 7946), GeoJSON-LDT represents collections of geographic features in a **FeatureCollection**, and its **geometry** element handles spatial attributes identically. Any software that interprets GeoJSON will interpret GeoJSON-T, simply ignoring certain of its elements.

But unlike GeoJSON, a **Feature** in GeoJSON-T can be either an essentially spatial thing (a place) or temporal thing (period or events of any complexity). This difference requires extending the GeoJSON format in a few ways:


### Adding time

A GeoJSON-T **Feature** can have optional temporal attributes, in one or more **when** elements

If a **Feature** has a single geometry, a single **when** temporal element can be added as a sibling to GeoJSON's standard **type**, **geometry**, and **properties** elements.

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

If a **Feature** had multiple locations or extents over time, its **geometry** will be of type **GeometryCollection** -- an array of one or more **geometry** objects. Each of these can have a single **when** temporal element, along with the standard **type** and **coordinates** elements

<pre>"geometry": {
 "type": "GeometryCollection",
 "geometries": [
   {
    "type": "LineString",
    "coordinates": [[93.867,40.35],[108.9423,34.26]],
    <strong><span style="color: #ff0000;">"when": {}</span></strong>,
    "attrib01": ""
   },
   {. . .}
 ]
}</pre>

Each **geometry** in a **GeometryCollection** can have an unlimited number of attributes. The term "properties" is reserved for the top-level element of a Feature in the GeoJSON spec. These, like **when** elements, are termed "Foreign Members" in the GeoJSON specification. They will be ignored by existing GeoJSON-compatible software, but can be parsed and manipulated by future GeoJSON-T compatible software.

### The structure of "when"

The **when** object in a GeoJSON-T **Feature** (or in a geometry object within a Feature's **GeometryCollection**) is comprised of at least one of:

- a set of one or more **timespans**

- a set of one or more **periods**

- an optional **label** property

- an optional **duration** property whose value is an integer followed by a single letter (d, m, y for day, month, years respectively) indicating the phenomena occurred or is valid for that duration _**some time during**_ the timespan; e.g. "4d" or "15y". Absence of a "duration" element will be interpreted as the phenomena occuring _**throughout**_ the timespan.

- an optional **follows** property, whose value can be an internal identifier for the preceding segment of an ordered set

<pre><strong><span style="color: #ff0000;">"when"</span></strong>: {
  "timespans": [
		{  
		  "start": { "in": "nnnn-nn" },
		  "end": {
	      "earliest": "-nnnn",
	      "latest": "nnnn-nn-nn"
	    },
	  }
	],
  "periods": [
   {
    "name": "Hellenistic Period",
    "uri": "http://n2t.net/ark:/99152/p0mn2ndq6bv"
   }
  ],
	"duration": "1y",
  "follows": "feature_00123",
  "label": "overlapping with the Hellenistic period"
}</pre>


An outstanding issue is whether to permit a **_circa_** operator for the four temporal expressions in timespans and/or the duration expression. For example: **~1635-01** could stand for 'circa January, 1653'; or **~3m** for 'about 3 months' -- with the question of how to represent that visually or in computation left to individual software applications.

## Please comment
This extension proposes what are called "Foreign Members" in the GeoJSON specification ("when" & variously named attributes). It is understood that the value of these is entirely dependent on there being software that expects and uses them in computation and/or display. 

### Uptake

An earlier version (GeoJSON-T) was implemented in the pilot web app [Linked Places](http://linkedplaces.org);

GeoJSON-T is the basis for a [proposed v2 of the Pelagios Gazetteer Interconnection Format (PGIFv2)](https://github.com/LinkedPasts/lp-network) in development for use by both the [Peripleo](http://peripleo.pelagios.org) and [World-Historical Gazetteer] (http://whgazetteer.org) projects.


The development of GeoJSON-T (and a future GeoJSON-LDT) should evolve into a more formal process, follow this more informal presentation and discussion. Please comment as [an Issue in this repository](https://github.com/kgeographer/geojson-t/issues).
