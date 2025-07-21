# property_query

## These are GraphQL questions in the old system but my new system uses SPARQL to solve them.

### Question template in Dutch and English
**Wat is de oppervlakte van mijn perceel?**  
**What is the surface area of my plot?**  

### Tested example in Dutch and English
**Wat is de oppervlakte van mijn perceel, adres Zuiderval 80, Enschede?**  
**What is the surface area of my plot at Zuiderval 80, Enschede?**  

GraphQL Query of the old system:
```graphql
{
  locatieserver(adres: "zuiderval 80 enschede") {
    nummeraanduidingen (actualiteit: {
    actueel_formeel: true, actueel_materieel: true}
    ) {
      hoofdadresvan(actualiteit: {
      actueel_formeel: true, actueel_materieel: true}
      ) {
        perceel(actualiteit: {
        actueel_formeel: true, actueel_materieel: true}
        ) {
        lokaaID:gml_id geometrie:begrenzing chatbotanswer:prompt(prompt: "De kadastrale grootte van dit perceel is @oppervlakte m2") perceeloppervlakte:oppervlakte akrgemeente sectie perceelnummer }
      }
    }
  }
}
```

Result in JSON output:
```json
{'data': {'locatieserver': {'nummeraanduidingen': [{'hoofdadresvan': [{'perceel': [{'lokaaID': 'NL.IMKAD.KadastraalObject.66620312570000', 'geometrie': 'POLYGON((257660.098 469643.85,257645.978 469647.992,257643.398 469639.161,257668.342 469631.843,257669.34 469626.271,257692.621 469630.439,257696.466 469637.043,257688.534 469681.349,257679.008 469699.152,257657.233 469695.253,257654.475 469675.252,257660.098 469643.85))', 'chatbotanswer': 'De kadastrale grootte van dit perceel is 2405 m2', 'perceeloppervlakte': 2405, 'akrgemeente': 'LNK00', 'sectie': 'P', 'perceelnummer': 3125}]}]}]}}}
```

SPARQL Query of the new system:
```sparql
PREFIX bag: <http://bag.basisregistraties.overheid.nl/def/bag#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX sor: <https://data.kkg.kadaster.nl/sor/model/def/>
PREFIX kad: <https://data.kkg.kadaster.nl/kad/model/def/>
PREFIX skos: <http://www.w3.org/2004/02/skos/core#>
PREFIX geo: <http://www.opengis.net/ont/geosparql#>

SELECT ?oppervlakte 
WHERE {
    ?vbo a sor:Verblijfsobject;
         sor:hoofdadres ?na;
         sor:maaktDeelUitVan ?geb.
    
    ?geb sor:oorspronkelijkBouwjaar ?bo;
         geo:hasGeometry [
             geo:asWKT ?geo_wgs84;
             rdfs:isDefinedBy bag:
         ].
    
    ?per a sor:Perceel;
         sor:hoortBij ?na;
         sor:oppervlakte ?oppervlakte.
    
    ?na a sor:Nummeraanduiding;
         sor:huisnummer 80;
         sor:ligtAan ?openbareruimte.
    
    ?openbareruimte a sor:OpenbareRuimte;
                    skos:prefLabel "Zuiderval"@nl;
                    sor:ligtIn ?woonplaats.
    
    ?woonplaats a sor:Woonplaats;
                skos:prefLabel "Enschede"@nl.
}
LIMIT 1
```

Result in JSON output:
```json
[{'oppervlakte': '2405'}]
```


### Question template in Dutch and English
****  
****  

### Tested example in Dutch and English
****  
****  

SPARQL Query of the old system:
```sparql

```

Result in JSON output:
```json

```

SPARQL Query of the new system:
```sparql

```

Result in JSON output:
```json

```