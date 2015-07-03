# Introduction #

RO contains relations for many different domains. It's rare the entire ontology is required. This page describes how to extract the relations you need.

The focus here is on extracting obo format relation stanzas that can be pasted into an ono file, but the same techniques can be used in OWL

# Details #

You will need [owltools](http://owltools.googlecode.com)

The basic idea is illustrated with this example:

```
owltools ro.owl --merge-imports-closure --extract-properties -p 'part of' -p 'develops from' -o -f obo anatomy-relations.obo
```

This yields:

```
[Typedef]
id: part_of
name: part of
xref: BFO:0000050
is_transitive: true
is_a: overlaps ! overlaps
inverse_of: has_part ! has part
...
```

  * merging the import closure is necessary if you want a mixture of RO and BFO (may be automatic in future versions)
  * relation labels or OBO-style IDs can be used with each "-p" arg
  * The axiom closure is retrieved automatically - e..g you get property chains or superproperties of the specified set. In future you may be able to control this in a fine-grained manner.
  * OBO relation shorthands are created by default. See [OBOFormatUsersGuide](OBOFormatUsersGuide.md) and [Identifiers](Identifiers.md) for more details. Use --no-shorthand if you do not want this

## Use in OWL ##

```
owltools ro.owl --merge-imports-closure --extract-properties -p 'part of' -p 'develops from' -o  file://`pwd`/anatomy-relations.owl
```


## Other techniques ##

TODO: test

  * OntoFox
  * OWLAPI modularization

## More details ##

The documentation on this page is mostly a stub. Please ask if you need more assistance.