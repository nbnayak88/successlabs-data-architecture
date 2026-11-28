# Master Data vs. Transactional Data in SAP SuccessFactors
SuccessLabs Academy — Data Architecture | Architecting Experiences for a Better World

Master data describes business entities; transactional data records business activities. Master data is relatively stable but may be effective-dated. Transactions reference master data and can create or change it.

| Dimension | Master data | Transactional data |
|---|---|---|
| Purpose | Business context | Business activity |
| Question | Who or what? | What happened, when and to whom? |
| Change | Periodic or event-driven | Process-driven |
| History | Effective dating where supported | Events, statuses and results |
| Dependency | Defines entities | References entities |

## Employee Central
| Area | Master data | Transaction |
|---|---|---|
| Legal entity | OmniVerse Energy Ltd. | Legal entity transfer |
| Business unit | Renewable Energy | Reassignment |
| Division | Solar Energy | Interdivision transfer |
| Department | Engineering | Department change |
| Location | Mumbai | Relocation |
| Cost center | CC1001 | Cost center reassignment |
| Job classification | Senior Electrical Engineer | Promotion |
| Position | POS10001 | Position assignment |
| Personal information | Name, birth date | Name change request |
| Employment | Employee ID, start date | Hire, rehire, termination |
| Job information | Manager, job code | Manager change |
| Compensation | Recurring salary | Salary revision |
| Pay grade | G07 | Grade change |
| Payment information | Bank details | Bank change request |
| Time | Work schedule, holiday calendar (reference/configuration) | Leave or overtime request |
| Global assignment | Home and host employment details | Assignment initiation |

## Employee Central Payroll
| Area | Master or reference data | Transaction |
|---|---|---|
| Personnel administration | Personnel number | Hiring action |
| Organization | Personnel area/subarea | Reassignment |
| Payroll area | Monthly India payroll (configuration) | Monthly payroll run |
| Basic pay | ₹80,000 monthly | Salary calculation |
| Recurring payments | ₹20,000 HRA | Monthly HRA payment |
| Additional payments | Wage type definition (configuration) | One-time bonus |
| Tax | Employee tax ID | Tax deduction |
| Bank | Account and IFSC | Salary transfer |
| Social insurance | PF membership | PF contribution |
| Loans | Loan agreement | Monthly recovery |
| Time | Work schedule | Overtime |
| Absence | Entitlement profile | Unpaid leave calculation |
| Retroactivity | Effective-dated salary | Retroactive adjustment |
| Payroll processing | Eligibility | Regular/off-cycle run |
| Posting | Cost center assignment | Finance posting |
| Payment | Payment method | Disbursement |

### Infotype mapping
| Infotype | Description | Classification |
|---|---|---|
| IT0000 | Actions | Event-related personnel master data |
| IT0001 | Organizational assignment | Master |
| IT0002 | Personal data | Master |
| IT0006 | Addresses | Master |
| IT0007 | Planned working time | Master |
| IT0008 | Basic pay | Master |
| IT0009 | Bank details | Master |
| IT0014 | Recurring payments/deductions | Master |
| IT0015 | Additional payments | Transaction-oriented input |
| IT0045 | Loans | Agreement/master data |
| IT2001 | Absences | Transaction-oriented time data |
| IT2002 | Attendances | Transaction-oriented time data |
| IT2010 | Employee remuneration | Transaction-oriented input |
| Payroll cluster | Payroll results | Transactional results |

Transaction-oriented infotypes may technically reside in HR master/time data structures; the classification here reflects business semantics.

## Recruiting (RCM)
| Area | Master or reference data | Transaction |
|---|---|---|
| Candidate | Candidate profile (master-like) | Application |
| Position | Position ID | Requisition |
| Job | Job classification | Job posting |
| Organization | Business unit, department | Hiring request |
| Location | Office location | Hiring campaign |
| Recruiter | User profile | Requisition assignment |
| Hiring manager | Employee profile | Approval |
| Skills | Candidate skill profile | Assessment |
| Interview | Interviewer profile | Scheduling |
| Evaluation | Assessment criteria (configuration) | Feedback |
| Offer | Salary structure | Offer creation |
| Approval | Approver profile | Approval/rejection |
| Communication | Candidate contact details | Invitation email |
| Talent pool | Pool definition (reference) | Pool membership addition |
| Hiring | Candidate profile | Ready to Hire transition |

## Integrated case: OmniVerse Energy
Illustrative fictional data, not production records.

| Step | Module | Record | Example | Class |
|---|---|---|---|---|
| 1 | EC | Legal entity | OmniVerse Energy Ltd. | Master |
| 2 | EC | Position | POS10001 | Master |
| 3 | EC | Job classification | Senior Electrical Engineer | Master |
| 4 | Recruiting | Requisition | REQ10001 | Transaction |
| 5 | Recruiting | Candidate | Arjun Sharma | Master-like |
| 6 | Recruiting | Application | APP20001 | Transaction |
| 7 | Recruiting | Interview | INT30001 | Transaction |
| 8 | Recruiting | Offer | ₹18 lakh annual CTC | Transaction |
| 9 | EC | Hire event | 1 Nov 2026 | Transaction |
| 10 | EC | Employee | EMP50001 | Master |
| 11 | EC | Compensation | ₹150,000 monthly CTC | Master |
| 12 | ECP | Personnel number | PERNR50001 | Master |
| 13 | ECP | Basic pay | ₹80,000 | Master |
| 14 | ECP | Bonus | ₹10,000 | Transaction input |
| 15 | ECP | Payroll run | Nov 2026 | Transaction |
| 16 | ECP | Gross earnings | ₹140,000 | Result |
| 17 | ECP | Deductions | ₹20,000 | Result |
| 18 | ECP | Net salary | ₹120,000 | Result |
| 19 | Finance | Posting | FI900001 | Transaction |

Flow: EC organizational master → Recruiting requisition/application/interview/offer → EC hire and employee master → ECP payroll master → payroll execution/results → Finance posting/payment.

## Five-category architecture
| Category | EC | ECP | Recruiting |
|---|---|---|---|
| Master | Employee, position | Personnel record | Candidate profile |
| Reference | Country codes | Currency codes | Candidate source categories |
| Configuration | Business rules | Schema/PCR | Requisition templates |
| Transaction | Promotion request | Payroll run | Application |
| Analytical | Attrition | Payroll cost per employee | Time to hire |

## Effective dating
| Effective date | Monthly basic pay | Event |
|---|---|---|
| 1 Jan 2026 | ₹80,000 | Hire |
| 1 Jul 2026 | ₹90,000 | Increment |
| 1 Jan 2027 | ₹100,000 | Promotion |

The salary approval is a transaction; the resulting effective-dated compensation record is master data; the payroll calculation is a transaction and its output is a transactional result.

## Typical system of record
| Object | Authoritative system | Consumer |
|---|---|---|
| Employee, position | EC | ECP, Recruiting |
| Cost center | Finance ERP | EC, ECP, Recruiting |
| Candidate, application, offer | Recruiting | EC/Onboarding as applicable |
| Compensation | EC | ECP |
| Country-specific payroll data | EC or ECP depending on design | Payroll engine |
| Payroll results | ECP | Finance, EC where configured |
| Financial posting | Finance ERP | Finance reporting |

Ownership depends on implementation and replication scope. This is a conceptual classification, not a claim that SAP formally labels every object this way.

**Principle:** Master data establishes context; transactional data captures activity; configuration governs execution; analytical data measures outcomes.

## Learning experiment
Trace a promotion from approval through effective-dated job and compensation records, replication, retroactive payroll and financial posting. Identify the authoritative system, keys, effective dates, validation controls and reconciliation evidence at each stage.
