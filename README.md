# Kube-News

[![CI/CD Pipeline](https://img.shields.io/badge/CI%2FCD-GitHub%20Actions-blue?logo=github&logoColor=white)](https://github.com/features/actions)
[![Node.js](https://img.shields.io/badge/Node.js-18+-green?logo=node.js&logoColor=white)](https://nodejs.org/)
[![Express.js](https://img.shields.io/badge/Express.js-4.18-blue?logo=express&logoColor=white)](https://expressjs.com/)
[![PostgreSQL](https://img.shields.io/badge/PostgreSQL-15+-336791?logo=postgresql&logoColor=white)](https://www.postgresql.org/)
[![Docker](https://img.shields.io/badge/Docker-Supported-2496ED?logo=docker&logoColor=white)](https://www.docker.com/)
[![Kubernetes](https://img.shields.io/badge/Kubernetes-Ready-326CE5?logo=kubernetes&logoColor=white)](https://kubernetes.io/)
[![License](https://img.shields.io/badge/License-ISC-green)](LICENSE)

Uma aplicação de notícias desenvolvida em NodeJS para demonstrar o uso de containers e Kubernetes.

🌐 **Demo Application** | 🚀 **Production-Ready** | 📦 **Containerized** | ☸️ **Kubernetes Native**

## 📋 Sobre o Projeto

O projeto Kube-News é uma aplicação web simples desenvolvida em Node.js, projetada como exemplo para demonstrar o uso de contêineres. É um portal de notícias que permite criar, visualizar e gerenciar artigos através de uma interface web.

### 🚀 Funcionalidades Principais

- Listagem de notícias na página inicial
- Criação de novas notícias através de formulário
- Visualização detalhada de cada notícia
- API REST para inserção em massa de notícias
- Endpoints de health check para monitoramento
- Coleta de métricas para Prometheus

## 🛠️ Tecnologias Utilizadas

- **Backend**: Node.js com Express.js
- **Frontend**: EJS (Embedded JavaScript) como motor de templates
- **Banco de Dados**: PostgreSQL com Sequelize ORM
- **Monitoramento**: Prometheus (via express-prom-bundle)

## 📦 Estrutura do Projeto

```
/
├── src/                      # Código-fonte principal
│   ├── models/               # Modelos de dados
│   │   └── post.js           # Definição do modelo Post
│   ├── views/                # Templates EJS
│   │   ├── partial/          # Componentes parciais (header, footer)
│   │   ├── edit-news.ejs     # Formulário de edição
│   │   ├── index.ejs         # Página principal
│   │   └── view-news.ejs     # Visualização de notícia
│   ├── static/               # Arquivos estáticos (CSS, imagens)
│   ├── middleware.js         # Middlewares personalizados
│   ├── server.js             # Ponto de entrada da aplicação
│   ├── system-life.js        # Endpoints de health check
│   ├── Dockerfile            # Configuração Docker
│   ├── docker-compose.yml    # Composição Docker local
│   └── package.json          # Dependências
├── k8s/                      # Manifestos Kubernetes
│   ├── manifest.yaml         # Todos os recursos K8s (namespace, deployments, services, secrets)
│   └── README.md             # Documentação Kubernetes
├── .github/                  # Configurações GitHub
│   └── workflows/
│       └── build-and-deploy.yml  # Pipeline CI/CD
├── postmortem/               # Documentação de postmortem
│   ├── template.md           # Template para postmortems
│   └── 2026-07-01_workflow_setup.md  # Postmortem do setup do workflow
├── popula-dados.http         # Arquivo para popular o banco com dados de exemplo
└── README.md                 # Documentação (este arquivo)
```

## 🔧 Configuração

### Pré-requisitos

- Node.js
- PostgreSQL
- Docker (opcional, para containerização)
- Kubernetes (opcional, para orquestração)

### Variáveis de Ambiente

Para configurar a aplicação, defina as seguintes variáveis de ambiente:

| Variável       | Descrição                  | Valor Padrão |
| -------------- | -------------------------- | ------------ |
| DB_DATABASE    | Nome do banco de dados     | kubedevnews  |
| DB_USERNAME    | Usuário do banco de dados  | kubedevnews  |
| DB_PASSWORD    | Senha do usuário           | Pg#123       |
| DB_HOST        | Endereço do banco de dados | localhost    |
| DB_PORT        | Porta do banco de dados    | 5432         |
| DB_SSL_REQUIRE | Habilitar SSL para conexão | false        |

## 🚀 Instalação e Execução

### Execução Local

1. Clone o repositório
2. Instale as dependências:
   ```bash
   cd src
   npm install
   ```
3. Configure as variáveis de ambiente necessárias
4. Inicie a aplicação:
   ```bash
   npm start
   ```
5. Acesse a aplicação em [http://localhost:8080](http://localhost:8080)

### População de Dados de Exemplo

Utilize o arquivo `popula-dados.http` para inserir notícias de exemplo:

```bash
# Com uma ferramenta como o REST Client no VS Code ou curl
POST http://localhost:8080/api/post
Content-Type: application/json
# Conteúdo do arquivo popula-dados.http
```

## 📊 Monitoramento e Health Checks

A aplicação disponibiliza endpoints para monitoramento e também recursos para simular cenários de falha, muito úteis para testar a resiliência em ambientes Kubernetes:

### Endpoints de Monitoramento

- `/health` - Verifica o estado atual da aplicação (retorna status da aplicação e hostname da máquina)
- `/ready` - Verifica se a aplicação está pronta para receber tráfego
- `/metrics` - Métricas do Prometheus (geradas pelo express-prom-bundle)

### Simulação de Falhas (Chaos Engineering)

- `/unhealth` - (PUT) Altera o estado da aplicação para não saudável. Todas as requisições subsequentes receberão status code 500.
- `/unreadyfor/:seconds` - (PUT) Simula indisponibilidade temporária por um número específico de segundos. Durante este período, o endpoint `/ready` retornará status code 500.

Estes recursos de simulação de falhas são extremamente úteis para testar:

- Comportamento de probes de liveness e readiness no Kubernetes
- Políticas de retry e circuit breaker
- Mecanismos de failover
- Resiliência geral da sua infraestrutura

## 🐳 Docker Deployment

### Build Docker Image

```bash
docker build -t kube-news:latest ./src
```

### Run with Docker Compose

```bash
docker-compose -f src/docker-compose.yml up -d
```

Acesse a aplicação em [http://localhost:8080](http://localhost:8080)

## ☸️ Kubernetes Deployment

### Pré-requisitos

- Cluster Kubernetes configurado
- `kubectl` instalado e configurado
- Docker image publicada em um registry

### Deploy com um Comando

```bash
kubectl apply -f k8s/manifest.yaml
```

Este manifesto cria:
- ✅ Namespace `kube-news`
- ✅ PostgreSQL StatefulSet
- ✅ Aplicação Node.js com 2 réplicas
- ✅ LoadBalancer Service
- ✅ ConfigMaps e Secrets para configuração

### Verificar Deployment

```bash
kubectl get all -n kube-news
kubectl get pods -n kube-news
kubectl logs -n kube-news -l app=kube-news-app
```

### Acessar a Aplicação

```bash
kubectl get svc -n kube-news kube-news-service
# Copie o EXTERNAL-IP e acesse: http://<EXTERNAL-IP>
```

### Escalar a Aplicação

```bash
kubectl scale deployment kube-news-app -n kube-news --replicas=5
```

### Limpar Recursos

```bash
kubectl delete namespace kube-news
```

## 🚀 CI/CD Pipeline com GitHub Actions

O projeto possui um pipeline automatizado de build e deploy usando GitHub Actions.

### Workflow: Build and Deploy

**Localização:** `.github/workflows/build-and-deploy.yml`

**Triggers:**
- Push para branches `main` ou `master`
- Execução manual (workflow_dispatch)

### Jobs do Pipeline

#### 1️⃣ **build-and-push**
- 🔨 Constrói a Docker image a partir de `./src/Dockerfile`
- 📦 Faz push para Docker Hub (`rafahueb/kube-news:latest`)
- ⚡ Utiliza cache do GitHub Actions para builds mais rápidos
- 🏷️ Gera tags automáticas (branch, SHA, semver, latest)

#### 2️⃣ **deploy**
- ⚙️ Aplica manifestos Kubernetes de `./k8s/manifest.yaml`
- ✔️ Verifica rollout status da implantação
- 🔗 Depende da conclusão bem-sucedida do build

### Configuração Necessária

Para ativar o pipeline, configure os seguintes secrets no repositório GitHub:

**GitHub Settings → Secrets and variables → Actions**

| Secret | Descrição | Como Obter |
|--------|-----------|-----------|
| `DOCKERHUB_USERNAME` | Seu usuário Docker Hub | hub.docker.com |
| `DOCKERHUB_TOKEN` | Token de acesso Docker Hub | hub.docker.com/settings/security |
| `KUBE_CONFIG` | Base64 encoded kubeconfig | `cat ~/.kube/config \| base64` |

### Monitoramento do Pipeline

1. Acesse a aba **Actions** no repositório GitHub
2. Selecione **Build and Deploy** workflow
3. Verifique o status de build e deployment
4. Inspecione logs de cada job

### Troubleshooting

| Erro | Causa Provável | Solução |
|------|---|---|
| Docker authentication failed | Credenciais inválidas | Verifique `DOCKERHUB_USERNAME` e `DOCKERHUB_TOKEN` |
| Kubernetes deployment fails | Kubeconfig inválido | Regenere: `cat ~/.kube/config \| base64` |
| Rollout timeout | Pod não atingiu estado Ready | Verifique logs: `kubectl logs -n kube-news <pod-name>` |
| Image pull failed | Imagem não disponível | Verifique Docker Hub image tags |

## 🔒 Modelo de Dados

O projeto utiliza um único modelo `Post` com os seguintes campos:

| Campo       | Tipo   | Descrição                                   |
| ----------- | ------ | ------------------------------------------- |
| title       | String | Título da notícia (limite: 30 caracteres)   |
| summary     | String | Resumo da notícia (limite: 50 caracteres)   |
| content     | String | Conteúdo completo (limite: 2000 caracteres) |
| publishDate | Date   | Data de publicação                          |

## 🐳 Docker Deployment

### Build Docker Image

```bash
docker build -t kube-news:latest ./src
```

### Run with Docker Compose

```bash
docker-compose -f src/docker-compose.yml up -d
```

Acesse a aplicação em [http://localhost:8080](http://localhost:8080)

## ☸️ Kubernetes Deployment

### Pré-requisitos

- Cluster Kubernetes configurado
- `kubectl` instalado e configurado
- Docker image publicada em um registry

### Deploy com um Comando

```bash
kubectl apply -f k8s/manifest.yaml
```

Este manifesto cria:
- ✅ Namespace `kube-news`
- ✅ PostgreSQL StatefulSet
- ✅ Aplicação Node.js com 2 réplicas
- ✅ LoadBalancer Service
- ✅ ConfigMaps e Secrets para configuração

### Verificar Deployment

```bash
kubectl get all -n kube-news
kubectl get pods -n kube-news
kubectl logs -n kube-news -l app=kube-news-app
```

### Acessar a Aplicação

```bash
kubectl get svc -n kube-news kube-news-service
# Copie o EXTERNAL-IP e acesse: http://<EXTERNAL-IP>
```

### Escalar a Aplicação

```bash
kubectl scale deployment kube-news-app -n kube-news --replicas=5
```

### Limpar Recursos

```bash
kubectl delete namespace kube-news
```

## 🚀 CI/CD Pipeline com GitHub Actions

O projeto possui um pipeline automatizado de build e deploy usando GitHub Actions.

### Workflow: Build and Deploy

**Localização:** `.github/workflows/build-and-deploy.yml`

**Triggers:**
- Push para branches `main` ou `master`
- Execução manual (workflow_dispatch)

### Jobs do Pipeline

#### 1️⃣ **build-and-push**
- 🔨 Constrói a Docker image a partir de `./src/Dockerfile`
- 📦 Faz push para Docker Hub (`rafahueb/kube-news:latest`)
- ⚡ Utiliza cache do GitHub Actions para builds mais rápidos
- 🏷️ Gera tags automáticas (branch, SHA, semver, latest)

#### 2️⃣ **deploy**
- ⚙️ Aplica manifestos Kubernetes de `./k8s/manifest.yaml`
- ✔️ Verifica rollout status da implantação
- 🔗 Depende da conclusão bem-sucedida do build

### Configuração Necessária

Para ativar o pipeline, configure os seguintes secrets no repositório GitHub:

**GitHub Settings → Secrets and variables → Actions**

| Secret | Descrição | Como Obter |
|--------|-----------|-----------|
| `DOCKERHUB_USERNAME` | Seu usuário Docker Hub | hub.docker.com |
| `DOCKERHUB_TOKEN` | Token de acesso Docker Hub | hub.docker.com/settings/security |
| `KUBE_CONFIG` | Base64 encoded kubeconfig | `cat ~/.kube/config \| base64` |

### Monitoramento do Pipeline

1. Acesse a aba **Actions** no repositório GitHub
2. Selecione **Build and Deploy** workflow
3. Verifique o status de build e deployment
4. Inspecione logs de cada job

### Troubleshooting

| Erro | Causa Provável | Solução |
|------|---|---|
| Docker authentication failed | Credenciais inválidas | Verifique `DOCKERHUB_USERNAME` e `DOCKERHUB_TOKEN` |
| Kubernetes deployment fails | Kubeconfig inválido | Regenere: `cat ~/.kube/config \| base64` |
| Rollout timeout | Pod não atingiu estado Ready | Verifique logs: `kubectl logs -n kube-news <pod-name>` |
| Image pull failed | Imagem não disponível | Verifique Docker Hub image tags |

## 📚 Recursos Adicionais

- 📖 [Documentação Kubernetes](./k8s/README.md)
- 📝 [Postmortem de Setup CI/CD](./postmortem/2026-07-01_workflow_setup.md)
- 📋 [Postmortem Template](./postmortem/template.md)

## 🐳 Docker Deployment

### Build Docker Image

```bash
docker build -t kube-news:latest ./src
```

### Run with Docker Compose

```bash
docker-compose -f src/docker-compose.yml up -d
```

Acesse a aplicação em [http://localhost:8080](http://localhost:8080)

## ☸️ Kubernetes Deployment

### Pré-requisitos

- Cluster Kubernetes configurado
- `kubectl` instalado e configurado
- Docker image publicada em um registry

### Deploy com um Comando

```bash
kubectl apply -f k8s/manifest.yaml
```

Este manifesto cria:
- ✅ Namespace `kube-news`
- ✅ PostgreSQL StatefulSet
- ✅ Aplicação Node.js com 2 réplicas
- ✅ LoadBalancer Service
- ✅ ConfigMaps e Secrets para configuração

### Verificar Deployment

```bash
kubectl get all -n kube-news
kubectl get pods -n kube-news
kubectl logs -n kube-news -l app=kube-news-app
```

### Acessar a Aplicação

```bash
kubectl get svc -n kube-news kube-news-service
# Copie o EXTERNAL-IP e acesse: http://<EXTERNAL-IP>
```

### Escalar a Aplicação

```bash
kubectl scale deployment kube-news-app -n kube-news --replicas=5
```

### Limpar Recursos

```bash
kubectl delete namespace kube-news
```

## 🚀 CI/CD Pipeline com GitHub Actions

O projeto possui um pipeline automatizado de build e deploy usando GitHub Actions.

### Workflow: Build and Deploy

**Localização:** `.github/workflows/build-and-deploy.yml`

**Triggers:**
- Push para branches `main` ou `master`
- Execução manual (workflow_dispatch)

### Jobs do Pipeline

#### 1️⃣ **build-and-push**
- 🔨 Constrói a Docker image a partir de `./src/Dockerfile`
- 📦 Faz push para Docker Hub (`rafahueb/kube-news:latest`)
- ⚡ Utiliza cache do GitHub Actions para builds mais rápidos
- 🏷️ Gera tags automáticas (branch, SHA, semver, latest)

#### 2️⃣ **deploy**
- ⚙️ Aplica manifestos Kubernetes de `./k8s/manifest.yaml`
- ✔️ Verifica rollout status da implantação
- 🔗 Depende da conclusão bem-sucedida do build

### Configuração Necessária

Para ativar o pipeline, configure os seguintes secrets no repositório GitHub:

**GitHub Settings → Secrets and variables → Actions**

| Secret | Descrição | Como Obter |
|--------|-----------|-----------|
| `DOCKERHUB_USERNAME` | Seu usuário Docker Hub | hub.docker.com |
| `DOCKERHUB_TOKEN` | Token de acesso Docker Hub | hub.docker.com/settings/security |
| `KUBE_CONFIG` | Base64 encoded kubeconfig | `cat ~/.kube/config \| base64` |

### Monitoramento do Pipeline

1. Acesse a aba **Actions** no repositório GitHub
2. Selecione **Build and Deploy** workflow
3. Verifique o status de build e deployment
4. Inspecione logs de cada job

### Troubleshooting

| Erro | Causa Provável | Solução |
|------|---|---|
| Docker authentication failed | Credenciais inválidas | Verifique `DOCKERHUB_USERNAME` e `DOCKERHUB_TOKEN` |
| Kubernetes deployment fails | Kubeconfig inválido | Regenere: `cat ~/.kube/config \| base64` |
| Rollout timeout | Pod não atingiu estado Ready | Verifique logs: `kubectl logs -n kube-news <pod-name>` |
| Image pull failed | Imagem não disponível | Verifique Docker Hub image tags |

## 📚 Recursos Adicionais

- 📖 [Documentação Kubernetes](./k8s/README.md)
- 📝 [Postmortem de Setup CI/CD](./postmortem/2026-07-01_workflow_setup.md)
- 📋 [Postmortem Template](./postmortem/template.md)
