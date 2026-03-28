---
name: fibo
description: >
  Navigate and explain the FIBO (Financial Industry Business Ontology) codebase.
  Use this skill whenever the user asks about FIBO classes, properties, module
  structure, class hierarchies, OWL/RDF constructs, or any financial concept
  modeled in FIBO. Trigger for questions like "explain X in FIBO", "where is Y
  defined", "what are the subclasses of Z", "how does module A relate to B",
  "draw a hierarchy diagram", or any task requiring reading or interpreting
  .rdf files in the FIBO repo. Also trigger for: "what is FIBO", "explain the
  CAE model", "explain FBC Security", "list FIBO security types".
---

# FIBO Navigation Skill

## Overview

FIBO (Financial Industry Business Ontology) is a production OWL 2 / RDF-XML ontology
maintained by EDMC/OMG. It models the full financial domain — instruments, entities,
agreements, derivatives, loans, corporate actions, indices, and market data.

- **Root directory:** `C:\work\projects\fibo`
- **~594 .rdf files** across 10 domain modules
- **License:** MIT
- **Namespace catalog:** `C:\work\projects\fibo\catalog-v001.xml` — maps namespace URIs to file paths

---

## Module Map

| Module | Path | Purpose |
|--------|------|---------|
| **FND** | `FND/` | Foundations — agreements, contracts, parties, dates, quantities, units |
| **BE**  | `BE/`  | Business Entities — legal entities, LEI, organizational structures |
| **BP**  | `BP/`  | Business Process — trading, lifecycle events |
| **FBC** | `FBC/` | Financial Business & Commerce — instruments, securities, debt, equity base classes |
| **SEC** | `SEC/` | Securities — bonds, equities, money market, structured products, funds |
| **DER** | `DER/` | Derivatives — futures, options, swaps, forwards |
| **LOAN**| `LOAN/`| Loans — mortgage, commercial, consumer, real estate loans |
| **CAE** | `CAE/` | Corporate Actions & Events — dividends, mergers, rights, redemptions |
| **IND** | `IND/` | Indices & Indicators — rate indices, economic indicators |
| **MD**  | `MD/`  | Market Data — pricing, quotes, analytics |

**Dependency direction (imports flow upward):**
```
FND ← BE ← FBC ← SEC ← DER
                 ← LOAN
          ← CAE
          ← IND ← MD
```

**Key entry-point files:**
- `FBC/FinancialInstruments/FinancialInstruments.rdf` — base `Security`, `DebtInstrument`, `EquityInstrument`
- `SEC/Debt/Bonds.rdf` — `Bond` and all subclasses
- `SEC/Equities/EquityInstruments.rdf` — `Share`, `CommonShare`, `PreferredShare`
- `DER/DerivativesContracts/Swaps.rdf` — `Swap`, `SwapLeg`
- `CAE/CorporateEvents/CorporateActions.rdf` — `CorporateAction`, `MandatoryCorporateAction`

---

## Reading OWL/RDF Files

Every FIBO file is OWL 2 serialized as RDF/XML. Key constructs:

### Class Declaration & Hierarchy
```xml
<owl:Class rdf:about="&fibo-sec-dbt-bnd;Bond">
  <rdfs:subClassOf rdf:resource="&fibo-fbc-dae-dbt;CreditAgreementRepaidAtMaturity"/>
  <rdfs:subClassOf rdf:resource="&fibo-sec-dbt-dbti;TradableDebtInstrument"/>
  <skos:definition>A debt instrument...definition text...</skos:definition>
</owl:Class>
```
⚠️ **Multiple `rdfs:subClassOf` = multiple inheritance.** Always collect ALL declared
superclasses — never assume a class has exactly one parent.

### Property Declarations
```xml
<!-- Object property (links two classes) -->
<owl:ObjectProperty rdf:about="&fibo-fbc-fi-fi;isIssuedBy">
  <rdfs:domain rdf:resource="&fibo-fbc-fi-fi;Security"/>
  <rdfs:range  rdf:resource="&fibo-be-le-lp;LegalPerson"/>
</owl:ObjectProperty>

<!-- Data property (links class to literal) -->
<owl:DatatypeProperty rdf:about="&fibo-fnd-acc-cur;hasNotionalAmount">
  <rdfs:range rdf:resource="&xsd;decimal"/>
</owl:DatatypeProperty>
```

### Restrictions (anonymous superclasses)
```xml
<rdfs:subClassOf>
  <owl:Restriction>
    <owl:onProperty rdf:resource="&fibo-fbc-fi-fi;isIssuedBy"/>
    <owl:minQualifiedCardinality rdf:datatype="&xsd;nonNegativeInteger">1
    </owl:minQualifiedCardinality>
    <owl:onClass rdf:resource="&fibo-be-le-lp;LegalPerson"/>
  </owl:Restriction>
</rdfs:subClassOf>
```
Restriction types: `owl:someValuesFrom`, `owl:allValuesFrom`, `owl:hasValue`,
`owl:minCardinality`, `owl:maxCardinality`, `owl:qualifiedCardinality`.

### Other Key Constructs
- `owl:imports` — dependency chain; follow to find inherited classes/properties
- `owl:equivalentClass` — class defined by logical expression
- `owl:disjointWith` — mutual exclusion (e.g., FixedLeg disjointWith FloatingLeg)
- `skos:definition` — authoritative human-readable definition
- `skos:example`, `skos:note` — supplementary annotations
- `owl:NamedIndividual` — enumerated values (e.g., day-count conventions, credit event types)

### Namespace Prefixes (common)
| Prefix | Module | Example URI |
|--------|--------|-------------|
| `fibo-fnd-*` | FND | `fibo-fnd-agr-ctr:Contract` |
| `fibo-fbc-*` | FBC | `fibo-fbc-fi-fi:Security` |
| `fibo-sec-*` | SEC | `fibo-sec-dbt-bnd:Bond` |
| `fibo-der-*` | DER | `fibo-der-drc-swp:Swap` |
| `fibo-loan-*` | LOAN | `fibo-loan-ln-ln:Loan` |
| `fibo-cae-*` | CAE | `fibo-cae-ce-act:CorporateAction` |
| `cmns-*` | Commons | `cmns-pts:Situation` |

---

## Hierarchy Tracing Workflow

Use this step-by-step process for any hierarchy question.

### Step 1 — Find the class definition
```bash
grep -r "rdf:about.*ClassName" /c/work/projects/fibo --include="*.rdf" -l
# Then read the file and find the owl:Class block
```

### Step 2 — Collect ALL superclasses (multiple inheritance!)
Read the class block completely. Count every `rdfs:subClassOf` element:
- Named superclasses: `<rdfs:subClassOf rdf:resource="..."/>`
- Anonymous restrictions: `<rdfs:subClassOf><owl:Restriction>...`

**Example — Bond has TWO named superclasses:**
```
fibo-sec-dbt-bnd:Bond
  rdfs:subClassOf  fibo-fbc-dae-dbt:CreditAgreementRepaidAtMaturity  ← path 1
  rdfs:subClassOf  fibo-sec-dbt-dbti:TradableDebtInstrument           ← path 2
```
Trace BOTH paths up to owl:Thing separately, then render the full diamond.

### Step 3 — Trace each path upward
For each named superclass, repeat Step 2 recursively until you reach `owl:Thing`.

**Bond full ancestry:**
```
owl:Thing
  └── cmns-pts:Situation                      [Commons/PartiesAndSituations]
        └── fibo-fnd-agr-agr:Agreement        [FND/Agreements/Agreements.rdf]
              └── fibo-fnd-agr-ctr:Contract   [FND/Agreements/Contracts.rdf]
                    └── fibo-fnd-agr-ctr:WrittenContract
                          ├── fibo-fbc-dae-dbt:CreditAgreement        [FBC/DebtAndEquities/Debt.rdf]
                          │     └── fibo-fbc-dae-dbt:CreditAgreementRepaidAtMaturity
                          │                                    ↘
                          └── fibo-fbc-fi-fi:FinancialInstrument       [FBC/FinancialInstruments/...]
                                └── fibo-fbc-fi-fi:DebtInstrument
                                      └── fibo-fbc-fi-fi:Security
                                            └── fibo-sec-dbt-dbti:TradableDebtInstrument
                                                                   ↘
                                                     fibo-sec-dbt-bnd:Bond  [SEC/Debt/Bonds.rdf]
```

### Step 4 — Find ALL subclasses exhaustively
```bash
# Find every class that declares X as superclass — search the ENTIRE repo
grep -r "subClassOf.*Bond\b" /c/work/projects/fibo --include="*.rdf" -l
grep -r "subClassOf.*Security\b" /c/work/projects/fibo --include="*.rdf" -l
```
Then read each file found and collect the subclass names.
Do NOT rely on a single file — subclasses can be declared across multiple modules.

### Step 5 — Render as ASCII tree
Show the full diamond for multiple-inheritance classes. Use `├──`, `└──`, `│` for branches.
Add `[also subClassOf X]` annotations where a class has additional parents.

---

## Finding Definitions

### Locate a class by local name
```bash
grep -r 'rdf:about="[^"]*ClassName"' /c/work/projects/fibo --include="*.rdf" -l
```

### Locate a property
```bash
# Common FBC Security properties to check:
#   isIssuedBy, isDenominatedIn, isLegallyRecordedIn, hasNominalValue,
#   isClassifiedBy, isTradedOn, isListedOn
grep -r 'ObjectProperty.*isLegallyRecordedIn\|isLegallyRecordedIn.*ObjectProperty' \
  /c/work/projects/fibo --include="*.rdf"
```

### Find all properties with a given domain
```bash
grep -r -A5 'domain.*Security\b' /c/work/projects/fibo/FBC --include="*.rdf"
grep -r -A5 'domain.*Security\b' /c/work/projects/fibo/SEC --include="*.rdf"
```

### Resolve a namespace prefix to a file
1. Open `catalog-v001.xml`
2. Find `<uri name="http://www.omg.org/spec/EDMC-FIBO/..." uri="relative/path.rdf"/>`
3. The `uri` attribute is relative to the catalog file location

---

## Common Tasks

### "Explain class X"
1. Grep for class URI → find file
2. Read the full `owl:Class` block: definition, all superclasses, all restrictions
3. Read `owl:imports` to understand dependencies
4. Summarize: what it IS (definition), what it IS-A (hierarchy), what it REQUIRES (restrictions)

### "What are the subclasses of X?"
1. Exhaustive grep: `grep -r "subClassOf.*X\b" /c/work/projects/fibo --include="*.rdf" -l`
2. Read each matching file; collect local names from `rdf:about`
3. Recurse for any subclasses that themselves have subclasses
4. Render as indented ASCII tree

### "How does module A relate to module B?"
1. Read the top-level `owl:Ontology` block of A's key files → find `owl:imports`
2. Check whether any imports resolve to module B's namespace
3. Identify which specific classes/properties A uses from B

### "Draw a hierarchy diagram"
1. Trace upward (Steps 2-3) — full ancestry chain
2. Trace downward (Step 4) — all subclasses recursively
3. Render as ASCII diamond for multiple inheritance

### "Explain the LOAN module"
1. List files: `ls /c/work/projects/fibo/LOAN/**/*.rdf`
2. **Read each file** — cite actual class names and `skos:definition` text; do not summarize from memory
3. Trace imports back to FBC to show the dependency chain

---

## Reference Files

| File | When to read |
|------|--------------|
| `references/modules.md` | Deep dive on a specific module: key files, top-level classes, import chains, common use cases |
| `references/owl-patterns.md` | Advanced OWL constructs: unionOf, intersectionOf, equivalentClass, complex restrictions, named individuals |

---

## Quick Grep Reference

```bash
# Find class definition file
grep -rl 'rdf:about="[^"]*Bond"' /c/work/projects/fibo --include="*.rdf"

# Find all subclasses of Bond
grep -rl 'subClassOf.*Bond\b' /c/work/projects/fibo --include="*.rdf"

# Find ObjectProperty by name
grep -rl 'ObjectProperty.*isLegallyRecordedIn' /c/work/projects/fibo --include="*.rdf"

# Find properties with Security as domain
grep -r -l 'domain.*Security' /c/work/projects/fibo/FBC /c/work/projects/fibo/SEC --include="*.rdf"

# Show imports of a file
grep 'owl:imports' /c/work/projects/fibo/SEC/Debt/Bonds.rdf

# Find skos:definition of a class
grep -A3 'skos:definition' /c/work/projects/fibo/SEC/Debt/Bonds.rdf | head -40
```
