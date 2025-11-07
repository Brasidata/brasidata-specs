# 🧱 Brasidata Naming Convention Spec

## 📜 Padrão Geral

```text
[namespace]-[context]-[stack]-[type]
```

* **kebab-case** (minúsculas, separadas por hífen)
* sem underscores ou espaços
* última parte (`type`) define o papel funcional do repositório
* `namespace` é opcional, usado apenas para famílias de projeto (ex: `obdc-*`)

---

## 🔹 Segmentos

| Segmento    | Obrigatório | Descrição                                                         | Exemplo                                                                         |
| ----------- | ----------- | ----------------------------------------------------------------- | ------------------------------------------------------------------------------- |
| `namespace` | opcional    | Agrupa módulos sob um ecossistema (OntoBDC, InfoBIM, etc.)        | `obdc-`                                                                         |
| `context`   | sim         | Define o domínio funcional ou tema principal                      | `database`, `proxy-manager`, `flowcenter`, `ontologies`, `propostas-comerciais` |
| `stack`     | sim         | Especifica a tecnologia-base ou implementação                     | `postgres`, `mongodb`, `n8n`, `nginx`, `latex`                                  |
| `type`      | sim         | Natureza do repositório: execução, documentação, biblioteca, etc. | `srvc`, `doc`, `app`, `lib`, `pkg`, `ui`                                        |

---

## 🔹 Exemplos Atuais (oficiais)

| Repositório                      | Interpretação                                 |
| -------------------------------- | --------------------------------------------- |
| `propostas-comerciais-latex-doc` | documentação de propostas comerciais em LaTeX |
| `obdc-ontologies-doc`            | documentação das ontologias OntoBDC           |
| `database-postgres-srvc`         | serviço PostgreSQL                            |
| `database-mongodb-srvc`          | serviço MongoDB                               |
| `flowcenter-n8n-srvc`            | serviço de orquestração N8n do FlowCenter     |
| `proxy-manager-nginx-srvc`       | serviço de proxy reverso com Nginx            |

---

## 🔹 Regex de Validação

```regex
^([a-z0-9]+-){1,3}[a-z0-9]+-(srvc|doc|app|lib|pkg|ui)$
```

✅ Aceita até 4 blocos antes do sufixo
✅ Garante o tipo final válido
✅ Bloqueia uso de maiúsculas e underscores

---

## 🔹 Convenções e Boas Práticas

* Prefixos opcionais de namespace (`obdc-`, `infobim-`, `infra-`) devem refletir **ecossistemas semânticos**.
* Evite nomes genéricos: use contextos explícitos (`proxy-manager`, não apenas `proxy`).
* Se houver componentes homônimos (ex: `flowcenter` em backend e frontend), diferencie com `-srvc` / `-ui`.
* Documentações de projeto usam `-doc` e devem conter um README descritivo do domínio, stack e versão da ontologia ou serviço.
* Tags de versão (`vX.Y.Z`) só são aplicadas a releases, nunca no nome do repositório.

---

## 🔹 Padrão de Licenciamento

**MIT License** (padrão Brasidata)

> Permite uso, cópia, modificação, distribuição e sublicenciamento, inclusive comercialmente, desde que o aviso de copyright e a licença original sejam mantidos.

Repositórios que dependam de componentes **GPL** ou **AGPL** devem declarar isso explicitamente no cabeçalho do README, sem alterar o nome do repositório.

---

## 🔹 Exemplo de Hierarquia Esperada

```text
Brasidata/
├── obdc-ontologies-doc
├── flowcenter-n8n-srvc
├── database-postgres-srvc
├── database-mongodb-srvc
├── proxy-manager-nginx-srvc
└── propostas-comerciais-latex-doc
```
