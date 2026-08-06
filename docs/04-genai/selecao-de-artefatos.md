# Seleção dos Artefatos de Especificação

Este documento registra a **análise que precedeu a produção** dos artefatos. Antes de escrever
qualquer especificação, a IA Generativa foi consultada com um prompt que pedia a recomendação de
artefatos para este projeto específico, com justificativa e prioridade. A resposta foi então avaliada
criticamente — o que foi aceito, adaptado ou recusado está registrado em
[registro-de-uso-da-ia.md](registro-de-uso-da-ia.md).

**Ferramenta utilizada:** Claude (Claude Code).

---

## 1. Critérios de decisão adotados

Um artefato só entrou no escopo se atendesse a pelo menos dois dos três critérios:

1. **Responde a uma pergunta que nenhum outro artefato responde.** Artefato redundante é custo de
   manutenção sem retorno: quando dois documentos descrevem a mesma regra, eles divergem na primeira
   mudança.
2. **Tem leitor identificado.** Se ninguém no projeto precisa lê-lo para tomar uma decisão, ele é
   cerimônia.
3. **Sobrevive à primeira mudança de requisito.** Com 15 questões em aberto
   ([lacunas-e-ambiguidades.md](../02-analise/lacunas-e-ambiguidades.md)), artefato de alto
   detalhamento sobre premissa não validada é retrabalho garantido.

O terceiro critério é decisivo neste projeto. A elicitação foi razoavelmente ampla em necessidades e
notavelmente lacunar em definições — cinco stakeholders ouvidos, mas nove pontos explicitamente sem
definição e o único detentor dos requisitos não funcionais (a Equipe de TI) sequer entrevistado. Isso
desaconselha artefatos que exijam precisão que o projeto ainda não tem.

---

## 2. Análise dos artefatos candidatos

| Artefato | Recomenda? | Prioridade | Justificativa |
|----------|:----------:|:----------:|---------------|
| **Requisitos funcionais catalogados com origem e MoSCoW** | Sim | Alta | A elicitação é um conjunto de falas dispersas por quatro grupos de stakeholders. Sem catálogo com identificador e origem, não há como saber o que foi pedido, por quem, nem o que cabe no MVP. É o artefato do qual todos os outros dependem. |
| **Regras de negócio explícitas** | Sim | Alta | O núcleo do sistema é um conjunto de invariantes sobre saldo (nunca negativo, sempre derivado de movimentações, sempre com autor). Deixá-las implícitas nas histórias significa reimplementá-las de forma inconsistente em cada tela. Artefato separado permite que os testes referenciem `RN-nn` (RNF-27). |
| **Requisitos não funcionais com métrica de verificação** | Sim | Alta | A observação O-09 declara que não foram levantados. Um sistema de auditoria de estoque sem requisito de segurança e de imutabilidade produz rastro sem valor. Cada item traz critério de verificação, porque RNF sem métrica é inverificável. |
| **Registro de lacunas, ambiguidades e conflitos** | Sim | Alta | Nove pontos em aberto na própria elicitação, mais seis identificados na análise da linguagem. Sem este artefato, as premissas adotadas para prosseguir ficariam invisíveis e seriam confundidas com decisões validadas. É o artefato que mantém a especificação honesta. |
| **Histórias de usuário refinadas** | Sim | Alta | O material de origem é composto de necessidades expressas na primeira pessoa por papéis distintos — formato que se traduz naturalmente em história. Serve de unidade de planejamento e conecta cada item do *backlog* ao seu beneficiário. |
| **Critérios de aceitação em Dado/Quando/Então** | Sim | Alta | As regras deste domínio são densas em exceção: saldo insuficiente, lote vencido, produto bloqueado, concorrência, permissão. História sem critério não é testável, e é precisamente nas exceções que um controle de estoque falha. Também é o artefato que traduz adjetivos vagos ("simples", "rapidamente") em condição verificável. |
| **Casos de uso detalhados — seletivos** | Sim | Média | Recomendado apenas para os três fluxos com múltiplos atores e mais exceções que caminhos normais: registrar saída, realizar inventário e repor a partir de alerta. Nesses fluxos, a história vira uma lista de critérios sem sequência. Estender casos de uso a todo o *backlog* seria duplicação. |
| **Matriz de rastreabilidade** | Sim | Média | Com 40 requisitos, 33 regras e 31 histórias, a coerência entre artefatos deixa de ser verificável por leitura. A matriz responde "quem pediu isto?" — pergunta que expõe escopo inflado — e revelou três problemas reais registrados na sua seção 7. |
| **Protótipos de baixa fidelidade** | Sim | Média | Validam com o estoquista o fluxo e o conteúdo das telas antes de qualquer código, e permitem verificar RNF-19 (máximo de cinco interações) por contagem. Vários requisitos de comportamento só ficam evidentes desenhados — o mais importante deles, ocultar o saldo esperado durante a contagem de inventário. |
| **Glossário** | Sim | Média | Dois conflitos concretos nasceram de vocabulário: "vendas" versus "saídas de estoque" e "tempo real". Além disso, a distinção entre saldo físico e saldo disponível é a principal fonte prevista de mal-entendido operacional. Custo baixo, retorno alto. |
| **Diagramas de estado e de fluxo** | Sim | Média | Estados de movimentação e de pedido de compra são regras de transição — texto os descreve mal. O diagrama de fluxo da saída tornou visível que cinco dos nove pontos de decisão são recusas, o que sustentou a decisão de detalhar esse fluxo em caso de uso. |
| **Diagrama de classes / modelo de domínio** | Parcial | Baixa | Útil, mas prematuro: [Q-15](../02-analise/lacunas-e-ambiguidades.md) (depósito único ou múltiplo) e [Q-03](../02-analise/lacunas-e-ambiguidades.md) (fator de conversão fixo ou variável) alteram diretamente a estrutura de saldo. Modelar antes das respostas produz diagrama que será refeito. Recomendado imediatamente após a sessão de validação. |
| **BPMN dos processos de negócio** | Não | Baixa | BPMN se paga quando há orquestração entre áreas, com raias, eventos e decisões distribuídas. Aqui os processos são curtos e majoritariamente internos ao armazém; o diagrama de sequência de UC-02 e o fluxo de UC-03 cobrem a necessidade com notação que a equipe lê sem treinamento. Reavaliar se a integração com vendas e compras (Q-07) for aprovada — aí passa a existir processo interorganizacional real. |
| **Diagrama de casos de uso UML (visão geral)** | Não | Baixa | A tabela de perfis por operação em [regras-de-negocio.md](../02-analise/regras-de-negocio.md) transmite a mesma informação com mais precisão: o diagrama mostra que o Gerente "aprova inventário", mas não que o aprovador deve ser distinto de quem contou (RN-27). O diagrama de atores em [fluxos-e-prototipos.md](../03-especificacao/fluxos-e-prototipos.md) cobre a visão panorâmica. |
| **Documento de requisitos monolítico (padrão IEEE 830)** | Não | Baixa | Formato incompatível com evolução incremental: um documento único de dezenas de páginas, com aprovação formal, envelhece na primeira resposta de stakeholder. Com 15 questões abertas, seriam muitas revisões. Os mesmos conteúdos estão aqui, modularizados e versionados por *commit*. |
| **Especificação suplementar de arquitetura** | Não | Baixa | Fora do escopo da engenharia de requisitos e impossível de fazer bem sem a Equipe de TI, que não foi entrevistada. RNF-30 (API documentada) registra a única restrição arquitetural que a análise de requisitos pode legitimamente impor. |
| **Protótipo de alta fidelidade / navegável** | Não | Baixa | O ganho sobre o *wireframe* é de identidade visual e microinteração — decisões de design de interface, não de requisitos. Investir agora significa refazer quando as premissas de Q-01 a Q-15 forem validadas. |
| **Modelo de dados físico (DER)** | Não | Baixa | Mesma dependência do diagrama de classes, com detalhamento maior e portanto retrabalho maior. Pertence à etapa de projeto. |
| **Matriz CRUD por entidade e perfil** | Não | Baixa | Redundante com a matriz de permissões já consolidada na seção 7 de [regras-de-negocio.md](../02-analise/regras-de-negocio.md). Falha no critério 1: não responde a nenhuma pergunta nova. |

**Resumo:** 11 artefatos recomendados (5 de prioridade alta, 6 de média), 1 parcial e 7 recusados.

---

## 3. Se fosse possível escolher apenas dois

A questão foi formulada supondo que já existisse documentação e se precisasse complementá-la com dois
artefatos. A escolha:

### Primeiro: critérios de aceitação em Dado/Quando/Então

É o artefato que transforma intenção em contrato verificável. Sem ele, "registrar saída de forma
simples" pode ser considerado entregue por qualquer implementação — inclusive uma que permita saldo
negativo em uso concorrente. Com ele, existe uma condição objetiva de pronto que a equipe, o
*stakeholder* e o teste automatizado leem da mesma forma.

Neste domínio o argumento é ainda mais forte por uma razão específica: **as exceções são mais
numerosas que os caminhos normais.** O fluxo de registro de saída tem quatro passos e sete exceções.
Um controle de estoque não falha ao registrar uma saída válida; falha quando duas saídas concorrem
pelo mesmo saldo, quando o lote está vencido, quando o produto está sob inventário. Critério de
aceitação é o único artefato que obriga a nomear cada uma dessas situações antes de codificar.

### Segundo: registro de lacunas, ambiguidades e conflitos

É o artefato de maior retorno sobre esforço neste projeto específico, e o mais frequentemente omitido.
A elicitação já declarava nove pontos sem definição; a análise encontrou seis outros — entre eles um
**conflito de fundo**: o Gerente pede relatório de produtos mais vendidos a um sistema que não conhece
vendas.

Sua função é preservar a distinção entre *o que foi acordado* e *o que foi assumido para poder
prosseguir*. Sem esse registro, as 15 premissas desta especificação seriam lidas como decisões
validadas, e o erro só apareceria em homologação — o momento mais caro possível. Ele também é o
único artefato que expõe o risco capaz de invalidar o projeto inteiro:
[Q-07](../02-analise/lacunas-e-ambiguidades.md). Se as vendas ocorrem em outro sistema e a baixa
depende de digitação manual, o saldo divergirá exatamente como divergia na planilha, e o objetivo
declarado do projeto não será alcançado por mais bem construído que o software esteja.

**Por que não histórias de usuário nesta escolha:** porque o enunciado pressupõe que a documentação
existente já as contenha. Fossem elas ausentes, ocupariam o primeiro lugar — não há *backlog* sem
elas.

---

## 4. Próximos passos recomendados

| # | Ação | Responsável | Precede |
|---|------|-------------|---------|
| 1 | Sessão de validação com Gerência, Compras e Equipe de TI para responder às seis questões bloqueantes, com prioridade para Q-15 e Q-07 | Analista de requisitos | Sprint 1 |
| 2 | Entrevistar a Equipe de TI — único stakeholder listado e não ouvido — e converter os RNFs de `A validar` para acordados | Analista de requisitos | Sprint 1 |
| 3 | Validar os protótipos de baixa fidelidade com dois estoquistas em uso real no armazém, medindo RNF-19 e RNF-20 | Analista + Estoquistas | Sprint 2 |
| 4 | Produzir o modelo de domínio, agora com Q-03 e Q-15 respondidas | Equipe de TI | Sprint 2 |
| 5 | Confirmar com o Gerente a substituição de "mais vendidos" por "maior saída" (Q-11) ou aprovar a integração de Q-07 | Analista + Gerência | Sprint 3 |
| 6 | Decidir Q-13: portal do fornecedor ou notificação por e-mail | Gerência + Compras | Sprint 5 |
| 7 | Instituir revisão da matriz de rastreabilidade ao fim de cada *sprint*, para que a documentação não se desatualize silenciosamente | Analista de requisitos | Contínuo |

**Observação sobre a ordem:** os itens 1 e 2 não são formalidade de abertura. Q-15 e Q-03 afetam a
estrutura de saldo, construída na Sprint 2 — e alterar essa estrutura depois de existirem
movimentações registradas é o retrabalho mais caro que este projeto pode sofrer. Uma reunião de duas
horas antes da Sprint 1 evita semanas de migração de dados depois.
