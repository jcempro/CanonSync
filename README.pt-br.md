# SYNC Engine

![Python](https://img.shields.io/badge/python-3.10%2B-blue)
![License](https://img.shields.io/badge/license-MP2%2B-green)
![Status](https://img.shields.io/badge/status-experimental-orange)
![Tests](https://img.shields.io/badge/tests-partial-yellow)
![CI](https://img.shields.io/badge/CI-not%20configured-lightgrey)
![Style](https://img.shields.io/badge/style-deterministic-informational)

![EN](./README.md)

Engine de sincronização determinística baseado em **reconciliação de estado**, **validação criptográfica** e **pipeline idempotente**. O projeto orquestra download, cache, integridade e pós-processamento com regras estritas e previsíveis.

---

## Índice

- [Visão Geral](#visão-geral)
- [Status do Projeto](#status-do-projeto)
- [Arquitetura](#arquitetura)
- [Formato `.syncdownload`](#formato-syncdownload)
- [Princípios de Projeto](#princípios-de-projeto)
- [Boas Práticas](#boas-práticas)
- [Como Contribuir](#como-contribuir)
- [Licença](#licença)

---

## Visão Geral

O **SYNC Engine** resolve sincronização de artefatos com foco em:

- Determinismo (mesma entrada → mesmo estado final)
- Integridade (hash SHA256/MD5 como fonte de verdade)
- Eficiência (evita downloads via reconciliação cache ↔ destino)
- Extensibilidade controlada (DSL + subscripts com isolamento)
- Síncrono

Não é um simples downloader: é um **sistema de decisão incremental** com semântica explícita.

---

## Status do Projeto

> ⚠️ **Fase atual: experimental / validação funcional**

- API interna ainda sujeita a ajustes
- Cobertura de testes parcial
- Contratos principais já definidos (RCF do `.syncdownload`)
- Foco atual: robustez de pipeline e coerência de estados

Uso em produção **não recomendado** sem validação própria.

---

## Arquitetura

```
sync/
├── main.py
├── commons.py
├── core/
│   ├── syncdownload.parser.py
│   ├── syncdownload.processor.py
│   ├── download_manager.py
│   ├── cache_validation.py
│   ├── cleanup.py
│   ├── file_operations.py
│   ├── metadata.py
│   └── retry.py
└── utils/
    ├── progress.py
    ├── naming.py
    ├── dsl.py
    └── logging.py
```

**Separação de responsabilidades é mandatória.**
Nenhum módulo deve invadir domínio de outro.

---

## Formato `.syncdownload`

Arquivo declarativo que define **origem, versão, integridade e comportamento**.

### Estrutura mínima

```
<url ou DSL>
[hash]
[nome_final]
[hash_remoto]
[arquivos_em_container]
>>>ext[,fase]
script opcional
```

### Exemplo simples

```
https://example.com/app.zip
d41d8cd98f00b204e9800998ecf8427e
app.zip
```

### Capacidades

- Versão fixa (linha 2) ou dinâmica (linha 4)
- Resolução via DSL (`${...}`)
- Extração seletiva de containers (.zip, .tar.gz, etc.)
- Subscripts com fases controladas (`start`, `end`, etc.)

### Observações

- Ordem das linhas é **semântica e obrigatória**
- Encoding obrigatório: UTF-8
- Scripts são **isolados e não interferem no core**

---

## Princípios de Projeto

### Técnicos

- Separação: **HEAD ≠ GET**
- Hash como autoridade (não metadata)
- Cache híbrido (memória + persistente)
- Retry apenas para falhas transitórias

### Execução

- Idempotente
- Determinística
- Ordenada
- Sem efeitos colaterais ocultos

### Regras críticas

- Nunca confiar apenas em cache
- Nunca inferir versão por metadata
- Nunca tratar container como artefato final

---

## Boas Práticas

### Código

- Funções pequenas e especializadas
- Sem duplicação de lógica
- Sem hardcode
- Imutabilidade sempre que possível

### Integração

- Use `download_manager` para I/O de rede
- Use `utils.dsl` para qualquer resolução dinâmica
- Centralize naming em `utils.naming`

### Evitar

- Parsing HTML quando API existe
- Acoplamento entre módulos core
- Heurísticas não determinísticas

---

## Como Contribuir

### Requisitos

- Seguir rigorosamente o contrato do projeto
- Preservar estilo **determinístico e diff-friendly**
- Não introduzir efeitos colaterais implícitos

### Fluxo sugerido

1. Fork
2. Branch isolada (`feature/...` ou `fix/...`)
3. Implementação mínima necessária
4. Teste local com múltiplos cenários
5. Pull Request objetivo

### Critérios de aceitação

- Coerência com invariantes do sistema
- Não regressão de comportamento
- Clareza estrutural

---

## Licença

Distribuído sob licença **MP2+**.

---

## Objetivo

Fornecer um **engine confiável e previsível** para sincronização de artefatos, com:

- Controle explícito de versão
- Garantia de integridade
- Pipeline transparente e auditável

Sem comprometer simplicidade operacional.

---
