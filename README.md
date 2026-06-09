## MARINA SOARES DE SOUZA - RA 10106224 - TURMA F -  MBA ENGENHARIA DE DADOS - MACKENZIE 

# Documentação do Projeto: Pipeline de CI/CD Automatizado com GitHub Actions

Este documento apresenta a especificação técnica e a arquitetura do pipeline de Integração Contínua (CI) e Entrega Contínua (CD) desenvolvido no repositório. O objetivo principal do projeto é aplicar os conceitos práticos de DataOps e DevOps, automatizando a validação de arquivos e a publicação de uma página estática em ambiente de produção utilizando o GitHub Pages.

---

## 1. Arquitetura e Ciclo de Vida do Projeto

O fluxo de trabalho foi estruturado com base na pirâmide DIKW e no movimento DataOps, garantindo que a matéria-prima (arquivos de código) passe por etapas automatizadas de validação e governança antes de ser exposta como produto final (vitrine do negócio).

```text
   [Desenvolvimento] 
          │
          ▼ (Push / Pull Request na branch main)
┌────────────────────────────────────────┐
│     Continuous Integration (CI)        │
│  - Checkout do código                  │
│  - Validação estrutural (index.html)   │
│  - Empacotamento de artefato seguro    │
└───────────────────┬────────────────────┘
                    │
                    ▼ (Gatilho: workflow_run com sucesso)
┌────────────────────────────────────────┐
│     Continuous Delivery (CD)           │
│  - Download seguro do artefato pai     │
│  - Configuração do ambiente Pages      │
│  - Deploy automatizado em produção     │
└───────────────────┬────────────────────┘
                    │
                    ▼
       [Página Ativa em Produção]
```
## 2. Estrutura de Diretórios

O projeto segue estritamente a padronização e a hierarquia de pastas exigidas pelo motor de orquestração do GitHub Actions:

```text
seu-projeto/
├── .github/
│   └── workflows/
│       ├── ci.yml      # Configuração da esteira de Integração Contínua
│       └── cd.yml      # Configuração da esteira de Entrega Contínua
└── site/
    └── index.html      # Página estática para publicação em ambiente produtivo
```
## 3. Especificações dos Workflows

### 3.1. Integração Contínua (`ci.yml`)
O pipeline de CI é acionado por qualquer evento de `push` ou `pull_request` direcionado à branch `main`. Suas principais responsabilidades são:
1. **Garantia de Qualidade:** Validar se o arquivo essencial `site/index.html` existe e está no diretório correto. Caso contrário, interrompe a execução imediatamente para evitar deploys inconsistentes.
2. **Gerenciamento de Artefatos:** Compactar e realizar o upload da pasta validada sob o nome de `site-producao`, disponibilizando-a de forma isolada e segura para os jobs seguintes.

### 3.2. Entrega Contínua (`cd.yml`)
O pipeline de CD é disparado de forma assíncrona por meio do gatilho `workflow_run`, escutando a conclusão do workflow de CI. Suas propriedades técnicas incluem:
1. **Governança de Escopo:** Utiliza o identificador dinâmico da execução pai (`run-id`) e o token de autenticação escopado (`secrets.GITHUB_TOKEN`) para localizar e baixar o artefato exato gerado no ciclo anterior, solucionando restrições de isolamento de dados.
2. **Infraestrutura como Código:** Realiza o provisionamento e a publicação segura dos arquivos tratados no ecossistema de nuvem do GitHub Pages, expondo uma URL pública para consumo do usuário final.

---

## 4. Métricas e Observabilidade Aplicadas

Conforme os fundamentos de engenharia moderna, a esteira foi auditada sob a ótica de monitoramento e observabilidade:
* **Métricas de Runner (Desempenho):** O pipeline de validação técnica opera em alta performance, registrando tempos de execução otimizados (média entre 8 e 10 segundos).
* **Análise de Logs textuais:** O console detalhado do Actions permitiu diagnosticar proativamente erros de escopo (ausência de artefatos entre execuções distintas) e erros sintáticos (declaração de propriedades em formato incorreto no arquivo descritor YAML).
* **Tracing Estrutural:** A dependência declarada entre os dois arquivos assegura que alterações de infraestrutura ou códigos malformados fiquem retidos na primeira linha de defesa (CI), mitigando o risco operacional e a taxa de falha em produção (*Change Failure Rate*).
