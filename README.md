# Introduction

SOGO (Scaling Ontology for Geospatial Observations) is a lightweight and generic ontology designed to capture the semantics of spatial scaling in spatial knowledge graphs. Building on the principles of SOSA and SSN for modeling observations and their associations with spatial entities, such as grid cells and regions, SOGO defines the classes and properties required to formally represent downscaling and upscaling processes, making scaling procedures first-class citizens of knowledge graphs. By explicitly representing scaling operations and, in particular, their semantic aspects, SOGO captures the nuances of spatial structure and distribution while enabling the logic underlying scaling procedures to be published alongside the data itself.


![SOGO Data Model](/SOGO_model.png)


## Upscaling Use Case 

Finding state population based on community populations: 

municipality (Gemeinde) --> district (Bezirk) --> state (Land)

Example of Burgenland (id/state/1) population based on 171 community populations will be 285685 (sum aggregation). 

```turtle
<id/observation/bf2adddd553646fd95e74047536b5d91> a sosa:Observation ;  
	sosa:resultTime "2011-10-31T00:00:00Z"^^xsd:dateTime ; 
	sosa:hasFeatureOfInterest <http://crispy.ai.wu.ac.at/crisp/id/state/1> ; 
	sosa:observedProperty <http://crispy.ai.wu.ac.at/crisp/population> ; 
	sosa:hasSimpleResult "285685"^^xsd:integer ; 
	sosa:usedProcedure <http://crispy.ai.wu.ac.at/crisp/populationUpscaling> . 
```



## Downscaling Example 

Finding H3 Cell populations based on community population and distributon of roads. 

Total roads: 1815437

Auxiliary observation: 

```turtle
<id/observation/881e064c83fffff/roads> a sosa:Observation ;  
	sosa:resultTime "2011-01-01T00:00:00Z"^^xsd:dateTime ; 
	sosa:hasFeatureOfInterest <id/cell/881e064c83fffff> ; 
	sosa:observedProperty :roadLength; 
	sosa:hasSimpleResult "10807"^^xsd:integer .
```


Property observation of community population:

```turtle
<id/observation/92101/POP_2011> a sosa:Observation ;  
	sosa:resultTime "2011-10-31T00:00:00Z"^^xsd:dateTime ; 
	sosa:hasFeatureOfInterest <id/community/92101> ; 
	sosa:observedProperty :population; 
	sosa:hasSimpleResult "143962"^^xsd:integer . 
```

Based on these and SOGO definitions, we infer the following population for the H3 Cells: 

10807 / 1815437 = cell_population / 143962 ==> cell_population = 857

```turtle
<id/observation/2c4c837ee0d44238bdb2443efab94eea> a sosa:Observation ;  
	sosa:resultTime "2011-10-31T00:00:00Z"^^xsd:dateTime ; 
	sosa:hasFeatureOfInterest <http://crispy.ai.wu.ac.at/crisp/id/cell/881e064c83fffff> ; 
	sosa:observedProperty <http://crispy.ai.wu.ac.at/crisp/population> ; 
	sosa:hasSimpleResult "857"^^xsd:integer ; 
	sosa:usedProcedure <http://crispy.ai.wu.ac.at/crisp/populationDownscaling> .
```


## Running Examples

```bash
qlever index --overwrite-existing --parallel-parsing false

qlever start

qlever stop 
```

