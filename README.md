## GeoJSON-T
This proposed extension to the GeoJSON format tries to accommodate the fact that many geographic features are "event-like" (e.g. journeys, crimes, tweets) or otherwise inherently temporal (e.g. political boundaries, flows, or anything else that changes position or shape over time). In fact **__all__** geographic features have temporal attributes, whether or not we have that data or use them for a particular application. Likewise, many temporal things are inherently spatial (e.g. historical periods like _Bronze Age Britain_; see [the PeriodO project](http//perio.do), [Pleiades Period Vocabulary](http://pleiades.stoa.org/vocabularies/time-periods)). All events occur somewhere, with a spatial footprint we might like to map or analyze.

Like the [GeoJSON standard](https://tools.ietf.org/html/rfc7946) (RFC 7946), GeoJSON-T represents collections of geographic features in a **FeatureCollection**, and its **geometry** element handles spatial attributes identically. Any software that interprets GeoJSON will interpret GeoJSON-T, simply ignoring certain of its elements.

But unlike GeoJSON, a **Feature** in GeoJSON-T can be either an essentially spatial thing (a place) or temporal thing (period or events of any complexity). This difference requires extending the GeoJSON format in a few ways:


### Adding time

A GeoJSON-T **Feature** can have optional temporal attributes, in one or more **when** elements

If a **Feature** has a single geometry, a single **when** temporal element can be added as a sibling to GeoJSON's standard **type**, **geometry**, and **properties** elements.

<pre>
{
"type": "FeatureCollection",
"features": [
{
   "type": "Feature",
   "id": "",
   "properties": {},
   "geometry": {},
   <span style="color:#FF0000; font-weight:800">"when": {}</span>
   }
]
}
</pre>

If a **Feature** had multiple locations or extents over time, its **geometry** will be of type **GeometryCollection** -- an array of one or more **geometry** objects. Each of these can have a single **when** temporal element, along with the standard **type** and **coordinates** elements

<pre>"geometry": {
 "type": "GeometryCollection",
 "geometries": [
   {
    "type": "LineString",
    "coordinates": [[93.867,40.35],[108.9423,34.26]],
    <strong>"when": {}</strong>,
    "attrib01": ""
   },
   {. . .}
 ]
}</pre>

Each **geometry** in a **GeometryCollection** can have an unlimited number of attributes. The term "properties" is reserved for the top-level element of a Feature in the GeoJSON spec. These, like **when** elements, are termed "Foreign Members" in the GeoJSON specification. They will be ignored by existing GeoJSON-compatible software, but can be parsed and manipulated by future GeoJSON-T compatible software.

### The structure of "when"

The **when** object in a GeoJSON-T **Feature** (or in a geometry object within a Feature's **GeometryCollection**) is comprised of at least one of:

- a set of one or more **timespans**

- a set of one or more named **period definitions**

- an optional **label** property

- an optional **duration** property whose value is the letter 'P' followed by an integer followed by a single letter (D, W, M, Y for day, week, month, year respectively) indicating the phenomena occurred or is valid for that duration _**some time during**_ the timespan; e.g. "P4D" or "P15Y". Absence of a "duration" element will be interpreted as the phenomena occurring _**throughout**_ the timespan.

- an optional **follows** property, whose value can be an internal identifier for the preceding segment of an ordered set

<pre><strong>"when"</span></strong>: {
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
  "duration": "P100Y",
  "follows": "http://mygaz.org/p_00123",
  "label": "overlapping with the Hellenistic period"
}</pre>


An outstanding issue is whether to support the operators proposed in ISO 8601 level 1 for uncertainty and approximation (formerly, [Extended Date/Time Format, EDTF](https://www.loc.gov/standards/datetime/pre-submission.html)). Of particular interest are the operators **~** (approximate), **?** (uncertain), and **%** (both approximate and uncertain). The question of how to represent these visually or in computation would be left to individual software applications supporting GeoJSON-T.

## Please comment
This extension proposes what are called "Foreign Members" in the GeoJSON specification ("when" & variously named key/value pairs in a geometry). It is understood that the value of these is entirely dependent on there being software that expects and uses them in computation and/or display.

### Uptake

GeoJSON-T is the basis for the JSON-LD compatible [Linked Places Interconnection Format (LPIF)](https://github.com/LinkedPasts/lpif) in development for use by both the [Peripleo](http://peripleo.pelagios.org) and [World-Historical Gazetteer] (http://whgazetteer.org) projects.

An earlier version of GeoJSON-T was implemented in the pilot web app [Linked Places](http://linkedplaces.org);

The development of GeoJSON-T (and a future GeoJSON-LDT) should evolve into a more formal process, following this more informal presentation and discussion. Please comment as [an Issue in this repository](https://github.com/kgeographer/geojson-t/issues).
