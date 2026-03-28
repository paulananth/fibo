# FIBO Module Reference

Detailed per-module guide: key files, top-level classes, import dependencies, and common questions.

---

## FND — Foundations
**Path:** `C:\work\projects\fibo\FND\`

The base layer. Every other module imports FND. Defines abstract concepts shared across finance.

### Key Files
| File | What it defines |
|------|----------------|
| `FND/Agreements/Agreements.rdf` | `Agreement` — base of ALL contractual relationships |
| `FND/Agreements/Contracts.rdf` | `Contract`, `WrittenContract`, `ContractParty` |
| `FND/Parties/Parties.rdf` | `Party`, `IndependentParty`, `RelativelyContextualParty` |
| `FND/Accounting/CurrencyAmount.rdf` | `MonetaryAmount`, `MonetaryPrice`, `hasNotionalAmount` |
| `FND/DatesAndTimes/FinancialDates.rdf` | `ExplicitDate`, `CalculatedDate`, `DatePeriod` |
| `FND/Quantities/QuantitiesAndUnits.rdf` | `Quantity`, `MeasurementUnit`, `ScalarQuantityValue` |
| `FND/ProductsAndServices/PaymentsAndSchedules.rdf` | `Payment`, `PaymentSchedule` |

### Key Classes
- `Agreement` — root of contract/instrument hierarchy (via cmns-pts:Situation)
- `Contract` / `WrittenContract` — immediate parents of most financial instruments
- `ContractParty` — a party with a contractual role

### Imports From
- `cmns` (Commons ontology — Situation, Occurrence, Party base classes)

---

## BE — Business Entities
**Path:** `C:\work\projects\fibo\BE\`

Legal entity types, corporate structures, LEI registration.

### Key Files
| File | What it defines |
|------|----------------|
| `BE/LegalEntities/LegalPersons.rdf` | `LegalPerson`, `LegalEntity` |
| `BE/LegalEntities/FormalOrganizations.rdf` | `FormalOrganization`, `RegisteredOrganization` |
| `BE/GovernmentEntities/GovernmentEntities.rdf` | `GovernmentalBody`, `Regulator` |
| `BE/LEIs/LEIs.rdf` | `LegalEntityIdentifier`, LEI registration framework |
| `BE/OwnershipAndControl/OwnershipAndControl.rdf` | Shareholding, control relationships |

### Imports From
- FND (Agreements, Parties, Dates)

---

## FBC — Financial Business & Commerce
**Path:** `C:\work\projects\fibo\FBC\`

The financial instrument base layer. Defines `Security`, `DebtInstrument`, `EquityInstrument`,
and base properties used by SEC, DER, LOAN.

### Key Files
| File | What it defines |
|------|----------------|
| `FBC/FinancialInstruments/FinancialInstruments.rdf` | `FinancialInstrument`, `Security`, `DebtInstrument` |
| `FBC/DebtAndEquities/Debt.rdf` | `CreditAgreement`, `CreditAgreementRepaidAtMaturity`, `Loan` |
| `FBC/DebtAndEquities/Equities.rdf` | `EquityInstrument`, `ShareholderEquity` |
| `FBC/ProductsAndServices/FinancialProductsAndServices.rdf` | `FinancialProduct`, `FinancialService` |
| `FBC/FunctionalEntities/FinancialServicesEntities.rdf` | `FinancialInstitution`, `Broker`, `Dealer` |

### Key Classes
```
WrittenContract (FND)
  ├── FinancialInstrument
  │     ├── Security
  │     │     ├── DebtInstrument  (also subClassOf CreditAgreement)
  │     │     └── EquityInstrument
  │     └── (many subclasses)
  └── CreditAgreement
        └── CreditAgreementRepaidAtMaturity  ← parent of Bond
```

### Key Properties
- `isIssuedBy` (domain: Security → range: LegalPerson)
- `isDenominatedIn` (domain: FinancialInstrument → range: Currency)
- `isLegallyRecordedIn` (domain: Security → range: Registry/Jurisdiction)
- `hasNominalValue` (domain: DebtInstrument → range: MonetaryAmount)
- `isTradedOn` (domain: Security → range: Exchange)

### Imports From
- FND, BE

---

## SEC — Securities
**Path:** `C:\work\projects\fibo\SEC\`

Concrete security types: all bond subtypes, equity shares, money market instruments,
structured products, funds, warrants.

### Key Files
| File | What it defines |
|------|----------------|
| `SEC/Debt/Bonds.rdf` | `Bond` and all 39 subclasses |
| `SEC/Debt/DebtInstruments.rdf` | `TradableDebtInstrument`, `FixedIncomeSecurity`, `VariableIncomeSecurity` |
| `SEC/Debt/SyntheticCDOs.rdf` | CDO tranches, structured debt |
| `SEC/Equities/EquityInstruments.rdf` | `Share`, `CommonShare`, `PreferredShare`, `DepositaryReceipt` |
| `SEC/Funds/Funds.rdf` | `Fund`, `MutualFund`, `ETF` |
| `SEC/Securities/SecuritiesListings.rdf` | `ListedSecurity`, exchange listing attributes |
| `SEC/Securities/Baskets.rdf` | `SecurityBasket`, `Index` |

### Bond Hierarchy Key Points
- **Bond has TWO parents:** `CreditAgreementRepaidAtMaturity` AND `TradableDebtInstrument`
- **39 direct/indirect subclasses** including: AmortizingBond, BulletBond, CallableBond,
  ConvertibleBond, CorporateBond, GovernmentBond, MunicipalBond, SovereignBond, TreasuryBond,
  GreenBond, FixedCouponBond, FloatingRateNote, ZeroCouponBond, TIPS, MediumTermNote, StripBond
- Multiple-inheritance is common: e.g., TreasuryBond subClassOf {SovereignBond, USTreasurySecurity}

### Imports From
- FBC, FND, BE

---

## DER — Derivatives
**Path:** `C:\work\projects\fibo\DER\`

Futures, options, swaps, forwards, and their legs/terms.

### Key Files
| File | What it defines |
|------|----------------|
| `DER/DerivativesContracts/DerivativesBasics.rdf` | `DerivativeInstrument`, base derivative properties |
| `DER/DerivativesContracts/Swaps.rdf` | `Swap`, `SwapLeg`, `FixedLeg`, `FloatingLeg`, `ReturnSwap` |
| `DER/RateDerivatives/IRSwaps.rdf` | `InterestRateSwap`, `FixedFloatIRS`, `OvernightIndexSwap` |
| `DER/CreditDerivatives/CreditDefaultSwaps.rdf` | `CreditDefaultSwap`, `SingleNameCDS`, `IndexCDS` |
| `DER/SecurityBasedDerivatives/EquitySwaps.rdf` | `EquitySwap`, `TotalReturnSwap`, `VarianceSwap` |
| `DER/DerivativesContracts/Options.rdf` | `Option`, `CallOption`, `PutOption` |
| `DER/DerivativesContracts/Futures.rdf` | `Future`, `ForwardContract` |
| `DER/DerivativesContracts/CurrencyContracts.rdf` | `CurrencySwap`, `CurrencyForward` |
| `DER/DerivativesContracts/CommoditiesContracts.rdf` | `CommoditySwap`, `CommodityDerivative` |

### Swap Class Hierarchy (collapsed)
```
Swap
  ├── RatesSwap → InterestRateSwap → FixedFloat, FloatFloat, OIS, ZeroCoupon, Inflation
  ├── ReturnSwap → TotalReturnSwap, IndexReturnSwap, ExcessReturnSwap
  ├── StatisticalSwap → DispersionSwap (variance), CorrelationSwap
  ├── CurrencySwap
  └── CommoditySwap
CreditDefaultSwap (separate — subClassOf DerivativeInstrument)
  ├── SingleNameCDS, MultiNameCDS, IndexCDS, BasketCDS, TrancheCDS
```

### Imports From
- FBC, SEC, FND, BE

---

## LOAN — Loans
**Path:** `C:\work\projects\fibo\LOAN\`

Consumer, commercial, and real estate loans. Extends FBC's CreditAgreement.

### Key Files
| File | What it defines |
|------|----------------|
| `LOAN/LoansGeneral/Loans.rdf` | `Loan`, `LoanAgreement`, `Borrower`, `Lender` |
| `LOAN/RealEstateLoans/RealEstateLoans.rdf` | `MortgageLoan`, `HomeEquityLoan`, `ReverseAnnuityMortgage` |
| `LOAN/LoansSpecific/StudentLoans.rdf` | `StudentLoan`, `FederalStudentLoan` |
| `LOAN/LoansSpecific/CommercialLoans.rdf` | `CommercialLoan`, `TermLoan`, `RevolvingCreditFacility` |
| `LOAN/LoansSpecific/ConsumerLoans.rdf` | `ConsumerLoan`, `AutoLoan` |
| `LOAN/RealEstateLoans/MortgageLoans.rdf` | Mortgage subtypes, LTV, amortization |

### Key Classes
```
FBC:CreditAgreement
  └── LOAN:Loan
        ├── MortgageLoan
        │     ├── ConformingMortgage, JumboMortgage, FHALoan
        │     └── AdjustableRateMortgage, FixedRateMortgage
        ├── CommercialLoan
        │     └── TermLoan, RevolvingCreditFacility, BridgeLoan
        ├── ConsumerLoan
        │     └── AutoLoan, PersonalLoan
        └── StudentLoan
```

### Imports From
- FBC (CreditAgreement as loan base), FND, BE

---

## CAE — Corporate Actions & Events
**Path:** `C:\work\projects\fibo\CAE\`

Corporate actions (dividends, mergers, rights issues) and lifecycle events.
Based on ISO 15022 and GLEIF standards.

### Key Files
| File | What it defines |
|------|----------------|
| `CAE/CorporateEvents/CorporateActions.rdf` | `CorporateAction`, `MandatoryCorporateAction`, `VoluntaryCorporateAction` |
| `CAE/CorporateEvents/SecurityRelatedCorporateActions.rdf` | 53 security-specific action types |
| `CAE/CorporateEvents/NotificationEvents.rdf` | `NotificationEvent`, `CompanyAnnouncementEvent` |
| `CAE/CorporateEvents/ExchangeSpecificCorporateActions.rdf` | Exchange-specific events |

### Key Classes
```
cmns-pts:Occurrence
  └── Action (CAE)
        └── CorporateAction
              ├── MandatoryCorporateAction   ← company-initiated, no shareholder choice
              │     ├── CashDividend, StockDividend, StockSplit, ReverseSplit
              │     ├── MergerAbsorption, Spinoff, Liquidation
              │     └── MaturityRedemption, EarlyRedemption, CallRedemption
              └── VoluntaryCorporateAction   ← shareholder must elect
                    ├── TenderOffer, RightsIssue, ExchangeOffer
                    └── DividendReinvestmentPlan (DRIP)
```

### Key Properties
- `convertsFrom` — what security is surrendered in a conversion event
- `convertsTo` — what security is received in a conversion event

### Imports From
- FBC, SEC, FND, BE

---

## IND — Indices & Indicators
**Path:** `C:\work\projects\fibo\IND\`

Rate indices (SOFR, EURIBOR), equity indices (S&P 500), economic indicators (CPI, GDP).

### Key Files
| File | What it defines |
|------|----------------|
| `IND/RateIndicators/RateIndicators.rdf` | `RateIndex`, `FloatingRateIndex`, base rate classes |
| `IND/EconomicIndicators/EconomicIndicators.rdf` | `EconomicIndicator`, `ConsumerPriceIndex` |
| `IND/MarketIndices/EquityIndexProducts.rdf` | `EquityIndex`, `CapitalizationWeightedIndex` |

### Imports From
- FBC, FND

---

## MD — Market Data
**Path:** `C:\work\projects\fibo\MD\`

Pricing, quotes, analytics, and market data for instruments and indices.

### Key Files
| File | What it defines |
|------|----------------|
| `MD/DebtTemporal/DebtAnalytics.rdf` | Bond analytics: `YieldToMaturity`, `ModifiedDuration`, `ConvexityValue` |
| `MD/DebtTemporal/BondPricing.rdf` | `DirtyPrice`, `CleanPrice`, `AccruedInterest` |
| `MD/SecuritiesExt/SecuritiesPricing.rdf` | Equity pricing, NAV, bid/ask |

### Imports From
- SEC, IND, FBC, FND
