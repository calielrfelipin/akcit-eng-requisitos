# Histórias de Usuário — Sistema de Controle de Estoque (StockPlus)

*Backlog* de produto refinado, derivado de [requisitos-funcionais.md](../02-analise/requisitos-funcionais.md)
e governado por [regras-de-negocio.md](../02-analise/regras-de-negocio.md).

**Formato:** `Como <papel>, quero <ação>, para <benefício>`.
**Estimativa:** *story points* em escala de Fibonacci (1, 2, 3, 5, 8, 13).
**Critérios de aceitação:** detalhados em [criterios-de-aceitacao.md](criterios-de-aceitacao.md).

---

## EP-01 — Catálogo de produtos

### US-01 — Cadastrar produto
> Como **Gerente**, quero cadastrar um produto com código, nome, unidade de medida base, categoria e
> estoque mínimo, para que o item passe a ser controlado pelo sistema.

| Prioridade | Pontos | RFs | RNs |
|---|---|---|---|
| Must | 5 | RF-01 | RN-10 |

### US-02 — Localizar produto
> Como **Estoquista**, quero buscar um produto pelo nome parcial ou pelo código e ver seu saldo e sua
> localização física, para encontrá-lo no armazém sem consultar planilhas.

| Prioridade | Pontos | RFs | RNs |
|---|---|---|---|
| Must | 3 | RF-02, RF-03 | — |

### US-03 — Cadastrar unidades alternativas
> Como **Estoquista**, quero registrar movimentações em uma unidade diferente da unidade base do
> produto, para lançar caixas na entrada e unidades na saída sem calcular a conversão de cabeça.

| Prioridade | Pontos | RFs | RNs |
|---|---|---|---|
| Should | 5 | RF-04 | RN-19, RN-20 |

> ⚠️ Depende da resposta a [Q-03](../02-analise/lacunas-e-ambiguidades.md). Se o fator de conversão
> variar por fornecedor, a história precisa ser reescrita.

### US-04 — Controlar lote e validade
> Como **Gerente**, quero marcar um produto como controlado por lote e validade, para que o sistema
> impeça a saída de itens vencidos e priorize os de validade mais próxima.

| Prioridade | Pontos | RFs | RNs |
|---|---|---|---|
| Should | 8 | RF-05 | RN-21, RN-22, RN-23 |

### US-05 — Inativar produto
> Como **Gerente**, quero inativar um produto que saiu de linha, para que ele deixe de aceitar
> movimentações sem perder o histórico já registrado.

| Prioridade | Pontos | RFs | RNs |
|---|---|---|---|
| Must | 2 | RF-06 | RN-09 |

---

## EP-02 — Movimentações de estoque

### US-06 — Registrar entrada de mercadoria
> Como **Estoquista**, quero registrar a entrada de mercadoria informando produto, quantidade e
> documento de origem, para que o saldo reflita o recebimento imediatamente.

| Prioridade | Pontos | RFs | RNs |
|---|---|---|---|
| Must | 5 | RF-07 | RN-04, RN-06, RN-21 |

### US-07 — Registrar saída de mercadoria
> Como **Estoquista**, quero registrar a saída de mercadoria informando produto, quantidade e motivo,
> para que o saldo seja baixado e a operação fique rastreada.

| Prioridade | Pontos | RFs | RNs |
|---|---|---|---|
| Must | 8 | RF-08 | RN-01, RN-02, RN-04, RN-22, RN-23 |

### US-08 — Registrar perda
> Como **Estoquista**, quero registrar perda por vencimento, avaria ou extravio com justificativa,
> para que a redução do estoque seja explicada e não apareça como divergência de inventário.

| Prioridade | Pontos | RFs | RNs |
|---|---|---|---|
| Must | 3 | RF-09 | RN-24 |

### US-09 — Consultar saldo atual
> Como **Gerente**, quero consultar o saldo atual de qualquer produto, para tomar decisões sem
> esperar o fechamento do dia.

| Prioridade | Pontos | RFs | RNs |
|---|---|---|---|
| Must | 3 | RF-10 | RN-03, RN-05 |

### US-10 — Consultar histórico de movimentações
> Como **Estoquista**, quero consultar o histórico de movimentações de um produto com filtro por
> período e tipo, para entender como o saldo atual foi formado.

| Prioridade | Pontos | RFs | RNs |
|---|---|---|---|
| Must | 5 | RF-11, RF-12 | RN-06 |

### US-11 — Estornar movimentação incorreta
> Como **Estoquista**, quero estornar uma movimentação lançada por engano, para corrigir o saldo sem
> apagar o registro original.

| Prioridade | Pontos | RFs | RNs |
|---|---|---|---|
| Must | 5 | RF-13 | RN-07 |

### US-12 — Registrar devoluções
> Como **Estoquista**, quero registrar devolução de cliente e devolução a fornecedor como
> movimentações de tipo próprio, para que elas não se confundam com compras e vendas nos relatórios.

| Prioridade | Pontos | RFs | RNs |
|---|---|---|---|
| Should | 5 | RF-14 | RN-29, RN-30 |

---

## EP-03 — Alertas e reposição

### US-13 — Ser alertado sobre estoque baixo
> Como **Comprador**, quero ser alertado quando um produto atingir o estoque mínimo, para repor antes
> que falte.

| Prioridade | Pontos | RFs | RNs |
|---|---|---|---|
| Must | 5 | RF-15, RF-17 | RN-13, RN-14, RN-15 |

### US-14 — Ser notificado sobre ruptura
> Como **Gerente**, quero ser notificado imediatamente quando um produto zerar, para acionar a
> reposição de urgência.

| Prioridade | Pontos | RFs | RNs |
|---|---|---|---|
| Must | 3 | RF-16 | RN-16 |

### US-15 — Obter estoque mínimo sugerido
> Como **Comprador**, quero que o sistema sugira o estoque mínimo de cada produto a partir do consumo
> médio e do prazo de entrega, para não depender da minha memória sobre cada item.

| Prioridade | Pontos | RFs | RNs |
|---|---|---|---|
| Must | 8 | RF-18 | RN-11, RN-12 |

> ⚠️ Depende de [Q-01](../02-analise/lacunas-e-ambiguidades.md).

### US-16 — Aprovar sugestão de compra
> Como **Comprador**, quero receber sugestões de compra com quantidade calculada e poder aprová-las,
> editá-las ou rejeitá-las, para agilizar a reposição mantendo a decisão sob meu controle.

| Prioridade | Pontos | RFs | RNs |
|---|---|---|---|
| Should | 8 | RF-19, RF-20 | RN-17, RN-18 |

---

## EP-04 — Inventário e ajustes

### US-17 — Realizar contagem de inventário
> Como **Estoquista**, quero registrar a contagem física de um conjunto de produtos e ver a
> divergência apurada, para conferir o estoque real contra o sistema.

| Prioridade | Pontos | RFs | RNs |
|---|---|---|---|
| Must | 8 | RF-21, RF-22 | RN-26 |

> ⚠️ Depende de [Q-04](../02-analise/lacunas-e-ambiguidades.md).

### US-18 — Aprovar e encerrar inventário
> Como **Gerente**, quero aprovar o inventário para que as divergências se convertam em ajustes
> rastreáveis, e ver a acurácia da contagem.

| Prioridade | Pontos | RFs | RNs |
|---|---|---|---|
| Must | 5 | RF-23, RF-26 | RN-27, RN-28 |

### US-19 — Registrar ajuste manual
> Como **Gerente**, quero registrar um ajuste manual de estoque com justificativa obrigatória, para
> corrigir divergências pontuais sem abrir um inventário.

| Prioridade | Pontos | RFs | RNs |
|---|---|---|---|
| Must | 3 | RF-24, RF-25 | RN-25 |

---

## EP-05 — Relatórios e indicadores

### US-20 — Acompanhar o painel de estoque
> Como **Gerente**, quero um painel com saldo consolidado, produtos em alerta e produtos em ruptura,
> para ter a situação do estoque em uma única tela.

| Prioridade | Pontos | RFs | RNs |
|---|---|---|---|
| Must | 5 | RF-27 | — |

### US-21 — Analisar giro de produtos
> Como **Gerente**, quero relatórios de produtos com maior movimentação de saída e de produtos sem
> saída em um período, para identificar itens de alto giro e capital parado.

| Prioridade | Pontos | RFs | RNs |
|---|---|---|---|
| Should | 5 | RF-28, RF-29 | — |

> ⚠️ Depende de [Q-11](../02-analise/lacunas-e-ambiguidades.md): o relatório mede **saídas**, não
> vendas.

### US-22 — Analisar perdas
> Como **Gerente**, quero um relatório de perdas por categoria e período, para agir sobre a causa
> predominante.

| Prioridade | Pontos | RFs | RNs |
|---|---|---|---|
| Could | 3 | RF-30 | RN-24 |

### US-23 — Exportar relatórios
> Como **Gerente**, quero exportar relatórios em CSV e PDF, para levá-los à reunião de diretoria.

| Prioridade | Pontos | RFs | RNs |
|---|---|---|---|
| Could | 3 | RF-31 | — |

---

## EP-06 — Acesso, perfis e auditoria

### US-24 — Autenticar com credencial individual
> Como **Administrador de TI**, quero que cada usuário tenha credencial nominal e individual, para
> que toda movimentação possa ser atribuída a uma pessoa.

| Prioridade | Pontos | RFs | RNs |
|---|---|---|---|
| Must | 5 | RF-32 | RN-08 |

### US-25 — Gerenciar perfis e permissões
> Como **Administrador de TI**, quero atribuir aos usuários os perfis Estoquista, Gerente, Compras,
> Fornecedor ou Administrador, para que cada um acesse apenas as operações que lhe cabem.

| Prioridade | Pontos | RFs | RNs |
|---|---|---|---|
| Must | 8 | RF-33 | RN-25, RN-27, RN-33 |

### US-26 — Consultar trilha de auditoria
> Como **Gerente**, quero consultar a trilha de auditoria filtrando por usuário, produto e período,
> para investigar divergências e responsabilizar corretamente.

| Prioridade | Pontos | RFs | RNs |
|---|---|---|---|
| Should | 5 | RF-34, RF-35 | RN-06, RN-07 |

---

## EP-07 — Compras e fornecedores

### US-27 — Registrar pedido de compra
> Como **Comprador**, quero registrar um pedido de compra com fornecedor, itens e prazo de entrega
> previsto, para acompanhar o que está por chegar.

| Prioridade | Pontos | RFs | RNs |
|---|---|---|---|
| Should | 5 | RF-36 | RN-31, RN-32 |

### US-28 — Dar entrada em pedido de compra
> Como **Estoquista**, quero dar entrada total ou parcial em um pedido de compra, para que o saldo e
> o status do pedido sejam atualizados em uma única operação.

| Prioridade | Pontos | RFs | RNs |
|---|---|---|---|
| Should | 5 | RF-38, RF-07 | RN-31, RN-32 |

### US-29 — Consultar histórico de compras
> Como **Comprador**, quero consultar o histórico de compras de um produto com fornecedor, data,
> quantidade e preço, para negociar com base em dados.

| Prioridade | Pontos | RFs | RNs |
|---|---|---|---|
| Should | 3 | RF-37 | — |

### US-30 — Consultar pedidos como fornecedor
> Como **Fornecedor**, quero consultar os pedidos emitidos para mim e seus prazos, para me organizar
> sem depender de ligações.

| Prioridade | Pontos | RFs | RNs |
|---|---|---|---|
| Could | 8 | RF-39 | RN-33 |

> ⚠️ Depende de [Q-13](../02-analise/lacunas-e-ambiguidades.md). Se bastar notificação por e-mail, a
> história deve ser substituída por uma versão de custo e risco muito menores.

### US-31 — Ser avisado de pedido atrasado
> Como **Comprador**, quero ser avisado quando um pedido passar do prazo de entrega sem recebimento,
> para cobrar o fornecedor.

| Prioridade | Pontos | RFs | RNs |
|---|---|---|---|
| Could | 3 | RF-40 | RN-32 |

---

## Consolidado do *backlog*

| Prioridade | Histórias | Pontos |
|------------|----------:|-------:|
| Must | 17 | 82 |
| Should | 8 | 44 |
| Could | 6 | 28 |
| **Total** | **31** | **154** |

---

## Proposta de sequenciamento

A ordem abaixo não é apenas priorização por valor: respeita dependências técnicas. Nenhuma história
de movimentação pode ser aceita antes de existir autenticação, porque RN-06 exige autor identificado.

| Sprint | Objetivo | Histórias | Pontos |
|--------|----------|-----------|-------:|
| 1 | Fundação: identidade, permissão e catálogo | US-24, US-25, US-01, US-02, US-05 | 23 |
| 2 | Núcleo transacional: o saldo passa a existir | US-06, US-07, US-08, US-09, US-11 | 24 |
| 3 | Visibilidade e alerta | US-10, US-13, US-14, US-15, US-20 | 26 |
| 4 | Reconciliação com o mundo físico | US-17, US-18, US-19, US-26 | 21 |
| 5 | Reposição e compras | US-16, US-27, US-28, US-29 | 21 |
| 6 | Refinamentos condicionados a validação | US-03, US-04, US-12, US-21 | 23 |

**Marco de MVP:** fim da Sprint 4. Nesse ponto o sistema já substitui a planilha de forma auditável
— cadastra, movimenta com autoria, exibe saldo confiável, alerta e reconcilia por inventário. As
Sprints 5 e 6 agregam valor, mas o problema central (P1, P2 e P3 da elicitação) já está endereçado.

**Condição de entrada da Sprint 1:** as seis questões bloqueantes de
[lacunas-e-ambiguidades.md](../02-analise/lacunas-e-ambiguidades.md) devem estar respondidas —
sobretudo **Q-15** (depósito único) e **Q-07** (integração com vendas), cujas respostas afetam o
modelo de dados de saldo construído já na Sprint 2.
