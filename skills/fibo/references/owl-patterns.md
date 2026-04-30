# OWL/RDF Patterns in FIBO

Use this reference when the task requires interpreting FIBO RDF/XML beyond a
simple label or definition lookup.

## XML Entities

FIBO RDF/XML uses entity declarations for namespace shorthand:

```xml
<!ENTITY fibo-sec-dbt-bnd "https://spec.edmcouncil.org/fibo/ontology/SEC/Debt/Bonds/">
```

`&fibo-sec-dbt-bnd;Bond` means the local name `Bond` in that ontology namespace.
When in doubt, read the file's `<!DOCTYPE rdf:RDF [...]>` block.

## Repeated Subject Blocks

RDF/XML files can contain multiple blocks about the same IRI across the repository.
Those blocks are cumulative OWL axioms, not competing definitions.

Example pattern:

```xml
<owl:Class rdf:about="&fibo-fbc-fi-fi;Security">
  ...
</owl:Class>
```

The base class may be declared in an FBC file and later constrained in SEC files.
For complete answers, search the full repository for the same expanded IRI or
entity/local-name pair and merge the axioms by source file.

If the entity prefix belongs to a different module than the file being read, the
file is usually extending or constraining an imported term.

## Classes and Multiple Inheritance

OWL classes may have multiple named superclasses:

```xml
<owl:Class rdf:about="&fibo-sec-dbt-bnd;Bond">
  <rdfs:subClassOf rdf:resource="&fibo-fbc-dae-dbt;CreditAgreementRepaidAtMaturity"/>
  <rdfs:subClassOf rdf:resource="&fibo-sec-dbt-dbti;TradableDebtInstrument"/>
</owl:Class>
```

Rules:

- Collect all `rdfs:subClassOf` entries.
- Trace every named parent separately.
- Treat anonymous restrictions as constraints, not as named hierarchy nodes.
- Render diamonds explicitly when paths merge.

## Restrictions

Anonymous `owl:Restriction` elements appear under `rdfs:subClassOf` or inside
`owl:equivalentClass` expressions.

Common forms:

| Element | Meaning |
| --- | --- |
| `owl:someValuesFrom` | At least one value from the target class |
| `owl:allValuesFrom` | Only values from the target class |
| `owl:hasValue` | A fixed individual value |
| `owl:minCardinality` / `owl:maxCardinality` | Unqualified count constraint |
| `owl:minQualifiedCardinality` / `owl:maxQualifiedCardinality` | Count constraint for a target class |
| `owl:qualifiedCardinality` | Exact count for a target class |

Read the `owl:onProperty` first, then the value/class/cardinality constraint.

## Equivalent Classes

`owl:equivalentClass` gives necessary and sufficient conditions. These often use
`owl:intersectionOf`, `owl:unionOf`, or restrictions:

```xml
<owl:equivalentClass>
  <owl:Class>
    <owl:intersectionOf rdf:parseType="Collection">
      <rdf:Description rdf:about="&fibo-sec-dbt-bnd;Bond"/>
      <owl:Restriction>...</owl:Restriction>
    </owl:intersectionOf>
  </owl:Class>
</owl:equivalentClass>
```

Read this as a defined class expression, not just another superclass.

## Union and Intersection

- `owl:intersectionOf` means all listed class expressions apply.
- `owl:unionOf` means any listed class expression may apply.

Both are represented as RDF collections with `rdf:parseType="Collection"`.
Read every member of the collection.

## Properties

Object properties link resources; datatype properties link resources to literals.

```xml
<owl:ObjectProperty rdf:about="&fibo-fbc-fi-fi;isIssuedBy">
  <rdfs:domain rdf:resource="&fibo-fbc-fi-fi;Security"/>
  <rdfs:range rdf:resource="&fibo-be-le-lp;LegalPerson"/>
</owl:ObjectProperty>
```

Rules:

- Report `rdfs:domain`, `rdfs:range`, inverse properties, subproperties, and property chains.
- Domain and range can be inherited through `rdfs:subPropertyOf`.
- Some usage constraints appear as restrictions on classes rather than on the property declaration.

## Named Individuals

FIBO uses `owl:NamedIndividual` for controlled vocabularies and reference data.
Find individuals by their `rdf:type`:

```bash
rg -n 'rdf:type rdf:resource="[^"]*(;|#)DayCountConvention"' -g '*.rdf'
```

Read labels, definitions, and source annotations before explaining the individual.

## Imports and Catalog Resolution

Imports use ontology IRIs:

```xml
<owl:imports rdf:resource="https://spec.edmcouncil.org/fibo/ontology/SEC/Debt/Bonds/"/>
```

Resolve local files through `catalog-v001.xml`, where `uri=` is relative to the
catalog file. Not every imported Commons ontology is present in this repository;
state when a dependency is external.

## Annotations

Common annotation properties:

| Annotation | Meaning |
| --- | --- |
| `skos:definition` | Authoritative definition |
| `skos:example` | Example usage |
| `skos:note` | Editorial or usage note |
| `skos:scopeNote` | Scope note |
| `rdfs:label` | Human-readable label |
| `cmns-av:*` | Commons/FIBO annotation metadata |

Use definitions as primary evidence, then notes/examples as supporting context.

## Property Chains

Some properties include `owl:propertyChainAxiom` collections:

```xml
<owl:propertyChainAxiom rdf:parseType="Collection">
  <rdf:Description rdf:about="...hasParent"/>
  <rdf:Description rdf:about="...hasParent"/>
</owl:propertyChainAxiom>
```

Read chain order exactly. Explain the inferred property only after identifying
the source property and all chained properties.
