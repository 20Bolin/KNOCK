# brt_filter_query

### Question template in Dutch and English
**Geef mij de hoeveelheid panden in {location} met {housesize} and {constructionyear}.**  
**Give me the number of properties in {location} with {housesize} and {constructionyear}.**  

### Tested example in Dutch and English
**Geef mij de hoeveelheid panden in de woonplaats Amsterdam met een oppervlakte groter dan 100 m2 and een oorspronkelijke bouwjaar na 2000.**  
**Give me the number of buildings in Amsterdam with a surface area greater than 100 m2 and an original construction year after 2000.**  

SPARQL Query of the old system:
```sparql
PREFIX kad: <https://data.kkg.kadaster.nl/kad/model/def/>
PREFIX sor: <https://data.kkg.kadaster.nl/sor/model/def/>
PREFIX geo: <http://www.opengis.net/ont/geosparql#>
PREFIX skos: <http://www.w3.org/2004/02/skos/core#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX bag: <http://bag.basisregistraties.overheid.nl/def/bag#>
SELECT DISTINCT (COUNT(?geb) as?aantal) WHERE {
  ?vbo a sor:Verblijfsobject;
  sor:oppervlakte?wo;
  sor:hoofdadres?na;
  sor:maaktDeelUitVan?geb.?geb sor:oorspronkelijkBouwjaar?bo;
  geo:hasGeometry [
  geo:asWKT?geo_wgs84;
  rdfs:isDefinedBy bag: ]. OPTIONAL {
    ?per a sor:Perceel;
    sor:hoortBij?na;
  sor:oppervlakte?po. }
  OPTIONAL {
    ?gebz sor:hoortBij?vbo;
  kad:gebouwtype/skos:prefLabel?tg. }
  ?woonplaats a sor:Woonplaats;
  skos:prefLabel "Amsterdam"@nl;
  ^sor:ligtIn?openbareruimte.?openbareruimte a sor:OpenbareRuimte;
  ^sor:ligtAan?na.?na a sor:Nummeraanduiding;
sor:huisnummer?huisnummers. FILTER (str(?bo) > '2000') FILTER (?wo > 100) }
LIMIT 9999
```

Result in JSON output:
**No DISTINCT, a lot of duplications.**  
```json
[{'aantal': '43429'}]
```

SPARQL Query of the new system:
```sparql
PREFIX bag: <http://bag.basisregistraties.overheid.nl/def/bag#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX sor: <https://data.kkg.kadaster.nl/sor/model/def/>
PREFIX kad: <https://data.kkg.kadaster.nl/kad/model/def/>
PREFIX skos: <http://www.w3.org/2004/02/skos/core#>
PREFIX geo: <http://www.opengis.net/ont/geosparql#>
prefix xsd: <http://www.w3.org/2001/XMLSchema#>

SELECT (COUNT(DISTINCT ?geb) as ?aantal) 
WHERE {
    ?woonplaats a sor:Woonplaats;
                skos:prefLabel "Amsterdam"@nl;
                ^sor:ligtIn ?openbareruimte.

    ?openbareruimte a sor:OpenbareRuimte;
                    ^sor:ligtAan ?na.

    ?na a sor:Nummeraanduiding;
         ^sor:hoofdadres ?vbo.

    ?vbo a sor:Verblijfsobject;
         sor:oppervlakte ?wo;
         sor:maaktDeelUitVan ?geb.
    
    ?geb a sor:Gebouw;
         sor:oorspronkelijkBouwjaar ?bo;
         geo:hasGeometry [
             geo:asWKT ?geo_wgs84;
             rdfs:isDefinedBy bag:
         ].
    
    FILTER (?wo > 100 && ?bo > "2000"^^xsd:gYear)
}
LIMIT 9999
```

Result in JSON output:
```json
[{'aantal': '15000'}]
```


### Question template in Dutch and English
**Hoeveel {brtproperty} {housesize} staan in {granularity} {location}?**  
**How many {brtproperty} {housesize} are in {granularity} {location}?**  

### Tested example in Dutch and English
**Hoeveel woningen groter dan 100 m2 staan er in de gemeente Enschede?**  
**How many houses larger than 100 m2 are there in the municipality of Enschede?**  

SPARQL Query of the old system:
```sparql
PREFIX sor-con: <https://data.kkg.kadaster.nl/sor/model/con/>
PREFIX sdo0: <http://schema.org/>
PREFIX kad: <https://data.kkg.kadaster.nl/kad/model/def/>
PREFIX sor: <https://data.kkg.kadaster.nl/sor/model/def/>
PREFIX geo: <http://www.opengis.net/ont/geosparql#>
PREFIX skos: <http://www.w3.org/2004/02/skos/core#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX bag: <http://bag.basisregistraties.overheid.nl/def/bag#>
SELECT DISTINCT (COUNT(?geb) as?aantal) WHERE {
  ?vbo a sor:Verblijfsobject;
  sor:oppervlakte?wo;
  sor:hoofdadres?na;
  sor:maaktDeelUitVan?geb;
  sor:gebruiksdoel sor-con:woonfunctie.?geb sor:oorspronkelijkBouwjaar?bo;
  geo:hasGeometry [
  geo:asWKT?geo_wgs84;
  rdfs:isDefinedBy bag: ]. OPTIONAL {
    ?per a sor:Perceel;
    sor:hoortBij?na;
  sor:oppervlakte?po. }
  OPTIONAL {
    ?gebz sor:hoortBij?vbo;
  kad:gebouwtype/skos:prefLabel?tg. }
?gemeente sdo0:identifier "GM0153".?gemeente ^geo:sfWithin/^geo:sfWithin/^geo:sfWithin?geb. FILTER (?wo > 100) }
LIMIT 9999

```

Result in JSON output:
**Not distinct, duplications.**
```json
[{'aantal': '36311'}]
```

SPARQL Query of the new system:
```sparql
PREFIX bag: <http://bag.basisregistraties.overheid.nl/def/bag#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX sor: <https://data.kkg.kadaster.nl/sor/model/def/>
PREFIX kad: <https://data.kkg.kadaster.nl/kad/model/def/>
PREFIX skos: <http://www.w3.org/2004/02/skos/core#>
PREFIX geo: <http://www.opengis.net/ont/geosparql#>
prefix owl: <http://www.w3.org/2002/07/owl#>
prefix wbk: <https://data.labs.kadaster.nl/cbs/wbk/vocab/>
prefix xsd: <http://www.w3.org/2001/XMLSchema#>

SELECT (COUNT(DISTINCT ?geb) as ?aantal) 
WHERE {
    ?vbo a sor:Verblijfsobject;
         sor:oppervlakte ?wo;
         sor:maaktDeelUitVan ?geb.
    
    ?geb a sor:Gebouw;
         sor:oorspronkelijkBouwjaar ?bo;
         geo:hasGeometry [
             geo:asWKT ?geo_wgs84;
             rdfs:isDefinedBy bag:
         ];
         geo:sfWithin ?wbk_buurt.
    
    OPTIONAL {
        ?per a sor:Perceel;
             sor:hoortBij ?na;
             sor:oppervlakte ?po;
             geo:sfWithin ?gemeente.
    }
    
    OPTIONAL {
        ?gebz sor:hoortBij ?vbo;
               kad:gebouwtype/skos:prefLabel ?tg.
    }

    ?wbk_buurt a wbk:Buurt;
              rdfs:label ?buurt_naam;
              geo:hasGeometry [
                  geo:asWKT ?buurt_geo_wgs84;
              ];
              geo:sfWithin ?wbk_wijk.

    ?wbk_wijk a wbk:Wijk;
              rdfs:label ?wijk_naam;
              geo:hasGeometry [
                  geo:asWKT ?wijk_geo_wgs84;
              ];
              geo:sfWithin ?wbk_gemeente.

    ?wbk_gemeente a wbk:Gemeente;
              rdfs:label ?wbk_gemeente_naam;
              ^owl:sameAs ?gemeente.

    ?gemeente a sor:Gemeente;
              owl:sameAs ?wbk_gemeente;
              skos:prefLabel "Enschede"@nl.

    FILTER (?wo > 100)
}
LIMIT 9999
```

Result in JSON output:
```json
[{'aantal': '33176'}]
```


### Question template in Dutch and English
**Hoeveel {brtproperty} staan in {location}?**  
**How many {brtproperty} properties are in {location}?**  

### Tested example in Dutch and English
**Hoeveel kerken staan er in de woonplaats Eindhoven?**  
**How many churches are there in the town of Eindhoven?**  

SPARQL Query of the old system:
```sparql
PREFIX kad: <https://data.kkg.kadaster.nl/kad/model/def/>
PREFIX sor: <https://data.kkg.kadaster.nl/sor/model/def/>
PREFIX geo: <http://www.opengis.net/ont/geosparql#>
PREFIX skos: <http://www.w3.org/2004/02/skos/core#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX bag: <http://bag.basisregistraties.overheid.nl/def/bag#>
SELECT DISTINCT (COUNT(?geb) as?aantal) WHERE {
  ?vbo a sor:Verblijfsobject;
  sor:oppervlakte?wo;
  sor:hoofdadres?na;
  sor:maaktDeelUitVan?geb.?geb sor:oorspronkelijkBouwjaar?bo;
  geo:hasGeometry [
  geo:asWKT?geo_wgs84;
  rdfs:isDefinedBy bag: ]. OPTIONAL {
    ?per a sor:Perceel;
    sor:hoortBij?na;
  sor:oppervlakte?po. }
  OPTIONAL {
    ?gebz sor:hoortBij?vbo;
  kad:gebouwtype/skos:prefLabel?tg. }
  ?woonplaats a sor:Woonplaats;
  skos:prefLabel "Eindhoven"@nl;
  ^sor:ligtIn?openbareruimte.?openbareruimte a sor:OpenbareRuimte;
  ^sor:ligtAan?na.?na a sor:Nummeraanduiding;
sor:huisnummer?huisnummers. FILTER (?tg = "kerk"@nl ) }
LIMIT 9999
```

Result in JSON output:
**Still duplications**  
```json
[{'aantal': '96'}]
```

SPARQL Query of the new system:
```sparql
PREFIX bag: <http://bag.basisregistraties.overheid.nl/def/bag#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX sor: <https://data.kkg.kadaster.nl/sor/model/def/>
PREFIX kad: <https://data.kkg.kadaster.nl/kad/model/def/>
PREFIX skos: <http://www.w3.org/2004/02/skos/core#>
PREFIX geo: <http://www.opengis.net/ont/geosparql#>
PREFIX owl: <http://www.w3.org/2002/07/owl#>
PREFIX nen3610: <https://data.kkg.kadaster.nl/nen3610/model/def/>
PREFIX wbk: <https://data.labs.kadaster.nl/cbs/wbk/vocab/>

SELECT (COUNT(DISTINCT ?geb) as ?aantal) 
WHERE {
    ?woonplaats a sor:Woonplaats;
                skos:prefLabel "Eindhoven"@nl;
                ^sor:ligtIn ?openbareruimte.

    ?openbareruimte a sor:OpenbareRuimte;
                    ^sor:ligtAan ?na.

    ?na a sor:Nummeraanduiding;
         ^sor:hoofdadres ?vbo.

    ?vbo a sor:Verblijfsobject;
         sor:oppervlakte ?wo;
         sor:maaktDeelUitVan ?geb.
    
    ?geb a sor:Gebouw;
         sor:oorspronkelijkBouwjaar ?bo;
         geo:hasGeometry [
             geo:asWKT ?geo_wgs84;
             rdfs:isDefinedBy bag:
         ].
    
    OPTIONAL {
        ?per a sor:Perceel;
             sor:hoortBij ?na;
             sor:oppervlakte ?po.
    }
    
    OPTIONAL {
        ?gebz sor:hoortBij ?vbo;
               kad:gebouwtype/skos:prefLabel ?tg.
    }
  
    FILTER (?tg = "kerk"@nl)
}
LIMIT 9999
```

Result in JSON output:
```json
[{'aantal': '52'}]
```