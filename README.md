# 🖥️ Web Server Lab — Docker Edition

Servidor web containerizado com monitoramento completo, construído como projeto prático de aprendizado em DevOps.

A stack roda inteiramente via Docker Compose em uma VM Debian (QEMU/KVM), com Nginx servindo conteúdo estático, Prometheus coletando métricas do sistema e do servidor web, e Grafana exibindo dashboards em tempo real.

---

## 📌 Sobre o projeto

Este projeto simula um ambiente de servidor web próximo do que se encontra em produção:
servidor web funcional, observabilidade completa com métricas e um processo de backup automatizado — tudo containerizado e reproduzível com um único comando.

**Motivação:** transição de carreira para DevOps, com foco em infraestrutura, automação e observabilidade.

---

## 📸 Screenshots

![Prometheus Targets](docs/prometheus-targets.png)
![Grafana Dashboard](docs/grafana-dashboard.png)

---

## 🛠️ Stack de tecnologias

| Tecnologia | Versão | Função |
|---|---|---|
| Docker + Docker Compose | 24+ | Containerização e orquestração |
| Debian | 12 (Bookworm) | Sistema operacional da VM |
| QEMU/KVM | — | Virtualização do ambiente |
| Nginx | alpine | Servidor web |
| nginx-prometheus-exporter | 1.1.0 | Traduz métricas do Nginx para o Prometheus |
| Prometheus | 2.51.0 | Coleta e armazenamento de métricas |
| node_exporter | 1.7.0 | Métricas do sistema operacional |
| Grafana | 10.4.0 | Visualização de métricas em dashboard |

---

## 🏗️ Arquitetura

\`\`\`
┌─────────────────────────────────────────────────────────┐
│                  VM Debian (QEMU/KVM)                   │
│                  IP fixo: 192.168.122.100               │
│                                                         │
│  ┌──────────────────────────────────────────────────┐   │
│  │              Docker — rede: lab-network          │   │
│  │                                                  │   │
│  │  ┌──────────┐        ┌──────────────────────┐   │   │
│  │  │  Nginx   │        │      Prometheus       │   │   │
│  │  │  :8080   │        │        :9090          │   │   │
│  │  └────┬─────┘        └──────────┬───────────┘   │   │
│  │       │                         │                │   │
│  │  ┌────▼──────────┐   ┌──────────▼───────────┐   │   │
│  │  │ nginx-exporter│   │      node-exporter    │   │   │
│  │  │    :9113      │   │         :9100         │   │   │
│  │  └───────────────┘   └──────────────────────┘   │   │
│  │                                                  │   │
│  │  ┌──────────────────────────────────────────┐   │   │
│  │  │              Grafana :3000               │   │   │
│  │  │     consulta Prometheus via PromQL       │   │   │
│  │  └──────────────────────────────────────────┘   │   │
│  └──────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────┘
\`\`\`

---

## 🚀 Como reproduzir

### 1. Instalar Docker na VM Debian

\`\`\`bash
curl -fsSL https://get.docker.com | sh
sudo usermod -aG docker $USER
\`\`\`

### 2. Clonar o repositório

\`\`\`bash
git clone https://github.com/douglas-dsantos/web-server-lab.git
cd web-server-lab
\`\`\`

### 3. Subir toda a stack

\`\`\`bash
docker compose up -d
\`\`\`

### 4. Verificar se está tudo rodando

\`\`\`bash
docker compose ps
\`\`\`

### 5. Acessar os serviços

| Serviço | Endereço | Credenciais |
|---|---|---|
| Nginx | http://192.168.122.100:8080 | — |
| Prometheus | http://192.168.122.100:9090 | — |
| Grafana | http://192.168.122.100:3000 | admin / admin |

### 6. Importar dashboard no Grafana

1. Acesse o Grafana
2. Dashboards → Import
3. ID: **1860** → Load
4. Selecione datasource **Prometheus** → Import

---

## 📊 O que é monitorado

- **CPU** — uso por core, load average, I/O wait
- **Memória** — RAM usada, cache, swap
- **Disco** — espaço usado por partição, I/O
- **Rede** — tráfego de entrada e saída
- **Nginx** — conexões ativas, requisições por segundo
- **Uptime** — tempo online do servidor

---

## ⚙️ Comandos úteis

\`\`\`bash
# Subir a stack
docker compose up -d

# Ver logs em tempo real
docker compose logs -f nginx

# Ver status dos containers
docker compose ps

# Derrubar a stack
docker compose down

# Derrubar e apagar volumes
docker compose down -v
\`\`\`

---

## 📁 Estrutura do repositório

\`\`\`
web-server-lab/
├── docker-compose.yml
├── nginx/
│   ├── Dockerfile
│   ├── nginx.conf
│   └── index.html
├── prometheus/
│   └── prometheus.yml
├── grafana/
│   └── provisioning/
│       └── datasources/
│           └── prometheus.yml
└── docs/
    ├── grafana-dashboard.png
    └── prometheus-targets.png
\`\`\`

---

## 🧠 O que aprendi

**Conceito mais importante:** a diferença entre instalar um serviço diretamente no sistema operacional e containerizá-lo. Com Docker Compose, toda a stack sobe com um único comando e cada serviço fica isolado no seu container, se comunicando pelo nome do container como hostname dentro da rede Docker.

**Erro que mais ensinou:** configurar o datasource do Grafana apontando para localhost faz o Grafana tentar acessar o Prometheus dentro do próprio container. O endereço correto é o nome do container (http://prometheus:9090), que o Docker resolve automaticamente pela rede interna.

---

## 🗺️ Próximos passos

- [ ] Adicionar HTTPS com certificado auto-assinado no Nginx
- [ ] Configurar alertas no Grafana (CPU > 80%, disco > 90%)
- [ ] Centralizar logs com Loki + Promtail
- [ ] Criar pipeline CI/CD com GitHub Actions
- [ ] Provisionar infraestrutura com Terraform

---

## 📚 Referências

- [Documentação oficial do Nginx](https://nginx.org/en/docs/)
- [Prometheus Getting Started](https://prometheus.io/docs/prometheus/latest/getting_started/)
- [Grafana Docs](https://grafana.com/docs/)
- [Node Exporter Full Dashboard](https://grafana.com/grafana/dashboards/1860)

---

*Projeto desenvolvido como parte do processo de transição de carreira para DevOps.*
