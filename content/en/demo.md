---
title : "Demo"
---

Please see the included video to see how to use the demo instance.
You can also go through the testing scenario seen in the video step by step yourself.

<a class="btn btn-primary btn-lg px-4 mb-2" href="https://mff-uk.github.io/atlas/" role="button">Go to the demo instance</a>

<div class="video-container" style="margin-top: 2em;">
    <iframe width="560" height="315" src="https://www.youtube.com/embed/m3_xrMNDJkc" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>
</div>

## Full testing scenario
In this scenario, we are going to create a specification for _Company data_ as the specification editor. Next, we will imagine that a data producer follows the specification for JSON and provides compliant data. Finally, we are going to show how a data consumer can reuse the specification to load the data from the publisher into a set of various local databases, each with its own data model.

### Specification editor
We start as the specification editor role.

1. Open the [Atlas demo instance](https://mff-uk.github.io/atlas/) in a new window, so that you can follow this scenario.
2. Click the "+" button to create a new project.
3. Name the project and click the Create button.
4. We will start with _Edit specification_
5. Now, we need to indicate, that our vocabulary source is going to be the EU Core Vocabularies, the vocabularies reused from them, and their supplement.[^1] We do that by clicking on _RDF files_ in the _Vocabulary sources_ section, providing this list and clicking the _Save_ button:
```
https://mff-uk.github.io/demo-vocabularies/original/adms.ttl
https://mff-uk.github.io/demo-vocabularies/original/dublin_core_terms.ttl
https://semiceu.github.io/Core-Business-Vocabulary/releases/2.00/voc/core-business-ap.ttl
https://semiceu.github.io/Core-Location-Vocabulary/releases/2.0.1/voc/core-location-ap.ttl
https://www.w3.org/ns/org
https://www.w3.org/ns/regorg
https://mff-uk.github.io/demo-vocabularies/original/skos.rdf
https://schema.org/version/latest/schemaorg-current-https.ttl
https://stirdata.github.io/data-specification/stirdata.ttl
https://mff-uk.github.io/demo-vocabularies/modified/modifications.ttl
```
6. Next, we create our desired data structure. Click on the _Create data structure_ tile.
7. We set the root of the structure using the _Set root element_ button and choosing _Legal Entity_
8. Now we add the contents of the _Legal Entity_ root by hovering over the Legal Entity and clicking the "+" button. We want to add
   1. Legal Entity -> Associations -> Identifier
   2. Legal Entity -> Attributes -> legal name
   3. Organization -> Attributes -> foundingDate
   4. Legal Entity -> Associations -> registered address
9. We click the _Confirm (4)_ button and expand the Legal Entity tree to see the added contents.
10. The same way as before, we are going to add the following content to Identifier
    1. Identifier -> Attributes -> notation
11. The same way as before, we are going to add the following content to registered address
    1. Address -> address area
    2. Address -> administrative unit level 1
    3. Address -> administrative unit level 2
    4. Address -> locator designator
    5. Address -> post code
    6. Address -> post name
    7. Address -> thoroughfare
12. Now, the basic structure is done. However, we need to adjust the technical labels of attributes and associations to be used in technical artifacts such as JSON data and JSON Schema, the multiplicities and the datatypes of the attributes as this information is missing in the vocabulary sources. We do that by using the "edit" button next to the attributes and associations and adjusting as necessary. We can also adjust the order of the attributes by dragging them. The result should look like this:
    1. identifier \[1..1\]
       1. notation: `String` \[1..1\]
    2. legal name: `Text` \[1..1\]
    3. *founding_date*: `Date` \[0..1\]
    4. registered_address \[0..1\]
       1. address_area: `Text` \[0..1\]
       2. locator_designator: `String` \[0..1\]
       3. post_code: `String` \[0..1\]
       4. post_name: `Text` \[0..1\]
       5. *admin_unit_l1*: `Text` \[0..1\]
       6. *admin_unit_l1*: `Text` \[0..1\]
       7. thoroughfare: `Text` \[0..1\]
13. Now, _Back to specification manager_ and _Go back to Atlas_
14. Go to _Generate artifacts_, click on _Generate .ZIP file_. The specification including the technical artifacts is downloaded and can be published on the Web. _Go back to Atlas_

### Data producer
1. We do not focus on the data producer role in this scenario. We imagine that the data producer finds the data specification and publishes data compliant with the JSON Schema from the specification, producing a JSON-LD file which is also an RDF serialization of the compliant data. We provide a [sample containing data about two companies](/test.json) from the Czech Business Registry.

### Data consumer
1. In Atlas with the project for _Company data_ open, click on _Import schema_ in the Data consumer section.
2. You can see the Schema category containing the loaded specification. Click the _Save_ button.
3. Go to _Data sources_ and create new JSON-LD store type data source, with custom label and the URL of the [sample data file](/test.json).
4. Go to _Jobs_, create a new _Import data_ job, label it and select the created data source.
5. Run the data import job. Now, the data from the publisher is loaded in the MM-evocat internal data representation.
6. Now, we are going to decompose the data into two logical models. We start with MongoDB.
    1. In MongoDB, we will store company data and addresses. Click on _Logical Models_, _Create new_, MongoDB database, custom label, e.g. "Mongo Company Data".
    2. Click on the created logical model and _Create new_ under _Mappings_
    3. Click on the node _Legal Entity_, _Confirm_. Note that the current mapping of the Schema category to the MongoDB data model can be seen on the right-hand side. Do not click on "Finish mapping" until we are done with creating it.
    4. Click on the "+" button in the `legal_entity` on the right hand side to add a property.[^2] Click on the _notation_ node in the graph at the end of the path _Legal Entity_ -> _Identifier_ -> _notation_.
    5. Click _Next_, select _Static_, click _Next_, rename to `_id` and _Finish_.
    6. In the same way, add _legalName_ and _foundingDate_.
    7. When adding _Address_, note that this needs to be added as a _Complex_ type of property, as it will have subproperties.
    8. Finally, we are going to add the properties of the address and click on _Finish mapping_.
15. Now, we add a second model for PostgreSQL
    1. Click on _Logical Models_, _Create new_, PostgreSQL database, custom label, e.g. "SQL Addresses".
    2. Click on the created logical model and _Create new_ under _Mappings_
    3. We select _Address_ as a root and add the address attributes (their `_value` parts) to the mapping and _Finish mapping_.
16. Click on _Jobs_ and create a _Category to Model_ job with the MongoDB model selected. Run the job to store the data from the MM-evocat internal representation to MongoDB.
17. Do the same for the PostgreSQL model.

[^1]: Note that the links point to the original vocabularies whenever they support [CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS), which is necessary for Dataspecer to be able to access them. The vocabularies that do not support CORS were cached in our [GitHub repository](https://github.com/mff-uk/demo-vocabularies).
[^2]: This is a bit similar to what we did as a Specification editor in Dataspecer. Re-using the data structure from there for this step is our future work.
