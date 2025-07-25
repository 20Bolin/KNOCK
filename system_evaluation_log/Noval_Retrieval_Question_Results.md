# System Evaluation Log: Noval Retrieval Questions

## These are the questions that can be solved by the new system, but not by the old system.

### Tested example in Dutch and English
**Hoeveel gebouwen zijn er in Amsterdam en Rotterdam die voor 1800 zijn gebouwd?**  
**How many buildings were built in Amsterdam and Rotterdam before 1800?**  

SPARQL Query of the old system:
**It only counts Amsterdam.**
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
sor:huisnummer?huisnummers. FILTER (str(?bo) < '1800') }
LIMIT 9999
```

Result in JSON output:
```json
Incorrect output that only contains results from Amsterdam.
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
prefix owl: <http://www.w3.org/2002/07/owl#>
prefix wbk: <https://data.labs.kadaster.nl/cbs/wbk/vocab/>

SELECT (COUNT(DISTINCT ?geb) as ?aantal) 
WHERE {
    ?woonplaats a sor:Woonplaats;
                skos:prefLabel ?stad;
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
    
    FILTER (?bo < "1800"^^xsd:gYear)
    FILTER (?stad IN ("Amsterdam"@nl, "Rotterdam"@nl))
}
LIMIT 9999
```

Result in JSON output:
```json
[{'aantal': '5130'}]
```


### Tested example in Dutch and English
**Welke woonplaats heeft de meeste gebouwen die voor 1700 zijn gebouwd?**  
**Which town has the most buildings built before 1700?**  

SPARQL Query of the old system:
```sparql
# The old system cannot answer this question
# 'query': 'Ik weet het antwoord niet. Stel een andere vraag.'
```

Result in JSON output:
```json
Invalid
```

SPARQL Query of the new system:
```sparql
PREFIX bag: <http://bag.basisregistraties.overheid.nl/def/bag#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX sor: <https://data.kkg.kadaster.nl/sor/model/def/>
PREFIX kad: <https://data.kkg.kadaster.nl/kad/model/def/>
PREFIX skos: <http://www.w3.org/2004/02/skos/core#>
PREFIX geo: <http://www.opengis.net/ont/geosparql#>
PREFIX nen3610: <https://data.kkg.kadaster.nl/nen3610/model/def/>
PREFIX brt: <http://brt.basisregistraties.overheid.nl/def/top10nl#>
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>

SELECT ?woonplaats (COUNT(DISTINCT ?geb) as ?aantal) 
WHERE {
    ?woonplaats a sor:Woonplaats;
                skos:prefLabel ?woonplaats_naam;
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
    
    FILTER (?bo < "1700"^^xsd:gYear)
}
GROUP BY ?woonplaats
ORDER BY DESC(COUNT(DISTINCT ?geb))
LIMIT 1
```

Result in JSON output:
```json
[{'woonplaats': 'https://data.kkg.kadaster.nl/id/woonplaats/3594', 'aantal': '1699'}]  Amsterdam
```