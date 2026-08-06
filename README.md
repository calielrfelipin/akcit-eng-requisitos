# Engenharia de Requisitos com IA Generativa — Sistema de Controle de Estoque (StockPlus)

Trabalho prático de pós-graduação. A partir de um documento de elicitação, foram produzidos os
artefatos de análise e de especificação de requisitos para o sistema de controle de estoque da
empresa fictícia **StockPlus**, utilizando IA Generativa como ferramenta de apoio.

**Ferramenta de IA Generativa utilizada:** Claude (Claude Code).

---

## Estrutura do repositório

```
docs/
├── 01-elicitacao/
│   └── documento-de-elicitacao.md      Artefato de entrada: 14 falas e 9 pontos em aberto
├── 02-analise/
│   ├── requisitos-funcionais.md        40 RFs com origem, épico e priorização MoSCoW
│   ├── requisitos-nao-funcionais.md    30 RNFs, cada um com critério de verificação
│   ├── regras-de-negocio.md            33 regras + matriz de permissões por perfil
│   ├── lacunas-e-ambiguidades.md       15 questões abertas e as premissas adotadas
│   └── matriz-de-rastreabilidade.md    Vínculo fala → RF → RN → história → critério
├── 03-especificacao/
│   ├── historias-de-usuario.md         31 histórias em 7 épicos, com sequenciamento
│   ├── criterios-de-aceitacao.md       Cenários Dado/Quando/Então
│   ├── casos-de-uso.md                 3 fluxos complexos detalhados
│   ├── fluxos-e-prototipos.md          Diagramas Mermaid e wireframes
│   └── glossario.md                    Vocabulário comum do domínio
└── 04-genai/
    ├── selecao-de-artefatos.md         Análise de quais artefatos produzir, e por quê
    └── registro-de-uso-da-ia.md        O que foi aceito, modificado e descartado
```

**Ordem de leitura sugerida:** `01-elicitacao` → `04-genai/selecao-de-artefatos` → `02-analise` →
`03-especificacao`. A seleção de artefatos vem antes da análise porque foi essa decisão que
determinou o que seria produzido.

---

## 1. Artefatos de especificação produzidos

| Artefato | Prioridade | O que representa |
|----------|------------|------------------|
| Requisitos funcionais catalogados | Alta | 40 requisitos com origem rastreável, agrupados em 7 épicos e priorizados por MoSCoW |
| Regras de negócio explícitas | Alta | 33 regras classificadas em invariante, cálculo, autorização e processo, mais a matriz de permissões |
| Requisitos não funcionais com métrica | Alta | 30 requisitos de desempenho, segurança, auditabilidade, disponibilidade, usabilidade, privacidade e manutenibilidade |
| Registro de lacunas e ambiguidades | Alta | 15 questões abertas, com tipo, impacto, pergunta ao stakeholder e premissa adotada |
| Histórias de usuário | Alta | 31 histórias com estimativa, vínculo a RF/RN e proposta de 6 sprints |
| Critérios de aceitação | Alta | Cenários Dado/Quando/Então, com caminhos alternativos e de exceção |
| Casos de uso seletivos | Média | Detalhamento dos 3 fluxos com múltiplos atores e mais exceções que caminhos normais |
| Matriz de rastreabilidade | Média | Vínculo bidirecional entre fala do stakeholder e artefato de implementação |
| Protótipos de baixa fidelidade | Média | 4 wireframes com as decisões de projeto neles embutidas |
| Diagramas de fluxo e de estado | Média | Estados de movimentação e de pedido, fluxos de saída, inventário e reposição |
| Glossário | Média | Termos do domínio, com marcação dos sentidos fixados pela análise |

Os artefatos **recusados** — BPMN, diagrama de casos de uso UML, documento IEEE 830, protótipo de alta
fidelidade, modelo de dados físico, matriz CRUD — estão justificados em
[selecao-de-artefatos.md](docs/04-genai/selecao-de-artefatos.md). Recusa documentada é decisão de
projeto; omissão silenciosa é lacuna.

---

## 2. Por que esses artefatos foram considerados os mais adequados

A escolha não seguiu uma lista genérica de boas práticas. Partiu de duas características do material
de origem.

**A elicitação é ampla em necessidades e lacunar em definições.** Cinco stakeholders foram ouvidos,
mas nove pontos ficaram explicitamente sem definição — e a Equipe de TI, único detentor dos requisitos
não funcionais, sequer foi entrevistada. Isso desaconselha artefatos que exijam precisão que o projeto
ainda não tem: modelo de dados físico, protótipo de alta fidelidade e documento formal de aprovação
única seriam refeitos na primeira resposta de stakeholder. Em contrapartida, torna indispensável um
artefato que registre o que foi assumido para poder prosseguir.

**O domínio é denso em regras e em exceções.** O núcleo do sistema é um conjunto de invariantes sobre
saldo: nunca negativo, sempre derivado de movimentações, sempre com autor identificado. O fluxo de
registro de saída tem quatro passos e sete exceções — saldo insuficiente, lote vencido, produto
bloqueado por inventário, concorrência sobre o mesmo saldo, permissão ausente, falha de notificação.
Um controle de estoque não falha ao registrar uma saída válida; falha nessas situações. Isso justifica
regras de negócio em artefato próprio, critérios de aceitação com cenários de exceção e caso de uso
detalhado para os três fluxos onde a história perderia a sequência.

Os três critérios aplicados a cada candidato foram: responder a uma pergunta que nenhum outro artefato
responde, ter leitor identificado e sobreviver à primeira mudança de requisito.

---

## 3. Como a IA Generativa apoiou o desenvolvimento

O uso foi estruturado em seis etapas, cada uma com verificação humana antes da seguinte: extração de
requisitos das falas, identificação de ambiguidades e contradições, proposição das regras implícitas,
recomendação de artefatos, redação e revisão cruzada.

**Contribuições decisivas:**

- **Detecção de um conflito de fundo.** O Gerente pede relatório de "produtos mais vendidos" (E-07) a
  um sistema que não conhece vendas, apenas saídas de estoque. Em leitura corrida a fala parece
  inofensiva e viraria requisito direto. Virou a questão Q-11 e uma redação deliberadamente diferente
  em RF-28.
- **Explicitação de invariantes que ninguém pediu.** Nenhum stakeholder disse "o saldo não pode ser
  negativo" nem "movimentação não pode ser editada". São regras óbvias para quem conhece o domínio e,
  por isso mesmo, ficam implícitas — e ausentes do sistema. Tornaram-se RN-01 e RN-07.
- **Cobertura sistemática de exceções.** A enumeração das sete condições de falha do fluxo de saída,
  incluindo concorrência sobre o mesmo saldo, é trabalho exaustivo e de baixa criatividade — perfil em
  que a ferramenta supera a revisão humana em cobertura.
- **Tradução de adjetivos em métrica.** "Rapidamente" e "de forma simples" viraram RNF-01, RNF-02,
  RNF-19 e RNF-20, cada um com critério de verificação.
- **Um insight de comportamento.** Ocultar o saldo do sistema durante a contagem de inventário
  (UC-02, passo 4). Exibi-lo transforma contagem em conferência de expectativa e produz inventários
  com acurácia aparente de 100% e nenhuma informação real.

**Limites observados:** a IA não resolveu nenhuma das 15 questões abertas — identificou-as e propôs
premissas. Tende a inflar escopo, sugerindo o que é plausível no domínio em vez do que foi pedido. E
produz texto que *parece* requisito validado: um número plausível de latência e um número acordado com
o cliente têm a mesma aparência no papel, e a diferença só aparece na homologação.

A resposta metodológica foi marcar procedência em todos os artefatos: coluna **Situação** nos
requisitos não funcionais, marcador `(premissa)` nas regras de negócio, ⚠️ no glossário e um artefato
dedicado às questões abertas.

---

## 4. Sugestões aproveitadas, modificadas e descartadas

O registro completo está em [registro-de-uso-da-ia.md](docs/04-genai/registro-de-uso-da-ia.md). Em
resumo:

### Aproveitadas

Identificação de cada fala com código rastreável (`E-nn`, `O-nn`); priorização MoSCoW; separação entre
requisitos funcionais e regras de negócio; saldo nunca negativo e movimentação imutável como
invariantes; distinção entre saldo físico e saldo disponível; correção por estorno em vez de edição;
segregação de função na aprovação do inventário; enumeração de exceções antes dos caminhos normais;
critérios em Dado/Quando/Então; critério de verificação obrigatório em todo requisito não funcional.

### Modificadas

- **Casos de uso para todos os requisitos → restritos a três fluxos.** Para cadastro e consulta,
  duplicariam a história sem acrescentar informação — e dois documentos que descrevem a mesma coisa
  divergem na primeira mudança.
- **Requisitos não funcionais como acordados → marcados `A validar`.** A observação O-09 é explícita:
  nenhum foi levantado. Apresentá-los como acordados atribuiria aos stakeholders posições que eles não
  tomaram.
- **Fórmula de estoque mínimo como definição → premissa parametrizável.** O cálculo é razoável, mas
  Q-01 está aberta. RNF-29 exige parametrização por configuração, porque a fórmula provavelmente
  mudará após a validação.
- **Disponibilidade de 99,9% → 99,5% na janela operacional.** Sobredimensionamento com custo real de
  infraestrutura para um sistema interno de armazém em janela comercial.
- **"Produtos mais vendidos" → "produtos de maior movimentação de saída".** O sistema não conhece
  vendas. Manter o termo original seria prometer o que a arquitetura especificada não entrega.
- **Portal do fornecedor como escopo normal → `Could`, fora do MVP.** A fala E-14 pede apenas ser
  informado sobre pedidos. Um portal autenticado para ator externo altera o perfil de risco do sistema
  — resposta desproporcional à necessidade expressa.
- **Sugestão de compra gerando pedido automático → aprovação obrigatória.** E-12 pede "sugestões", não
  compra autônoma.

### Descartadas

Documento IEEE 830 monolítico (incompatível com 15 questões abertas); modelagem BPMN (não há
orquestração interorganizacional); diagrama de casos de uso UML (a matriz de permissões é mais
precisa); leitura por código de barras, valorização de estoque, curva ABC e gestão de transportadoras
(não elicitados); protótipo de alta fidelidade (decisão de design, não de requisitos); requisitos de
escalabilidade para "milhões de movimentações" (o volume real é desconhecido); matriz CRUD (redundante
com a matriz de permissões); chatbot de consulta por linguagem natural (sem qualquer relação com as
necessidades levantadas).

Praticamente toda intervenção humana foi um **corte**. A ferramenta é generosa por construção: sugere
o que é plausível no domínio, não o que é apropriado a este projeto, neste estágio, com estas questões
em aberto.

---

## 5. Resultado em números

| Métrica | Valor |
|---------|------:|
| Falas de stakeholders analisadas | 14 |
| Requisitos funcionais catalogados | 40 (18 `Must`) |
| Requisitos não funcionais propostos | 30 |
| Regras de negócio explicitadas | 33 |
| Questões abertas registradas | 15 (6 bloqueantes do MVP) |
| Histórias de usuário | 31 / 154 pontos |
| Casos de uso detalhados | 3 |

**Verificações de coerência realizadas:** as 14 falas da elicitação têm requisito correspondente; as
33 regras de negócio têm ponto de aplicação; os 8 requisitos sem origem direta na elicitação têm
justificativa de derivação registrada; os 4 requisitos recusados estão explicitamente classificados
como `Won't`.

---

## 6. O ponto mais importante desta análise

A questão [Q-07](docs/02-analise/lacunas-e-ambiguidades.md) — existe integração com o sistema de
vendas? — é a única capaz de invalidar o projeto inteiro. Se as vendas ocorrem em outro sistema e a
baixa de estoque depende de digitação manual, o saldo divergirá exatamente como divergia na planilha,
e o problema que motivou o projeto permanecerá sem solução por mais bem construído que o software
esteja.

Nenhuma entrevista tocou no assunto. Nenhum requisito funcional a expõe. Ela só se tornou visível ao
cruzar o objetivo declarado do projeto com o fluxo de reposição de UC-03 — e é esse tipo de achado, não
o volume de documentação produzida, que justifica a etapa de análise de requisitos.
