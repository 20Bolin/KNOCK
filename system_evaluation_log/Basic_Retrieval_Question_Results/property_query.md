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
**Hoe groot is mijn tuin?**  
**How big is my garden?**  

### Tested example in Dutch and English
**Hoe groot is mijn tuin, adres Heiligenbergerweg 64, 3816 AL Amersfoort?/**  
**How big is my garden, address Heiligenbergerweg 64, 3816 AL Amersfoort?**  

GraphQL Query of the old system:
```graphql
{
  locatieserver(adres: "heiligenbergerweg 64 3816 al amersfoort") {
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
{'data': {'locatieserver': {'nummeraanduidingen': [{'hoofdadresvan': [{'perceel': [{'lokaaID': 'NL.IMKAD.KadastraalObject.25490295770000', 'geometrie': 'POLYGON((155835.927 462734.515,155837.192 462735.074,155870.196 462749.435,155887.78 462757.051,155880.959 462762.062,155868.783 462775.791,155825.987 462756.805,155835.927 462734.515))', 'chatbotanswer': 'De kadastrale grootte van dit perceel is 1287 m2', 'perceeloppervlakte': 1287, 'akrgemeente': 'AMF00', 'sectie': 'G', 'perceelnummer': 2957}]}]}]}}}
```

GraphQL Query of the new system:
```graphql
PREFIX bag: <http://bag.basisregistraties.overheid.nl/def/bag#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX sor: <https://data.kkg.kadaster.nl/sor/model/def/>
PREFIX kad: <https://data.kkg.kadaster.nl/kad/model/def/>
PREFIX skos: <http://www.w3.org/2004/02/skos/core#>
PREFIX geo: <http://www.opengis.net/ont/geosparql#>
PREFIX nen3610: <https://data.kkg.kadaster.nl/nen3610/model/def/>
PREFIX wbk: <https://data.labs.kadaster.nl/cbs/wbk/vocab/>
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>

SELECT ?oppervlakte
WHERE {
    ?vbo a sor:Verblijfsobject;
         sor:hoofdadres ?na;
         sor:maaktDeelUitVan ?geb.
    
    ?geb a sor:Gebouw;
         geo:hasGeometry [
             geo:asWKT ?geo_wgs84;
             rdfs:isDefinedBy bag:
         ].
    
    ?per a sor:Perceel;
         sor:hoortBij ?na;
         sor:oppervlakte ?oppervlakte.
    
    ?na a sor:Nummeraanduiding;
         sor:huisnummer 64;
         sor:ligtAan ?openbareruimte.
    
    ?openbareruimte a sor:OpenbareRuimte;
                    skos:prefLabel "Heiligenbergerweg"@nl;
                    sor:ligtIn ?woonplaats.
    
    ?woonplaats a sor:Woonplaats;
                skos:prefLabel "Amersfoort"@nl.
}
LIMIT 1
```

Result in JSON output:
```json
[{'oppervlakte': '1287'}]
```


### Question template in Dutch and English
**Wat is mijn perceelnummer?**  
**What is my plot number?**  

### Tested example in Dutch and English
**Wat is mijn perceelnummer, adres Hoptille 121, Amsterdam?**  
**What is my plot number, address Hoptille 121, Amsterdam?**  

GraphQL Query of the old system:
```graphql
{
  locatieserver(adres: "hoptille 121 amsterdam") {
    nummeraanduidingen (actualiteit: {
    actueel_formeel: true, actueel_materieel: true}
    ) {
      hoofdadresvan(actualiteit: {
      actueel_formeel: true, actueel_materieel: true}
      ) {
        perceel(actualiteit: {
        actueel_formeel: true, actueel_materieel: true}
        ) {
        lokaaID:gml_id geometrie:begrenzing chatbotanswer:prompt(prompt: "Het perceelnummer van dit perceel is @perceelnummer") perceelnummer akrgemeente sectie }
      }
    }
  }
}
```

Result in JSON output:
```json
{'data': {'locatieserver': {'nummeraanduidingen': [{'hoofdadresvan': [{'perceel': [{'lokaaID': 'NL.IMKAD.KadastraalObject.14430521970000', 'geometrie': 'POLYGON((125539.547 480196.013,125549.729 480183.39,125545.797 480180.214,125549.603 480175.373,125553.661 480170.211,125558.872 480163.859,125562.841 480159.021,125568.088 480152.752,125572.152 480147.896,125575.927 480150.946,125578.028 480148.344,125579.297 480149.079,125584.8 480153.523,125585.548 480152.597,125592.351 480158.111,125581.098 480172.057,125586.366 480176.31,125584.732 480178.334,125579.464 480174.08,125574.248 480180.549,125573.201 480181.837,125578.469 480186.09,125576.835 480188.114,125571.568 480183.86,125565.054 480191.941,125562.668 480194.884,125560.187 480197.944,125554.928 480204.471,125560.196 480208.724,125558.562 480210.748,125557.868 480210.175,125553.3 480206.487,125544.774 480217.043,125550.039 480221.294,125548.405 480223.318,125543.14 480219.067,125536.673 480227.075,125541.938 480231.326,125540.304 480233.349,125535.04 480229.098,125518.395 480249.716,125523.669 480253.978,125522.087 480255.937,125516.812 480251.676,125508.277 480262.24,125513.552 480266.501,125511.912 480268.53,125506.638 480264.27,125494.156 480279.722,125492.079 480282.982,125467.442 480320.833,125473.114 480324.525,125471.671 480326.691,125466.025 480323.014,125458.612 480334.4,125464.303 480338.105,125462.88 480340.293,125457.19 480336.589,125451.735 480344.964,125457.413 480348.66,125455.994 480350.842,125450.317 480347.147,125442.54 480359.088,125440.368 480362.422,125435.806 480369.436,125439.999 480372.161,125438.593 480374.323,125434.399 480371.599,125426.983 480383.007,125432.67 480386.705,125431.25 480388.891,125425.564 480385.194,125419.958 480393.811,125425.658 480397.517,125424.234 480399.708,125418.535 480396.003,125408.789 480410.986,125402.929 480407.176,125403.594 480406.153,125394.947 480400.531,125396.48 480398.173,125393.701 480396.601,125392.77 480398.44,125392.64 480398.628,125392.466 480398.775,125392.258 480398.869,125392.033 480398.906,125391.806 480398.88,125391.594 480398.796,125391.412 480398.658,125389.674 480396.926,125398.581 480383.783,125404.891 480387.897,125405.866 480386.397,125400.578 480382.956,125415.473 480360.052,125419.542 480362.699,125428.443 480349.034,125424.367 480346.382,125455.142 480299.061,125459.218 480301.711,125463.497 480295.13,125459.421 480292.48,125467.94 480279.383,125471.016 480274.651,125475.712 480277.702,125477.283 480275.287,125474.411 480273.421,125479.13 480270.306,125481.206 480271.659,125482.389 480270.058,125480.386 480268.456,125481.941 480263.031,125484.039 480264.753,125486.777 480261.361,125482.967 480258.319,125489.643 480250,125495.21 480243.127,125504.382 480231.803,125508.441 480226.792,125512.225 480229.849,125517.16 480223.736,125513.327 480220.739,125522.681 480209.157,125531.838 480197.817,125535.686 480193.051,125539.547 480196.013))', 'chatbotanswer': 'Het perceelnummer van dit perceel is 5219', 'perceelnummer': 5219, 'akrgemeente': 'WPK02', 'sectie': 'L'}]}]}]}}}
```

SPARQL Query of the new system:
```sparql
PREFIX bag: <http://bag.basisregistraties.overheid.nl/def/bag#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX sor: <https://data.kkg.kadaster.nl/sor/model/def/>
PREFIX kad: <https://data.kkg.kadaster.nl/kad/model/def/>
PREFIX skos: <http://www.w3.org/2004/02/skos/core#>
PREFIX geo: <http://www.opengis.net/ont/geosparql#>

SELECT DISTINCT ?perceelnummer
WHERE {
    ?vbo a sor:Verblijfsobject;
         sor:hoofdadres ?na;
         sor:maaktDeelUitVan ?geb.
    
    ?geb a sor:Gebouw;
         geo:hasGeometry [
             geo:asWKT ?geo_wgs84;
             rdfs:isDefinedBy bag:
         ].
    
    ?per a sor:Perceel;
         sor:hoortBij ?na;
         sor:perceelnummer ?perceelnummer.
    
    ?na a sor:Nummeraanduiding;
         sor:huisnummer 121;
         sor:ligtAan ?openbareruimte.
    
    ?openbareruimte a sor:OpenbareRuimte;
                    skos:prefLabel "Hoptille"@nl;
                    sor:ligtIn ?woonplaats.
    
    ?woonplaats a sor:Woonplaats;
                skos:prefLabel "Amsterdam"@nl.
}
LIMIT 1
```

Result in JSON output:
```json
[{'perceelnummer': '5219'}]
```


### Question template in Dutch and English
**Hoe groot is mijn woning?**  
**How big is my house?**  

### Tested example in Dutch and English
**Hoe groot is mijn woning, adres Calslaan 42A34, 7522 ME Enschede?**  
**How big is my house, address Calslaan 42A34, 7522 ME Enschede?**  

GraphQL Query of the old system:
```graphql
{
  locatieserver(adres: "calslaan 42 34 7522 me enschede") {
    nummeraanduidingen (actualiteit: {
    actueel_formeel: true, actueel_materieel: true}
    ) {
      hoofdadresvan(actualiteit: {
      actueel_formeel: true, actueel_materieel: true}
      ) {
        chatbotanswer:prompt(prompt: "De oppervlakte van dit verblijfsobject is @oppervlakte m2") maaktdeeluitvan_object(actualiteit: {
        actueel_formeel: true, actueel_materieel: true}
        ) {
        oorspronkelijkbouwjaar lokaalID begingeldigheid status:pandstatus geometrie }
      }
    }
  }
}
```

Result in JSON output:
```json
{'data': {'locatieserver': {'nummeraanduidingen': [{'hoofdadresvan': [{'chatbotanswer': 'De oppervlakte van dit verblijfsobject is 206 m2', 'maaktdeeluitvan_object': [{'oorspronkelijkbouwjaar': 1969, 'lokaalID': '0153100000281025', 'begingeldigheid': '2019-05-08', 'status': 'Pand in gebruik', 'geometrie': 'POLYGON Z ((254699.31 473729.437 0,254692.811 473733.362 0,254692.582 473733.5 0,254692.056 473733.847 0,254692.042 473733.856 0,254692.008 473733.805 0,254691.85 473733.565 0,254685.842 473723.571 0,254692.801 473719.417 0,254692.636 473719.14 0,254695.119 473717.629 0,254697.523 473716.178 0,254697.544 473716.213 0,254697.688 473716.448 0,254703.877 473726.721 0,254699.31 473729.437 0))'}]}]}]}}}
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
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>

SELECT ?oppervlakte 
WHERE {
    ?vbo a sor:Verblijfsobject;
         sor:oppervlakte ?oppervlakte;
         sor:hoofdadres ?na;
         sor:maaktDeelUitVan ?geb.
    
    ?geb a sor:Gebouw;
         sor:oorspronkelijkBouwjaar ?bo;
         geo:hasGeometry [
             geo:asWKT ?geo_wgs84;
             rdfs:isDefinedBy bag:
         ].
    
    ?na a sor:Nummeraanduiding;
         sor:huisnummer 42;
         sor:ligtAan ?openbareruimte.
         Optional{?na sor:huisletter "A"}
         Optional{?na sor:huisnummertoevoeging "34"}

    ?openbareruimte a sor:OpenbareRuimte;
                    skos:prefLabel "Calslaan"@nl;
                    sor:ligtIn ?woonplaats.
    
    ?woonplaats a sor:Woonplaats;
                skos:prefLabel "Enschede"@nl.
}
LIMIT 1
```

Result in JSON output:
```json
[{'oppervlakte': '206'}]
```


### Question template in Dutch and English
**Wat is het leeftijd van mijn huis?**  
**What is the age of my house?**  

### Tested example in Dutch and English
**Wat is het leeftijd van mijn huis, adres Calslaan 42A34, 7522 ME Enschede?**  
**What is the age of my house, address Calslaan 42A34, 7522 ME Enschede?**  

GraphQL Query of the old system:
```graphql
{
  locatieserver(adres: "calslaan 42 34 a34 7522 me enschede") {
    nummeraanduidingen (actualiteit: {
    actueel_formeel: true, actueel_materieel: true}
    ) {
      hoofdadresvan(actualiteit: {
      actueel_formeel: true, actueel_materieel: true}
      ) {
        maaktdeeluitvan_object(actualiteit: {
        actueel_formeel: true, actueel_materieel: true}
        ) {
        oorspronkelijkbouwjaar lokaalID chatbotanswer:prompt(prompt: "Dit pand is gebouwd in @oorspronkelijkbouwjaar") status:pandstatus begingeldigheid geometrie }
      }
    }
  }
}
```

Result in JSON output:
```json
{'data': {'locatieserver': {'nummeraanduidingen': [{'hoofdadresvan': [{'maaktdeeluitvan_object': [{'oorspronkelijkbouwjaar': 1969, 'lokaalID': '0153100000281025', 'chatbotanswer': 'Dit pand is gebouwd in 1969', 'status': 'Pand in gebruik', 'begingeldigheid': '2019-05-08', 'geometrie': 'POLYGON Z ((254699.31 473729.437 0,254692.811 473733.362 0,254692.582 473733.5 0,254692.056 473733.847 0,254692.042 473733.856 0,254692.008 473733.805 0,254691.85 473733.565 0,254685.842 473723.571 0,254692.801 473719.417 0,254692.636 473719.14 0,254695.119 473717.629 0,254697.523 473716.178 0,254697.544 473716.213 0,254697.688 473716.448 0,254703.877 473726.721 0,254699.31 473729.437 0))'}]}]}]}}}
```

SPARQL Query of the new system:
```sparql
PREFIX bag: <http://bag.basisregistraties.overheid.nl/def/bag#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX sor: <https://data.kkg.kadaster.nl/sor/model/def/>
PREFIX kad: <https://data.kkg.kadaster.nl/kad/model/def/>
PREFIX skos: <http://www.w3.org/2004/02/skos/core#>
PREFIX geo: <http://www.opengis.net/ont/geosparql#>
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>

SELECT ?bo 
WHERE {
    ?vbo a sor:Verblijfsobject;
         sor:hoofdadres ?na;
         sor:maaktDeelUitVan ?geb.
    
    ?geb a sor:Gebouw;
         sor:oorspronkelijkBouwjaar ?bo;
         geo:hasGeometry [
             geo:asWKT ?geo_wgs84;
             rdfs:isDefinedBy bag:
         ].
    
    ?na a sor:Nummeraanduiding;
         sor:huisnummer 42;
         sor:ligtAan ?openbareruimte.
         Optional{?na sor:huisletter "A"}
         Optional{?na sor:huisnummertoevoeging "34"}

    ?openbareruimte a sor:OpenbareRuimte;
                    skos:prefLabel "Calslaan"@nl;
                    sor:ligtIn ?woonplaats.
    
    ?woonplaats a sor:Woonplaats;
                skos:prefLabel "Enschede"@nl.
}
LIMIT 1
```

Result in JSON output:
```json
[{'bo': '1969'}]
```


### Question template in Dutch and English
**Wat is de bouw status van mijn huis?**  
**What is the construction status of my house?**  

### Tested example in Dutch and English
**Wat is de bouw status van mijn huis, adres Calslaan 42A34, 7522 ME Enschede?**  
**What is the construction status of my house at Calslaan 42A34, 7522 ME Enschede?**  

GraphQL Query of the old system:
```graphql
{
  locatieserver(adres: "calslaan 42 34 7522 me enschede") {
    nummeraanduidingen (actualiteit: {
    actueel_formeel: true, actueel_materieel: true}
    ) {
      hoofdadresvan(actualiteit: {
      actueel_formeel: true, actueel_materieel: true}
      ) {
        maaktdeeluitvan_object(actualiteit: {
        actueel_formeel: true, actueel_materieel: true}
        ) {
        lokaalID status:pandstatus chatbotanswer:prompt(prompt: "Dit pand heeft bagstatus @pandstatus") begingeldigheid geometrie }
      }
    }
  }
}
```

Result in JSON output:
```json
{'data': {'locatieserver': {'nummeraanduidingen': [{'hoofdadresvan': [{'maaktdeeluitvan_object': [{'lokaalID': '0153100000281025', 'status': 'Pand in gebruik', 'chatbotanswer': 'Dit pand heeft bagstatus Pand in gebruik', 'begingeldigheid': '2019-05-08', 'geometrie': 'POLYGON Z ((254699.31 473729.437 0,254692.811 473733.362 0,254692.582 473733.5 0,254692.056 473733.847 0,254692.042 473733.856 0,254692.008 473733.805 0,254691.85 473733.565 0,254685.842 473723.571 0,254692.801 473719.417 0,254692.636 473719.14 0,254695.119 473717.629 0,254697.523 473716.178 0,254697.544 473716.213 0,254697.688 473716.448 0,254703.877 473726.721 0,254699.31 473729.437 0))'}]}]}]}}}
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
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>

SELECT ?status
WHERE {
    ?vbo a sor:Verblijfsobject;
         sor:hoofdadres ?na;
         sor:maaktDeelUitVan ?geb.
    
    ?geb a sor:Gebouw;
         sor:oorspronkelijkBouwjaar ?bo;
         sor:status ?status;
         geo:hasGeometry [
             geo:asWKT ?geo_wgs84;
             rdfs:isDefinedBy bag:
         ].
    
    ?na a sor:Nummeraanduiding;
         sor:huisnummer 42;
         sor:postcode "7522ME";
         sor:ligtAan ?openbareruimte.
         Optional{?na sor:huisletter "A"}
         Optional{?na sor:huisnummertoevoeging "34"}

    ?openbareruimte a sor:OpenbareRuimte;
                    skos:prefLabel "Calslaan"@nl;
                    sor:ligtIn ?woonplaats.
    
    ?woonplaats a sor:Woonplaats;
                skos:prefLabel "Enschede"@nl.
}
LIMIT 1
```

Result in JSON output:
```json
[{'status': 'https://data.kkg.kadaster.nl/kad/model/con/pandInGebruik'}]
```