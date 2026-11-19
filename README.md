# College Event Management System (CEMS) — Project Documentation

Software engineering artifacts, system architecture specifications, API references, and quality assurance records for **Team 15's College Event Management System**.

**Application Repository:** [SE-MiniProject-Team15/cems-codebase](https://github.com/SE-MiniProject-Team15/cems-codebase)

---

## 📁 Documentation Structure

```text
Documentation/
├── API/
│   └── REST_API_Reference.md
│       # Endpoint contracts, request/response schemas & RBAC rules
│
├── SAD/
│   ├── diagrams/
│   │   # Architecture & component diagrams
│   ├── CEMS_Implementation_Architecture.md
│   │   # Implementation-level architecture & module breakdown
│   ├── README.md
│   │   # Architecture overview & navigation
│   └── SAD_CollegeEventManagementSystem_Team15.md
│       # Software Architecture Document (SAD)
│
├── SRS/
│   ├── diagrams/
│   │   └── CEMS_UseCase_Diagram.png
│   │       # System use-case diagram
│   └── SRS_CollegeEventManagementSystem.md
│       # Software Requirements Specification (FRs & NFRs)
│
└── TestPlan/
    ├── parts/
    │   # Modular test specification sections
    ├── STP_CollegeEventManagementSystem_Team15.md
    │   # Software Test Plan (STP)
    ├── TestExecution_CollegeEventManagementSystem_Team15.md
    │   # Test execution logs & evidence
    └── TestPlan_CollegeEventManagementSystem_Team15.md
        # Complete test case suite & final audit results
```

---

## ✅ Verification Summary

The project underwent backend automated testing, cross-browser end-to-end testing, and a comprehensive test plan audit.

| Verification Category | Results | Status |
|---|---:|---|
| Backend Automated Suite (`node --test`) | 141 / 141 passing | Pass |
| Cross-Browser E2E Suite (Playwright) | 231 / 231 passing | Pass |
| Test Plan Audit | 95 total test cases | Reviewed |

### Test Plan Audit Breakdown

The audit of `TestPlan_CollegeEventManagementSystem_Team15.md` recorded the following results:

| Result | Count |
|---|---:|
| Pass | 93 |
| Fail | 0 |
| Partial | 1 |
| Pending | 1 |
| **Total** | **95** |

**Cross-browser coverage:** Chromium, Firefox, and WebKit.

---

## 📚 Documentation Contents

| Directory | Description |
|---|---|
| `API/` | REST API endpoint specifications, request/response schemas, and role-based access rules. |
| `SAD/` | System architecture, implementation architecture, component diagrams, and architectural decisions. |
| `SRS/` | Functional and non-functional requirements, including system use cases. |
| `TestPlan/` | Software test plans, detailed test cases, execution records, and audit results. |

---

## 👥 Contributors

**Team 15 — Software Engineering Laboratory Mini-Project**

- **Prajwal Hegde** — [@prajwalhegde593](https://github.com/prajwalhegde593)
- **Ashith Rao** — [@DarthCoder01](https://github.com/DarthCoder01)
- **G Purvi** — [@purvigiridhar](https://github.com/purvigiridhar)
- **Leesha R** — [@Leeshar1905](https://github.com/Leeshar1905)

---

**Related Repository:** [CEMS Application Source Code](https://github.com/SE-MiniProject-Team15/cems-codebase)

**Built by Team 15.**
