# Introduction #

The current version of RO includes both general and specific relations from multiple domains. This is not ideal for many users, who typically require a small subset of these.

# Dividing into domain-specific modules #

One approach would be to divide the RO into a collection of domain-specific modules - for each ro-neuro, ro-developmental-biology, etc. These modules could import one another where appropriate, and a parent ro ontology could import everything.

We decided against this at this stage, since it will difficult to tell a-priori which were sensible divisions. There are many ways to partition into mutually exclusive sets - by upper level (e.g. process-process relations vs process-continuant) or by domain. Many domains overlap.

We may still pre-modularize the relations at some stage in the future, but for now they are organized as a single ontology (or rather as two: with the core relations in BFO and derived and domain specific relations in RO)

# Extracting Modules #

As the RO is not modularized in advanced, it would be useful for users to have ways to extract the modules they need.

## Automatic Module Extraction ##

There are a number of ways to automatically extract a module from an ontology - the OWLAPI implements some of these.

You can use the [OWLTools](http://code.google.com/p/owltools) command line facility to access the OWLAPI and it's modularization capabilities.

Using 'develops from' as a seed using the TOP strategy:

```
 owltools  ro.owl --extract-module -m TOP 'develops from' -o file://`pwd`/ro-subset-part-of.owl
```

Using 'part of' and 'has part' and all their descendant relations:

```
 owltools  ro.owl --extract-module -m STAR 'has part' 'part of' -o file://`pwd`/ro-subset-part-of.owl
```



Getting the right module requires an understanding of the modularization algorithm which is non-trivial - particularly where this interacts with [ShortcutRelations](ShortcutRelations.md) - in future we will provide a more intuitive high-level modularization capability.

## Manual Module Extraction ##

Another method is to manually take the properties and/or related axioms one requires and copy them into the working ontology. This technique is not ideal, but the operation is relatively infrequent since most ontologies do not use large numbers of relations. This is the method that has been used in practice for GO, the drosophila anatomy, Uberon, etc.

See [OBOFormatUsersGuide](OBOFormatUsersGuide.md) for details