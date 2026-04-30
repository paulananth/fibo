# FIBO Skill User Guide

Use the `$fibo` skill when you want Codex to inspect, explain, or help edit the
Financial Industry Business Ontology repository. The skill is designed for work
that requires reading FIBO RDF/XML, module metadata, catalogs, SPARQL hygiene
tests, or ontology structure from the checked-out repository.

## Quick Start

Invoke the skill by naming it in your request:

```text
$fibo Where is Bond defined?
```

Good FIBO skill questions usually include one of these:

- A local name, such as `Bond`, `Security`, `isIssuedBy`, or `hasCouponRate`.
- A module or domain, such as FBC, SEC, DER, LOAN, CAE, IND, or MD.
- A task type, such as explain, trace hierarchy, find subclasses, resolve an import, inspect metadata, or check hygiene tests.
- A required output style, such as "cite files", "draw an ASCII hierarchy", or "list restrictions".

## When To Use `$fibo`

Use `$fibo` for questions about:

- Classes, properties, named individuals, labels, and definitions in `.rdf` files.
- Class hierarchies, subclass searches, multiple inheritance, restrictions, and equivalent classes.
- FIBO modules and package files such as `FBC/AllFBC.rdf` or `SEC/AllSEC.rdf`.
- Catalog mappings in `catalog-v001.xml` and module catalogs.
- Metadata files such as `MetadataFIBO.rdf` and `Metadata*.rdf`.
- SPARQL hygiene tests under `etc/testing/hygiene_parameterized/`.
- Ontology edit guidance, including how to avoid duplicate local names or stale imports.

Do not use `$fibo` for general financial advice, live market data, or business
interpretation that is not grounded in the FIBO repository.

## Example Questions

### Classes and Definitions

```text
$fibo Where is Bond defined?
$fibo Explain FBC Security and cite the RDF files used.
$fibo What is DebtInstrument, and what are its named parents?
$fibo Find classes whose label or definition mentions mortgage.
$fibo Search for "legal entity identifier" and explain the matching FIBO terms.
```

### Hierarchies and Subclasses

```text
$fibo What are the direct subclasses of Bond?
$fibo Trace the ancestry of DebtInstrument.
$fibo Draw the hierarchy for TreasuryInflationProtectedSecurity.
$fibo Find recursive subclasses of Security and explain the search scope.
```

### Properties and Restrictions

```text
$fibo What is the domain and range of isIssuedBy?
$fibo Find restrictions that use hasCouponRate.
$fibo Explain any inverse properties or property chains in BE ownership and control.
$fibo Which classes constrain Security through restrictions outside FBC?
```

### Modules and Packages

```text
$fibo Explain the DER swaps model.
$fibo Explain the LOAN module and how it depends on FBC debt concepts.
$fibo List the main files to inspect for CAE corporate actions.
$fibo What does FBC/AllFBC.rdf aggregate?
```

### Catalogs, Metadata, and Tests

```text
$fibo Resolve the SEC/Debt/Bonds ontology IRI through catalog-v001.xml.
$fibo What should I do when an import points to external Commons?
$fibo Explain MetadataFIBO.rdf and the module Metadata*.rdf files.
$fibo Where are the SPARQL hygiene tests for labels and definitions?
$fibo What are the etc/vocabulary SPARQL files used for?
```

### Ontology Editing

```text
$fibo How should I add a new ontology class without duplicating a local name?
$fibo What should I check before adding a new object property?
$fibo If I add an import, what catalog or aggregate files might need updates?
$fibo How should I update a skos:definition while preserving local RDF/XML style?
```

## What To Expect In Answers

FIBO skill answers should be evidence-backed. Expect Codex to:

- Cite local file paths, such as `SEC/Debt/Bonds.rdf`.
- Identify local names and prefixes, such as `fibo-sec-dbt-bnd;Bond`.
- Use `skos:definition` as primary human-readable evidence.
- Report named parents, restrictions, domains, ranges, imports, and equivalent class expressions when relevant.
- Mention when an answer is inferred from imports, restrictions, or repeated same-IRI blocks rather than directly declared in one file.
- State the search scope for broad questions, especially recursive subclass searches.

If you need a specific format, ask for it directly:

```text
$fibo Trace Bond ancestry and return an ASCII tree with file paths.
$fibo List properties with domain Security in a table.
$fibo Explain DER swaps in three bullets and cite the entry-point RDF files.
```

## Troubleshooting

If a term is ambiguous, include the module or file path:

```text
$fibo Explain Security in FBC/FinancialInstruments/FinancialInstruments.rdf.
```

If a name appears in multiple files, ask for all same-IRI blocks:

```text
$fibo Find every RDF block about fibo-fbc-fi-fi;Security and explain which files add constraints.
```

If a search returns nothing, ask for label and definition searches, not just class names:

```text
$fibo Search labels, definitions, notes, examples, and named individuals for "mortgage".
```

If an import resolves outside this repository, expect the answer to say that the
dependency is external, commonly a Commons ontology.

If you are asking about an ontology edit, include the target file and intended
local name. That lets Codex check nearby style, duplicate local names, imports,
metadata, and relevant hygiene tests before recommending changes.
