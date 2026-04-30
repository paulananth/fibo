# FIBO Module Reference

Use this as an entry-point map. Verify details in the `.rdf` files before answering.

## Cross-Cutting Files

| File or directory | Useful for |
| --- | --- |
| `catalog-v001.xml` | Root ontology IRI to local file mappings |
| `*/catalog-v001.xml` | Module-specific catalog context where present |
| `MetadataFIBO.rdf` | Top-level ontology metadata |
| `*/Metadata*.rdf` | Domain and subdomain metadata, maturity, publication annotations |
| `*/All*.rdf` | Aggregate ontologies for modules, examples, reference individuals, and regional packages |
| `etc/testing/hygiene_parameterized/*.sparql` | Hygiene checks for labels, definitions, licenses, imports, cycles, deprecated resources, and property constraints |
| `etc/vocabulary/*.sparql` | Vocabulary-product queries used by ontology-publisher |
| `etc/vocabulary/scaffolding.ttl` | Vocabulary-product scaffolding triples |

## FND - Foundations

Base concepts imported throughout FIBO.

Key files:

| File | Useful for |
| --- | --- |
| `FND/Agreements/Agreements.rdf` | `Agreement` and agreement relationships |
| `FND/Agreements/Contracts.rdf` | `Contract`, `WrittenContract`, contract terms and parties |
| `FND/Parties/Parties.rdf` | Party concepts and party-in-role patterns |
| `FND/Accounting/CurrencyAmount.rdf` | Monetary amounts, currencies, interest rates |
| `FND/DatesAndTimes/FinancialDates.rdf` | Financial dates, schedules, business dates |
| `FND/ProductsAndServices/PaymentsAndSchedules.rdf` | Payment and schedule concepts |
| `FND/Relations/Relations.rdf` | General relationship vocabulary |
| `FND/Utilities/AnnotationVocabulary.rdf` | FIBO annotation vocabulary and maturity metadata |

## BE - Business Entities

Legal entities, organizations, government bodies, trusts, and ownership/control.

Key files:

| File | Useful for |
| --- | --- |
| `BE/LegalEntities/LegalPersons.rdf` | Legal persons and legal entities |
| `BE/LegalEntities/FormalBusinessOrganizations.rdf` | Formal and registered organizations |
| `BE/LegalEntities/LEIEntities.rdf` | LEI-related entity concepts |
| `BE/GovernmentEntities/GovernmentEntities.rdf` | Governmental bodies and jurisdictions |
| `BE/Trusts/Trusts.rdf` | Trusts, trustees, beneficiaries |
| `BE/OwnershipAndControl/OwnershipParties.rdf` | Ownership parties and ownership roles |
| `BE/OwnershipAndControl/ControlParties.rdf` | Control parties and control roles |
| `BE/OwnershipAndControl/CorporateOwnership.rdf` | Corporate ownership |
| `BE/OwnershipAndControl/CorporateControl.rdf` | Corporate control |

## FBC - Financial Business and Commerce

Base layer for instruments, services, markets, debt, and commerce concepts.

Key files:

| File | Useful for |
| --- | --- |
| `FBC/FinancialInstruments/FinancialInstruments.rdf` | `FinancialInstrument`, `Security`, `DebtInstrument`, `EquityInstrument` |
| `FBC/FinancialInstruments/InstrumentPricing.rdf` | Pricing concepts for instruments |
| `FBC/DebtAndEquities/Debt.rdf` | `CreditAgreement`, debt terms, repayment concepts |
| `FBC/DebtAndEquities/CreditRatings.rdf` | Credit ratings and rating agencies |
| `FBC/DebtAndEquities/Guaranty.rdf` | Guaranty concepts |
| `FBC/ProductsAndServices/FinancialProductsAndServices.rdf` | Financial products and services |
| `FBC/ProductsAndServices/ClientsAndAccounts.rdf` | Clients, accounts, account relationships |
| `FBC/FunctionalEntities/Markets.rdf` | Markets and exchanges |
| `FBC/FunctionalEntities/FinancialServicesEntities.rdf` | Financial institutions and service providers |

Common anchors:

```text
FND:WrittenContract
  -> FBC:FinancialInstrument
  -> FBC:Security
  -> FBC:DebtInstrument / FBC:EquityInstrument

FND:WrittenContract
  -> FBC:CreditAgreement
  -> FBC:CreditAgreementRepaidAtMaturity
```

## SEC - Securities

Securities, bonds, equities, funds, listings, restrictions, pools, and baskets.

Key files:

| File | Useful for |
| --- | --- |
| `SEC/Debt/Bonds.rdf` | `Bond` and bond subtypes |
| `SEC/Debt/DebtInstruments.rdf` | Tradable debt, fixed/variable income securities |
| `SEC/Debt/TradedShortTermDebt.rdf` | Short-term debt instruments |
| `SEC/Debt/MortgageBackedSecurities.rdf` | Mortgage-backed securities |
| `SEC/Equities/EquityInstruments.rdf` | `Share`, common shares, preferred shares |
| `SEC/Equities/DepositaryReceipts.rdf` | Depositary receipts |
| `SEC/Funds/Funds.rdf` | Funds and fund-related concepts |
| `SEC/Funds/CollectiveInvestmentVehicles.rdf` | Collective investment vehicles |
| `SEC/Securities/SecuritiesListings.rdf` | Listings and listing relationships |
| `SEC/Securities/SecuritiesIssuance.rdf` | Security issuance concepts |
| `SEC/Securities/SecuritiesIdentification.rdf` | Security identifiers |
| `SEC/Securities/Baskets.rdf` | Security baskets |
| `SEC/Securities/Pools.rdf` | Pools of assets |

Important pattern: `SEC/Debt/Bonds.rdf` imports both FBC debt/instrument files and
SEC debt files. `Bond` has multiple named parents; trace all declared parents.

## DER - Derivatives

Derivatives contracts, swaps, options, futures, forwards, credit derivatives, and
security-based derivatives.

Key files:

| File | Useful for |
| --- | --- |
| `DER/DerivativesContracts/DerivativesBasics.rdf` | Base derivative concepts |
| `DER/DerivativesContracts/Swaps.rdf` | `Swap`, swap legs, return swaps, statistical swaps |
| `DER/DerivativesContracts/Options.rdf` | Options, calls, puts |
| `DER/DerivativesContracts/FuturesAndForwards.rdf` | Futures and forwards |
| `DER/DerivativesContracts/CurrencyContracts.rdf` | Currency swaps and forwards |
| `DER/DerivativesContracts/CommoditiesContracts.rdf` | Commodity derivatives |
| `DER/RateDerivatives/IRSwaps.rdf` | Interest-rate swaps |
| `DER/CreditDerivatives/CreditDefaultSwaps.rdf` | Credit default swaps |
| `DER/SecurityBasedDerivatives/SecurityBasedDerivatives.rdf` | Security-based derivatives |
| `DER/SecurityBasedDerivatives/EquitySwaps.rdf` | Equity swaps |

## LOAN - Loans

Loan agreements, parties, products, consumer loans, commercial loans, student
loans, and real-estate loans.

Key files:

| File | Useful for |
| --- | --- |
| `LOAN/LoansGeneral/Loans.rdf` | General loan concepts and parties |
| `LOAN/LoansGeneral/LoanApplications.rdf` | Loan applications |
| `LOAN/LoansGeneral/LoanEvents.rdf` | Loan lifecycle events |
| `LOAN/LoansSpecific/LoanProducts.rdf` | Loan product concepts |
| `LOAN/LoansSpecific/ConsumerLoans.rdf` | Consumer loans |
| `LOAN/LoansSpecific/CommercialLoans.rdf` | Commercial loans |
| `LOAN/LoansSpecific/StudentLoans.rdf` | Student loans |
| `LOAN/RealEstateLoans/Mortgages.rdf` | Mortgages and reverse mortgages |
| `LOAN/RealEstateLoans/MortgageOrigination.rdf` | Mortgage origination concepts |
| `LOAN/RealEstateLoans/HomeMortgageDisclosureActCoveredMortgages.rdf` | HMDA-covered mortgages |

## CAE - Corporate Actions and Events

Corporate events, corporate actions, and security-related actions.

Key files:

| File | Useful for |
| --- | --- |
| `CAE/CorporateEvents/CorporateActions.rdf` | Corporate actions and action lifecycle concepts |
| `CAE/CorporateEvents/SecurityRelatedCorporateActions.rdf` | Security-related corporate actions |
| `CAE/CorporateEvents/ISO15022-CorporateActionIndividuals.rdf` | ISO 15022 action individuals |
| `CAE/CorporateEvents/GLEIF-CorporateActionIndividuals.rdf` | GLEIF action individuals |

## IND - Indices and Indicators

Interest rates, economic indicators, foreign exchange, market indices, and
reference data.

Key files:

| File | Useful for |
| --- | --- |
| `IND/Indicators/Indicators.rdf` | Base indicator concepts |
| `IND/InterestRates/InterestRates.rdf` | Interest rates and rate indices |
| `IND/InterestRates/CommonInterestRates.rdf` | Common interest-rate individuals |
| `IND/InterestRates/MarketDataProviders.rdf` | Market data providers |
| `IND/EconomicIndicators/EconomicIndicators.rdf` | Economic indicators |
| `IND/EconomicIndicators/NorthAmericanIndicators/USEconomicIndicators.rdf` | US economic indicators |
| `IND/ForeignExchange/ForeignExchange.rdf` | Foreign exchange concepts |
| `IND/MarketIndices/BasketIndices.rdf` | Basket indices |
| `IND/MarketIndices/EquityIndexExampleIndividuals.rdf` | Equity index examples |

## MD - Market Data

Temporal market data for debt, derivatives, funds, and security trading/credit status.

Key files:

| File | Useful for |
| --- | --- |
| `MD/DebtTemporal/DebtAnalytics.rdf` | Debt analytics such as yield, duration, convexity |
| `MD/DerivativesTemporal/FuturesTemporal.rdf` | Futures temporal concepts |
| `MD/DerivativesTemporal/ETOptionsTemporal.rdf` | Exchange-traded options temporal concepts |
| `MD/CIVTemporal/FundsTemporal.rdf` | Fund temporal concepts |
| `MD/TemporalCore/SecurityTradingStatuses.rdf` | Trading status concepts |
| `MD/TemporalCore/SecurityCreditStatuses.rdf` | Credit status concepts |

## BP - Business Process

Business process and securities issuance.

Key files:

| File | Useful for |
| --- | --- |
| `BP/Process/FinancialContextAndProcess.rdf` | Financial context and process vocabulary |
| `BP/SecuritiesIssuance/IssuanceProcess.rdf` | Securities issuance process |
| `BP/SecuritiesIssuance/IssuanceDocuments.rdf` | Issuance documents |
| `BP/SecuritiesIssuance/DebtIssuance.rdf` | Debt issuance |
| `BP/SecuritiesIssuance/EquitiesIPOIssuance.rdf` | Equity IPO issuance |
| `BP/SecuritiesIssuance/MBSIssuance.rdf` | Mortgage-backed securities issuance |

## ACTUS

ACTUS taxonomy and contract term mappings.

Key files:

| File | Useful for |
| --- | --- |
| `ACTUS/ACTUSTaxonomy.rdf` | ACTUS taxonomy concepts |
| `ACTUS/ACTUSContractTerms.rdf` | ACTUS contract terms |
| `ACTUS/ACTUSTermApplicabilityMapping.rdf` | Term applicability mappings |
| `ACTUS/ACTUS-examples.ttl` | Turtle examples |
