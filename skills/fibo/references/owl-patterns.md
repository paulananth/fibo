# OWL/RDF Patterns in FIBO

Advanced constructs you will encounter when reading FIBO `.rdf` files.

---

## 1. Multiple Inheritance

OWL allows a class to have multiple named superclasses. FIBO uses this extensively.

```xml
<owl:Class rdf:about="&fibo-sec-dbt-bnd;Bond">
  <rdfs:subClassOf rdf:resource="&fibo-fbc-dae-dbt;CreditAgreementRepaidAtMaturity"/>
  <rdfs:subClassOf rdf:resource="&fibo-sec-dbt-dbti;TradableDebtInstrument"/>
</owl:Class>
```

**Reading rule:** Collect ALL `rdfs:subClassOf` elements. Never stop at the first one.
Both chains must be traced upward independently and merged into a diamond diagram.

**Common multiple-inheritance classes in FIBO:**
| Class | Parents |
|-------|---------|
| `Bond` | CreditAgreementRepaidAtMaturity, TradableDebtInstrument |
| `DebtInstrument` | FinancialInstrument, CreditAgreement |
| `TradableDebtInstrument` | DebtInstrument, Security |
| `TreasuryBond` | SovereignBond, USTreasurySecurity |
| `TreasuryInflationProtectedSecurity` | InflationLinkedBond, USTreasurySecurity, VariablePrincipalBond |
| `CallableConvertibleBond` | CallableBond, ConvertibleBond |

---

## 2. Anonymous Restriction Superclasses

A class's restrictions are declared as anonymous `owl:Restriction` superclasses.

```xml
<owl:Class rdf:about="&fibo-sec-dbt-bnd;Bond">
  <rdfs:subClassOf rdf:resource="&fibo-fbc-dae-dbt;CreditAgreementRepaidAtMaturity"/>

  <!-- Anonymous restriction: Bond must be issued by at least 1 LegalPerson -->
  <rdfs:subClassOf>
    <owl:Restriction>
      <owl:onProperty rdf:resource="&fibo-fbc-fi-fi;isIssuedBy"/>
      <owl:minQualifiedCardinality rdf:datatype="&xsd;nonNegativeInteger">1
      </owl:minQualifiedCardinality>
      <owl:onClass rdf:resource="&fibo-be-le-lp;LegalPerson"/>
    </owl:Restriction>
  </rdfs:subClassOf>
</owl:Class>
```

**Cardinality types:**
| OWL element | Meaning |
|-------------|---------|
| `owl:someValuesFrom` | ∃ — at least one value from range class |
| `owl:allValuesFrom` | ∀ — all values must be from range class |
| `owl:hasValue` | fixed individual value |
| `owl:minCardinality` | minimum count (unqualified) |
| `owl:maxCardinality` | maximum count (unqualified) |
| `owl:qualifiedCardinality` | exact count for specified class |
| `owl:minQualifiedCardinality` | minimum count for specified class |
| `owl:maxQualifiedCardinality` | maximum count for specified class |

---

## 3. owl:equivalentClass (Defined Classes)

A class is *defined* (necessary AND sufficient) via `owl:equivalentClass`:

```xml
<owl:Class rdf:about="&fibo-sec-dbt-bnd;FixedCouponBond">
  <owl:equivalentClass>
    <owl:Class>
      <owl:intersectionOf rdf:parseType="Collection">
        <rdf:Description rdf:about="&fibo-sec-dbt-bnd;Bond"/>
        <rdf:Description rdf:about="&fibo-sec-dbt-dbti;FixedIncomeSecurity"/>
        <owl:Restriction>
          <owl:onProperty rdf:resource="&fibo-sec-dbt-dbti;hasCouponRate"/>
          <owl:someValuesFrom rdf:resource="&fibo-fnd-acc-cur;InterestRate"/>
        </owl:Restriction>
      </owl:intersectionOf>
    </owl:Class>
  </owl:equivalentClass>
</owl:Class>
```

Reading: FixedCouponBond ≡ Bond ∩ FixedIncomeSecurity ∩ (∃hasCouponRate.InterestRate)

---

## 4. owl:unionOf (Polymorphic Domains/Ranges)

Used in property restrictions to allow multiple types:

```xml
<owl:Restriction>
  <owl:onProperty rdf:resource="&fibo-der-drc-swp;hasUnderlier"/>
  <owl:someValuesFrom>
    <owl:Class>
      <owl:unionOf rdf:parseType="Collection">
        <rdf:Description rdf:about="&fibo-fbc-fi-fi;Security"/>
        <rdf:Description rdf:about="&fibo-ind-ri-ri;RateIndex"/>
        <rdf:Description rdf:about="&fibo-der-drc-comm;CommodityIndex"/>
      </owl:unionOf>
    </owl:Class>
  </owl:someValuesFrom>
</owl:Restriction>
```

Reading: the underlier can be a Security OR a RateIndex OR a CommodityIndex.

---

## 5. owl:disjointWith

Explicitly states two classes can never have a common instance:

```xml
<owl:Class rdf:about="&fibo-der-drc-swp;FixedLeg">
  <owl:disjointWith rdf:resource="&fibo-der-drc-swp;FloatingLeg"/>
</owl:Class>
```

Common disjoint pairs in FIBO:
- `FixedLeg` / `FloatingLeg`
- `MandatoryCorporateAction` / `VoluntaryCorporateAction`
- `CommonShare` / `PreferredShare`

---

## 6. owl:NamedIndividual (Enumerated Values)

FIBO uses named individuals for controlled vocabularies:

```xml
<owl:NamedIndividual rdf:about="&fibo-fnd-dt-bd;ActualActualISDA">
  <rdf:type rdf:resource="&fibo-fnd-dt-bd;DayCountConvention"/>
  <skos:definition>Day count convention where both numerator and denominator
    are calculated based on actual calendar days...</skos:definition>
</owl:NamedIndividual>
```

To find all members of an enumeration:
```bash
grep -r 'rdf:type.*DayCountConvention' /c/work/projects/fibo --include="*.rdf"
grep -r 'rdf:type.*CreditEventType' /c/work/projects/fibo --include="*.rdf"
```

---

## 7. owl:imports Chain Resolution

Each FIBO file declares its dependencies at the top:

```xml
<owl:Ontology rdf:about="https://spec.edmcouncil.org/fibo/ontology/SEC/Debt/Bonds/">
  <owl:imports rdf:resource="https://spec.edmcouncil.org/fibo/ontology/FBC/FinancialInstruments/FinancialInstruments/"/>
  <owl:imports rdf:resource="https://spec.edmcouncil.org/fibo/ontology/SEC/Debt/DebtInstruments/"/>
</owl:Ontology>
```

**To resolve a namespace URI to a file path:**
1. Open `C:\work\projects\fibo\catalog-v001.xml`
2. Search: `<uri name="https://spec.edmcouncil.org/.../SEC/Debt/Bonds/" uri="SEC/Debt/Bonds.rdf"/>`
3. The `uri=` attribute is relative to the catalog file location

**Import chain depth for Bond:**
```
SEC/Debt/Bonds.rdf
  → SEC/Debt/DebtInstruments.rdf
  → FBC/FinancialInstruments/FinancialInstruments.rdf
      → FBC/DebtAndEquities/Debt.rdf
      → FND/Agreements/Contracts.rdf
          → FND/Agreements/Agreements.rdf
              → cmns/PartiesAndSituations [owl:Thing root]
```

---

## 8. SKOS Annotation Properties

| Annotation | Meaning |
|-----------|---------|
| `skos:definition` | Authoritative definition (every class and property) |
| `skos:example` | Concrete example |
| `skos:note` | Editorial or usage note |
| `skos:scopeNote` | Scope restriction |
| `rdfs:label` | Human-readable name |

```bash
# Extract all definitions from a file
grep -A2 'skos:definition' /c/work/projects/fibo/SEC/Debt/Bonds.rdf
```

---

## 9. XML Entity Declarations (Namespace Shorthand)

```xml
<!DOCTYPE rdf:RDF [
  <!ENTITY fibo-sec-dbt-bnd "https://spec.edmcouncil.org/fibo/ontology/SEC/Debt/Bonds/#">
  <!ENTITY fibo-fbc-fi-fi   "https://spec.edmcouncil.org/fibo/ontology/FBC/FinancialInstruments/FinancialInstruments/#">
  <!ENTITY cmns-pts         "https://www.omg.org/spec/Commons/PartiesAndSituations/#">
]>
```

When you see `&fibo-sec-dbt-bnd;Bond`, the full URI is:
`https://spec.edmcouncil.org/fibo/ontology/SEC/Debt/Bonds/#Bond`

The local name (after `#`) is the class/property name used in diagrams and documentation.

---

## 10. Property Chain Axioms

Used to infer transitive relationships:

```xml
<owl:ObjectProperty rdf:about="...hasUltimateParent">
  <owl:propertyChainAxiom rdf:parseType="Collection">
    <rdf:Description rdf:about="...hasParent"/>
    <rdf:Description rdf:about="...hasParent"/>
  </owl:propertyChainAxiom>
</owl:ObjectProperty>
```

Reading: if A hasParent B and B hasParent C, then A hasUltimateParent C (two hops).
