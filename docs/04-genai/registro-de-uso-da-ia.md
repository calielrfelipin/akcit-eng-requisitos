# Registro de Uso da IA Generativa

Documento de transparência metodológica: o que a IA fez, o que foi aceito, o que foi modificado e o
que foi descartado. A distinção importa — usar IA em engenharia de requisitos é legítimo; apresentar
saída de IA como se fosse levantamento validado com stakeholder, não é.

**Ferramenta utilizada:** Claude (Claude Code)
**Material de entrada:** exclusivamente o documento de elicitação
([01-elicitacao](../01-elicitacao/documento-de-elicitacao.md)) — 14 falas de stakeholders e 9
observações de pontos em aberto.

---

## 1. Como a IA foi empregada

O uso não foi "gerar a documentação". Foi uma sequência de seis etapas, cada uma com verificação
humana antes da seguinte.

| Etapa | O que foi pedido à IA | O que foi verificado manualmente |
|-------|------------------------|----------------------------------|
| 1 | Extrair, de cada fala, o requisito implícito e o papel que o solicita | Se cada requisito extraído era realmente sustentado pela fala, e não inferência do modelo |
| 2 | Identificar ambiguidades, lacunas e **contradições** na elicitação | Se as contradições apontadas eram reais ou artefato de leitura literal |
| 3 | Propor as regras de negócio implícitas no domínio de controle de estoque | Se cada regra tinha origem no material ou era conhecimento genérico do domínio importado sem base |
| 4 | Recomendar quais artefatos de especificação produzir, com prioridade e justificativa | Confrontar com o contexto real: quantidade de questões abertas e maturidade do projeto |
| 5 | Redigir os artefatos escolhidos, mantendo rastreabilidade por identificador | Cobertura, coerência entre arquivos e ausência de regra órfã ou requisito sem origem |
| 6 | Revisar o conjunto em busca de incoerências entre artefatos | Leitura cruzada da matriz de rastreabilidade |

A etapa 2 foi a de maior retorno. A etapa 4 corresponde ao prompt de análise de artefatos e está
documentada em [selecao-de-artefatos.md](selecao-de-artefatos.md).

### Onde a IA contribuiu de forma decisiva

**Detecção do conflito "vendas versus saídas de estoque".** O Gerente pede relatório de "produtos mais
vendidos" (E-07) a um sistema que não conhece vendas, apenas saídas de estoque. Em leitura corrida, a
fala parece inofensiva e seria transformada diretamente em requisito. A IA sinalizou a incompatibilidade
categorial, o que gerou [Q-11](../02-analise/lacunas-e-ambiguidades.md) e a redação deliberadamente
diferente de RF-28.

**Explicitação de invariantes que ninguém pediu.** Nenhum stakeholder disse "o saldo não pode ser
negativo" nem "movimentação não pode ser editada". São regras óbvias para quem conhece o domínio e,
por isso mesmo, costumam ficar implícitas — e ausentes do sistema. Vieram de sugestão da IA e se
tornaram RN-01 e RN-07.

**Cobertura sistemática de exceções.** No fluxo de registro de saída, a listagem de sete condições de
falha (incluindo concorrência sobre o mesmo saldo) foi produzida por solicitação explícita de
"enumerar tudo que pode dar errado". É trabalho exaustivo e de baixa criatividade — exatamente o
perfil em que a IA supera a revisão humana em cobertura.

**Tradução de adjetivos em métrica.** "Rapidamente" e "de forma simples" viraram RNF-01, RNF-02,
RNF-19 e RNF-20, com critério de verificação. Os números são propostas, não medições — e estão
marcados como `A validar`.

### Onde a IA foi insuficiente

**Não substitui o stakeholder.** Nenhuma das 15 questões abertas foi resolvida pela IA; ela as
identificou e propôs premissas plausíveis. A diferença entre premissa plausível e requisito acordado é
todo o valor da engenharia de requisitos, e nenhuma ferramenta a elimina.

**Tende a inflar escopo.** Deixada livre, a IA propôs funcionalidades coerentes com o domínio e não
pedidas por ninguém — leitura de código de barras, valorização de estoque, curva ABC, gestão de
transportadoras. Foram cortadas ou registradas como `Won't`. A tabela da seção 3 de
[matriz-de-rastreabilidade.md](../02-analise/matriz-de-rastreabilidade.md) existe justamente para que
requisito sem origem tenha de justificar sua permanência.

**Confunde plausibilidade com validação.** As primeiras versões dos requisitos não funcionais traziam
números apresentados com aparência de acordo firmado. Foi necessário introduzir a coluna **Situação**
e o aviso de procedência no topo do documento, porque um número plausível e um número acordado se
parecem no papel e não se parecem em nada na homologação.

---

## 2. Sugestões aproveitadas

| Sugestão da IA | Onde está | Por que foi aceita |
|----------------|-----------|--------------------|
| Identificar cada fala da elicitação com código `E-nn` e cada observação com `O-nn` | [documento-de-elicitacao.md](../01-elicitacao/documento-de-elicitacao.md) | Sem isso, "origem do requisito" seria uma referência vaga a uma entrevista. Com isso, a rastreabilidade é verificável linha a linha. |
| Priorização MoSCoW nos requisitos funcionais | [requisitos-funcionais.md](../02-analise/requisitos-funcionais.md) | Delimita um MVP defensável, com 18 requisitos `Must`, em vez de uma lista plana de 40 itens de igual peso. |
| Separar requisitos funcionais de regras de negócio em artefatos distintos | `02-analise/` | Regra de negócio vive mais que tela e é reaproveitada por vários requisitos. Permite que os testes citem `RN-nn` (RNF-27). |
| Saldo nunca negativo e movimentação imutável como invariantes explícitas | RN-01, RN-07 | Nenhum stakeholder as pediu; são o núcleo da confiabilidade do sistema. Deixá-las implícitas resultaria em implementação inconsistente por tela. |
| Distinguir saldo físico de saldo disponível | RN-05, glossário | Resolve de forma limpa três situações que de outro modo colidiriam: lote vencido, inventário aberto e devolução não conferida. |
| Estorno por contra-lançamento em vez de edição | RN-07, RF-13, US-11 | Única forma de conciliar correção de erro com a exigência de auditoria de E-08. |
| Segregação de função na aprovação do inventário | RN-27, CA-18.2 | Controle interno clássico que nenhum stakeholder mencionou. Sem ele, quem conta aprova a própria contagem e o inventário perde a função de verificação. |
| Enumerar exceções antes dos caminhos normais nos fluxos complexos | [casos-de-uso.md](../03-especificacao/casos-de-uso.md) | Neste domínio as exceções são mais numerosas que os caminhos normais. A inversão de ordem mudou a qualidade do resultado. |
| Registrar tratamento de concorrência como exceção de requisito | UC-01 E5, CA-07.6 | É o defeito clássico desta classe de sistema. Deixá-lo para "detalhe de implementação" é como a planilha atual falha hoje. |
| Critérios de aceitação em Dado/Quando/Então | [criterios-de-aceitacao.md](../03-especificacao/criterios-de-aceitacao.md) | Formato executável por teste automatizado e legível pelo stakeholder ao mesmo tempo. |
| Coluna de critério de verificação obrigatória em todo RNF | [requisitos-nao-funcionais.md](../02-analise/requisitos-nao-funcionais.md) | RNF sem métrica é declaração de intenção. Com métrica, é requisito. |
| Ocultar o saldo do sistema durante a contagem de inventário | UC-02 passo 4, CA-17.4 | Insight de comportamento que nenhum requisito funcional expressaria. Exibir o saldo esperado transforma contagem em conferência de expectativa e produz inventário com acurácia aparente de 100%. |
| Glossário com marcação dos termos cujo sentido foi fixado pela análise | [glossario.md](../03-especificacao/glossario.md) | Torna visível quais definições ainda precisam de confirmação, em vez de naturalizá-las. |

---

## 3. Sugestões modificadas

| Sugestão original da IA | O que foi feito | Razão da modificação |
|--------------------------|-----------------|----------------------|
| Produzir casos de uso detalhados para **todos** os requisitos | Restrito a três fluxos: UC-01, UC-02 e UC-03 | Caso de uso agrega valor onde há múltiplos atores e muitas exceções. Para cadastro e consulta, duplicaria a história sem acrescentar informação — e dois documentos que descrevem a mesma coisa divergem na primeira mudança. |
| Apresentar os requisitos não funcionais como requisitos acordados | Adicionada coluna **Situação** (`A validar` / `Derivado`) e aviso de procedência no topo | A observação O-09 é explícita: nenhum RNF foi levantado. Apresentá-los como acordados seria atribuir aos stakeholders posições que eles não tomaram. |
| Fórmula de estoque mínimo apresentada como definição do negócio | Mantida como premissa explícita de RN-11, com sobreposição manual (RN-12) e parametrização por configuração (RNF-29) | O cálculo é razoável, mas Q-01 está aberta. A parametrização foi acrescentada porque a fórmula provavelmente mudará após a validação — projetar para essa mudança é mais barato que reimplementar. |
| Nível de disponibilidade de 99,9% | Reduzido a 99,5% na janela operacional (RNF-15) | 99,9% para um sistema interno de armazém que opera em janela comercial é sobredimensionamento com custo real de infraestrutura. Nenhum stakeholder pediu operação contínua. |
| Estoque mínimo e ponto de reposição como conceitos idênticos | Mantidos equivalentes nesta versão, mas com a distinção documentada no glossário e vinculada a Q-01 | A simplificação faz alerta e sugestão de compra coincidirem. Separá-los permitiria comprar antes da zona crítica, mas exige decisão do negócio. Registrar a limitação é mais honesto que escondê-la sob uma simplificação silenciosa. |
| Relatório de "produtos mais vendidos", conforme a fala do Gerente | Redigido como "produtos de maior movimentação de **saída**" (RF-28), com o conflito registrado em Q-11 | O sistema não conhece vendas. Escrever "mais vendidos" seria prometer o que a arquitetura especificada não entrega. |
| Portal do fornecedor como requisito de escopo normal | Mantido, mas classificado como `Could`, fora do MVP, com Q-13 aberta e RNF-10 exigindo teste de IDOR | A fala E-14 pede apenas ser informado. Um portal autenticado para ator externo altera substancialmente o perfil de risco do sistema — resposta desproporcional à necessidade expressa. |
| Diagrama de classes junto com os demais artefatos | Postergado, com justificativa registrada em [selecao-de-artefatos.md](selecao-de-artefatos.md) | Q-15 (depósito único ou múltiplo) e Q-03 (fator de conversão) alteram diretamente a estrutura de saldo. Modelar antes das respostas garante refazer. |
| Aprovação de inventário por qualquer Gerente | Acrescentada a exigência de aprovador distinto de quem contou (RN-27) | A sugestão inicial permitia que o mesmo usuário com perfil Gerente contasse e aprovasse, esvaziando o controle. |
| Sugestão de compra gerando pedido automaticamente | Exigida aprovação explícita do perfil Compras (RN-18, RF-20) | A fala E-12 pede "sugestões", não compra autônoma. Comprometer orçamento sem decisão humana extrapola o pedido. |

---

## 4. Sugestões descartadas

| Sugestão da IA | Motivo do descarte |
|----------------|--------------------|
| Documento único de requisitos no padrão IEEE 830 | Incompatível com 15 questões abertas: um documento monolítico com aprovação formal envelhece na primeira resposta de stakeholder. Os mesmos conteúdos estão modularizados e versionados por *commit*. |
| Modelagem BPMN dos processos de armazém | BPMN se paga quando há orquestração entre organizações ou áreas, com raias e eventos. Aqui os processos são curtos e internos; o diagrama de sequência de UC-02 cobre a necessidade com notação que a equipe lê sem treinamento. Reavaliável se Q-07 aprovar a integração. |
| Diagrama de casos de uso UML como visão geral | A matriz de permissões por perfil transmite a mesma informação com mais precisão. O diagrama mostraria que o Gerente aprova inventário, mas não que o aprovador deve ser distinto de quem contou — perderia exatamente a regra que importa. |
| Leitura por código de barras e coletor de dados móvel | Não elicitado. Faz sentido no volume descrito e por isso ficou registrado como RF-44 (`Won't`), candidato à segunda versão — mas incluí-lo agora seria decidir escopo no lugar do cliente. |
| Valorização de estoque, custo médio e integração contábil | Nenhuma entrevista menciona valor financeiro. Traria consigo regras fiscais e um universo de complexidade não pedido. |
| Curva ABC de produtos | Análise gerencial legítima, porém não solicitada. E-07 pede maior e menor movimentação, que RF-28 e RF-29 atendem. |
| Gestão de transportadoras e rastreio de entrega | Extrapola o domínio de controle de estoque. E-14 trata de prazo de entrega, não de logística. |
| Protótipo de alta fidelidade navegável | O ganho sobre o *wireframe* é de identidade visual e microinteração — decisões de design, não de requisitos, e sujeitas a refazer após a validação das premissas. |
| Requisitos de "escalabilidade horizontal para milhões de movimentações" | Q-09 não foi respondida: não se sabe o volume real. RNF-05 propõe uma linha de base modesta e explicitamente marcada como `A validar`. Dimensionar para milhões sem dado é adivinhação com custo. |
| Matriz CRUD por entidade e perfil | Redundante com a matriz de permissões de `regras-de-negocio.md`. Dois documentos com a mesma informação divergem na primeira alteração. |
| Chatbot para consulta de saldo por linguagem natural | Nenhuma relação com as necessidades elicitadas. Exemplo claro da tendência da ferramenta a propor o que é tecnicamente interessante em vez do que foi pedido. |

---

## 5. Balanço crítico

**O que a IA fez melhor que a revisão humana:** cobertura exaustiva. Enumerar as sete exceções do
fluxo de saída, cruzar 33 regras contra 40 requisitos em busca de regra órfã, verificar que as 14 falas
foram atendidas — trabalho sistemático e de baixa criatividade, no qual o cansaço humano produz
omissões.

**O que exigiu julgamento humano em todas as etapas:** decidir o que **não** fazer. Praticamente todas
as intervenções registradas na seção 3 e todos os itens da seção 4 são cortes, reduções de ambição ou
rebaixamentos de prioridade. A ferramenta é generosa por construção: sugere o que é plausível no
domínio, não o que é apropriado a este projeto, neste estágio de maturidade, com estas 15 questões
abertas.

**O risco central de usar IA em engenharia de requisitos:** ela produz texto que *parece* requisito
validado. Um número plausível de latência, uma fórmula razoável de estoque mínimo e uma regra sensata
de aprovação têm exatamente a mesma aparência de itens acordados com o cliente — e a diferença só
aparece na homologação. A resposta metodológica adotada foi marcar procedência em todos os artefatos:
a coluna **Situação** nos requisitos não funcionais, o marcador `(premissa)` nas regras de negócio, o
símbolo ⚠️ no glossário e um artefato dedicado às questões abertas.

**Conclusão prática:** a IA acelerou a produção e melhorou a cobertura, sobretudo na identificação de
conflitos e na enumeração de exceções. Não reduziu em nada a necessidade de conversar com os
stakeholders. As seis questões bloqueantes de
[lacunas-e-ambiguidades.md](../02-analise/lacunas-e-ambiguidades.md) continuam exigindo uma reunião —
e é justamente essa reunião, não a documentação produzida aqui, que determinará se o projeto resolve
o problema que motivou sua existência.
