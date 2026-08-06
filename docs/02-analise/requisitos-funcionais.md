# Requisitos Funcionais — Sistema de Controle de Estoque (StockPlus)

Requisitos derivados de [documento-de-elicitacao.md](../01-elicitacao/documento-de-elicitacao.md).
A coluna **Origem** aponta a fala (`E-nn`) ou observação (`O-nn`) que sustenta o requisito. Requisitos
sem origem direta na elicitação estão marcados como `Derivado` e a razão da derivação é explicitada.

**Priorização:** MoSCoW — `Must` (indispensável ao MVP), `Should` (importante, mas postergável),
`Could` (desejável), `Won't` (fora do escopo desta versão).

---

## 1. Épicos

| Épico | Nome | Objetivo |
|-------|------|----------|
| EP-01 | Catálogo de produtos | Ter um cadastro único e confiável dos itens controlados |
| EP-02 | Movimentações de estoque | Registrar toda entrada, saída e perda com rastro de autoria |
| EP-03 | Alertas e reposição | Antecipar ruptura e apoiar a decisão de compra |
| EP-04 | Inventário e ajustes | Reconciliar saldo do sistema com a contagem física |
| EP-05 | Relatórios e indicadores | Dar visibilidade gerencial sobre giro e níveis de estoque |
| EP-06 | Acesso, perfis e auditoria | Garantir que cada ação seja autorizada e atribuível |
| EP-07 | Compras e fornecedores | Acompanhar pedidos e prazos de entrega |

---

## 2. Requisitos funcionais

### EP-01 — Catálogo de produtos

| ID | Requisito | Origem | Prioridade |
|----|-----------|--------|------------|
| RF-01 | O sistema deve permitir cadastrar, editar e inativar produtos, contendo no mínimo código (SKU), nome, descrição, unidade de medida base, categoria e estoque mínimo. | Derivado (pré-condição de E-01 a E-14) | Must |
| RF-02 | O sistema deve permitir buscar produtos por nome (parcial, sem distinção de acento/caixa) e por código exato. | E-01 | Must |
| RF-03 | O sistema deve registrar a localização física (endereço de armazenagem) de cada produto e exibi-la no resultado da busca. | E-01 ("localizar itens no estoque") | Should |
| RF-04 | O sistema deve permitir associar a um produto uma unidade de medida base e unidades alternativas com fator de conversão. | O-03 | Should |
| RF-05 | O sistema deve permitir marcar um produto como controlado por lote e/ou por validade, habilitando o registro de lote e data de validade nas movimentações. | O-05 | Should |
| RF-06 | O sistema deve impedir a exclusão física de produto que possua movimentações, oferecendo apenas inativação. | Derivado (integridade do histórico exigido por E-04) | Must |

### EP-02 — Movimentações de estoque

| ID | Requisito | Origem | Prioridade |
|----|-----------|--------|------------|
| RF-07 | O sistema deve permitir registrar entrada de mercadoria informando produto, quantidade, unidade, data e documento de origem, opcionalmente vinculada a um pedido de compra. | E-02 | Must |
| RF-08 | O sistema deve permitir registrar saída de mercadoria informando produto, quantidade, unidade, data e motivo da saída. | E-02 | Must |
| RF-09 | O sistema deve permitir registrar perda classificada em vencimento, avaria ou extravio, com justificativa textual obrigatória. | E-05 | Must |
| RF-10 | O sistema deve calcular e exibir o saldo atual de cada produto após cada movimentação, sem necessidade de processamento em lote. | E-06 | Must |
| RF-11 | O sistema deve exibir o histórico de movimentações de um produto, com filtro por período e por tipo de movimentação, apresentando o saldo resultante de cada lançamento. | E-04 | Must |
| RF-12 | O sistema deve registrar, em toda movimentação, o usuário autor, a data e a hora do lançamento. | E-08 | Must |
| RF-13 | O sistema deve impedir a alteração e a exclusão de movimentações já registradas, permitindo correção apenas por lançamento de estorno vinculado ao original. | E-08 + O-02 | Must |
| RF-14 | O sistema deve permitir registrar devolução de cliente como entrada de tipo próprio e devolução a fornecedor como saída vinculada ao pedido de compra correspondente. | O-06 | Should |

### EP-03 — Alertas e reposição

| ID | Requisito | Origem | Prioridade |
|----|-----------|--------|------------|
| RF-15 | O sistema deve gerar alerta quando o saldo de um produto atingir ou ficar abaixo do estoque mínimo. | E-03, E-11 | Must |
| RF-16 | O sistema deve gerar notificação de ruptura de estoque quando o saldo de um produto chegar a zero. | O-08 | Must |
| RF-17 | O sistema deve exibir os alertas em painel dentro da aplicação e enviá-los por e-mail aos perfis Gerente e Compras. | E-11 + O-08 | Should |
| RF-18 | O sistema deve calcular o estoque mínimo de cada produto a partir do consumo médio diário, do prazo de entrega do fornecedor e de uma margem de segurança, permitindo sobreposição manual do valor. | O-01 | Must |
| RF-19 | O sistema deve gerar sugestão de compra para produtos abaixo do ponto de reposição, informando a quantidade sugerida e descontando pedidos já em trânsito. | E-12 | Should |
| RF-20 | O sistema deve permitir que o Setor de Compras aprove, edite ou rejeite cada sugestão de compra antes de gerar um pedido. | Derivado (E-12 — a sugestão não pode virar compra automática) | Should |

### EP-04 — Inventário e ajustes

| ID | Requisito | Origem | Prioridade |
|----|-----------|--------|------------|
| RF-21 | O sistema deve permitir abrir um inventário para um conjunto de produtos ou localizações, registrar a quantidade contada e calcular a divergência em relação ao saldo do sistema. | E-09, O-04 | Must |
| RF-22 | O sistema deve bloquear novas movimentações dos produtos incluídos em um inventário aberto até seu encerramento. | O-04 | Should |
| RF-23 | O sistema deve exigir aprovação de um Gerente para encerrar o inventário e converter as divergências em ajustes de estoque. | E-09, E-10, O-02 | Must |
| RF-24 | O sistema deve permitir registrar ajuste manual de estoque com justificativa textual obrigatória e categoria do ajuste. | E-10 | Must |
| RF-25 | O sistema deve restringir o registro de ajuste manual ao perfil Gerente. | O-02 | Must |
| RF-26 | O sistema deve apresentar o indicador de acurácia do inventário (percentual de itens sem divergência) ao final de cada contagem. | Derivado (E-09 — conferir sem medir não fecha o ciclo) | Could |

### EP-05 — Relatórios e indicadores

| ID | Requisito | Origem | Prioridade |
|----|-----------|--------|------------|
| RF-27 | O sistema deve apresentar painel com o saldo atual consolidado, produtos em alerta e produtos em ruptura, atualizado a cada carregamento da tela. | E-06 | Must |
| RF-28 | O sistema deve gerar relatório de produtos de maior movimentação de saída em um período, ordenado por quantidade. | E-07 | Should |
| RF-29 | O sistema deve gerar relatório de produtos de baixa movimentação, listando itens sem saída em um período parametrizável. | E-07 | Should |
| RF-30 | O sistema deve gerar relatório de perdas por categoria e por período. | E-05 | Could |
| RF-31 | O sistema deve permitir exportar os relatórios em CSV e PDF. | Derivado (E-07 — relatório gerencial circula fora do sistema) | Could |

### EP-06 — Acesso, perfis e auditoria

| ID | Requisito | Origem | Prioridade |
|----|-----------|--------|------------|
| RF-32 | O sistema deve autenticar usuários por credencial individual e nominal, vedando o uso de contas compartilhadas. | E-08 | Must |
| RF-33 | O sistema deve suportar os perfis Estoquista, Gerente, Compras, Fornecedor e Administrador de TI, com permissões distintas por operação. | O-02 | Must |
| RF-34 | O sistema deve manter registro de auditoria imutável de todas as operações que alterem saldo, permissões ou cadastro de produto, contendo autor, data, hora, valor anterior e valor posterior. | E-08 | Must |
| RF-35 | O sistema deve permitir ao Administrador de TI consultar a trilha de auditoria com filtro por usuário, produto e período. | Derivado (RF-34 sem consulta é registro inútil) | Should |

### EP-07 — Compras e fornecedores

| ID | Requisito | Origem | Prioridade |
|----|-----------|--------|------------|
| RF-36 | O sistema deve permitir registrar pedidos de compra com fornecedor, itens, quantidades, data de emissão e prazo de entrega previsto. | E-13, E-14 | Should |
| RF-37 | O sistema deve exibir o histórico de compras de cada produto, com fornecedor, quantidade, data e preço unitário. | E-13 | Should |
| RF-38 | O sistema deve permitir dar entrada parcial ou total em um pedido de compra, atualizando seu status para pendente, parcialmente recebido ou recebido. | Derivado (E-13 + RF-07) | Should |
| RF-39 | O sistema deve disponibilizar ao Fornecedor uma consulta autenticada dos pedidos a ele destinados, com quantidades e prazos de entrega. | E-14 | Could |
| RF-40 | O sistema deve notificar o Setor de Compras quando um pedido de compra ultrapassar o prazo de entrega previsto sem recebimento. | Derivado (E-14 — prazo sem acompanhamento não gera ação) | Could |

---

## 3. Fora do escopo desta versão

| ID | Item | Prioridade | Razão |
|----|------|------------|-------|
| RF-41 | Integração automática com sistema de vendas (PDV/e-commerce) para baixa de estoque | Won't | Depende de decisão pendente em [Q-07](lacunas-e-ambiguidades.md). Até lá, saídas são registradas manualmente. |
| RF-42 | Integração automática com sistema de compras/ERP para emissão de pedidos | Won't | Mesma dependência de Q-07. |
| RF-43 | Gestão de custo médio, valorização de estoque e apuração contábil | Won't | Não mencionado em nenhuma entrevista; risco de escopo. |
| RF-44 | Leitura por código de barras / coletor de dados móvel | Won't | Não elicitado. Candidato natural à segunda versão, dado o volume operacional descrito. |

---

## 4. Distribuição por prioridade

| Prioridade | Quantidade | Épicos predominantes |
|------------|-----------:|----------------------|
| Must | 21 | EP-01, EP-02, EP-04, EP-06 |
| Should | 14 | EP-03, EP-05, EP-07 |
| Could | 5 | EP-05, EP-07 |
| Won't | 4 | — |
| **Total** | **44** (40 no escopo) | — |

O MVP é composto pelos 21 requisitos `Must`: eles entregam o ciclo mínimo capaz de substituir a
planilha — cadastrar produto, movimentar com autoria, ver saldo confiável, ser alertado e reconciliar
por inventário.
