# p3-simple-demo-app
A simple demo app that shows pubs, restaurants, pharmacies, accommodations, museums and - optionally - events in Trentino and Tuscany regions.
You can click the location you are interested in to see what can be found nearby. To use event data from a specific resource, use the “events” attribute in the URL in which provide the URI of the resource:
**app URL**/?events=http://sandbox.fusepool.info:8181/ldp/demo/wr-ldpc/Trentino-Events/events-xml-xml-transformed

It uses the following SPARQL endpoint: http://sandbox.fusepool.info:8181/sparql/select

...and works with this SPARQL  query:

```
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX geo: <http://www.w3.org/2003/01/geo/wgs84_pos#>
PREFIX schema: <http://schema.org/>

SELECT distinct ?name ?type ?lat ?long ?eventLabel ?eventDescription ?eventCategory ?eventCategory ?eventStart ?eventEnd

FROM <http://sandbox.fusepool.info:8181/ldp/tuscany-museums-1/y-csv-transformed>
FROM <http://sandbox.fusepool.info:8181/ldp/tuscany-restaurants-1/c-csv-transformed>
FROM <http://sandbox.fusepool.info:8181/ldp/tuscany-accommodations-1/v-csv-transformed>
FROM <http://sandbox.fusepool.info:8181/ldp/trentino-pharmacies/c>
FROM <http://sandbox.fusepool.info:8181/ldp/demo/wr-ldpc/Trentino-Events/events-xml-xml-transformed>

WHERE { { 
	?entity schema:address ?address ;
  			geo:lat ?lat ;
			geo:long ?long ;
			rdf:type ?type ;
			rdfs:label ?name .	}
   UNION {
 	?entity schema:event ?event ;
 			geo:lat ?lat ;
 			geo:long ?long ;
 			rdf:type ?type ;
 			rdfs:label ?name . 
	?event rdfs:label ?eventLabel ;
 		   schema:description ?eventDescription ;
 		   schema:category ?eventCategory ;
 		   schema:startDate ?eventStart ;
 		   schema:endDate ?eventEnd .
 	FILTER ( ?eventStart >= "2015-03-01"^^xsd:date && ?eventEnd <= "2015-03-01"^^xsd:date )
 	FILTER(langMatches(lang(?eventLabel), "it"))
 	FILTER(langMatches(lang(?eventCategory), "it"))
 	FILTER(langMatches(lang(?eventDescription), "it"))
 	}
   FILTER ( ( ?lat >= 45.96365339743794 && ?lat <= 46.06365339743793 )
  	 && ( ?long >= 10.900897290708068 && ?long <= 11.00089729070807 ) ) }

ORDER BY ?name
```

...where:
* the last FROM clause is used only if a resource URI is provided in the query string (the other four resource URIs are static)
* **_eventStart_** and **_eventEnd_** uses exactly what is typed to the input fields on the top left corner
* **_lat_** and **_long_** come from the position of the marker on the map

---

An example installation can be found here:

[http://fusepoolp3.github.io/p3-simple-demo-app/?events=http://sandbox.fusepool.info:8181/ldp/demo/wr-ldpc/Trentino-Events/events-xml-xml-transformed](http://fusepoolp3.github.io/p3-simple-demo-app/?events=http://sandbox.fusepool.info:8181/ldp/demo/wr-ldpc/Trentino-Events/events-xml-xml-transformed "Simple demo app")
