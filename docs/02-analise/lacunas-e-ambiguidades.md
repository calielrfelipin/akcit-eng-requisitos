# Lacunas, Ambiguidades e Conflitos — Sistema de Controle de Estoque (StockPlus)

Registro das questões abertas identificadas na análise de
[documento-de-elicitacao.md](../01-elicitacao/documento-de-elicitacao.md). Enquanto uma questão não
for respondida pelo stakeholder, a especificação avança sob a **premissa** aqui declarada — o que
mantém o trabalho em movimento sem esconder a dívida.

**Classificação:**

| Tipo | Significado |
|------|-------------|
| `Lacuna` | Informação necessária que simplesmente não foi coletada. |
| `Ambiguidade` | Informação coletada, mas com mais de uma leitura possível. |
| `Conflito` | Duas informações da elicitação que não podem ser ambas verdadeiras. |
| `Risco` | Ponto que não bloqueia a especificação, mas pode inviabilizar a solução se ignorado. |

**Impacto:** `Alto` = bloqueia implementação de requisito `Must`; `Médio` = afeta requisito
`Should`; `Baixo` = afeta requisito `Could` ou apenas a experiência.

---

## 1. Questões originadas das observações da elicitação

### Q-01 — Cálculo do estoque mínimo

| Campo | Conteúdo |
|-------|----------|
| **Origem** | O-01 |
| **Tipo** | Lacuna |
| **Impacto** | Alto — RF-15 e RF-18 são `Must` e não têm parâmetro de disparo |
| **Pergunta ao stakeholder** | O estoque mínimo é um valor fixo definido pelo comprador, ou deve ser calculado a partir do histórico de consumo? Se calculado, qual a janela de histórico e a margem de segurança aceitável? O prazo de entrega é cadastrado por fornecedor ou por produto? |
| **Premissa adotada** | Fórmula de RN-11: `consumo médio diário (90 dias) × prazo de entrega × 1,20`, com possibilidade de sobreposição manual pelo Gerente (RN-12). A fórmula é parametrizável por configuração (RNF-29). |
| **Responsável** | Setor de Compras + Gerência |

### Q-02 — Quem pode ajustar estoque manualmente

| Campo | Conteúdo |
|-------|----------|
| **Origem** | O-02 |
| **Tipo** | Lacuna |
| **Impacto** | Alto — define o modelo de permissões (RF-25, RF-33) |
| **Pergunta ao stakeholder** | O ajuste manual é privativo do Gerente, ou o Estoquista pode registrá-lo sujeito a aprovação posterior? Existe limite de quantidade ou de valor acima do qual o ajuste exige aprovação de nível superior? |
| **Premissa adotada** | RN-25: exclusivo do perfil Gerente, sempre com justificativa. Não há alçada por valor nesta versão. |
| **Responsável** | Gerência |
| **Observação** | Se o volume de ajustes for alto, concentrá-los no Gerente pode criar gargalo operacional. Vale medir a frequência real antes de fixar a regra. |

### Q-03 — Produtos com diferentes unidades de medida

| Campo | Conteúdo |
|-------|----------|
| **Origem** | O-03 |
| **Tipo** | Lacuna |
| **Impacto** | Médio — RF-04 é `Should`, mas erro de conversão corrompe o saldo (RN-01) |
| **Pergunta ao stakeholder** | Existem produtos comprados em uma unidade e vendidos em outra (por exemplo, caixa na entrada e unidade na saída)? O fator de conversão é estável ao longo do tempo ou varia por fornecedor/lote? Há produtos de medida fracionada (peso, volume)? |
| **Premissa adotada** | RN-19: uma unidade base por produto, com unidades alternativas de fator fixo. Fator imutável após uso (RN-20). |
| **Responsável** | Estoquistas + Setor de Compras |
| **Observação** | Fator variável por fornecedor invalidaria RN-19 e exigiria remodelagem. É a premissa de maior risco técnico desta lista. |

### Q-04 — Processo de inventário físico

| Campo | Conteúdo |
|-------|----------|
| **Origem** | O-04 |
| **Tipo** | Lacuna |
| **Impacto** | Alto — RF-21 e RF-23 são `Must` |
| **Pergunta ao stakeholder** | O inventário é geral (parada total) ou rotativo por categoria/localização? Qual a periodicidade? A operação pode ser interrompida durante a contagem? Haverá dupla contagem em caso de divergência? |
| **Premissa adotada** | Inventário por conjunto de produtos ou localizações, com bloqueio de movimentação apenas dos itens envolvidos (RN-26) e aprovação por Gerente distinto de quem contou (RN-27). Sem dupla contagem nesta versão. |
| **Responsável** | Gerência + Estoquistas |

### Q-05 — Controle por lote e validade

| Campo | Conteúdo |
|-------|----------|
| **Origem** | O-05 |
| **Tipo** | Lacuna |
| **Impacto** | Médio — condiciona RF-05, mas afeta o modelo de dados do saldo |
| **Pergunta ao stakeholder** | Que proporção do catálogo é perecível? O controle de validade é exigência legal ou sanitária para algum item? A política de saída deve ser FEFO, FIFO ou livre? |
| **Premissa adotada** | Controle por lote e validade é opcional por produto (RF-05). Quando ativo, a saída segue FEFO com desvio justificado (RN-22) e lote vencido é bloqueado (RN-23). |
| **Responsável** | Gerência + Estoquistas |
| **Observação** | E-05 cita perda por vencimento, o que indica que produtos perecíveis existem. Isso sugere que o controle não é meramente opcional na prática. |

### Q-06 — Devoluções de cliente e a fornecedor

| Campo | Conteúdo |
|-------|----------|
| **Origem** | O-06 |
| **Tipo** | Lacuna |
| **Impacto** | Médio — RF-14 é `Should` |
| **Pergunta ao stakeholder** | Produto devolvido por cliente retorna ao estoque vendável, vai para área de quarentena ou é descartado? Quem decide? A devolução a fornecedor precisa referenciar a nota fiscal de entrada? |
| **Premissa adotada** | RN-29: devolução de cliente entra como tipo próprio e só compõe o saldo disponível após conferência; item avariado vira perda. RN-30: devolução a fornecedor referencia o pedido de compra. |
| **Responsável** | Gerência + Setor de Compras |

### Q-07 — Integração com vendas e compras

| Campo | Conteúdo |
|-------|----------|
| **Origem** | O-07 |
| **Tipo** | Risco |
| **Impacto** | Alto — determina se o saldo será confiável na prática |
| **Pergunta ao stakeholder** | Existe hoje sistema de vendas ou ERP em uso? Se sim, a baixa de estoque por venda continuará sendo lançada manualmente? Qual o volume diário de vendas? |
| **Premissa adotada** | Sem integração nesta versão (RF-41, RF-42 classificados como `Won't`). Todas as saídas são lançadas manualmente. Para não fechar a porta, RNF-30 exige API documentada de saldos e movimentações. |
| **Responsável** | Equipe de TI + Gerência |
| **Observação** | Esta é a maior ameaça ao objetivo do projeto. Se as vendas ocorrem em outro sistema e a baixa depende de digitação manual, o saldo divergirá exatamente como divergia na planilha — o problema P1 da elicitação não seria resolvido. Recomenda-se tratar como decisão prioritária, antes do início do desenvolvimento. |

### Q-08 — Regras de notificação

| Campo | Conteúdo |
|-------|----------|
| **Origem** | O-08 |
| **Tipo** | Lacuna |
| **Impacto** | Médio — RF-15/RF-16 são `Must`, mas o canal (RF-17) é `Should` |
| **Pergunta ao stakeholder** | Por qual canal os alertas devem chegar: painel, e-mail, mensageria? Quem deve ser notificado em cada caso? Qual frequência é aceitável sem se tornar ruído? |
| **Premissa adotada** | Painel sempre atualizado mais e-mail para Gerente e Compras (RF-17), com supressão de 24 h para estoque baixo (RN-14) e envio imediato para ruptura (RN-16). |
| **Responsável** | Setor de Compras + Gerência |

### Q-09 — Requisitos não funcionais ausentes

| Campo | Conteúdo |
|-------|----------|
| **Origem** | O-09 |
| **Tipo** | Lacuna |
| **Impacto** | Alto — atinge todo o conjunto de requisitos não funcionais |
| **Pergunta ao stakeholder** | Quantos usuários simultâneos e quantas movimentações diárias são esperados? Qual a janela operacional do armazém? Há exigência legal de retenção de histórico? O sistema será acessado fora da rede corporativa? |
| **Premissa adotada** | Todo o conteúdo de [requisitos-nao-funcionais.md](requisitos-nao-funcionais.md), marcado item a item como `A validar` ou `Derivado`. |
| **Responsável** | Equipe de TI + Gerência |
| **Observação** | A Equipe de TI é o único stakeholder listado que não foi entrevistado, e é justamente o detentor da informação faltante. A lacuna é consequência direta da cobertura incompleta da elicitação. |

---

## 2. Ambiguidades e conflitos identificados na linguagem das entrevistas

Estes itens não constavam das observações originais: emergiram da análise das falas.

### Q-10 — "Estoque em tempo real" (E-06)

| Campo | Conteúdo |
|-------|----------|
| **Tipo** | Ambiguidade |
| **Impacto** | Médio |
| **Problema** | "Tempo real" admite leituras muito distintas: atualização imediata a cada lançamento, painel com atualização automática por *push*, ou simplesmente "sem esperar o fechamento do dia". Cada leitura implica arquitetura e custo diferentes. |
| **Pergunta ao stakeholder** | Qual atraso é tolerável entre o lançamento de uma movimentação e a sua visibilidade no painel: segundos, minutos ou apenas "antes do próximo turno"? |
| **Premissa adotada** | Interpretação conservadora: saldo consistente por consulta, com latência máxima de 5 s (RNF-03) e atualização a cada carregamento de tela (RF-27). Não há *push* automático nesta versão. |

### Q-11 — "Produtos mais vendidos" em um sistema de estoque (E-07)

| Campo | Conteúdo |
|-------|----------|
| **Tipo** | Conflito |
| **Impacto** | Médio |
| **Problema** | O Gerente pede relatório de **produtos mais vendidos** (E-07), mas o sistema especificado não conhece vendas — conhece **saídas de estoque**, que também incluem consumo interno, transferências e devoluções a fornecedor. Sem integração com vendas (Q-07), "mais vendido" não é calculável; "maior saída" é. |
| **Pergunta ao stakeholder** | O relatório deve refletir vendas (exigindo dado de faturamento) ou é suficiente medir saídas de estoque por tipo de movimentação? |
| **Premissa adotada** | RF-28 foi redigido como "produtos de maior movimentação de **saída**", não "mais vendidos". A troca de termo é intencional e precisa ser confirmada com o Gerente. |

### Q-12 — "Localizar itens no estoque" (E-01)

| Campo | Conteúdo |
|-------|----------|
| **Tipo** | Ambiguidade |
| **Impacto** | Médio |
| **Problema** | A fala mistura duas necessidades: *encontrar o registro do produto no sistema* (busca por nome/código) e *encontrar o produto fisicamente no armazém* (endereçamento). A segunda pressupõe um cadastro de localizações que nunca foi mencionado. |
| **Pergunta ao stakeholder** | O armazém possui endereçamento formal (rua, prateleira, nível)? Um produto pode ocupar mais de uma localização simultaneamente? |
| **Premissa adotada** | RF-03 prevê uma localização única e textual por produto. Endereçamento múltiplo e controle de saldo por localização estão fora do escopo desta versão. |

### Q-13 — Fornecedor como usuário do sistema (E-14)

| Campo | Conteúdo |
|-------|----------|
| **Tipo** | Ambiguidade |
| **Impacto** | Baixo (escopo) / Alto (segurança) |
| **Problema** | "Seria útil receber informações sobre pedidos" pode significar acesso autenticado a um portal externo ou simplesmente receber um e-mail com a confirmação do pedido. A primeira opção introduz um usuário externo à rede corporativa e altera substancialmente o perfil de risco do sistema. |
| **Pergunta ao stakeholder** | O fornecedor deve acessar o sistema, ou basta receber notificação por e-mail com os dados do pedido? |
| **Premissa adotada** | RF-39 mantém a consulta autenticada, mas classificada como `Could` — fora do MVP. RN-33 e RNF-10 isolam o acesso. A alternativa por e-mail é preferível se a resposta for "basta ser informado". |

### Q-14 — "De forma simples" e "rapidamente" (E-01, E-02)

| Campo | Conteúdo |
|-------|----------|
| **Tipo** | Ambiguidade |
| **Impacto** | Baixo |
| **Problema** | Adjetivos sem métrica não são verificáveis e não podem constituir critério de aceitação. |
| **Premissa adotada** | Traduzidos em requisitos mensuráveis: RNF-01 (busca em até 2 s), RNF-02 (movimentação em até 3 s), RNF-19 (máximo de 5 interações) e RNF-20 (teste de usabilidade com 90% de sucesso). |

### Q-15 — Ausência de tratamento para transferência entre locais

| Campo | Conteúdo |
|-------|----------|
| **Tipo** | Lacuna |
| **Impacto** | Baixo |
| **Problema** | A elicitação menciona varejo **e** atacado, o que sugere possibilidade de mais de um depósito ou de segregação de estoque por canal. Nenhuma fala trata de transferência entre locais, e nenhum requisito a prevê. |
| **Pergunta ao stakeholder** | Existe mais de um depósito físico? O estoque de varejo e de atacado é o mesmo pool ou é segregado? |
| **Premissa adotada** | Depósito único, estoque não segregado por canal. Se a resposta for negativa, o modelo de saldo precisará de dimensão "local", com impacto em RF-10, RN-01 e RN-03. |

---

## 3. Consolidado e priorização das decisões pendentes

| ID | Tema | Tipo | Impacto | Bloqueia o MVP? |
|----|------|------|---------|:---------------:|
| Q-07 | Integração com vendas/compras | Risco | Alto | **Sim** |
| Q-01 | Cálculo do estoque mínimo | Lacuna | Alto | **Sim** |
| Q-02 | Autorização de ajuste manual | Lacuna | Alto | **Sim** |
| Q-04 | Processo de inventário | Lacuna | Alto | **Sim** |
| Q-09 | Requisitos não funcionais | Lacuna | Alto | **Sim** |
| Q-15 | Múltiplos depósitos | Lacuna | Baixo | **Sim** (modelo de dados) |
| Q-03 | Unidades de medida | Lacuna | Médio | Não |
| Q-05 | Lote e validade | Lacuna | Médio | Não |
| Q-06 | Devoluções | Lacuna | Médio | Não |
| Q-08 | Regras de notificação | Lacuna | Médio | Não |
| Q-10 | "Tempo real" | Ambiguidade | Médio | Não |
| Q-11 | "Mais vendidos" vs. saídas | Conflito | Médio | Não |
| Q-12 | Localização física | Ambiguidade | Médio | Não |
| Q-13 | Fornecedor como usuário | Ambiguidade | Baixo | Não |
| Q-14 | Adjetivos não mensuráveis | Ambiguidade | Baixo | Não |

**Recomendação:** convocar uma sessão de validação com Gerência, Compras e Equipe de TI para
resolver as seis questões bloqueantes antes do início da Sprint 1. As nove restantes podem ser
resolvidas durante o refinamento das respectivas histórias, desde que as premissas permaneçam
visíveis no *backlog*.

**Q-15 merece atenção especial:** apesar do impacto classificado como baixo em termos de
funcionalidade, é a única questão cuja resposta negativa exige alterar o modelo de dados do saldo
depois de construído — o tipo de retrabalho mais caro. Deve ser respondida antes da primeira linha
de código, mesmo que a funcionalidade fique para depois.
