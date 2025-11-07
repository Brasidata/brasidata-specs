# 🧱 Brasidata Naming Convention Spec

## 📜 General Pattern

```text
[namespace]-[context]-[stack]-[type]
```

* kebab-case (lowercase, hyphen-separated)
* no underscores or spaces
* the last part (`type`) defines the repository’s functional role
* `namespace` is optional, used only for project families (e.g., `obdc-*`)

---

## 🔹 Segments

| Segment     | Required | Description                                                    | Example                                                                          |
|-------------|----------|----------------------------------------------------------------|----------------------------------------------------------------------------------|
| `namespace` | optional | Groups modules under an ecosystem (OntoBDC, InfoBIM, etc.)    | `obdc-`                                                                          |
| `context`   | yes      | Defines the functional domain or main theme                    | `database`, `proxy-manager`, `flowcenter`, `ontologies`, `propostas-comerciais`  |
| `stack`     | yes      | Specifies the base technology or implementation                | `postgres`, `mongodb`, `n8n`, `nginx`, `latex`                                   |
| `type`      | yes      | Repository nature: runtime, documentation, library, etc.       | `srvc`, `doc`, `app`, `lib`, `pkg`, `ui`                                         |

---

## 🔹 Current Official Examples

| Repository                      | Interpretation                                  |
|---------------------------------|--------------------------------------------------|
| `propostas-comerciais-latex-doc`| documentation for commercial proposals in LaTeX  |
| `obdc-ontologies-doc`           | documentation of OntoBDC ontologies              |
| `database-postgres-srvc`        | PostgreSQL service                               |
| `database-mongodb-srvc`         | MongoDB service                                  |
| `flowcenter-n8n-srvc`           | N8n orchestration service for FlowCenter         |
| `proxy-manager-nginx-srvc`      | reverse proxy service using Nginx                |

---

## 🔹 Validation Regex

```regex
^([a-z0-9]+-){1,3}[a-z0-9]+-(srvc|doc|app|lib|pkg|ui)$
```

✅ Accepts up to 4 blocks before the suffix  
✅ Ensures the final type is valid  
✅ Blocks uppercase letters and underscores

---

## 🔹 Conventions and Best Practices

* Optional namespace prefixes (`obdc-`, `infobim-`, `infra-`) should reflect semantic ecosystems.
* Avoid generic names: use explicit contexts (`proxy-manager`, not just `proxy`).
* If there are homonymous components (e.g., `flowcenter` in backend and frontend), differentiate with `-srvc` / `-ui`.
* Project documentation uses `-doc` and must include a README describing the domain, stack, and the ontology or service version.
* Version tags (`vX.Y.Z`) apply only to releases, never in the repository name.

---

## 🔹 Licensing Standard

**MIT License** (Brasidata default)

> Allows use, copy, modification, distribution, and sublicensing, including commercially, provided the copyright notice and the original license are retained.

Repositories depending on **GPL** or **AGPL** components must explicitly declare this in the README header, without changing the repository name.

---

## 🔹 Example of Expected Hierarchy

```text
Brasidata/
├── obdc-ontologies-doc
├── flowcenter-n8n-srvc
├── database-postgres-srvc
├── database-mongodb-srvc
├── proxy-manager-nginx-srvc
└── propostas-comerciais-latex-doc
```
