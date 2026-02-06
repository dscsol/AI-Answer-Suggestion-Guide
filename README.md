# AI Answer Suggestion Guide for Hong Kong Tax Return Questionnaire

## Overview

This document provides comprehensive guidance on how the AI application should automatically suggest answers to tax related questions.

Instead of asking users to manually answer each question, the application analyzes the financial documents and suggests the most probable answers for user review and approval.

---

## PART A: GENERAL QUESTIONNAIRE QUESTIONS

### Section: Company Details

| Question                                                     | Field Name          | Data Source     | Extraction Logic                                                |
| ------------------------------------------------------------ | ------------------- | --------------- | --------------------------------------------------------------- |
| Postal address if different from that printed on this return | `address`           | Previous Return | Check last year's return                                        |
| Telephone Number                                             | `telephoneNumber`   | Previous Return | Check last year's return                                        |
| Principal business activity                                  | `principalBusiness` | Previous Return | Check last year's return                                        |
| Hong Kong Standard Industrial Classification Code            | `industryCode`      | Previous Return | Check last year's return                                        |
| Name of authorized representative (if any)                   | `authorizedRep`     | Previous Return | Check last year's return                                        |
| Address of authorized representative                         | `repAddress`        | Previous Return | Check last year's return                                        |
| The representative's Business Registration No. (if any)      | `repBR`             | Previous Return | Check last year's return                                        |
| The representative's Branch No. (if any)                     | `repBranch`         | Previous Return | Check last year's return                                        |
| Practising certificate number of the CPA                     | `cpaCertNo`         | Previous Return | Check last year's return                                        |
| Did you commence business within the basis period?           | `commenceBiz`       | Trial Balance   | Check if TB shows first year of operation (no opening balances) |
| If yes, state the date of commencement                       | `dateOfCommence`    | Manual          | Not extractable - user must provide                             |
| Did you cease business within the basis period?              | `ceaseBiz`          | Trial Balance   | Check if final year (assets sold, closing operations)           |
| If yes, state the date of cessation                          | `dateOfCease`       | Manual          | Not extractable - user must provide                             |
| On cessation, was your business transferred?                 | `bizTransfer`       | Manual          | Not extractable - user must provide                             |
| If yes, state the name of the person                         | `bizTransferTo`     | Manual          | Not extractable - user must provide                             |
| On cessation, were assets sold to an associated person?      | `assetSold`         | Trial Balance   | Check disposal of fixed assets section                          |

---

### Section: Currency Conversion Rate

| Question                                    | Field Name | Data Source          | Extraction Logic                                                                             |
| ------------------------------------------- | ---------- | -------------------- | -------------------------------------------------------------------------------------------- |
| Please provide the currency conversion rate | `rate`     | Trial Balance Header | Check currency noted in TB; if not HKD, suggest default rate of 1.0 or extract from TB notes |

---

### Section: Brought Forward (from Last Year Tax Computation)

| Question                                               | Field Name                             | Data Source  | Extraction Logic                                           |
| ------------------------------------------------------ | -------------------------------------- | ------------ | ---------------------------------------------------------- |
| Provisional tax charged                                | `provisionalTaxCharged`                | Last Year TC | Extract from "Tax Payable" section of prior year           |
| Depreciation allowances - Written down value b/f - 10% | `tenPercentPoolWrittenDownValueBF`     | Last Year TC | Extract from "Depreciation Schedule - 10% Pool - WDV C/F"  |
| Depreciation allowances - Written down value b/f - 20% | `twentyPercentPoolWrittenDownValueBF`  | Last Year TC | Extract from "Depreciation Schedule - 20% Pool - WDV C/F"  |
| Depreciation allowances - Written down value b/f - 30% | `thirtyPercentPoolWrittenDownValueBF`  | Last Year TC | Extract from "Depreciation Schedule - 30% Pool - WDV C/F"  |
| Hire purchase - Written down value b/f                 | `hirePurchaseWrittenDownValueBF`       | Last Year TC | Extract from "Hire Purchase Schedule"                      |
| Commercial building allowance breakdown                | `commercialBuildingAllowanceBreakdown` | Last Year TC | Extract from "Commercial Building Allowance Schedule"      |
| Industrial building allowance breakdown                | `industrialBuildingAllowanceBreakdown` | Last Year TC | Extract from "Industrial Building Allowance Schedule"      |
| Refurbishment breakdown                                | `refurbishmentBreakdown`               | Last Year TC | Extract from "Refurbishment Expenditure Schedule"          |
| Bad debts - Balance b/f - Specific                     | `badDebtsBalanceBFSpecific`            | Last Year TC | Extract from "Bad Debts Movement - Specific Provision C/F" |
| Bad debts - Balance b/f - General                      | `badDebtsBalanceBFGeneral`             | Last Year TC | Extract from "Bad Debts Movement - General Provision C/F"  |
| Obsolete inventories - Balance b/f - Specific          | `obsoleteInventoryBalanceBFSpecific`   | Last Year TC | Extract from "Obsolete Stock Movement - Specific C/F"      |
| Obsolete inventories - Balance b/f - General           | `obsoleteInventoryBalanceBFGeneral`    | Last Year TC | Extract from "Obsolete Stock Movement - General C/F"       |
| Statement of Loss - Balance b/f                        | `lossBroughtForward`                   | Last Year TC | Extract from "Tax Loss Available for Set-off"              |

---

### Q1: Sales Offshore Contracts

| Question | Field Name | Data Source | Extraction Logic
| Please provide the Company's contracts of sales and purchase which were negotiated, concluded and effected outside Hong Kong. (please input zero if none) | `salesOffshoreContracts` | Trial Balance | Check "Sales - Offshore" or similar accounts; if no suggest 0, otherwise ask for breakdown |

---

### Q2: Management or Service Fee Income

| Question                                              | Field Name                              | Data Source   | Extraction Logic                       |
| ----------------------------------------------------- | --------------------------------------- | ------------- | -------------------------------------- |
| Offshore-sourced income based on location of services | `offshoreTradingIncome`                 | Trial Balance | Look for "Offshore Trading Income"     |
| Offshore service fee income                           | `offshoreServiceFeeIncome`              | Trial Balance | Look for "Offshore Service Fee Income" |
| Offshore other income                                 | `offshoreOtherIncome`                   | Trial Balance | Look for "Other Income - Offshore"     |
| Relevant expenses                                     | `offshoreSourcedIncomeRelevantExpenses` | Manual        | Not extractable - user must provide    |

---

### Q3: Royalty Income

| Question                                             | Field Name           | Data Source   | Extraction Logic                    |
| ---------------------------------------------------- | -------------------- | ------------- | ----------------------------------- |
| Gross amount of royalty income                       | `royaltyGrossIncome` | Trial Balance | Look for "Royalty Income" account   |
| Amount of royalty expense                            | `royaltyExpense`     | Trial Balance | Look for "Royalty Expense" account  |
| For royalty paid to non-residents, provide breakdown | `royaltyNonResident` | Manual        | Not extractable - user must provide |

---

### Q4: Sundry Income

| Question                                 | Field Name     | Data Source   | Extraction Logic                                                                              |
| ---------------------------------------- | -------------- | ------------- | --------------------------------------------------------------------------------------------- |
| Breakdown of sundry income               | `sundryIncome` | Trial Balance | Look for "Sundry Income", "Other Income", "Miscellaneous Income" accounts; extract line items |
| Advise if any items are capital/offshore | `incomeNature` | AI Analysis   | Analyze description: keywords like "disposal", "asset sale" = capital; "overseas" = offshore  |

---

### Q5: Gain on Foreign Exchange Contracts Trading

| Question                              | Field Name | Data Source     | Extraction Logic                                                                    |
| ------------------------------------- | ---------- | --------------- | ----------------------------------------------------------------------------------- |
| Purpose of foreign exchange contracts | `purpose`  | Manual/TB Notes | Not extractable - default suggest "hedging" if company has foreign revenue/expenses |

---

### Q6: Subsidy, Financial Assistance, and Government Grant

| Question                                                     | Field Name                                     | Data Source | Extraction Logic |
| Breakdown of subsidy, financial assistance, government grant | `subsidyFinancialAssistanceAndGovernmentGrant` | Manual      | Not extractable - user must provide |

---

### Q7: Provision for Doubtful Debts, Obsolete Stocks, Other Expenses

| Question                                        | Field Name                                     | Data Source   | Extraction Logic                                                                                             |
| ----------------------------------------------- | ---------------------------------------------- | ------------- | ------------------------------------------------------------------------------------------------------------ |
| General provision - bad debts - Provision made  | `generalProvisionDoubtfulDebts.provisionMade`  | Trial Balance | Look for "Provision for Doubtful Debts" in expenses, treat as general by default unless marked as "Specfic"  |
| General provision - bad debts - Write-back      | `generalProvisionDoubtfulDebts.writeBack`      | Trial Balance | Look for negative entries or "Reversal of Provision", treat as general by default unless marked as "Specfic" |
| General provision - bad debts - Write-off       | `generalProvisionDoubtfulDebts.writeOff`       | Trial Balance | Look for "Bad Debts Written Off", treat as general by default unless marked as "Specfic"                     |
| Specific provision - bad debts - Provision made | `specificProvisionDoubtfulDebts.provisionMade` | Trial Balance | Same logic as general, but for "Specific Provision" accounts                                                 |
| Specific provision - bad debts - Write-back     | `specificProvisionDoubtfulDebts.writeBack`     | Trial Balance | Same logic as general, but for "Specific Provision" accounts                                                 |
| Specific provision - bad debts - Write-off      | `specificProvisionDoubtfulDebts.writeOff`      | Trial Balance | Same logic as general, but for "Specific Provision" accounts                                                 |
| General provision - obsolete inventory          | `generalProvisionObsoleteInventory.*`          | Trial Balance | Look for "Provision for Obsolete Stock - General"                                                            |
| Specific provision - obsolete inventory         | `specificProvisionObsoleteInventory.*`         | Trial Balance | Look for "Provision for Obsolete Stock - Specific"                                                           |

---

### Q8: Exchange Gain/Loss

| Question                               | Field Name                | Data Source   | Extraction Logic                                                          |
| -------------------------------------- | ------------------------- | ------------- | ------------------------------------------------------------------------- |
| Trading transactions gain/(losses)     | `tradingTransactions`     | Trial Balance | Look for "FX Gain/Loss - Trading"                                         |
| Non-trading transactions gain/(losses) | `nonTradingTransactions`  | Trial Balance | Look for "FX Gain/Loss - Non-Trading"                                     |
| Translation of year end bank balance   | `translationBankBalance`  | Trial Balance | Look for "FX Translation - Bank" or calculate from bank account movements |
| Translation of year end trade balance  | `translationTradeBalance` | Trial Balance | Look for "FX Translation - Receivables/Payables"                          |

---

### Q9: Interest Income

| Question                                  | Field Name                             | Data Source   | Extraction Logic                           |
| ----------------------------------------- | -------------------------------------- | ------------- | ------------------------------------------ |
| Local bank deposits - Amount              | `localBankDeposits.amount`             | Trial Balance | Look for "Interest Income - Local Bank"    |
| Local bank deposits - Secured/guarantee   | `localBankDeposits.securedGuarantee`   | Manual        | Not extractable - default suggest "false"  |
| Overseas bank deposits - Amount           | `overseasBankDeposits.amount`          | Trial Balance | Look for "Interest Income - Overseas Bank" |
| Overseas bank deposits - Deposit location | `overseasBankDeposits.depositLocation` | Manual        | Not extractable - default suggest "false"  |
| Loan receivables                          | `loanReceivables`                      | Manual        | Not extractable - user must provide        |

---

### Q10: Interest Expenses on Bank Loans

| Question                                | Field Name                    | Data Source   | Extraction Logic                                                           |
| --------------------------------------- | ----------------------------- | ------------- | -------------------------------------------------------------------------- |
| Detailed breakdown of interest expenses | `interestExpensesOnBankLoans` | Trial Balance | Look for "Interest Expense - Bank Loan", "Finance Cost"; ask for breakdown |
| Recipient details, loan terms, purpose  | -                             | Manual        | Not extractable - user must provide                                        |

---

### Q11: Legal and Professional Fees

| Question                                   | Field Name                 | Data Source   | Extraction Logic                                                                                                       |
| ------------------------------------------ | -------------------------- | ------------- | ---------------------------------------------------------------------------------------------------------------------- |
| Breakdown with nature                      | `legalAndProfessionalFees` | Trial Balance | Look for "Legal Fees", "Professional Fees", "Audit Fees"; ask for breakdown                                            |
| Nature (Capital/Non-deductible/Deductible) | `nature`                   | AI Analysis   | Analyze description: "incorporation", "capital raising" = Capital; "tax appeal" = Non-deductible; "audit" = Deductible |

---

### Q12: Repair, Maintenance, or Sundry Expenses

| Question                                       | Field Name                          | Data Source   | Extraction Logic                                                       |
| ---------------------------------------------- | ----------------------------------- | ------------- | ---------------------------------------------------------------------- |
| Breakdown showing capital/non-deductible items | `repairMaintenanceOrSundryExpenses` | Trial Balance | Look for "Repairs & Maintenance", "Sundry Expenses"; ask for breakdown |
| Capital or non-deductible?                     | `isCapitalOrNonDeductible`          | AI Analysis   | Keywords: "renovation", "improvement" = Capital                        |

---

### Q13: Share-Based Payment

| Question                                 | Field Name | Data Source   | Extraction Logic                                                                         |
| ---------------------------------------- | ---------- | ------------- | ---------------------------------------------------------------------------------------- |
| Whether there is any share-based payment | `value`    | Trial Balance | Look for "Share-based Payment Expense", "Stock Option Expense" - if exists, suggest true |

---

### Q14: Donations

| Question                          | Field Name                       | Data Source         | Extraction Logic                                                          |
| --------------------------------- | -------------------------------- | ------------------- | ------------------------------------------------------------------------- |
| Full names of charity and amounts | `donations`                      | Trial Balance       | Look for "Donation Expense", "Charitable Contribution"; ask for breakdown |
| Approved charitable organization  | `approvedCharitableOrganization` | Manual/Charity List | Not extractable - user must verify                                        |

---

### Q15: Additions to Fixed Assets

| Question                             | Field Name               | Data Source      | Extraction Logic                                                        |
| ------------------------------------ | ------------------------ | ---------------- | ----------------------------------------------------------------------- |
| Breakdown of additions               | `additionsToFixedAssets` | Trial Balance    | Compare Fixed Asset accounts from opening to closing; extract additions |
| Category (Furniture, Computer, etc.) | `category`               | TB Account Names | Extract from account name                                               |
| Description                          | `description`            | TB Account Names | Extract from account name                                               |
| Amount                               | `amount`                 | Trial Balance    | Calculate: Closing balance - Opening balance + Disposals                |

---

### Q16: Disposal of Fixed Assets

| Question                                       | Field Name                                         | Data Source   | Extraction Logic                    |
| ---------------------------------------------- | -------------------------------------------------- | ------------- | ----------------------------------- |
| Breakdown of disposals                         | `disposalOfFixedAssets`                            | Trial Balance | Look for "Loss/Gain on Disposal"    |
| Category, Description                          | `category`, `description`                          | Manual        | Not extractable - user must provide |
| Cost, Depreciation Accumulated, Sales proceeds | `cost`, `depreciationAccumulated`, `salesProceeds` | Manual        | Not extractable - user must provide |

---

### Q17: Transactions with Closely Connected Non-Residents

| Question                                  | Field Name                                     | Data Source   | Extraction Logic                                                              |
| ----------------------------------------- | ---------------------------------------------- | ------------- | ----------------------------------------------------------------------------- |
| Name, Place, Nature, Relationship, Amount | `transactionsWithCloselyConnectedNonResidents` | Trial Balance | Look for intercompany accounts, related party transactions; ask for breakdown |

---

### Q18: Expenses Paid on Behalf of Related Entities

| Question                       | Field Name                                             | Data Source   | Extraction Logic                                                      |
| ------------------------------ | ------------------------------------------------------ | ------------- | --------------------------------------------------------------------- |
| List expenses without recharge | `expensesPaidOnBehalfOfRelatedEntitiesWithoutRecharge` | Trial Balance | Look for "Expenses on Behalf" without corresponding "Recharge Income" |

---

### Q19: Non-Deductible Foreign Withholding Tax Paid

| Question                     | Field Name                               | Data Source   | Extraction Logic                                  |
| ---------------------------- | ---------------------------------------- | ------------- | ------------------------------------------------- |
| List foreign withholding tax | `nonDeductibleForeignWithholdingTaxPaid` | Trial Balance | Look for "Foreign WHT", "Withholding Tax Expense" |

---

### Q20: Consultancy Fee

| Question              | Field Name       | Data Source   | Extraction Logic                                 |
| --------------------- | ---------------- | ------------- | ------------------------------------------------ |
| Breakdown with nature | `consultancyFee` | Trial Balance | Look for "Consultancy Fee", "Consulting Expense" |
| Name, ID, Address     | -                | Manual        | Not extractable - user must provide              |

---

### Q21: Dividend Income

| Question                           | Field Name       | Data Source   | Extraction Logic                    |
| ---------------------------------- | ---------------- | ------------- | ----------------------------------- |
| Breakdown with nature              | `dividendIncome` | Trial Balance | Look for "Dividend Income"          |
| Nature (Capital/Business/Offshore) | `incomeNature`   | Manual        | Not extractable - user must provide |

---

### Q22: Non-Taxable Capital Items

| Question                | Field Name               | Data Source   | Extraction Logic                         |
| ----------------------- | ------------------------ | ------------- | ---------------------------------------- |
| Other non-taxable items | `nonTaxableCapitalItems` | Trial Balance | Look for "Capital Gain", "Disposal Gain" |

---

### Q23: Commission Expense

| Question                        | Field Name          | Data Source   | Extraction Logic                                  |
| ------------------------------- | ------------------- | ------------- | ------------------------------------------------- |
| Breakdown of commission expense | `commissionExpense` | Trial Balance | Look for "Commission Expense", "Sales Commission" |
| Recipient details               | -                   | Manual        | Not extractable - user must provide               |

---

### Q24: Research and Development Expenditure

| Question                     | Field Name                          | Data Source   | Extraction Logic                                            |
| ---------------------------- | ----------------------------------- | ------------- | ----------------------------------------------------------- |
| Breakdown of R&D expenditure | `researchAndDevelopmentExpenditure` | Trial Balance | Look for "R&D Expense", "Research Cost", "Development Cost" |
| Deductible                   | `deductible`                        | AI Analysis   | Suggest true for qualifying R&D                             |

---

## PART B: COVERING LETTER QUESTIONNAIRE QUESTIONS

### Section: Basic Info

| Question                         | Field Name | Data Source          | Extraction Logic                   |
| -------------------------------- | ---------- | -------------------- | ---------------------------------- |
| Name of the tax service provider | `agent`    | Previous Return      | Check last year's covering letter  |
| Name of representative           | `agentRep` | Previous Return      | Check last year's covering letter  |
| Position of representative       | `position` | Previous Return      | Check last year's covering letter  |
| Financial year end date          | `endDate`  | Trial Balance Header | Extract from TB period end date    |
| Deadline for tax return          | `deadline` | AI Analysis          | Calculate based on period end date |

---

### Section: Client Opting (Filing Method)

| Question                                 | Field Name            | Data Source     | Extraction Logic                                            |
| ---------------------------------------- | --------------------- | --------------- | ----------------------------------------------------------- |
| Paper Filing or e-Filing?                | `opting`              | Previous Return | Check last year's method; suggest "Paper Filing" as default |
| Is there any S-Form completed?           | `hasSForm`            | Previous Return | Check if special forms were filed last year                 |
| Engaged as service provider to sign?     | `eFilingDetails`      | Manual          | Not extractable - default suggest true if using e-filing    |
| Opt for voluntary e-filing of iXBRL?     | `opt`                 | Manual          | Suggest true if using e-filing                              |
| Is financial statements prepared by you? | `financialStatements` | Manual          | Suggest true                                                |
| Is Company part of in-scope MNE group?   | `mneGroup`            | Previous Return | Check last year's answer                                    |

---

### Section: Two-Tiered Profits Tax Rates

| Question                               | Field Name          | Data Source     | Extraction Logic                     |
| -------------------------------------- | ------------------- | --------------- | ------------------------------------ |
| Does concessionary regime apply?       | `twoTieredRates`    | Previous Return | Check if company qualified last year |
| Electing for Two-Tiered Rates?         | `electing`          | Previous Return | Default suggest true if eligible     |
| Part of connected entities?            | `connectedEntities` | Previous Return | Check group structure from last year |
| Member of HK listed group?             | `listedGroup`       | Previous Return | Check last year's answer             |
| S1 form completed by you?              | `completedByYou`    | Manual          | Suggest true                         |
| Only entity in group with HK business? | `tradeOrBusiness`   | Previous Return | Check group structure                |

---

### Section: Companies Ordinance

| Question            | Field Name           | Data Source   | Extraction Logic                              |
| ------------------- | -------------------- | ------------- | --------------------------------------------- |
| Considered dormant? | `companiesOrdinance` | Trial Balance | If TB shows minimal/no activity, suggest true |

---

### Section: HK Branch

| Question                 | Field Name           | Data Source     | Extraction Logic                        |
| ------------------------ | -------------------- | --------------- | --------------------------------------- |
| Is company a HK branch?  | `hkBranch`           | Previous Return | Check if Branch No. is greater than 000 |
| Foreign company name     | `foreignCompanyName` | Manual          | Not extractable - user must provide     |
| Incorporated country     | `foreignCountry`     | Manual          | Not extractable - user must provide     |
| AOA adjustment provided? | `aoaAdjustment`      | Tax Computation | Check if AOA included in last year TC   |
| Reason AOA not provided  | `aoaReason`          | Manual          | Not extractable - user must provide     |

---

### Section: Agency Permanent Establishment

| Question                       | Field Name                     | Data Source     | Extraction Logic                    |
| ------------------------------ | ------------------------------ | --------------- | ----------------------------------- |
| Is company an agency PE?       | `agencyPermanentEstablishment` | Previous Return | Check last year's status            |
| Activities in HK               | `activity`                     | Manual          | Not extractable - user must provide |
| Name of non-resident principal | `principal`                    | Manual          | Not extractable - user must provide |
| AOA adjustment provided?       | `aoaAdjustmentProvided`        | Tax Computation | Check last year TC                  |
| Reason not provided            | `aoaReason`                    | Manual          | Not extractable - user must provide |
| Engage to work out AOA?        | `companyEngage`                | Manual          | Not extractable - user must provide |

---

### Section: Amalgamation

| Question                            | Field Name                                   | Data Source     | Extraction Logic                                           |
| ----------------------------------- | -------------------------------------------- | --------------- | ---------------------------------------------------------- |
| Undergo amalgamation this year?     | `undergoAmalgamation`                        | Trial Balance   | Check for major structural changes; not easily extractable |
| Date of amalgamation                | `amalgamationDate`                           | Manual          | Not extractable - user must provide                        |
| Amalgamated or Amalgamating?        | `amalgamationType`                           | Manual          | Not extractable - user must provide                        |
| Name of other company               | `amalgamatingCompany` / `amalgamatedCompany` | Manual          | Not extractable - user must provide                        |
| Utilized tax losses?                | `utilizedTaxLosses`                          | Tax Computation | Check if losses carried forward                            |
| Continued same trade?               | `sameTradeBusinessNotUtilizedTaxLoss`        | Manual          | Not extractable - user must provide                        |
| Same trade test satisfied?          | `sameTradeTest`                              | Manual          | Not extractable - user must provide                        |
| Cease business before amalgamation? | `ceaseBusiness`                              | Manual          | Not extractable - user must provide                        |

---

### Section: Offshore Claim

**This is a complex section with multiple sub-questions:**

| Question                                 | Field Name                                                                     | Data Source        | Extraction Logic                               |
| ---------------------------------------- | ------------------------------------------------------------------------------ | ------------------ | ---------------------------------------------- |
| Belonging to MNE group?                  | `belongingToMNE`                                                               | Previous Return    | Check last year's answer                       |
| Has offshore claim?                      | `hasOffshoreClaim`                                                             | Last Year TC       | Check if offshore exemption claimed            |
| IR1478 completed by agent?               | `ir1478CompletedByAgent`                                                       | Previous Return    | Check if form filed                            |
| Offshore sourcing commission income?     | `offshoreSourcingCommission`                                                   | Trial Balance      | Check for offshore commission accounts         |
| Offshore equity interest disposal gains? | `offshoreEquityInterest`                                                       | Trial Balance      | Look for "Gain on Disposal - Offshore"         |
| Offshore IP income?                      | `offshoreIPIncome`                                                             | Trial Balance      | Look for "IP Income - Offshore"                |
| Offshore disposal gain?                  | `offshoreDisposalGain`                                                         | Trial Balance      | Look for offshore disposal accounts            |
| Offshore disposal asset description      | `offshoreDisposalAsset`                                                        | Manual             | Not extractable - user must provide            |
| Pursue 50:50 apportionment?              | `pursueApportionment`                                                          | Last Year TC       | Check if the is 50% offshore claim             |
| Offshore brokerage commission?           | `brokerageCommission`                                                          | Trial Balance      | Look for "Brokerage Commission - Offshore"     |
| Re-invoicing centre?                     | `reinvoicingCentre`                                                            | Manual             | Not extractable - user must provide            |
| Agreement date with IRD                  | `agreementDate`                                                                | Manual             | Not extractable - user must provide            |
| Profits nature (trading/services)        | `profitNature`                                                                 | Trial Balance      | "trading profits" if "Cost of Good Sold" found |
| Reason for classification                | `reinvoicingReasonTrade` / `reinvoicingReasonServices`                         | Manual             | Not extractable - user must provide            |
| Electing CTC rate?                       | `electingCTC`                                                                  | Previous Return    | Check if CTC regime applied, section 9.8       |
| First time?                              | `firstTime`                                                                    | Previous Return    | Check if CTC claimed before, S8 form           |
| Treasury centre profit                   | `treasuryCentreProfit`                                                         | Manual             | Not extractable - user must provide            |
| Treasury activities                      | `treasuryActivities`                                                           | Manual             | Not extractable - user must provide            |
| Other activities meet thresholds?        | `otherActivitiesFirstTime` / `otherActivitiesNotFirstTime`                     | Manual             | Not extractable - user must provide            |
| S Form 8 filled by you?                  | `sform8Agent`                                                                  | Manual             | Suggest true                                   |
| Electing for patent box?                 | `electingPatentBox`                                                            | Previous Return    | Check if patent box claimed                    |
| Internet offshore claim?                 | `internetOffshore`                                                             | Previous Return    | Check if internet business                     |
| Internet offshore basis                  | `internetOffshoreBasis`                                                        | Manual             | Not extractable - user must provide            |
| Has IP income?                           | `hasIPIncome`                                                                  | Trial Balance      | Look for IP-related income accounts            |
| Obtained Commissioner's Opinion?         | `obtainedOpinion`                                                              | Previous Return    | Check if ruling obtained, section 3.5          |
| Year obtained                            | `yearObtained`                                                                 | Manual             | Not extractable - user must provide            |
| Meet exception requirements?             | `meetExceptionRequirements`                                                    | Manual             | Not extractable - user must provide            |
| Which exception?                         | `exceptionRequirement`                                                         | Manual             | Not extractable - user must provide            |
| Disallowable expenses?                   | `disallowableExpenses`                                                         | Manual             | Not extractable - user must provide            |
| Foreign income received in HK?           | `foreignIncomeReceivedInHKException` / `foreignIncomeReceivedInHKNotException` | Manual             | Not extractable - user must provide            |
| Onshore gains on equity disposal?        | `hasOnshoreGains`                                                              | Trial Balance      | Look for "Gain on Disposal - Equity Interest"  |
| Equity interest                          | `equityInterest`                                                               | Manual             | Not extractable - user must provide            |
| Gains amount                             | `onshoreGains`                                                                 | Trial Balance      | Extract from disposal accounts                 |
| Gains nature (capital/revenue)           | `gainsNature`                                                                  | Manual/AI Analysis | Not extractable - user must provide            |
| Elected enhancement scheme?              | `electedEnhancementScheme`                                                     | Previous Return    | Check if scheme used, section 9.21             |
| Reason not elected                       | `notElectReason`                                                               | Manual             | Not extractable - user must provide            |
| Lodged capital claim?                    | `lodgedCapitalClaim`                                                           | Previous Return    | Check if claim filed. seciont 10.4             |

---

### Section: BEPS 2.0 Pillar Two

| Question                                    | Field Name            | Data Source     | Extraction Logic                    |
| ------------------------------------------- | --------------------- | --------------- | ----------------------------------- |
| MNE in scope for Pillar Two?                | `isMNEInScope`        | Previous Return | Check if group revenue > EUR 750M   |
| Has offshore claim/concessionary treatment? | `hasOffshoreClaim`    | Previous Return | Check offshore claim section 10.1   |
| Headquarter overseas?                       | `headquarterOverseas` | Manual          | Not extractable - user must provide |

---

### Section: Profit Margin Fluctuation

| Question                                      | Field Name                | Data Source                | Extraction Logic                                                         |
| --------------------------------------------- | ------------------------- | -------------------------- | ------------------------------------------------------------------------ |
| Gross profit margin fluctuated significantly? | `significantFluctuation`  | Trial Balance vs Last Year | Calculate: (Current GP% - Prior GP%) / Prior GP%; if > 10%, suggest true |
| Previous year GP margin                       | `fromPercentage`          | Last Year TC               | Calculate: Gross Profit / Revenue from last year                         |
| Continuous losses?                            | `continuousLosses`        | Last Year TC               | Check if losses in past 2-3 years                                        |
| P&L items changed significantly?              | `panlItemsChanges`        | Trial Balance vs Last Year | Compare each line item; if variance > 20%, suggest true                  |
| What items?                                   | `panlItemsChangesDetails` | Trial Balance vs Last Year | List items with >20% variance                                            |

---

### Section: Section 20AC - Offshore Fund Exemption

| Question                                              | Field Name                 | Data Source   | Extraction Logic                    |
| ----------------------------------------------------- | -------------------------- | ------------- | ----------------------------------- |
| Hold beneficial interest in Section 20AC exempt fund? | `holdBeneficialInterest`   | Trial Balance | Look for "Investment in Funds"      |
| Hold investment through SPV?                          | `holdInvestmentThroughSPV` | Manual        | Not extractable - user must provide |
| Meets 30% threshold?                                  | `meetsThreshold`           | Manual        | Not extractable - user must provide |
| Bona fide widely held?                                | `bonaFideWidelyHeld`       | Manual        | Not extractable - user must provide |

---

### Section: Section 20AN - Unified Fund Exemption

| Question                                   | Field Name                   | Data Source   | Extraction Logic                    |
| ------------------------------------------ | ---------------------------- | ------------- | ----------------------------------- |
| Hold interest in Section 20AN exempt fund? | `holdInvestmentFundInterest` | Trial Balance | Look for "Investment in Funds"      |
| Hold investment through SPE?               | `holdInvestmentThroughSPE`   | Manual        | Not extractable - user must provide |
| Meets fund threshold?                      | `meetsFundThreshold`         | Manual        | Not extractable - user must provide |
| Fund bona fide widely held?                | `fundBonaFideWidelyHeld`     | Manual        | Not extractable - user must provide |

---

### Section: FIHV/FSPE

| Question                    | Field Name             | Data Source   | Extraction Logic                    |
| --------------------------- | ---------------------- | ------------- | ----------------------------------- |
| Hold interest in FIHV/FSPE? | `holdFIHVFSPEInterest` | Trial Balance | Look for "Investment - FIHV/FSPE"   |
| FIHV only or both?          | `investmentType`       | Manual        | Not extractable - user must provide |
| Meets FIHV threshold?       | `meetsFIHVThreshold`   | Manual        | Not extractable - user must provide |
| Is ESF Office?              | `isESFOffice`          | Manual        | Not extractable - user must provide |

---

### Section: Carried Interest

| Question                   | Field Name                | Data Source   | Extraction Logic                   |
| -------------------------- | ------------------------- | ------------- | ---------------------------------- |
| Received carried interest? | `receivedCarriedInterest` | Trial Balance | Look for "Carried Interest Income" |
| Amount                     | `carriedInterestAmount`   | Trial Balance | Extract amount from account        |

---

### Section: Notional Interest

| Question                                         | Field Name                | Data Source   | Extraction Logic                                                         |
| ------------------------------------------------ | ------------------------- | ------------- | ------------------------------------------------------------------------ |
| Provide interest-free loan to connected parties? | `provideInterestFreeLoan` | Trial Balance | Look for "Loan to Related Parties" with no corresponding interest income |
| Which parties?                                   | `loanConnectedParty`      | Manual        | Not extractable - user must provide                                      |
| Notional interest amount                         | `notionalInterestAmount`  | Trial Balance | Look for "Notional Interest Income"                                      |

---

### Section: Qualifying Debt Instruments (QDI)

| Question                         | Field Name            | Data Source   | Extraction Logic                    |
| -------------------------------- | --------------------- | ------------- | ----------------------------------- |
| Derive QDI income?               | `deriveQDIIncome`     | Trial Balance | Look for "Interest Income - QDI"    |
| Amount                           | `qdiInterestAmount`   | Trial Balance | Extract from account                |
| Associate of issuer?             | `isAssociateOfIssuer` | Manual        | Not extractable - user must provide |
| QDIs issued before 1 April 2018? | `qdiIssueDate`        | Manual        | Not extractable - user must provide |
| Short/medium/long term?          | `qdiTerm`             | Manual        | Not extractable - user must provide |

---

### Section: MLPG Debt Instruments

| Question             | Field Name         | Data Source   | Extraction Logic                           |
| -------------------- | ------------------ | ------------- | ------------------------------------------ |
| Derive MLPG income?  | `deriveMLPGIncome` | Trial Balance | Look for "Interest Income - MLPG"          |
| Amount               | `mlpgAmount`       | Trial Balance | Extract from account                       |
| Interest or profits? | `mlpgType`         | Trial Balance | Check if from "Interest" or "Gain on Sale" |

---

### Section: Unrealized Revaluation Gains - Capital Nature

| Question                           | Field Name                                | Data Source   | Extraction Logic                                             |
| ---------------------------------- | ----------------------------------------- | ------------- | ------------------------------------------------------------ |
| Record unrealized gains (capital)? | `hasUnrealizedGainsCapital`               | Trial Balance | Look for "Unrealized Revaluation Gain - Investment Property" |
| Amount                             | `unrealizedRevaluationGainsAmountCapital` | Trial Balance | Extract from account                                         |
| Capital asset description          | `unrealizedRevaluationGainsAssetCapital`  | TB Notes      | Extract from account name                                    |

---

### Section: Unrealized Revaluation Gains - Trading Securities

| Question                           | Field Name                                | Data Source   | Extraction Logic                                |
| ---------------------------------- | ----------------------------------------- | ------------- | ----------------------------------------------- |
| Record unrealized gains (trading)? | `hasUnrealizedGainsTrading`               | Trial Balance | Look for "Unrealized Gain - Trading Securities" |
| Amount                             | `unrealizedRevaluationGainsAmountTrading` | Trial Balance | Extract from account                            |
| Trading securities description     | `unrealizedRevaluationGainsAssetTrading`  | Manual        | Not extractable - user must provide             |
| Elect fair value accounting?       | `electFairValue`                          | Manual        | Not extractable - user must provide             |

---

### Section: Unrealized Revaluation Losses - Trading Securities

| Question                            | Field Name                               | Data Source   | Extraction Logic                                |
| ----------------------------------- | ---------------------------------------- | ------------- | ----------------------------------------------- |
| Record unrealized losses (trading)? | `hasUnrealizedLossesTrading`             | Trial Balance | Look for "Unrealized Loss - Trading Securities" |
| Amount                              | `unrealizedRevaluationLossAmountTrading` | Trial Balance | Extract from account (negative)                 |
| Trading securities description      | `unrealizedRevaluationLossAssetTrading`  | Manual        | Not extractable - user must provide             |
| Elect fair value accounting?        | `electFairValueLosses`                   | Manual        | Not extractable - user must provide             |

---

### Section: Unrealized Exchange Gains/Losses - Capital Nature

| Question                                  | Field Name                        | Data Source   | Extraction Logic                               |
| ----------------------------------------- | --------------------------------- | ------------- | ---------------------------------------------- |
| Record unrealized exchange G/L (capital)? | `hasUnrealizedExchangeCapital`    | Trial Balance | Look for "FX Unrealized - Capital Assets"      |
| Amount                                    | `unrealizedExchangeAmountCapital` | Trial Balance | Extract from account                           |
| Arose from?                               | `unrealizedExchangeFromCapital`   | Manual        | Not extractable - user must provide            |
| Gains or losses?                          | `exchangeGainOrLossCapital`       | Trial Balance | Check if positive (gains) or negative (losses) |

---

### Section: Unrealized Exchange Gains/Losses - Revenue Nature

| Question                                  | Field Name                        | Data Source   | Extraction Logic                               |
| ----------------------------------------- | --------------------------------- | ------------- | ---------------------------------------------- |
| Record unrealized exchange G/L (revenue)? | `hasUnrealizedExchangeRevenue`    | Trial Balance | Look for "FX Unrealized - Operating"           |
| Amount                                    | `unrealizedExchangeAmountRevenue` | Trial Balance | Extract from account                           |
| Arose from?                               | `unrealizedExchangeFromRevenue`   | Manual        | Not extractable - user must provide            |
| Related to financial instruments?         | `relatedToFinancialInstruments`   | Manual        | Not extractable - user must provide            |
| Elect fair value accounting?              | `electFairValueExchange`          | Manual        | Not extractable - user must provide            |
| Gains or losses?                          | `exchangeGainOrLossRevenue`       | Trial Balance | Check if positive (gains) or negative (losses) |

---

### Section: Research and Development Expenditure

| Question               | Field Name           | Data Source   | Extraction Logic                                 |
| ---------------------- | -------------------- | ------------- | ------------------------------------------------ |
| Incur R&D expenditure? | `incurRND`           | Trial Balance | Look for "R&D Expense", "Research Cost" accounts |
| Amount                 | `rndAmount`          | Trial Balance | Sum R&D expense accounts                         |
| R&D activities         | `rndActivities`      | Manual        | Not extractable - user must provide              |
| S3 completed by agent? | `s3CompletedByAgent` | Manual        | Suggest true                                     |

---

### Section: Leases (HKFRS 16)

| Question                                   | Field Name                   | Data Source   | Extraction Logic                                                  |
| ------------------------------------------ | ---------------------------- | ------------- | ----------------------------------------------------------------- |
| Incur amortisation & interest on leases?   | `incurLeaseExpense`          | Trial Balance | Look for "Amortisation - ROU Asset", "Interest - Lease Liability" |
| Amortisation amount                        | `amortisation`               | Trial Balance | Extract from amortisation account                                 |
| Interest expense                           | `leaseInterest`              | Trial Balance | Extract from lease interest account                               |
| Claiming deduction on contractual payment? | `claimingContractualPayment` | Manual        | Not extractable - user must provide                               |
| Claiming deduction on HKFRS 16 basis?      | `claimingHKFRS16`            | Manual        | Not extractable - user must provide                               |
| Recorded impairment loss?                  | `impairmentLoss`             | Trial Balance | Look for "Impairment - ROU Asset"                                 |
| Recorded at end or middle of year?         | `impairmentRecordTiming`     | Manual        | Not extractable - suggest "end" as default                        |

---

### Section: Obsolete Stock Provision

| Question                           | Field Name                   | Data Source   | Extraction Logic                            |
| ---------------------------------- | ---------------------------- | ------------- | ------------------------------------------- |
| Make provision for obsolete stock? | `makeObsoleteStockProvision` | Trial Balance | Look for "Provision for Obsolete Inventory" |
| Amount                             | `provisionObsoleteStock`     | Trial Balance | Extract from provision account              |

---

### Section: Provision for Outstanding Debts

| Question                     | Field Name              | Data Source   | Extraction Logic                        |
| ---------------------------- | ----------------------- | ------------- | --------------------------------------- |
| Make provision for debts?    | `makeDebtProvision`     | Trial Balance | Look for "Provision for Doubtful Debts" |
| Elect fair value accounting? | `electFairValueDebt`    | Manual        | Not extractable - user must provide     |
| Any specific provision?      | `hasSpecificProvision`  | Trial Balance | Check for "Specific Provision" account  |
| Specific provision amount    | `specificProvisionDebt` | Trial Balance | Extract from account                    |
| Due from whom?               | `dueFrom`               | Manual        | Not extractable - user must provide     |
| Any general provision?       | `hasGeneralProvision`   | Trial Balance | Check for "General Provision" account   |
| General provision amount     | `generalProvisionDebt`  | Trial Balance | Extract from account                    |

---

### Section: Impairment Loss - HKFRS 9

| Question                            | Field Name                  | Data Source   | Extraction Logic                    |
| ----------------------------------- | --------------------------- | ------------- | ----------------------------------- |
| Recognize impairment under HKFRS 9? | `recognizeImpairmentHKFRS9` | Trial Balance | Look for "Impairment Loss - Debt"   |
| Elect fair value accounting?        | `electFairValueHKFRS9`      | Manual        | Not extractable - user must provide |
| Amount                              | `impairmentAmount`          | Trial Balance | Extract from impairment account     |
| Is it deductible?                   | `isDeductible`              | AI Analysis   | Analyze based on tax rules          |

---

### Section: Reinstatement Cost

| Question                                | Field Name              | Data Source   | Extraction Logic                       |
| --------------------------------------- | ----------------------- | ------------- | -------------------------------------- |
| Pursue deduction on reinstatement cost? | `pursueReinstatement`   | Trial Balance | Look for "Provision for Reinstatement" |
| Amount                                  | `reinstatementAmount`   | Trial Balance | Extract from account                   |
| Related to?                             | `reinstatementProperty` | Manual        | Not extractable - user must provide    |

---

### Section: Sundry Cost

| Question                         | Field Name     | Data Source   | Extraction Logic                                          |
| -------------------------------- | -------------- | ------------- | --------------------------------------------------------- |
| Pursue deduction on sundry cost? | `pursueSundry` | Trial Balance | Check if sundry expenses exist; suggest based on presence |

---

### Section: Apportionment of Expenses - Rule 2C

| Question                           | Field Name           | Data Source     | Extraction Logic                     |
| ---------------------------------- | -------------------- | --------------- | ------------------------------------ |
| Incur apportionment under Rule 2C? | `incurApportionment` | Previous Return | Check if investment portfolio exists |
| Agree to disallow portion?         | `agreeDisallow`      | Previous Return | Check last year's approach           |
| Basis used?                        | `basisUsed`          | Previous Return | Extract from last year's computation |

---

### Section: Deductible Interest Expense

| Question                               | Field Name                       | Data Source     | Extraction Logic                                               |
| -------------------------------------- | -------------------------------- | --------------- | -------------------------------------------------------------- |
| Incur deductible interest expense?     | `incurInterestExpense`           | Trial Balance   | Look for "Interest Expense" accounts                           |
| Dividends paid during year?            | `dividendsPaid`                  | Trial Balance   | Look for "Dividends Paid" account                              |
| Dividend amount                        | `dividend`                       | Trial Balance   | Extract from dividends account                                 |
| Interest expense amount                | `interestExpense`                | Trial Balance   | Sum all interest expense accounts                              |
| Loan secured or guaranteed?            | `loanSecuredOrGuaranteed`        | Manual          | Not extractable                                                |
| Arrangement for interest to flow back? | `arrangementFlowBack`            | Manual          | Not extractable                                                |
| Name of lender                         | `lender`                         | Manual          | Not extractable                                                |
| Lender is person?                      | `lenderType`                     | Manual          | Not extractable                                                |
| Financial institution?                 | `isFinancialInstitution`         | Manual          | Not extractable                                                |
| Lender is associate?                   | `lenderIsAssociate`              | Manual          | Not extractable                                                |
| Associate is HK corporation?           | `associateIsHKCorporation`       | Manual          | Not extractable                                                |
| Foreign country                        | `foreignCountry`                 | Manual          | Not extractable                                                |
| Following facts true (intra-group)?    | `followingFactsTrue`             | Manual          | Not extractable                                                |
| Lender is treasury centre?             | `lenderIsTreasuryCentre`         | Previous Return | Checked if S8 is completed                                     |
| Has non-taxable assets?                | `hasNonTaxableAssets`            | Trial Balance   | Check for investment assets that don't generate taxable income |
| Borrowed for plant/machinery/stock?    | `borrowedForPlantMachineryStock` | Manual          | Not extractable                                                |
| Plant or machinery or stock?           | `plantOrMachineryType`           | Manual          | Not extractable                                                |

---

### Section: Interest-Free Loan - HKFRS 9

| Question                                          | Field Name                | Data Source   | Extraction Logic                     |
| ------------------------------------------------- | ------------------------- | ------------- | ------------------------------------ |
| Obtain interest-free loan with notional interest? | `obtainInterestFreeLoan`  | Trial Balance | Look for "Notional Interest Expense" |
| Relationship with lender                          | `relatedParty`            | Manual        | Not extractable                      |
| Notional interest amount                          | `notionalInterestExpense` | Trial Balance | Extract from account                 |

---

### Section: Share-Based Payments

| Question                                   | Field Name                           | Data Source   | Extraction Logic                                               |
| ------------------------------------------ | ------------------------------------ | ------------- | -------------------------------------------------------------- |
| Make employee share-based payments?        | `makeShareBasedPayment`              | Trial Balance | Look for "Share-based Payment Expense", "Stock Option Expense" |
| Stock option or award?                     | `stockOptionOrAward`                 | Manual        | Not easily extractable - default suggest "stock option"        |
| Amount                                     | `sharePayment`                       | Trial Balance | Extract from expense account                                   |
| Grant stock options/awards for own shares? | `grantStockOptionAward`              | Manual        | Not extractable                                                |
| Employee received parent stock options?    | `receivedParentStockOptionAward`     | Manual        | Not extractable                                                |
| Recharged from parent?                     | `rechargedFromParent`                | Trial Balance | Look for "Recharge - Share-based Payment"                      |
| Granted parent share options/awards?       | `grantedParentShareOptionAward`      | Manual        | Not extractable                                                |
| Phantom stock options?                     | `purchaseOrPhantomType`              | Manual        | Not extractable                                                |
| Purchase shares or cash?                   | `purchaseSharesOrCash`               | Manual        | Not extractable                                                |
| Fully vested?                              | `fullyVested`                        | Manual        | Not extractable                                                |
| Received subsidiary stock options?         | `receivedSubsidiaryStockOptionAward` | Manual        | Not extractable                                                |
| Subsidiary name                            | `subSid`                             | Manual        | Not extractable                                                |
| Recharge amount                            | `recharge`                           | Trial Balance | Look for recharge amounts                                      |

---

### Section: Eligible Group Stock Benefits

| Question                                     | Field Name                   | Data Source | Extraction Logic |
| -------------------------------------------- | ---------------------------- | ----------- | ---------------- |
| Employees eligible for group stock benefits? | `eligibleGroupStockBenefits` | Manual      | Not extractable  |

---

### Section: Is HK Listed Company

| Question              | Field Name          | Data Source          | Extraction Logic                        |
| --------------------- | ------------------- | -------------------- | --------------------------------------- |
| Is HK listed company? | `isHKListedCompany` | Company Registration | Not extractable from TB - default false |

---

### Section: Intellectual Property Payments

| Question                | Field Name           | Data Source      | Extraction Logic                                                |
| ----------------------- | -------------------- | ---------------- | --------------------------------------------------------------- |
| Pay IP to non-resident? | `payIPToNonResident` | Trial Balance    | Look for "Royalty Expense - Overseas", "License Fee - Overseas" |
| IP payments paid to?    | `ipPaymentTo`        | Manual/TB Ledger | Extract payee from ledger                                       |
| Associated?             | `isAssociated`       | Manual           | Not extractable                                                 |
| Carry business in HK?   | `carryBusinessInHK`  | Manual           | Not extractable                                                 |
| IP description          | `ip`                 | Manual/TB Notes  | Extract from expense description                                |
| IP in Hong Kong?        | `ipInHongKong`       | Manual           | Not extractable                                                 |

---

### Section: Local Individual Payment

| Question                                         | Field Name                   | Data Source      | Extraction Logic                         |
| ------------------------------------------------ | ---------------------------- | ---------------- | ---------------------------------------- |
| Make payment to local individual (non-employee)? | `makeLocalIndividualPayment` | Trial Balance    | Look for "Consultancy", "Freelance Fees" |
| Individual acted as?                             | `individualActAs`            | Manual/TB Ledger | Extract from expense description         |

---

### Section: Non-Resident Entertainer Payment

| Question                                   | Field Name                          | Data Source   | Extraction Logic                                |
| ------------------------------------------ | ----------------------------------- | ------------- | ----------------------------------------------- |
| Make payment to non-resident entertainers? | `makeNonResidentEntertainerPayment` | Trial Balance | Look for "Entertainment Expense - Non-resident" |

---

### Section: Non-Resident Professional Services Payment

| Question                                         | Field Name                           | Data Source   | Extraction Logic                            |
| ------------------------------------------------ | ------------------------------------ | ------------- | ------------------------------------------- |
| Make payment to non-resident for services in HK? | `makeNonResidentProfessionalPayment` | Trial Balance | Look for "Professional Fees - Non-resident" |

---

### Section: HKAS 38 - Royalties and License Fees

| Question                                         | Field Name                | Data Source     | Extraction Logic                                                    |
| ------------------------------------------------ | ------------------------- | --------------- | ------------------------------------------------------------------- |
| Recognize royalties/license as intangible asset? | `recognizeHKAS38`         | Trial Balance   | Look for "Intangible Assets - License", "Amortisation - Intangible" |
| IP description                                   | `hkas38Ip`                | Manual/TB Notes | Extract from asset description                                      |
| Fees paid to?                                    | `hkas38IpTo`              | Manual          | Not extractable                                                     |
| Amount                                           | `hkas38IpFee`             | Trial Balance   | Extract from amortisation + remaining balance                       |
| Capitalized upfront payment?                     | `capitalizedUpfront`      | Trial Balance   | Check if intangible asset has upfront component                     |
| Upfront amount                                   | `capitalizeAmountUpfront` | Trial Balance   | Extract from intangible asset opening balance                       |
| Capitalized advance royalties?                   | `capitalizedAdvance`      | Trial Balance   | Check if prepayment account exists                                  |
| Advance amount                                   | `capitalizeAmountAdvance` | Trial Balance   | Extract from prepayment account                                     |

---

### Section: Patent/Know-How Rights

| Question                         | Field Name             | Data Source   | Extraction Logic                                       |
| -------------------------------- | ---------------------- | ------------- | ------------------------------------------------------ |
| Purchase patent/know-how rights? | `purchasePatentRights` | Trial Balance | Look for "Intangible Assets - Patent", "Patent Rights" |
| Patent right description         | `patentRight`          | Manual        | Not extractable                                        |
| Cost                             | `patentRightCost`      | Trial Balance | Extract from asset account                             |

---

### Section: Specified Intellectual Property Rights

| Question                      | Field Name                | Data Source   | Extraction Logic                                            |
| ----------------------------- | ------------------------- | ------------- | ----------------------------------------------------------- |
| Purchase specified IP rights? | `purchaseSpecifiedRights` | Trial Balance | Look for "Intangible Assets - Copyright", "Trademark", etc. |
| Specified right description   | `specifiedRight`          | Manual        | Not extractable                                             |
| Cost                          | `specifiedRightCost`      | Trial Balance | Extract from asset account                                  |

---

### Section: Non-Capitalized Fixed Assets

| Question                          | Field Name                | Data Source     | Extraction Logic                              |
| --------------------------------- | ------------------------- | --------------- | --------------------------------------------- |
| Any non-capitalized fixed assets? | `hasNonCapitalFixedAsset` | Trial Balance   | Look for "Fixed Asset Expense < $X threshold" |
| Claim expenditure?                | `claimExpenditure`        | Last Year TC    | Check last year's approach                    |
| Included in 20% pool?             | `includedIn20Pool`        | Last Year TC    | Check depreciation schedule                   |

---

### Section: Plant and Machinery - Contract Processing

| Question                              | Field Name                       | Data Source | Extraction Logic        |
| ------------------------------------- | -------------------------------- | ----------- | ----------------------- |
| Purchase P&M for contract processing? | `purchasePlantMachineryContract` | Manual      | Not extractable         |
| Factory location                      | `factoryLocation`                | Manual      | Not extractable         |

---

### Section: Plant and Machinery - Import Processing

| Question                            | Field Name                     | Data Source     | Extraction Logic         |
| ----------------------------------- | ------------------------------ | --------------- | ------------------------ |
| Purchase P&M for import processing? | `purchasePlantMachineryImport` | Manual          | Not extractable          |
| Factory location                    | `factoryLocationImport`        | Manual          | Not extractable          |
| Request depreciation allowance?     | `requestDepreciationAllowance` | Last Year TC    | Check if claimed before  |
| Claim made in prior years?          | `claimMadeInPriorYearsDep`     | Last Year TC    | Check prior year returns |
| Request section 16G deduction?      | `requestSection16G`            | Last Year TC    | Check if claimed before  |
| Claim made in prior years?          | `claimMadeInPriorYears16G`     | Last Year TC    | Check prior year returns |

---

### Section: Environmental Protection Expenditure

| Question                         | Field Name                      | Data Source     | Extraction Logic                                       |
| -------------------------------- | ------------------------------- | --------------- | ------------------------------------------------------ |
| Incur environmental expenditure? | `incurEnvironmentalExpenditure` | Trial Balance   | Look for "Fixed Assets - Environmental", "EV Purchase" |
| Amount                           | `environmentalExp`              | Trial Balance   | Extract from asset account                             |
| Asset description                | `environmentalAsset`            | Manual          | Not extractable                                        |
| Qualified as?                    | `expenditureQualifiedAs`        | Manual          | Match to categories (machinery/installation/vehicle)   |
| S4 filled by agent?              | `s4FilledByAgent`               | Manual          | Suggest true                                           |

---

### Section: Non-Resident Associated Persons

| Question                                           | Field Name                    | Data Source     | Extraction Logic                            |
| -------------------------------------------------- | ----------------------------- | --------------- | ------------------------------------------- |
| Transactions with non-resident associated persons? | `transactionsWithNonResident` | Trial Balance   | Look for "Intercompany - Overseas" accounts |
| Exempted from master/local file?                   | `exemptedFromMasterLocalFile` | Previous Return | Check if exemption applied last year        |

---

### Section: Country-by-Country Report (CbCR)

| Question                            | Field Name                | Data Source     | Extraction Logic                  |
| ----------------------------------- | ------------------------- | --------------- | --------------------------------- |
| Belong to MNE group requiring CbCR? | `belongToMNEGroup`        | Previous Return | Check if group revenue > EUR 750M |
| Made CbCR notification?             | `madeCbCRNotice`          | Previous Return | Check if BIR81 has been filed     |
| Other HK entity made notice?        | `otherHKEntityMadeNotice` | Manual          | Not extractable                   |
| Name of HK company                  | `otherHkEntity`           | Manual          | Not extractable                   |

---

### Section: Long Outstanding Account Receivable

| Question                                | Field Name             | Data Source      | Extraction Logic                                  |
| --------------------------------------- | ---------------------- | ---------------- | ------------------------------------------------- |
| Long outstanding AR from related party? | `hasLongOutstandingAR` | Trial Balance    | Look for "AR - Related Party"                     |
| Amount                                  | `arAmount`             | Trial Balance    | Extract AR balance                                |
| Related party name                      | `relatedPartyAR`       | Manual           | Not extractable                                   |

---

### Section: Double Taxation Agreement

| Question                        | Field Name                 | Data Source      | Extraction Logic                              |
| ------------------------------- | -------------------------- | ---------------- | --------------------------------------------- |
| Entitled to foreign tax credit? | `entitledForeignTaxCredit` | Trial Balance    | Look for "Foreign Tax Paid" accounts          |
| Tax paid in DTA territories?    | `taxPaidInDTA`             | Manual           | Not extractable                               |
| Foreign country                 | `foreignCountryDTA`        | Manual           | Not extractable                               |
| Foreign tax type (CIT/WHT)?     | `foreignTaxType`           | Trial Balance    | Check if "Corporate Tax" or "Withholding Tax" |
| WHT income nature               | `whtIncome`                | Manual           | Not extractable                               |
| Income not in section 16(1)(c)? | `incomeNotInSection16c`    | Manual           | Not extractable                               |
| Agree not to claim deduction?   | `agreeNotClaimDeduction`   | Previous TC      | Check last year's approach                    |
| Foreign tax voluntary?          | `foreignTaxVoluntary`      | Manual           | Not extractable                               |
| Company pay WHT?                | `companyPayWHT`            | Trial Balance    | Look for WHT paid                             |
| WHT income nature               | `whtIncome2`               | Manual           | Not extractable                               |
| Income not in section 16(1)(c)? | `incomeNotInSection16c2`   | Manual           | Not extractable                               |

---

### Section: Treaty Benefit

| Question                     | Field Name              | Data Source   | Extraction Logic                      |
| ---------------------------- | ----------------------- | ------------- | ------------------------------------- |
| Eligible for treaty benefit? | `eligibleTreatyBenefit` | Manual        | Not extractable from TB               |
| Income amount                | `treatyBenefitAmount`   | Trial Balance | Extract from offshore income accounts |
| WHT location                 | `treatyBenefitLocation` | Manual        | Not extractable                       |
| WHT rate                     | `wthRate`               | Manual        | Not extractable                       |
| Income type                  | `incomeType`            | Trial Balance | Match to income account types         |

---

### Section: Ship Owner

| Question            | Field Name        | Data Source     | Extraction Logic                 |
| ------------------- | ----------------- | --------------- | -------------------------------- |
| Is ship owner?      | `isShipOwner`     | Previous Return | Check Section 9.5                |
| S5 filled by agent? | `s5FilledByAgent` | Manual          | Suggest true                     |

---

### Section: FIHV/FSPE/ESF Office

| Question                             | Field Name         | Data Source     | Extraction Logic                        |
| ------------------------------------ | ------------------ | --------------- | --------------------------------------- |
| Categorized as FIHV/FSPE/ESF Office? | `isFamilyCategory` | Previous Return | Check section 9.20                      |
| Which one?                           | `familyType`       | Previous Return | Extract from Form S20                   |
| S20 filled by agent?                 | `s20FilledByAgent` | Manual          | Suggest true                            |
| Assessable profits                   | `familyProfit`     | Trial Balance   | Calculate: Net profit + adjustments     |

---

## IMPLEMENTATION WORKFLOW

### Phase 1: Document Upload

1. User uploads Trial Balance (Excel/PDF)
2. User uploads Last Year Tax Computation (PDF)
3. System parses and extracts structured data

### Phase 2: AI Processing

1. Map TB accounts to standard chart of accounts
2. Extract all numerical values
3. Identify account relationships
4. Load last year's answers from tax computation

### Phase 3: Answer Suggestion

1. Iterate through all questions in both schemas
2. Apply extraction logic for each question
3. Calculate confidence scores
4. Flag items requiring review

### Phase 4: User Review Interface

Display suggested answers with:

- **Green checkmark**: High confidence (>80%), auto-accept option
- **Yellow warning**: Medium confidence (50-80%), review recommended
- **Red flag**: Low confidence (<50%) or mandatory review

### Phase 5: User Approval

1. User reviews flagged items
2. User can:
   - Accept AI suggestion
   - Modify suggested value
   - Override with manual input
   - Mark as "Not Applicable"

### Phase 6: Final Validation

1. Run cross-validation checks
2. Generate warnings for inconsistencies
3. Require user confirmation for high-risk items
4. Lock approved answers

---

## CONCLUSION

This AI-powered answer suggestion system significantly reduces the manual effort required to complete Hong Kong tax return questionnaires. By intelligently analyzing the Trial Balance and Last Year Tax Computation, the application can:

✅ Auto-fill 60-70% of questions with high confidence
✅ Flag critical items requiring professional review
✅ Ensure consistency with prior year positions
✅ Detect potential tax optimization opportunities
✅ Reduce filing errors through validation checks

The user's role shifts from **data entry** to **review and approval**, dramatically improving efficiency while maintaining accuracy and compliance.
