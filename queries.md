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

# Wrong preferred rank

The prefered rank does not match with the most recent data

[code link](http://tinyurl.com/yd95yaar)
```SPARQL
SELECT ?Quartier ?preferredYear ?maxYear {
  FILTER(?preferredYear < ?maxYear)
  {
SELECT ?Quartier (year(?Zeitpunkt_Stand) as ?preferredYear) WHERE {
  SERVICE wikibase:label { bd:serviceParam wikibase:language "[AUTO_LANGUAGE],en". }
  ?Quartier wdt:P31 wd:Q19644586 ;
            p:P1082 ?Einwohnerzahl. 
  ?Einwohnerzahl pq:P585 ?Zeitpunkt_Stand;
                 wikibase:rank wikibase:PreferredRank .
}
}
  
  {
  
  SELECT ?Quartier (year(max(?Zeitpunkt_Stand2)) as ?maxYear) WHERE {
  SERVICE wikibase:label { bd:serviceParam wikibase:language "[AUTO_LANGUAGE],en". }
  ?Quartier wdt:P31 wd:Q19644586 ;
            p:P1082 ?Einwohnerzahl2. 
  ?Einwohnerzahl2 pq:P585 ?Zeitpunkt_Stand2 .
                 
}
GROUP BY ?Quartier

  }

  }
```

# Wrong data

List all the datapoints of population from Wikidata, that do not with the LOSD version from SSZ

[code link](http://yasgui.org/short/Sk1dz4ZdX)
```SPARQL
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX qb: <http://purl.org/linked-data/cube#>
PREFIX owl: <http://www.w3.org/2002/07/owl#>
PREFIX skos: <http://www.w3.org/2004/02/skos/core#>
PREFIX wdt: <http://www.wikidata.org/prop/direct/>
PREFIX wd: <http://www.wikidata.org/entity/>
PREFIX pq: <http://www.wikidata.org/prop/qualifier/>
PREFIX ps: <http://www.wikidata.org/prop/statement/>
PREFIX p: <http://www.wikidata.org/prop/>
PREFIX wikibase: <http://wikiba.se/ontology#>
SELECT ?Raum ?RaumLabel ?ZeitSSZ ?JahrSSZ ?WikiTime ?WikidataUID ?BevSSZ ?BevWiki ?Rank WHERE {
  ?sub a qb:Observation ;
       qb:dataSet <https://ld.stadt-zuerich.ch/statistics/dataset/BEW-RAUM-ZEIT> ;
       <https://ld.stadt-zuerich.ch/statistics/measure/BEW> ?Bevoelkerung ;
       <https://ld.stadt-zuerich.ch/statistics/property/RAUM> ?Raum ;
       
                <https://ld.stadt-zuerich.ch/statistics/property/ZEIT> ?ZeitSSZ .
  ?Raum owl:sameAs ?WikidataUID ;
        skos:broader <https://ld.stadt-zuerich.ch/statistics/code/Quartier> ;
        rdfs:label ?RaumLabel . 
  BIND(xsd:decimal(?Bevoelkerung) AS ?BevSSZ)
  BIND(YEAR(?ZeitSSZ) AS ?JahrSSZ)
  BIND(xsd:dateTime(CONCAT(SUBSTR(STR(?ZeitSSZ),1,4),"-01-01T00:00:00Z")) AS ?WikiTime)
  # FILTER (?Raum=<http://ld.stadt-zuerich.ch/statistics/code/R00014>)
  

  
SERVICE <https://query.wikidata.org/bigdata/namespace/wdq/sparql> {
    select ?BevWiki ?WikiTime ?WikidataUID ?Rank where {
    ?WikidataUID wdt:P31 wd:Q19644586 ;
    
            p:P1082 ?EinwohnerProperty.
      
  ?EinwohnerProperty ps:P1082 ?BevWiki ;
                 pq:P585 ?WikiTime;
                  wikibase:rank ?Rank.
        
    }
  }

} 
HAVING (?BevSSZ != ?BevWiki)
ORDER BY ?Raum ?ZeitSSZ
LIMIT 100
```
