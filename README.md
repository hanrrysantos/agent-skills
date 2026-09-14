# Agent Skills

Coleção de Skills reutilizáveis para agentes de IA voltados ao desenvolvimento de software.

O objetivo deste repositório é criar instruções especializadas que permitam que agentes trabalhem de forma mais previsível, segura e organizada durante tarefas como implementação, revisão de código, correção de bugs, testes e debugging.

As Skills são projetadas para serem simples, modulares e reutilizáveis em diferentes projetos e agent harnesses.

## Objetivo

Em vez de colocar todas as regras de desenvolvimento em um único prompt ou arquivo de instruções, este projeto divide comportamentos especializados em Skills independentes.

Cada Skill responde a uma responsabilidade específica:

```text
Agent
  │
  ├── code-review
  │     └── revisar alterações
  │
  ├── fix-findings
  │     └── corrigir problemas identificados
  │
  ├── systematic-debugging
  │     └── investigar bugs
  │
  └── testing
        └── criar e executar testes
```

O agente carrega apenas as Skills necessárias para a tarefa atual.

## Skills disponíveis

### `code-review`

Realiza revisão técnica de alterações de código antes de aprovação ou merge.

Principais características:

- começa pelo `git diff` e expande apenas o contexto necessário;
- prioriza bugs, regressões, segurança e regras de negócio;
- classifica findings como `CRITICAL`, `HIGH`, `MEDIUM` ou `LOW`;
- exige evidência concreta para todos os findings;
- executa testes relevantes quando possível;
- possui heurísticas adicionais para projetos Java/Spring;
- evita comentários baseados apenas em preferência pessoal;
- funciona em modo **read-only**;
- termina com um parecer objetivo sobre a alteração.

📁 [`skills/code-review/SKILL.md`](skills/code-review/SKILL.md)

### `fix-findings`

Investiga e corrige problemas previamente identificados.

Principais características:

- aceita findings de code review ou problemas claramente definidos;
- confirma o problema antes de modificar código;
- busca a causa raiz;
- aplica a menor correção segura e suficiente;
- utiliza testes proporcionalmente ao risco;
- evita refatorações fora do escopo;
- não modifica código apenas para satisfazer um finding incorreto;
- interrompe loops de tentativa e erro sem hipótese concreta;
- nunca aprova a própria correção.

Possíveis resultados:

```text
FIX_APPLIED
FINDING_NOT_CONFIRMED
FIX_BLOCKED
```

📁 [`skills/fix-findings/SKILL.md`](skills/fix-findings/SKILL.md)

## Workflow recomendado

As Skills podem trabalhar juntas em um ciclo de desenvolvimento:

```text
Implementação
     │
     ▼
   Testes
     │
     ▼
 code-review
     │
     ├── APPROVED ─────────────────► Finalizar
     │
     └── CHANGES_REQUESTED
                │
                ▼
          fix-findings
                │
                ├── FIX_BLOCKED
                │
                └── FIX_APPLIED
                       │
                       ▼
                     Testes
                       │
                       ▼
                  code-review
                       │
                       └── ...
```

Uma regra importante desse fluxo é a separação de responsabilidades:

> **Quem corrige não aprova a própria correção.**

A `fix-findings` pode informar que uma correção foi aplicada e que os testes passaram, mas a aprovação final pertence novamente à `code-review`.

## Estrutura

```text
agent-skills/
├── README.md
└── skills/
    ├── code-review/
    │   └── SKILL.md
    └── fix-findings/
        └── SKILL.md
```

Cada Skill possui seu próprio `SKILL.md`:

```yaml
---
name: nome-da-skill
description: Descreve quando o agente deve utilizar esta Skill.
---
```

O restante do arquivo contém as instruções que orientam o comportamento do agente.

## Instalação

As Skills podem ser instaladas usando o [`skills` CLI](https://skills.sh).

### Code Review

```bash
npx skills add hanrrysantos/agent-skills --skill code-review
```

### Fix Findings

```bash
npx skills add hanrrysantos/agent-skills --skill fix-findings
```

### Instalar todas as Skills

```bash
npx skills add hanrrysantos/agent-skills --all
```

## Princípios

As Skills deste repositório seguem alguns princípios:

- **Responsabilidade única** — cada Skill deve resolver um tipo específico de problema.
- **Evidência antes de ação** — agentes não devem inventar problemas ou alterações.
- **Causa raiz** — corrigir a origem do problema, não apenas seus sintomas.
- **Mudanças mínimas** — evitar refatorações e abstrações desnecessárias.
- **Validação** — alterações importantes devem ser verificadas por testes ou outras evidências.
- **Separação de responsabilidades** — implementação, correção e aprovação não precisam pertencer ao mesmo agente.
- **YAGNI** — não adicionar complexidade sem necessidade concreta.

## Roadmap

Skills planejadas:

- [x] `code-review`
- [x] `fix-findings`
- [ ] `systematic-debugging`
- [ ] `testing`
- [ ] `java-springboot`
- [ ] `security-review`
- [ ] `git-workflow`
- [ ] `development-loop`

Com o crescimento do projeto, Skills mais especializadas poderão ser adicionadas para áreas como Spring Data JPA, Spring Security, APIs, migrations, observabilidade e mensageria.

## Contribuindo

Contribuições, sugestões e melhorias são bem-vindas.

Ao propor uma nova Skill, procure manter:

1. responsabilidade clara;
2. instruções objetivas;
3. escopo bem definido;
4. comportamento verificável;
5. proteção contra overengineering;
6. compatibilidade com outras Skills sempre que possível.