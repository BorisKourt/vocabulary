# vocabulary

Clojure provides for the definition of [keywords](https://clojure.org/reference/data_structures#Keywords), which function as identifiers within Clojure code, and serve many useful purposes.  These keywords can be interned within specific namespaces to avoid collisions. The role played by these keywords is very similar to the role played by IRIs within the [Linked Open Data](http://linkeddata.org/) (LOD) community, which also has a regime for providing namespaces.

This is an experiment exploring the notion that some synergy can emerge from integrating Linked Data IRIs with Clojure keywords interned within namepaces using Clojure's metadata facility.

## Installation

[![Clojars Project](https://img.shields.io/clojars/v/ont-app/vocabulary.svg)](https://clojars.org/ont-app/vocabulary)

Include this in your project.clj...
```
 (defproject .....
  :dependencies 
  [...
   [ont-app/vocabulary "0.1.0-SNAPSHOT"]
   ...
   ])
```   


## Usage

Require ....

```
(ns ...
 (:require
   ...
   [vocabulary.core :as voc]
   ...))
```
This will load function definitions interned in the vocabulary.core namespace, and also a number of other `ns` declarations, each dedicated to an LOD namespace.

There are also modules for `vocabulary.wikidata` and `vocabulary.linguistics`, described below.

The `vocabulary.core` module starts with this `ns` declaration:

```
(ns vocabulary.core
  {:doc "Defines utilities and a set of namespaces for commonly used linked data constructs, metadata of which specifies RDF namespaces, prefixes and other details."
   :vann/preferredNamespacePrefix "voc"
   :vann/preferredNamespaceUri
   "http://rdf.naturallexicon.org/ont-app/vocabulary/"
   }
   (:require ...))
```

Note that the metadata for this module includes some keywords in this format:
```
:<prefix>:<name>
```

With these statements...
```
:vann/preferredNamespaceUri "http://rdf.naturallexicon.org/ont-app/vocabulary/"
:vann/preferredNamespacePrefix "voc"
```
...declaring that this namespace is publicly addressable with that IRI, with the prefix 'voc'.

The namespace for `vann` is also declared as an `ns` further down in the `vocab.core.clj` file:

```
(ns org.naturallexicon.lod.vann
{
  :rdfs/label "VANN"
  :dc/description "A vocabulary for annotating vocabulary descriptions"
  :vann/preferredNamespaceUri "http://purl.org/vocab/vann"
  :vann/peferredNamespacePrefix "vann"
  :foaf/homepage "http://vocab.org/vann/"
})
```

The relations [preferredNamespaceUri](http://vocab.org/vann/#preferredNamespacePrefix) and [preferredNamespacePrefix](http://vocab.org/vann/#preferredNamespaceUri) are part of the public VANN vocabulary, with well-defined usage and semantics.

```
The vocabulary.core module declares a number of commonly used namespaces, e.g.

(ns org.naturallexicon.lod.foaf
{
 :dc/title "Friend of a Friend (FOAF) vocabulary"
 :dc/description "The Friend of a Friend (FOAF) RDF vocabulary,
 described using W3C RDF Schema and the Web Ontology Language."
 :vann/preferredNamespaceUri "http://xmlns.com/foaf/0.1/"
 :vann/preferredNamespacePrefix "foaf"
 :foaf/homepage "http://xmlns.com/foaf/spec/"
 :dcat/downloadURL "http://xmlns.com/foaf/spec/index.rdf"
 :voc/appendix [["http://xmlns.com/foaf/spec/index.rdf"
                 :dcat:mediaType "application/rdf+xml"]]
 }
)
```

Note that these are all simple key/value declarations except the `:voc:appendix` declaration which is in the form 

```
:voc:appendix [[<subject> <predicate> <object>]....], 
```

This includes triples which elaborate on constructs mentioned in the key-value paris in the rest of the metadata, in this case describing the media types of files describing the vocabulary which are available for download at the URLs given.


Requiring the  `vocab.core` module also loads `ns` declarations dedicated to some of the most commonly used RDF/Linked Open Data prefixes: [rdf](https://www.w3.org/2001/sw/wiki/RDF), [rdfs](https://www.w3.org/TR/rdf-schema/) (both integral to basic RDF constructs), [owl](https://www.w3.org/OWL/) (for more elaborate ontologies) [vann](http://vocab.org/vann/) (for annotating vocabulary descriptons), [dc](http://purl.org/dc/elements/1.1/), [dct](http://purl.org/dc/elements/1.1/) (both components of the [Dublin Core](http://dublincore.org/)), [sh](https://www.w3.org/TR/shacl/) (SHACL -- for defining well-formedness constraints), [dcat](https://www.w3.org/TR/vocab-dcat/) (Data Catalog vocabulary), [foaf](http://xmlns.com/foaf/spec/) (the 'Friend of a Friend' vocabulary), [skos](https://www.w3.org/2009/08/skos-reference/skos.html) (for loose taxonomies), and [schema](https://schema.org/), (the [schema.org](https://schema.org/) vocabulary, mostly dedicated to commercial topics, with web-page metadata and search-engine indexes in mind).

The `vocabulary.wikidata` module has declarations for the [several namespaces](https://www.mediawiki.org/wiki/Wikibase/Indexing/RDF_Dump_Format#Full_list_of_prefixes) pertinent to the [Wikidata](https://www.wikidata.org/wiki/Wikidata:Main_Page) database.

The `vocabulary.linguistics` module declares namespaces for  [ontolex](https://www.w3.org/2016/05/ontolex/) (for encoding lexical data), [pmn](http://premon.fbk.eu/ontology/core.html) (PreMOn - dedicated to describing English verbs), [nif](http://persistence.uni-leipzig.org/nlp2rdf/ontologies/nif-core/nif-core.html) (Natural Language Interchange Format - for annotating corpora), and a set of namespaces particular to my Natural Lexicon project, which are still under development.
    
## Functionaity 

There are a number of functions defined within `vocabulary.core` which provide some utility.


We can get all the prefixes of namespaces declared within the current lexical environment:
```
(prefix-to-ns)
;; ->
{"dc" #namespace[org.naturallexicion.lod.dc],
 "owl" #namespace[org.naturallexicon.lod.owl],
 "ontolex" #namespace[org.naturallexicon.lod.ontolex],
 "foaf" #namespace[org.naturallexicon.lod.foaf],
 ...
 }
 ```
 
We can get the IRI namespace associated with an `ns`
```
(ns-to-namespace (find-ns 'org.naturallexicon.lod.foaf))
;; --> "http://xmlns.com/foaf/0.1/"
```

We can get the namespaces associated with IRIs:
```
(namespace-to-ns)
;;-> 
{"
 "http://www.w3.org/2002/07/owl#"
 #namespace[org.naturallexicon.lod.owl],
 "http://persistence.uni-leipzig.org/nlp2rdf/ontologies/nif-core#"
 #namespace[org.naturallexicon.lod.nif],
 "http://purl.org/dc/elements/1.1/"
 #namespace[org.naturallexicion.lod.dc],
 "http://www.w3.org/ns/dcat#"
 #namespace[org.naturallexicon.lod.dcat],
 ...
```

We can get the IRI associated with a keyword:

```
(iri-for :foaf/homepage)
;; ->
"http://xmlns.com/foaf/0.1/homepage"
```

The function `iri-for` works as well for aliases interned in the local lexical environment are handled appropriately:
```
(require '[vocabulary.core :as v])

(iri-for :v/appendix)
;;-> "http://rdf.naturallexicon.org/ont-app/vocabulary/appendix"
```

We can get the prefix associated with an `ns`:
```
(ns-to-prefix (find-ns 'org.naturallexicon.lod.foaf))
;;->
"foaf"
```

We can get the [qname](https://en.wikipedia.org/wiki/QName) for a keyword:
```
(qname-for :foaf:homepage)
;; -> "foaf:homepage"

;; (with the 'v' declaration above)...
(qname-for :v/appendix)
;; -> "voc:appendix"
```

We can get a keyword for a IRI...
```
(keyword-for "http://xmlns.com/foaf/0.1/homepage")
;; -> :foaf/homepage
```

We can infer the PREFIX declarations appropriate to a [SPARQL](https://en.wikipedia.org/wiki/SPARQL) query:
```
(sparql-prefixes-for
             "Select * Where{?s foaf:homepage ?homepage}")
;; -> ("PREFIX foaf: <http://xmlns.com/foaf/0.1/>")
```

Or we can just go ahead and prepend the prefixes...
```
(prepend-prefix-declarations
               "Select * Where{?s foaf:homepage ?homepage}")
;; -> 
"PREFIX foaf: <http://xmlns.com/foaf/0.1/>
Select * Where{?s foaf:homepage ?homepage}"
```

## License

Copyright © 2019 Eric D. Scott

Distributed under the Eclipse Public License either version 1.0 or (at
your option) any later version.
