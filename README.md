# SYNC ENGINE 🚀

![Python Version](https://shields.io)
![License](https://shields.io)
![Build Status](https://shields.io)
![Stage](https://shields.io)

**Sync Engine** é um ecossistema de sincronização determinística e inteligente escrito em Python. O projeto foi desenhado para ser idempotente, garantindo que o estado final entre a origem e o destino seja sempre coerente, sem redundâncias e com integridade verificada via SHA256.

---

## 📌 Índice

1. [Sobre o Projeto](#sobre-o-projeto)
2. [Estágio de Desenvolvimento](#estágio-de-desenvolvimento)
3. [O Formato .syncdownload](#o-formato-syncdownload)
   - [Sintaxe e Exemplo](#sintaxe-e-exemplo)
4. [Arquitetura do Engine](#arquitetura-do-engine)
5. [Diretrizes de Contribuição](#diretrizes-de-contribuição)
6. [Licença](#licença)

---

## 📖 Sobre o Projeto

O objetivo principal deste projeto é orquestrar pipelines de download e sincronização complexos através de uma interface unificada. Ao contrário de scripts de download simples, o Sync Engine gerencia:

- **Abstração de Origens:** Interface única para GitHub, GitLab e APIs oficiais.
- **Reconciliação de Cache:** Inteligência para evitar tráfego de rede desnecessário comparando cache local vs. destino.
- **Tratamento de Containers:** Descompactação automática (.zip, .tar.gz) com extração seletiva de artefatos.
- **Extensibilidade:** Suporte a subscripts embutidos para ações em fases específicas do pipeline.

## 🛠 Estágio de Desenvolvimento

Atualmente, o projeto encontra-se em **Fase de Teste (Beta)**.

- **Estabilidade:** O core de parsing e o pipeline de download estão funcionais.
- **O que esperar:** Podem ocorrer ajustes na DSL de resolução de URLs e refinamentos no tratamento de erros de scripts externos.
- **Uso Recomendado:** Ambientes de staging ou para automação de ferramentas de infraestrutura que exigem versionamento rígido de binários.

---

## 📝 O Formato .syncdownload

O coração do processamento individual é o arquivo `.syncdownload`. Ele é um arquivo normativo que define a origem, a identidade e o comportamento de cada artefato.

### Sintaxe Curta

```text
Linha 1: URL ou DSL de origem (ex: github|user/repo)
Linha 2: Hash fixo (Opcional - fixa a versão)
Linha 3: Nome final do arquivo (Opcional)
Linha 4: Fonte de hash remoto (URL/DSL para validação dinâmica)
Linha 5: Vetor JSON para extração de containers (Ex: [["bin/app.exe", "app.exe"]])
Linha 6+: Blocos de Script (>>>py, >>>sh)
```

### Exemplo Prático

```text
https://example.com
8cf927... (sha256)
ferramenta_v1.exe
https://example.com
[["bin/tool.exe", "ferramenta_v1.exe"]]
>>>py,end
print("Download e extração concluídos com sucesso!")
```

---

## 🏗 Arquitetura do Engine

O projeto é modularizado para evitar efeitos colaterais e garantir baixo acoplamento:

- **`core/`**: Contém o cérebro do projeto (Parsers, Processadores de Pipeline, Gerenciador de Cache).
- **`utils/`**: Utilitários de interface (Rich progress bar), Naming canonicalization e o motor DSL.
- **`commons.py`**: Definições globais e estruturas compartilhadas.

### Invariantes Críticas

- **Integridade:** A referência de sucesso é sempre o hash do artefato final, não do container.
- **Determinismo:** O sistema deve chegar ao mesmo resultado final, independentemente de quantas vezes for executado.

---

## 🤝 Contribuição e Estilo

Se você deseja contribuir, mantenha em mente:

1. **Pequenas Funções:** Valorizamos funções especializadas e reutilizáveis.
2. **Sem Side-effects:** Evite hardcoding de paths ou configurações. Use os módulos `commons` e `metadata`.
3. **Estilo de Código:** Seguimos o PEP 8 com foco em legibilidade. Comentários devem explicar o "porquê", não o "quê".
4. **Preservação de Estilo:** Mantenha a imutabilidade das estruturas de dados sempre que possível durante o parsing.

---

## ⚖️ Licença

Este projeto é distribuído sob a licença **MPL 2.0 (Mozilla Public License 2.0)**. Isso permite o uso em projetos proprietários, desde que alterações no código-fonte do Sync Engine sejam disponibilizadas publicamente.

---

**FIM DO CONTRATO** (Simulado para fins de README)
