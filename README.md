This proposed format standard tries to accommodate the fact that many geographic features are "event-like" (e.g. crimes, tweets, and journeys) or otherwise inherently temporal (e.g. political boundaries, flows, or anything else that changes position or shape over time). In fact **__all__** geographic features have temporal attributes, whether or not we have that data or use them for a particular application. Likewise, many temporal things are inherently spatial (e.g. historical periods like _Bronze Age Britain_; see [the PeriodO project](http//perio.do), [Pleiades Period Vocabulary](http://pleiades.stoa.org/vocabularies/time-periods)). All events occur somewhere, with a spatial footprint we might like to map or analyze.

Like GeoJSON and the emerging GeoJSON-LD, GeoJSON-T represents collections of geographic features in a **FeatureCollection**, and its **geometry** element handles spatial attributes identically.

But unlike GeoJSON, a **Feature** in GeoJSON-T can be either an essentially spatial thing (a place) or temporal thing (period or events of any complexity). This difference requires extending the GeoJSON format in a few ways:


###Adding time

* A GeoJSON-T **Feature** can have optional temporal attributes, in a **when** element
* If a **Feature** has a single geometry, a **when** temporal element can be added as a sibling to GeoJSON's standard **type**, **geometry**, and **properties** elements
* If a **Feature** had multiple positions or extents over time, its **geometry** will be of type **GeometryCollection** -- an array of one or more **geometry** objects. Each of these must have a **when** temporal element, along with the standard **type** and **coordinates** elements.
* Each **geometry** in a **GeometryCollection** can have an unlimited number of **properties**, although these are outside the GeoJSON specification and, like **when** elements, will be ignored by existing GeoJSON-compatible software.

###The structure of "when"

The **when** object in a GeoJSON-T **Feature** (or in a **geometry** within a Feature's **GeometryCollection**) is comprised of:

- a set of one or more **timespans**, specified by a 5-part array, the positions of which correspond to [start, latest-start, earliest-end, end, label]. Values for the first four can be any valid ISO-8601 expression, e.g. YYYY-MM-DD, YYYY-MM or YYYY; by convention, years preceded by a minus (-) are interpreted as BCE.

- a **duration** property whose value can either be 
	- null (indicating the phenomena occured or was valid _**throughout**_ the timespan(s); or
	
	- an integer followed by a single letter (d, m, y for day, month, years respectively)

- an optional **follows** property, whose value can be an internal identifier for the preceding segment of an ordered set

An outstanding issue is whether to permit a **_circa_** operator for the four temporal expressions in timespans. For example: **~1635-01** could stand for 'circa January, 1653' -- with the question of how to represent that visually or in computation left to individual software applications.