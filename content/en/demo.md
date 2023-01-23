---
title : "Demo"
---

Please see the included video to see how to use the demo instance.

<a class="btn btn-primary btn-lg px-4 mb-2" href="https://mff-uk.github.io/atlas/" role="button">Go to the demo instance</a>

<div class="video-container" style="margin-top: 2em;">
    <iframe width="560" height="315" src="https://www.youtube.com/embed/m3_xrMNDJkc" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>
</div>

## Basic testing scenario
TODO

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
1. We do not focus on the data producer role in this scenario. We imagine that the data producer finds the data specification and publishes data compliant with the JSON Schema from the specification, producing a JSON-LD file which is also an RDF serialization of the compliant data. We provide a [sample containing data about two companies](/static/test.json) from the Czech Business Registry.

### Data consumer
TODO

[^1]: Note that the links point to the original vocabularies whenever they support [CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS), which is necessary for Dataspecer to be able to access them. The vocabularies that do not support CORS were cached in our [GitHub repository](https://github.com/mff-uk/demo-vocabularies).
