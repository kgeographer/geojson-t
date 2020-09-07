## GeoJSON-T

_Updated, 15 September 2019_

This proposed extension to the GeoJSON format tries to accommodate the fact that many geographic features are "event-like" (e.g. journeys, crimes, tweets) or otherwise inherently temporal (e.g. political boundaries, flows, or anything else that changes position or shape over time). In fact **__all__** geographic features have temporal attributes, whether or not we have that data or use them for a particular application. Likewise, many temporal things are inherently spatial (e.g. historical periods like _Bronze Age Britain_; see [the PeriodO project](http//perio.do), [Pleiades Period Vocabulary](http://pleiades.stoa.org/vocabularies/time-periods)). All events occur somewhere, with a spatial footprint we might like to map or analyze.

Like the [GeoJSON standard](https://tools.ietf.org/html/rfc7946) (RFC 7946), GeoJSON-T represents collections of geographic features in a **FeatureCollection**, and its **geometry** element handles spatial attributes identically. Any software that interprets GeoJSON will interpret GeoJSON-T, simply ignoring certain of its elements.

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
   <strong>"when": {}</strong>
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

- a set of one or more named, web-published **period definitions**

and any of these optional properties:

- a **label**

- a **duration** whose value is the letter 'P' followed by an integer followed by a single letter (D, W, M, Y for day, week, month, year respectively) indicating the phenomena occurred or is valid for that duration _**some time during**_ the timespan; e.g. "P4D" or "P15Y". Absence of a "duration" element will be interpreted as the phenomena occurring _**throughout**_ the timespan.

- a **follows** property, whose value can be an internal identifier for the preceding segment of an ordered set

<pre><strong>"when"</strong>: {
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
  "label": "for a century in the Hellenistic period"
}</pre>

### Possible extension
An outstanding issue is whether to support features proposed in Levels 0 and 1 of the [Extended Date/Time Format, EDTF](https://www.loc.gov/standards/datetime/edtf.html)). Two elements are of particular interest:

- The operators **~**, **?**, and **%** (respectively: approximate, uncertain and both approximate and uncertain). The question of how to represent these visually or compute over them would be left to individual software applications supporting GeoJSON-T.

- A simplified method of representing intervals that adds capability for open or uncertain bounds. Start and end dates are ISO 8601 expressions separated by a forward slash character "/". The GeoJSON-T format's start and end objects above could be replaced by single ISO 8601 dates or EDTF pairs standing for "earliest" and "latest." For example, the following would mean "from April, 1832 to sometime between 1 June and 14 August in 1835."

<pre>
{  
  "start": "1832-04",
  "end": "1835-06-01/1835-08-15",
}
</pre>

## Please comment
This extension proposes what are called "Foreign Members" in the GeoJSON specification ("when" & variously named key/value pairs in a geometry). It is understood that the value of these is entirely dependent on there being software that expects and uses them in computation and/or display.

### Uptake

GeoJSON-T is the basis for the JSON-LD compatible [Linked Places format (LP)](https://github.com/LinkedPasts/linked-places) in development for use by both the [Peripleo](http://peripleo.pelagios.org) and [World-Historical Gazetteer] (http://whgazetteer.org) projects.

GeoJSON-T is the proposed format for the [WebMaps-T project](https://medium.com/pelagios/introducing-the-webmaps-t-working-group-7cff98021e42) working to "visualise Linked Open Data (LOD) and other humanities data in time and space." [GitHub](https://github.com/pelagios/Leaflet.timeline.webmapst)

An earlier version of GeoJSON-T was implemented in the pilot web app [Linked Paths](http://http://linkedpaths.kgeographer.org)

The development of GeoJSON-T should evolve into a more formal process following this informal presentation and discussion. Please comment as [an Issue in this repository](https://github.com/kgeographer/geojson-t/issues).
