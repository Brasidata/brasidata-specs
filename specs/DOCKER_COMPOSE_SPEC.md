# 🐳 Brasidata Docker Compose Spec

## 📜 Estrutura Geral

```yaml
version: '3.8'

services:
  <service-name>:
    image: <image>:<tag>
    container_name: <project>-<service>
    restart: unless-stopped
    depends_on: []
    ports: []
    volumes: []
    environment: []
    networks: []
    healthcheck: {}
    command: []

volumes: {}
networks:
  <network-name>:
    driver: bridge
```

---

## 🔹 Convenções de Nomenclatura

| Elemento         | Padrão                                    | Exemplo                                   |
| ---------------- | ----------------------------------------- | ----------------------------------------- |
| `container_name` | `${PROJECT_NAME:-<context>-<stack>-srvc}` | `${PROJECT_NAME:-database-postgres-srvc}` |
| `volume`         | `<service>_data`                          | `mongo_data`, `redis_data`                |
| `network`        | `<project>-network`                       | `obdc-network`, `overleaf-network`        |
| `env var`        | SCREAMING_SNAKE_CASE                      | `POSTGRES_USER`, `SHARELATEX_MONGO_URL`   |

---

## 🔹 Padrões de Estilo

* Ordem lógica: **Mongo → Redis → App/Service**
* Comentários curtos e técnicos
* Sempre incluir:

  * `restart: unless-stopped`
  * `healthcheck` com `interval`, `timeout`, `retries`
  * bloco `networks:` explícito, mesmo que único
* Variáveis externas controladas via `.env`
* Evitar **paths absolutos** — usar `../` ou `./data/`
* Arquivos de compose ficam em `docker-compose/`
* Usar **compose overrides** (`docker-compose.override.yml`) para ambientes locais.

---

## 🔹 Template Base (Multi-Service)

```yaml
version: '3.8'

services:
  # Database service
  postgres:
    image: postgres:15
    container_name: ${PROJECT_NAME:-database-postgres-srvc}
    restart: unless-stopped
    volumes:
      - postgres_data:/var/lib/postgresql/data
    networks:
      - ${NETWORK_NAME:-brasidata-network}
    environment:
      - POSTGRES_USER=admin
      - POSTGRES_PASSWORD=password
      - POSTGRES_DB=appdb
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U admin"]
      interval: 30s
      timeout: 10s
      retries: 5

  # Application service
  app:
    image: ${APP_IMAGE:-brasidata/app:latest}
    container_name: ${PROJECT_NAME:-app-srvc}
    restart: unless-stopped
    depends_on:
      postgres:
        condition: service_healthy
    ports:
      - "${PORT:-8080}:80"
    networks:
      - ${NETWORK_NAME:-brasidata-network}
    environment:
      - DATABASE_URL=postgresql://admin:password@postgres:5432/appdb
      - TZ=America/Sao_Paulo

volumes:
  postgres_data:

networks:
  brasidata-network:
    driver: bridge
```

---

## 🔹 Estrutura de Diretórios Esperada

```
docker-compose/
├── docker-compose.yml
├── manifests/
│   ├── base.Dockerfile
│   ├── dev.Dockerfile
│   └── prod.Dockerfile
├── .env
└── README.md
```

---

## 🧱 Dockerfile Spec — Brasidata Standard

### 📦 Estrutura de Diretórios

```
docker-compose/
├── docker-compose.yml
├── manifests/
│   ├── dev.Dockerfile
│   ├── prod.Dockerfile
│   └── base.Dockerfile
```

### 📜 Convenções de Nome

| Tipo               | Nome Sugerido                       | Uso                                             |
| ------------------ | ----------------------------------- | ----------------------------------------------- |
| Base               | `base.Dockerfile`                   | imagem fundamental, comum a múltiplos ambientes |
| Desenvolvimento    | `dev.Dockerfile`                    | ambiente de testes locais                       |
| Produção           | `prod.Dockerfile`                   | imagem otimizada para deploy                    |
| Serviço específico | `[context]-[stack]-srvc.Dockerfile` | build específico de serviço                     |

> Exemplo: `erpnext-frappe-srvc.Dockerfile`

---

### ⚙️ Estrutura Interna Mínima

```dockerfile
# --- Stage 1: Base image
FROM python:3.11-slim AS base
LABEL maintainer="Brasidata <contato@brasidata.com.br>"

# --- Stage 2: Dependencies
RUN apt-get update && apt-get install -y \
    git curl make && \
    rm -rf /var/lib/apt/lists/*

# --- Stage 3: Application setup
WORKDIR /app
COPY . /app

ENV TZ=America/Sao_Paulo
ENV PYTHONUNBUFFERED=1

CMD ["python", "main.py"]
```

---

### 🧩 Boas Práticas Brasidata

| Categoria         | Regra                                                                                 |
| ----------------- | ------------------------------------------------------------------------------------- |
| **Base images**   | Preferir `*-slim`, `*-alpine`, ou imagens oficiais.                                   |
| **Stage naming**  | Usar *multi-stage builds* nomeados (`AS base`, `AS builder`, `AS final`).             |
| **Metadados**     | Incluir `LABEL maintainer`, `LABEL org.opencontainers.image.source`, `LABEL version`. |
| **Cache hygiene** | Sempre limpar cache (`apt-get clean && rm -rf /var/lib/apt/lists/*`).                 |
| **Portability**   | Evitar `COPY` de caminhos relativos fora do contexto.                                 |
| **Security**      | Nunca expor credenciais; use `ARG` e `.env`.                                          |
| **Dev images**    | Podem usar `latest`, mas precisam do aviso `# Development only`.                      |

---

### 🚟 Integração com Compose

```yaml
services:
  erpnext:
    build:
      context: ../
      dockerfile: docker-compose/manifests/dev.Dockerfile
    container_name: ${PROJECT_NAME:-erpnext-frappe-srvc}
    ports:
      - "${PORT:-8080}:80"
    environment:
      - TZ=America/Sao_Paulo
```

---

### 🔹 Licenciamento

Cada `Dockerfile` herda a licença do repositório (padrão **MIT License**).
Se usar imagens derivadas de licença **GPL/AGPL**, incluir aviso:

```dockerfile
# License Notice: includes GPL/AGPL-based image dependencies.
```

---

## 🔹 Padrão de Licenciamento Geral

**MIT License** (padrão Brasidata)

> Permite uso, cópia, modificação, distribuição e sublicenciamento, inclusive comercialmente, desde que o aviso de copyright e a licença original sejam mantidos.

Repositórios com dependências **GPL/AGPL** devem declarar isso no cabeçalho do Compose ou Dockerfile.

---

## 🔹 Exemplo de Hierarquia Esperada

```
Brasidata/
├── obdc-ontologies-doc
├── flowcenter-n8n-srvc
├── database-postgres-srvc
├── database-mongodb-srvc
├── proxy-manager-nginx-srvc
└── propostas-comerciais-latex-doc
```
