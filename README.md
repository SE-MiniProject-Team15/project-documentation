# College Event Management System (CEMS) — Project Documentation

Software engineering artifacts, system architecture specifications, API references, and quality assurance records for **Team 15**'s College Event Management System.

**Application Repository:** [SE-MiniProject-Team15/cems-codebase](https://github.com/SE-MiniProject-Team15/cems-codebase)[cite: 7]

---

## Documentation Structure

```text
Documentation/
├── API/
│   └── REST_API_Reference.md                          # Endpoint contracts, request/response schemas & RBAC rules
├── SAD/
│   ├── diagrams/                                      # Architecture & component diagrams
│   ├── CEMS_Implementation_Architecture.md            # Implementation-level architecture & module breakdown
│   ├── README.md                                      # Architecture overview & navigation
│   └── SAD_CollegeEventManagementSystem_Team15.md     # Software Architecture Document (SAD)
├── SRS/
│   ├── diagrams/
│   │   └── CEMS_UseCase_Diagram.png                   # System use-case diagram
│   └── SRS_CollegeEventManagementSystem.md            # Software Requirements Specification (FRs & NFRs)
└── TestPlan/
    ├── parts/                                         # Modular test specification sections
    ├── STP_CollegeEventManagementSystem_Team15.md     # Software Test Plan (STP)
    ├── TestExecution_CollegeEventManagementSystem_Team15.md # Test execution logs & evidence
    └── TestPlan_CollegeEventManagementSystem_Team15.md      # Complete test case suite & final audit results
```[cite: 8]

---

## Verification Summary

* **Backend Automated Suite:** `141 / 141` tests passing (`node --test`)
* **Cross-Browser E2E Suite:** `231 / 231` Playwright checks passing (Chromium, Firefox, WebKit)[cite: 1]
* **Test Plan Audit (`TestPlan_CollegeEventManagementSystem_Team15.md`):** **93 Pass, 0 Fail, 1 Partial, 1 Pending** across 95 total functional and non-functional test cases[cite: 8]

---

## Contributors

* **Prajwal Hegde** (`@prajwalhegde593`)[cite: 7]
* **Ashith Rao** (`@DarthCoder01`)[cite: 7]
* **G Purvi** (`@purvigiridhar`)[cite: 7, 8]
* **Leesha R** (`@Leeshar1905`) [cite: 7]
