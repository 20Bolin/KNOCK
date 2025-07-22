# top10_nl_query

## Some of the questions in this category are GraphQL questions in the old system but my new system uses SPARQL to solve them.

### Question template in Dutch and English
**Hoi welke {property} zijn er op een kleine afstand?**  
**Hi, which {property} are within a short distance?**  

### Tested example in Dutch and English
**Hoi welke kapel is er op een kleine afstand vanaf mijn adres Mauritsweg 218, Dordrecht?**  
**Hi, which chapel is a short distance from my address Mauritsweg 218, Dordrecht?**  

SPARQL Query of the old system:
**The old system can not answer this question. **  
```sparql
Invalid.
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
PREFIX uom: <http://www.opengis.net/def/uom/OGC/1.0/>
PREFIX geof: <http://www.opengis.net/def/function/geosparql/>

SELECT ?kapel ?afstand
WHERE {
    ?woonplaats a sor:Woonplaats;
                skos:prefLabel "Dordrecht"@nl;
                ^sor:ligtIn ?openbareruimte.

    ?openbareruimte a sor:OpenbareRuimte;
                    skos:prefLabel "Mauritsweg"@nl;
                    ^sor:ligtAan ?na.

    ?na a sor:Nummeraanduiding;
         sor:huisnummer 218;
         ^sor:hoofdadres ?vbo.
  
    ?vbo a sor:Verblijfsobject;
         sor:maaktDeelUitVan ?geb.
    
    ?geb a sor:Gebouw;
         sor:oorspronkelijkBouwjaar ?bo;
         geo:hasGeometry [
             geo:asWKT ?gebouwShape;
             rdfs:isDefinedBy bag:
         ].
    
    ?kapel a sor:Gebouwzone;
           kad:gebouwtype ?gebouwtype;
           geo:hasGeometry/geo:asWKT ?kapel_geometrie_wgs84.
    ?gebouwtype skos:prefLabel "kapel"@nl.
    BIND(geof:distance(?gebouwShape, ?kapel_geometrie_wgs84, uom:metre) as ?afstand)
}
ORDER BY ?afstand
LIMIT 1
```

Result in JSON output:
```json
[{'kapel': 'https://data.kkg.kadaster.nl/id/gebouwzone/131688311', 'afstand': '13084.5'}]
```


### Question template in Dutch and English
**Wat voor {property} zijn er dichtbij {location}?**  
**What kind of {property} is near {location}?**  

### Tested example in Dutch and English
**Wat voor kerken zijn er dichtbij vanaf Deldenerstraat 134, Hengelo?**  
**What churches are close to Deldenerstraat 134, Hengelo?**  

**The old system can not answer this question. The query it generates is irrelevant.**  
GraphQL Query of the old system:
```graphql
{
  dopaperinspection(where: {
  adres: "vanaf deldenerstraat 134 hengelo", vervoertype: "auto", aantalMinuten: 15}
  ) {
    adres maxaantal geometrie __typename scholen {
    __typename lokaalID naam type:schooltype straatnaam geometrie }
  }
}
```

Result in JSON output:
```json
{'data': {'dopaperinspection': {'adres': 'Deldenerstraat 134, 7551AK Hengelo', 'maxaantal': None, 'geometrie': 'POINT(250169.149 476273.443)', '__typename': 'Resultaat', 'scholen': []}}}
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
prefix uom: <http://www.opengis.net/def/uom/OGC/1.0/>
prefix geof: <http://www.opengis.net/def/function/geosparql/>

SELECT ?kerk ?afstand
WHERE {
    ?woonplaats a sor:Woonplaats;
                skos:prefLabel "Hengelo"@nl;
                ^sor:ligtIn ?openbareruimte.

    ?openbareruimte a sor:OpenbareRuimte;
                    skos:prefLabel "Deldenerstraat"@nl;
                    ^sor:ligtAan ?na.

    ?na a sor:Nummeraanduiding;
         sor:huisnummer 134;
         ^sor:hoofdadres ?vbo.
  
    ?vbo a sor:Verblijfsobject;
         sor:maaktDeelUitVan ?geb.
    
    ?geb a sor:Gebouw;
         sor:oorspronkelijkBouwjaar ?bo;
         geo:hasGeometry [
             geo:asWKT ?gebouwShape;
             rdfs:isDefinedBy bag:
         ].
    
    ?kerk a sor:Gebouwzone;
           kad:gebouwtype ?gebouwtype;
           geo:hasGeometry/geo:asWKT ?kerk_geometrie_wgs84.
    ?gebouwtype skos:prefLabel "kerk"@nl.
    BIND(geof:distance(?gebouwShape, ?kerk_geometrie_wgs84, uom:metre) as ?afstand)
}
ORDER BY ?afstand
LIMIT 10
```

Result in JSON output:
```json
[{'kerk': 'https://data.kkg.kadaster.nl/id/gebouwzone/101375829', 'afstand': '379.846'}, {'kerk': 'https://data.kkg.kadaster.nl/id/gebouwzone/101376492', 'afstand': '423.24'}, {'kerk': 'https://data.kkg.kadaster.nl/id/gebouwzone/101377532', 'afstand': '510.852'}, {'kerk': 'https://data.kkg.kadaster.nl/id/gebouwzone/101376471', 'afstand': '587.813'}, {'kerk': 'https://data.kkg.kadaster.nl/id/gebouwzone/101373596', 'afstand': '596.81'}, {'kerk': 'https://data.kkg.kadaster.nl/id/gebouwzone/101367409', 'afstand': '642.093'}, {'kerk': 'https://data.kkg.kadaster.nl/id/gebouwzone/101375521', 'afstand': '819.903'}, {'kerk': 'https://data.kkg.kadaster.nl/id/gebouwzone/101324340', 'afstand': '1061.39'}, {'kerk': 'https://data.kkg.kadaster.nl/id/gebouwzone/101366201', 'afstand': '1214.68'}, {'kerk': 'https://data.kkg.kadaster.nl/id/gebouwzone/131685340', 'afstand': '1382.85'}]
```