# Intro #

Note:

This is a draft guide. It pertains to a version of the ontology that
has yet to be released. The current latest release is 2015-02-11. The
next release is expected in March. For now, consult the editors
version.

The Relation Ontology (RO) includes over 400 relations covering a
variety of domains in the biological, biomedical and environmental
sciences, making use of a wide variety of axiom types. This guide is
intended to help navigate the structure of the RO and provide
assistance with interpreting it.

Note that we expect very few users will need to peruse the whole
ontology in its entirety. Most people will encounter RO through some
subset that is used within another ontology. For example, a user
browsing [Uberon](http://uberon.org) may see a
[develops from](http://purl.obolibrary.org/obo/RO_0002202) relationship
linking two classes. Clicking on this may lead to a resource such as
OntoBee, where more details can be found. In fact many users may not
need to be aware of RO at all: the ontology may be used behind the
scenes to calculate results.

However, we expect some users may have a need to browse the whole
ontology. These include ontology developers or data modelers looking
to reuse relations. Here some additional guidance may prove useful.

# Details #

## How to browse RO ##

In order to fully comprehend RO it is necessary to inspect it using a
tool that supports the entirety of the OWL2 language. In practice this
means Protege (4 or 5), a desktop application.

The best web-based way to browse RO is through OntoBee. However, RO
sometimes uses constructs not supported by OntoBee, and sometimes
OntoBee fails to display the full hierarchy. However, OntoBee provides
one advantage over Protege: each relation can be browsed in the
context of its usage.

We recommend use of Protege, sometimes complemented by OntoBee. For
Protege, like all OBO ontologies, the ontology should be accessed via
its purl, i.e. http://purl.obolibrary.org/obo/ro.owl

In order to best understand RO, it helps tremendously to be familiar
with OWL2. This advice does not necessarily hold for looking at RO
through an application-specific prism: here the best way to view RO
will be dictated by the application and the domain. However, when
looking at the entire ontology understanding OWL2 will definitely
help. At a minimum, it helps to know:

  * How to read [ManchesterSyntax](ManchesterSyntax.md)
  * The difference between an [ObjectProperty](ObjectProperty.md) and an [AnnotationProperty](AnnotationProperty.md)
  * The meaning of Object Property Characteristics, such as 'Transitive'
  * The meaning of Object Property Axiom types, such as SubPropertyOf
  * What a [PropertyChain](PropertyChain.md) is
  * What it means to import an ontology

It also helps to know some of the practices of the OBO Community. For example:

  * [naming conventions](http://www.biomedcentral.com/1471-2105/10/125)
  * [ID policy and URI structure](http://www.obofoundry.org/id-policy.shtml) (also: [Identifiers](Identifiers.md)).
  * Use of the IAO metadata vocabulary

Finally, for browsing in Protege, we recommend a tutorial such as the
Pizza tutorial. A few additional tips help:

  * Hovering over an axiom reveals its ontology source (RO imports parts of other ontologies)
  * Using a reasoner will show you entailed axioms
  * A colored "@" means the axiom has annotations (e.g. provenance of the axiom). Click on the "@" to see these

If at this stage you are undaunted, read on!

## Logical Design Patterns ##

RO makes use of a number of logical ontology design patterns (ODPs)
(TODO: link to ODP docs). These crop up repeatedly, so it can help to
be aware of these. Note that each usage of an ODP should be documented
directly in the ontology as well as here.

### Direct and Indirect forms of relations ###

The following pattern shows up a lot:

```
 * "<R>"   (a Transitive OP)
    * "directly <R>" (non-transitive)
```

And occasionally extended to this:

```
 * "<R>"   (a Transitive OP)
    * "directly <R>" (non-Transitive)
    * "indirectly <R>" (Transitive)
```

The first thing to point out is that the structure is sometimes
confusing to users coming from an Object-Oriented Programming
perspective. The intuition is that characteristics of the parent
should be inherited as characteristics of the child. In fact this is
not the case: the direct form of a transitive relation is both a
subproperty of the transitive form, and itself intransitive.

This can be seen with a specific example, e.g.

  * http://purl.obolibrary.org/obo/RO_0002202 develops from
    * http://purl.obolibrary.org/obo/RO_0002207 directly develops from

Here the 'direct' prefix means that the two structures or classes
should be in direct succession (no intermediates).

Consider a structure:

```
 A directly-develops-from B directly-develops-from C
```

(e.g. A is a limb, B is a limb bud, and C is a limb primordium).

From the above, we can infer:

```
 1. A develops-from B develops-from C
 2. A develops-from C
```

(1) follows from SubPropertyOf, and (2) follows from (1) and the transitivity axiom.

In RO, every usage of this ODP should have an 'is direct form of'
(http://purl.obolibrary.org/obo/RO_0002575) annotation; e.g. 'directly
develops from' is-direct-form-of 'indirectly develops from'.

Note that we do not always create an indirect relation as sibling to
the direct form. This is in part because inference of the indirect
form can be problemmatic.

Note that RO does not include a 'directly part of' relation. For a discussion see:

https://keet.wordpress.com/2010/11/14/nontransitive-vs-intransitive-direct-part-whole-relations-in-owl/

### Defining Property Chains involving Reflexivity ###

The pattern here is

```
 * "<R> <P>" <- R o P
    * <R>
```

WHen this ODP is used, the property chain axiom should be tagged with
an 'is a defining property chain axiom where second argument is
reflexive' axiom.

In this case, the parent relation is a grouping relation for query purposes. Consider:

  * http://purl.obolibrary.org/obo/RO_0002177 'attached to part of'
    * http://purl.obolibrary.org/obo/RO_0002371 'attached to'

Consider a ligament or tendon attached to some projection P that is
part of a bone B, that is part of the limb skeleton. We may be
interested in all attachmed structures for any part of the body, and
this named property chain provides a convenient way of querying for it.

In all cases of this ODP, the parent property can be safely 'unfolded'
away with no loss of information. i.e. expanded to an actual chain
with two property expressions or assertions.

The placement of the base form as a child of the parent is justified
via the local reflexivity of the 2nd argument.

### Local Reflexivity ###

In many formal treatments of relations, the Reflexivity characteristic
is frequently used. For example, in mereology, 'part of' is frequently
characterized as transitive. This can seem confusing when compared
with everyday usage (no one typically considers an object to be part
of itself). However, it is convenient from the point of view of
mathematical formulation. A 'proper R' sub-relation is often
introduced, e.g. 'proper part of'.

(We typically do not include a 'proper' form of the relation. This is
because it inflates the ontology, and complicates reasoning. Also, it
does not actually add information or provide real utility or intuition).

We rarely if ever declare a relation to be reflexive. Reflexivity is
incompatible with domain and range assertions. Consider:

```
  ObjectProperty: P
   Characteristics: Reflexive
   Domain: C
  Class: C
   DisjointWith: D
```

reflexivity of P literally means that every individual in the domain
stands in a P relation to itself. This would include any instances of
D. However, it an instance of D cannot be the subject of a P relation,
hence D is unsatisfiable.

This is rarely a concern in formal treatments, where a restricted
domain is assumed; however, RO covers all domains so full reflexivity
is almost always inappropriate.

Instead, here we would make use of a 'local reflexivity' axiom. E.g.

```
  ObjectProperty: P
   Domain: C
  Class: C
   DisjointWith: D
   SubClassOf: P Self
```

In practice we may not have asserted all cases of local reflexivity (TODO: tracker link).

### Interaction Relations ###

It can be useful to have relations that are shortcuts for more
detailed modeling using processes. For example, to model two organisms
interacting, we may use an 'interaction' process, with the organisms
linked via participation relations. We can then subclass the
interaction process class to describe different kinds of interaction.

It can also be convenient to have relations that 'shortcut' across the
process, e.g. x interacts-with y, x phosphorylates y, x hunts y.

The way we do this is via the introduction of 'helper
properties'. Unfortunately these clog up the ontology from a browsing
perspective, but assist with reasoning.

The design pattern is documented here:
http://ontologydesignpatterns.org/wiki/Submissions:N-Ary_Relation_Pattern_%28OWL_2%29

For every interaction process Proc we create as isProc ObjectProperty that is locally reflexive for any instance of Proc, ie

```
  Class: Proc
   SubClassOf: isProc Self

 ObjectProperty: isProc
  SubPropertyOf: 'helper process'
```

(the subPropertyOf axiom does not affect the logic, it exists purely for aesthetic purposes)

We can now define property chains using this 'fake' property, e.g.

```
 ObjectProperty: RelProc
  SubPropertyChain: 'partner in' o isProc o inv('partner in')
```

See also: [InteractionRelations](InteractionRelations.md)

### Shortcut Relations ###

See: https://code.google.com/p/obo-relations/wiki/ShortcutRelations

## Annotation Assertions in RO ##

In RO we aim to have comprehensive metadata for each ObjectProperty
declared. In practice we often fall short, but this is something we
are striving to improve.

Metadata is provided via annotation assertions using annotation
properties (APs), and other object types, from a variety of ontologies, including:

  * IAO/metadata
  * dublin core
  * RDFS
  * OboInOwl

We have also defined APs within RO. Some of these may migrate to IAO.

### Definitions ###

Like all OBO ontologies, definitions are provided using an
http://purl.obolibrary.org/obo/IAO_0000115 'definition' AP.

We strive to give each OP a definition. In practice we may omit
definitions for OPs that are trivially the inverse of another defined
OP (TODO link to tracker).

### Definition Source and Attribution ###

### Notes ###

  * curator notes: aimed at curator users of ontologies
  * editor notes: primarily internal notes for other RO editors, but exposed for the sake of transparency

### Synonyms and alternate terms ###

'OBO foundry unique label' is used where the primary label could potentially be ambiguous when compared to other ontologies.

### Provenance and Attribution ###

We currently use IAO 'term editor' APs to record a literal with the
full name of the person who added the term to the ontology.

The IAO def of this is:
"Name of editor entering the term in the file. The term editor is a point of contact for information regarding the term. The term editor may be, but is not always, the author of the definition, which may have been worked upon by several people"

We may decide to use ORCIDs in future.

TOOD: link to tracker

### Curation Status ###


## Organization of Relations into Domain Modules ##

One of the first things a user may notice on browsing RO is it
contains a number of highly specific relations particular to
sub-domains of science. This can be disconcerting, especially as (for
logical reasons), the hierarchies can be mingled. For example,
sub-relations of generic relations 'part of' or 'precedes' include
specific neuroanatomical relations mixed with environmental relations.

In general, typical users would not be exposed to this variety: a
neuroanatomist should consume a RO module intended specifically for
neuroanatomy. This section deals with how RO is organized into
domains, and how these modules are extracted.

### Imports ###

RO imports a handful of portions of other ontologies, primarily in
order to specify domain and range constraints.

In practice, some external ontologies are not properly imported:
portions of the ontologies are copied directly into RO. This is
something we will resolve in future (TODO tracker link).

RO uses BFO classes as an upper level ontology; this is imported from
the RO-core module (next section):

### RO/core ###

One of the most important modules in RO is the module called
RO-core. It has its own URI:

  * http://purl.obolibrary.org/obo/ro/core.owl

And is documented separately [ROCore](ROCore.md).

It can be consumed independently of RO.

RO/core aims to faithfully follow the BFO2 reference document. There
is ongoing discussion on representation of the temporal aspects of BFO.

For more information see:

  * [ROCore](ROCore.md)
  * [ROAndBFO](ROAndBFO.md)
  * [ROAndTime](ROAndTime.md)

### Domain Specific Modules ###

We currently support two overlapping mechanisms to partition RO into
modules according to application or domain.

  * Grouping relations
  * Subsets

Grouping relations are OPs that form the root of a property hierarchy
including the desired properties. For example, 'developmentally
related to' (http://purl.obolibrary.org/obo/RO_0002324) groups all OPs
used to specify developmental relationships between two structures.

All such grouping relations should be tagged with a 'curator note'
property describing the intent of the grouping, and warning not to use
the OP in logical assertions.

There are some objections to grouping OPs. Philosophically there are
objections that the OP does not represent a true relationship in the
world. However, in practice there are no negative consequences of this
philosophical infelicity - the OP can simply be ignored. A more
serious objection may be that the presence of these relations can slow
down reasoning. If this turns out to be a problem we may find another
strategy.

Despite these objections, grouping relations are undeniably convenient
for browsing in an ontology browser like Protege.

With the grouping strategy, all subproperties are included in the
group. Sometimes it is desirable to cherry pick certain OPs
independently of the subProperty hierarchy. Here we explicitly assert
membership in a group.

Currently this is done using the 'in subset' AP from the OiO
vocabulary. This may be changed to IAO in future, but for now use of
this vocabulary has advantages for mapping to obo format and making
use of OORT to build releases - subset files are generated as part of
the release process.

Currently we only have a single subset, ro-eco, but we will add more
later, e.g. for [RONeuro](RONeuro.md). One possibility is to have an editor in
charge of the content of each module (or at least representing a
community of stakeholders).

See also:

  * [ROModules](ROModules.md)