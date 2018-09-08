# Population "City"
Queries the population of the quarter "City" from LOSD as an example

[code link](http://yasgui.org/short/r19a_z-_X)
```SPARQL
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX qb: <http://purl.org/linked-data/cube#>
PREFIX owl: <http://www.w3.org/2002/07/owl#>
PREFIX skos: <http://www.w3.org/2004/02/skos/core#>
PREFIX dataset: <https://ld.stadt-zuerich.ch/statistics/dataset/>
PREFIX measure: <https://ld.stadt-zuerich.ch/statistics/measure/>
PREFIX dimension: <https://ld.stadt-zuerich.ch/statistics/property/>
PREFIX code: <https://ld.stadt-zuerich.ch/statistics/code/>
SELECT ?Raum ?RaumLabel ?Zeit ?WikidataUID ?Bevoelkerung WHERE {
  ?sub a qb:Observation ;
       qb:dataSet dataset:BEW-RAUM-ZEIT ;
       measure:BEW ?Bevoelkerung ;
       dimension:RAUM ?Raum ;
       
 	dimension:ZEIT ?Zeit .
  ?Raum owl:sameAs ?WikidataUID ;
        skos:broader code:Quartier ;
        rdfs:label ?RaumLabel .  
  FILTER (?Raum=code:R00014)
} ORDER BY ?Zeit
```

The query can be extended by removing the filter
