# Engenharia de Requisitos com IA Generativa — Sistema de Controle de Estoque

## Visão geral

Este repositório reúne os artefatos de análise e de especificação produzidos a partir de um documento
de elicitação do sistema de controle de estoque da empresa **StockPlus**. A abordagem adotada é ágil,
com *backlog* priorizado e critérios de aceitação, complementada por casos de uso nos fluxos de maior
complexidade.

## Ferramenta de GenAI utilizada

A ferramenta utilizada foi o **Claude (Claude Code)**, empregada como apoio na análise da elicitação,
na identificação de lacunas e na redação dos artefatos.

## Fontes de referência

Toda a documentação deriva de [docs/01-elicitacao/documento-de-elicitacao.md](docs/01-elicitacao/documento-de-elicitacao.md),
única fonte primária do projeto. Os artefatos de especificação devem permanecer coerentes com os
documentos de análise em [docs/02-analise/](docs/02-analise/), em especial as regras de negócio e o
registro de questões abertas.

## Estrutura do repositório

- **docs/01-elicitacao/** — documento de entrada, com as 14 falas de stakeholders e os 9 pontos em
  aberto, cada um identificado para permitir rastreabilidade.
- **docs/02-analise/** — requisitos funcionais, requisitos não funcionais, regras de negócio, lacunas
  e ambiguidades, e a matriz de rastreabilidade.
- **docs/03-especificacao/** — histórias de usuário, critérios de aceitação, casos de uso, fluxos e
  protótipos, e glossário.
- **docs/04-genai/** — decisão sobre quais artefatos produzir e registro crítico do uso da IA.

Sugestão de leitura: elicitação → seleção de artefatos → análise → especificação.

## Artefatos de especificação produzidos

- `historias-de-usuario.md` — 31 histórias em 7 épicos, priorizadas por MoSCoW e sequenciadas em 6 *sprints*
- `criterios-de-aceitacao.md` — cenários Dado/Quando/Então, com caminhos alternativos e de exceção
- `casos-de-uso.md` — os 3 fluxos com múltiplos atores e mais exceções que caminhos normais
- `fluxos-e-prototipos.md` — diagramas de estado e de fluxo, mais 4 protótipos de baixa fidelidade
- `glossario.md` — vocabulário do domínio, com marcação dos sentidos fixados pela análise

Sustentados pelos artefatos de análise: 44 requisitos funcionais, 30 requisitos não funcionais, 33
regras de negócio, 15 questões abertas e a matriz de rastreabilidade que conecta tudo.

## Por que esses artefatos foram considerados adequados

Os artefatos escolhidos foram considerados os mais adequados porque:

- traduzem necessidades expressas em primeira pessoa no formato que melhor as representa — a história
  de usuário;
- tornam testável cada requisito, o que importa especialmente aqui: neste domínio as exceções são mais
  numerosas que os caminhos normais;
- separam regra de negócio de requisito funcional, permitindo que a regra seja implementada uma única
  vez e citada pelos testes;
- mantêm visível a diferença entre o que foi acordado e o que foi assumido como premissa, dado que a
  elicitação deixou nove pontos sem definição;
- permitem evolução incremental, sem depender de um documento único de aprovação formal;
- sobrevivem à primeira mudança de requisito — critério decisivo em um projeto com 15 questões abertas.

Artefatos como BPMN, diagrama de casos de uso UML, protótipo de alta fidelidade e modelo de dados
físico foram avaliados e **recusados**, com justificativa em
[selecao-de-artefatos.md](docs/04-genai/selecao-de-artefatos.md).

## Como a IA apoiou a engenharia de requisitos

A IA ajudou a:

- extrair de cada fala o requisito implícito e o papel que o solicita;
- identificar ambiguidades, lacunas e **contradições** — inclusive um conflito de fundo: o Gerente pede
  relatório de "produtos mais vendidos" a um sistema que não conhece vendas, apenas saídas de estoque;
- explicitar invariantes que ninguém pediu por serem óbvias demais, como saldo nunca negativo e
  movimentação imutável;
- enumerar exaustivamente as exceções de cada fluxo, inclusive concorrência entre duas saídas
  simultâneas sobre o mesmo saldo;
- converter adjetivos vagos como "rapidamente" e "de forma simples" em requisitos com métrica de
  verificação;
- organizar a especificação ágil e manter a rastreabilidade por identificador entre os artefatos.

A IA não resolveu nenhuma das 15 questões abertas: identificou-as e propôs premissas. Também tende a
inflar escopo, sugerindo o que é plausível no domínio em vez do que foi pedido.

## Sugestões da IA aproveitadas, modificadas e descartadas

**Aproveitadas:** identificação de cada fala com código rastreável, priorização MoSCoW, separação entre
requisitos funcionais e regras de negócio, saldo nunca negativo e movimentação imutável como
invariantes, distinção entre saldo físico e disponível, correção por estorno em vez de edição,
segregação de função na aprovação do inventário, critérios em Dado/Quando/Então e critério de
verificação obrigatório em todo requisito não funcional.

**Modificadas:** a sugestão de detalhar casos de uso para todos os requisitos foi restrita a três
fluxos, porque para cadastro e consulta duplicaria a história sem acrescentar informação; os requisitos
não funcionais deixaram de ser apresentados como acordados e passaram a trazer a marcação `A validar`;
a fórmula de estoque mínimo tornou-se premissa parametrizável em vez de definição do negócio; a
disponibilidade proposta caiu de 99,9% para 99,5% na janela operacional; e "produtos mais vendidos"
virou "produtos de maior movimentação de saída".

**Descartadas:** documento monolítico no padrão IEEE 830, modelagem BPMN, diagrama de casos de uso UML,
protótipo de alta fidelidade e matriz CRUD — todos por redundância ou por exigirem precisão que o
projeto ainda não tem. Também foram descartadas funcionalidades sugeridas pela IA e não solicitadas por
nenhum stakeholder, como leitura por código de barras, valorização de estoque, curva ABC e consulta de
saldo por chatbot.

## Observações de coerência

- As regras de negócio detalhadas estão em [docs/02-analise/regras-de-negocio.md](docs/02-analise/regras-de-negocio.md);
  as histórias e os critérios de aceitação devem refleti-las, citando o identificador `RN-nn`.
- Regras marcadas como `(premissa)`, requisitos não funcionais marcados como `A validar` e termos
  marcados com ⚠️ no glossário derivam de pontos ainda não confirmados com os stakeholders. A
  procedência é sempre declarada — premissa plausível não é requisito acordado.
- A [matriz de rastreabilidade](docs/02-analise/matriz-de-rastreabilidade.md) verifica que as 14 falas
  têm requisito correspondente, que as 33 regras têm ponto de aplicação e que todo requisito sem origem
  direta na elicitação traz justificativa de derivação.
- Seis das 15 questões abertas bloqueiam o MVP e devem ser resolvidas antes da Sprint 1. A mais crítica
  é [Q-07](docs/02-analise/lacunas-e-ambiguidades.md): sem integração com o sistema de vendas, o saldo
  divergirá como divergia na planilha, e o problema que motivou o projeto permanecerá sem solução.
