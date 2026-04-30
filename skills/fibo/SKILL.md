---
name: fibo
description: "Use when working in the FIBO ontology repository: answering questions about FIBO classes, properties, modules, RDF/XML OWL constructs, class hierarchies, imports, restrictions, SPARQL hygiene tests, catalog mappings, or any financial concept modeled in FIBO .rdf files."
---

# FIBO

Use this skill to navigate, explain, or edit the Financial Industry Business Ontology
(FIBO) repository. Treat the checked-out repository as the source of truth; examples
in this skill are orientation only.

## First Steps

1. Work from the repository root: `ROOT=$(git rev-parse --show-toplevel 2>/dev/null || pwd)`.
2. Use `rg`, `rg --files`, and targeted file reads. Avoid hard-coded absolute paths.
3. Verify every class, property, subclass, import, and definition in the `.rdf` files before answering.
4. Read `catalog-v001.xml` when resolving ontology IRIs to local files.

## Repository Map

Core domain directories in this checkout:

| Directory | Domain |
| --- | --- |
| `FND/` | Foundations: contracts, parties, dates, accounting, relations, products and services |
| `BE/` | Business entities: legal entities, government entities, trusts, ownership and control |
| `FBC/` | Financial business and commerce: instruments, products, services, markets, debt base classes |
| `SEC/` | Securities: bonds, equities, funds, listings, restrictions, baskets, issuance |
| `DER/` | Derivatives: swaps, options, futures, forwards, credit derivatives, security-based derivatives |
| `LOAN/` | Loans: general, specific, and real-estate loan concepts |
| `CAE/` | Corporate actions and events |
| `IND/` | Indicators, interest rates, foreign exchange, market indices, economic indicators |
| `MD/` | Market data temporal models for securities, derivatives, debt, and funds |
| `BP/` | Business process and securities issuance |
| `ACTUS/` | ACTUS contract term mappings and examples |

Read `references/modules.md` for module-specific entry points. Read
`references/owl-patterns.md` for RDF/XML and OWL interpretation rules.

## Search Patterns

Find a class, property, or individual by local name:

```bash
ROOT=$(git rev-parse --show-toplevel 2>/dev/null || pwd)
rg -n 'rdf:about="[^"]*(;|#)Bond"' -g '*.rdf' "$ROOT"
rg -n '<owl:(ObjectProperty|DatatypeProperty) rdf:about="[^"]*(;|#)isIssuedBy"' -g '*.rdf' "$ROOT"
rg -n '<owl:NamedIndividual rdf:about="[^"]*(;|#)ActualActualISDA"' -g '*.rdf' "$ROOT"
```

Find direct subclasses by resource reference:

```bash
rg -n 'rdfs:subClassOf rdf:resource="[^"]*(;|#)Bond"' -g '*.rdf' "$ROOT"
```

Find references that may appear inside restrictions, equivalent classes, or examples:

```bash
rg -n '(^|[;#/])Bond("|<|$)' -g '*.rdf' "$ROOT"
```

Resolve imports:

```bash
rg -n 'owl:imports' SEC/Debt/Bonds.rdf
rg -n 'SEC/Debt/Bonds' catalog-v001.xml
```

Search by label, definition text, or annotations when the user gives a business phrase:

```bash
rg -n 'legal entity identifier|Legal Entity Identifier|mortgage' -g '*.rdf' "$ROOT"
rg -n '<(rdfs:label|skos:definition)[^>]*>.*mortgage' -g '*.rdf' "$ROOT"
```

Find property declarations, domains/ranges, and class restrictions that use a property:

```bash
rg -n '<owl:(ObjectProperty|DatatypeProperty) rdf:about="[^"]*(;|#)hasCouponRate"' -g '*.rdf' "$ROOT"
rg -n 'rdfs:(domain|range) rdf:resource="[^"]*(;|#)Security"' -g '*.rdf' "$ROOT"
rg -n 'owl:onProperty rdf:resource="[^"]*(;|#)hasCouponRate"' -g '*.rdf' "$ROOT"
```

## Term Resolution Rules

- Treat same-IRI blocks as cumulative. FIBO may place additional axioms about a
  class or property in later modules, e.g. `fibo-fbc-fi-fi;Security` appears in
  `FBC/FinancialInstruments/FinancialInstruments.rdf` and can be constrained in
  SEC files. Read all matching blocks when the user asks for a complete account.
- Distinguish a local definition from an extension block. If a file's `rdf:about`
  uses another module's entity prefix, the file is adding axioms about that term,
  not introducing a new local term.
- Local names are not globally unique. Disambiguate `Bond`, `Security`, `Loan`,
  or similar names by ontology IRI/prefix, file path, definition, and imports.
- For phrase searches, inspect `rdfs:label`, `skos:definition`, `skos:note`,
  examples, and named individuals; do not rely only on class names.

## Reading RDF/XML

FIBO files are OWL 2 serialized as RDF/XML. When reading a class or property:

- Read the complete XML element, not just the matching line.
- Collect every `rdfs:subClassOf`; FIBO uses multiple inheritance.
- Separate named superclasses from anonymous `owl:Restriction` superclasses.
- Include `owl:equivalentClass`, `owl:disjointWith`, `owl:unionOf`, and `owl:intersectionOf` when present.
- Prefer `skos:definition` for authoritative human-readable meaning.
- Use `skos:note`, `skos:example`, and `rdfs:label` as supporting evidence.
- Follow imports when a referenced class or property is not defined in the current file.

Example class shape:

```xml
<owl:Class rdf:about="&fibo-sec-dbt-bnd;Bond">
  <rdfs:subClassOf rdf:resource="&fibo-fbc-dae-dbt;CreditAgreementRepaidAtMaturity"/>
  <rdfs:subClassOf rdf:resource="&fibo-sec-dbt-dbti;TradableDebtInstrument"/>
  <skos:definition>...</skos:definition>
</owl:Class>
```

Read this as: `Bond` has two named parents. Trace both ancestry paths.

## Common Workflows

### Explain a class or property

1. Locate the defining `.rdf` file with `rg`.
2. Read all class/property blocks for the same IRI when completeness matters.
3. Resolve entity prefixes from the file's `<!ENTITY ...>` declarations if needed.
4. Summarize definition, superclasses/domain/range, restrictions, equivalent class axioms, and important imports.
5. Cite the local file paths used.

### Trace a hierarchy

1. Find the class definition.
2. Collect all named `rdfs:subClassOf` parents.
3. Recursively trace each parent until the chain reaches external Commons concepts or `owl:Thing`.
4. Find subclasses with an exhaustive repo-wide `rg` search.
5. Render multiple inheritance explicitly; do not collapse it to one parent.

### Explain a module

1. Start with the module `README.md` and module metadata RDF files.
2. Read the main ontology files listed in `references/modules.md`.
3. Inspect `owl:imports` to explain dependencies.
4. Check aggregate files such as `FBC/AllFBC.rdf`, `SEC/AllSEC.rdf`, and
   regional/example aggregate files when the question is about package contents.
5. Use actual class and property names from the files. Do not summarize from memory.

### Inspect catalogs, metadata, and tests

1. Use root `catalog-v001.xml` first; module catalogs such as `BE/catalog-v001.xml`
   and `ACTUS/catalog-v001.xml` can provide narrower import-resolution context.
2. Use `MetadataFIBO.rdf` and `Metadata*.rdf` files for module identity,
   maturity, license, copyright, and publication metadata.
3. Use `etc/testing/hygiene_parameterized/*.sparql` for hygiene expectations
   such as labels, definitions, licenses, deprecated resources, circular imports,
   subclass cycles, property domains/ranges, and inverse properties.
4. Use `etc/vocabulary/*.sparql` plus `etc/vocabulary/scaffolding.ttl` for the
   ontology-publisher vocabulary product.

### Edit ontology files

1. Preserve RDF/XML style, indentation, namespace entity usage, and annotation patterns in the target file.
2. Update related metadata RDF files, aggregate `All` RDF files, or `catalog-v001.xml` only when the change requires it.
3. Check same-IRI blocks and duplicate local names before introducing new classes,
   properties, restrictions, or named individuals.
4. Run relevant validation or hygiene tests if available under `etc/testing/`;
   otherwise at least run targeted `rg` checks for duplicate definitions, broken
   local references, missing labels, and missing definitions.

## Output Guidance

- Be explicit about evidence: include file paths and local names.
- State uncertainty when a concept is inferred from imports or restrictions rather than directly defined.
- For diagrams, use compact ASCII trees unless the user asks for Mermaid.
- For large subclass sets, explain search scope and whether recursion was exhaustive.
