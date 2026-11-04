# Guia de Benchmarking Automatizado - AirTrail

## Visão Geral

Este sistema executa automaticamente testes de performance no AirTrail usando JMeter, tudo orquestrado via Ansible.

## Requisitos

- Ansible instalado
- Cluster GKE com AirTrail deployed
- `kubectl` configurado
- Acesso à Google Cloud

## Comandos Disponíveis

### 1. Configurar Monitoring (executar uma vez)

```bash
ansible-playbook monitoring-setup.yml -i inventory/gcp.yml
```

**O que faz:**

- Ativa monitoring no cluster GKE
- Configura logging
- Prepara dashboards no GCP

### 2. Executar Teste Simples (10 utilizadores)

```bash
ansible-playbook -i inventory/hosts run-benchmark.yml
```

**O que faz automaticamente:**

- Instala JMeter (se necessário)
- Obtém o IP do AirTrail
- Gera 3 testes JMeter
- Executa os 3 testes
- Gera relatórios HTML

**Duração:** ~2-3 minutos

### 3. Executar Teste com Carga Média (50 utilizadores)

```bash
ansible-playbook -i inventory/hosts run-benchmark.yml -e "benchmark_scenario=medium"
```

**Duração:** ~3-4 minutos

### 4. Executar Teste com Carga Pesada (100 utilizadores)

```bash
ansible-playbook -i inventory/hosts run-benchmark.yml -e "benchmark_scenario=heavy"
```

**Duração:** ~4-5 minutos
