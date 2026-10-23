# Introduction

SOGO (Scaling Ontology for Geospatial Observations) is a lightweight and generic ontology designed to capture the semantics of spatial scaling in spatial knowledge graphs. Building on the principles of SOSA and SSN for modeling observations and their associations with spatial entities, such as grid cells and regions, SOGO defines the classes and properties required to formally represent downscaling and upscaling processes, making scaling procedures first-class citizens of knowledge graphs. By explicitly representing scaling operations and, in particular, their semantic aspects, SOGO captures the nuances of spatial structure and distribution while enabling the logic underlying scaling procedures to be published alongside the data itself. The overview of the SOGO ontology is depicted in the picture below. 

![SOGO Data Model](/SOGO_model.png)


## Ontology Overview

The ontology is organized around the ScalingProcedure class, which represents a procedure for transforming observations or properties between different spatial resolutions. Each scaling procedure is associated with a ScalingProperty, a ScalingMethod, and a SpatialMap. It may also involve auxiliary properties and specify one or more temporal extents. The repository includes the [ontology file](qlever/data/sogo.ttl) and [example use cases](qlever/data/examples.ttl) demonstrating its use for upscaling and downscaling scenarions.



### SOGO Core Classes 

<table>
<thead>
<tr>
<th>Class</th>
<th>Definition</th>
<th>Superclass</th>
</tr>
</thead>
<tbody>
<tr>
<td><code>ScalingProcedure</code></td>
<td>A procedure that transforms observations or properties from one spatial resolution to another.</td>
<td><code>Procedure</code></td>
</tr>
<tr>
<td><code>ScalingMethod</code></td>
<td>A method specifying how a scaling transformation is performed, with disjoint subclasses representing upscaling and downscaling methods.</td>
<td>—</td>
</tr>
<tr>
<td><code>UpscalingMethod</code></td>
<td>A class that defines aggregation-based scaling methods for deriving observations at a coarser spatial resolution from observations at a finer resolution.</td>
<td><code>ScalingMethod</code></td>
</tr>
<tr>
<td><code>DownscalingMethod</code></td>
<td>A class that defines disaggregation-based scaling methods for deriving observations at a finer spatial resolution from observations at a coarser resolution.</td>
<td><code>ScalingMethod</code></td>
</tr>
<tr>
<td><code>ScalingProperty</code></td>
<td>The primary observed property whose values are transformed by a scaling procedure.</td>
<td><code>sosa:Property</code></td>
</tr>
<tr>
<td><code>AuxiliaryProperty</code></td>
<td>An auxiliary observed property that may be required by specific scaling methods and used as a reference or predictor during a scaling procedure.</td>
<td><code>sosa:Property</code></td>
</tr>
<tr>
<td><code>SpatialMap</code></td>
<td>A specification of the spatial correspondence between source and target geographic features involved in a scaling procedure.</td>
<td>—</td>
</tr>
</tbody>
</table>

### SOGO object properties and their semantics

<table>
<thead>
<tr>
<th>Property</th>
<th>Domain</th>
<th>Range</th>
<th>Cardinality</th>
<th>Definition</th>
</tr>
</thead>
<tbody>
<tr>
<td><code>hasScalingProperty</code></td>
<td><code>ScalingProcedure</code></td>
<td><code>ScalingProperty</code></td>
<td>1..1</td>
<td>Associates a scaling procedure with the primary property being scaled.</td>
</tr>
<tr>
<td><code>hasAuxiliaryProperty</code></td>
<td><code>ScalingProcedure</code></td>
<td><code>AuxiliaryProperty</code></td>
<td>0..*</td>
<td>Associates a scaling procedure with auxiliary properties used to support the scaling procedure.</td>
</tr>
<tr>
<td><code>usesScalingMethod</code></td>
<td><code>ScalingProcedure</code></td>
<td><code>ScalingMethod</code></td>
<td>1..1</td>
<td>Specifies the scaling method employed by a procedure.</td>
</tr>
<tr>
<td><code>hasSpatialMap</code></td>
<td><code>ScalingProcedure</code></td>
<td><code>SpatialMap</code></td>
<td>1..1</td>
<td>Associates a scaling procedure with the spatial mapping used to establish source–target correspondence.</td>
</tr>
<tr>
<td><code>hasTemporalExtent</code></td>
<td><code>ScalingProcedure</code></td>
<td><code>time:Instant</code>, <code>time:Interval</code>, <code>time:Year</code></td>
<td>0..*</td>
<td>Specifies the allowed temporal extent values during which the scaling procedure can be applied.</td>
</tr>
<tr>
<td><code>sourceFeature</code></td>
<td><code>SpatialMap</code></td>
<td><code>geo:Feature</code></td>
<td>1..1</td>
<td>Specifies the source geographic feature from which values are scaled.</td>
</tr>
<tr>
<td><code>targetFeature</code></td>
<td><code>SpatialMap</code></td>
<td><code>geo:Feature</code></td>
<td>1..1</td>
<td>Specifies the target geographic feature for which the scaled values are generated.</td>
</tr>
<tr>
<td><code>mappingQuery</code></td>
<td><code>SpatialMap</code></td>
<td><code>xsd:string</code></td>
<td>1..1</td>
<td>Defines a SPARQL query that specifies the spatial mapping between the source and target regions.</td>
</tr>
</tbody>
</table>


## Scaling Examples


### Upscaling Use Case 

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



### Downscaling Example 

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

The examples are available using qlever SPARQL enghine. Please follow the qlever installation guides and then you can run the endpoint via the following commands: 

The example dataset is available through the QLever SPARQL engine. Please follow the [QLever installation guides](https://github.com/ad-freiburg/qlever) to set up the engine. Once the installation is complete, you can start the SPARQL endpoint by running the following commands:


```bash
# Creating index 
cd qlever
qlever index --overwrite-existing --parallel-parsing false

# start the SPARQL server
qlever start

# stop the SPARQL server
qlever stop 
```

Once the setup is complete, you can run the scaling examples provided in the [`examples.ipynb`](examples.ipynb) notebook.
