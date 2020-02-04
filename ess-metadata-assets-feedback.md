# Feedback on the RDF metadata assets published by Eurostat

Eurostat has collaborated with the Publications Office of the European Union (OP) to disseminate various statistical metadata assets using LOD technologies. Some of these metadata assets are now available on the OP's EU Vocabularies website at the following addresses:

  * Combined Nomenclature [CN, version 2019](https://op.europa.eu/en/web/eu-vocabularies/at-dataset/-/resource/dataset/combined-nomenclature/version-2019)
  * Classification of Products by Activity [CPA, version 2.1](https://op.europa.eu/en/web/eu-vocabularies/at-dataset/-/resource/dataset/statistical-classification/version-2.1)
  * [SDMX Glossary 2018](https://op.europa.eu/en/web/eu-vocabularies/at-dataset/-/resource/dataset/sdmx-glossary/version-2018)

For each of these objects a URI has been created for each concept of the glossary as well as for each code in the classifications. So by clicking on the URIs the user is able to retrieve information about the concepts and codes in RDF format. These are still test publications, which means that some visualisation / navigation elements on the EU Vocabularies website do not work properly and could be improved, and most importantly that the URIs used are temporary and will certainly change when we make a more official release. 

This page presents Insee's feedback regarding those publications

## General feedback

## Specific remarks

### Common

### Combined Nomenclature

#### Multiple notations

According to the [SKOS Reference](https://www.w3.org/TR/skos-reference/#L2637), "It is not common practice to assign more than one notation from the same notation system (i.e., with the same datatype URI)". An example in the CN2018 is http://publications.europa.eu/resource/authority/cn2019/010221, which has two values for `skos:notation`, without datatype (thus defaulting to the same datatype).

### Classification of Products by Activity

### SDMX Glossary 2018

## References

  * [Stamina project](https://github.com/FranckCo/Stamina)
  * [Classification explorer](http://classifications.scfe.eu/)
  * [Insee's RDF metadata](http://rdf.insee.fr/)
