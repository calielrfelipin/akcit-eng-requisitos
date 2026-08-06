# Regras de Negócio — Sistema de Controle de Estoque (StockPlus)

Regras que governam o comportamento do sistema independentemente da interface. Cada regra indica seu
**tipo**, sua **origem** e os **requisitos funcionais** que a aplicam.

**Tipos de regra:**

- `Invariante` — condição que nunca pode ser violada, em nenhum estado do sistema.
- `Cálculo` — fórmula ou derivação de valor.
- `Autorização` — quem pode executar determinada operação.
- `Processo` — sequência ou condição de transição obrigatória.

---

## 1. Integridade do saldo

| ID | Regra | Tipo | Origem | RFs |
|----|-------|------|--------|-----|
| RN-01 | O saldo de um produto nunca pode ser negativo. | Invariante | Derivada de E-06 | RF-08, RF-10 |
| RN-02 | Uma saída só pode ser efetivada se a quantidade solicitada, convertida para a unidade base, for menor ou igual ao saldo disponível no momento do lançamento. | Invariante | Derivada de RN-01 | RF-08 |
| RN-03 | O saldo de um produto é sempre o resultado da soma algébrica de suas movimentações; não pode ser digitado diretamente. Toda alteração de saldo exige uma movimentação correspondente. | Invariante | E-08, E-10 | RF-10, RF-24 |
| RN-04 | Quantidade de movimentação deve ser sempre maior que zero. O sentido (acréscimo ou decréscimo) é dado pelo tipo de movimentação, não pelo sinal da quantidade. | Invariante | Derivada de E-02 | RF-07, RF-08, RF-09 |
| RN-05 | Saldo disponível é distinto de saldo físico: quantidades pertencentes a lotes vencidos ou a produtos sob inventário aberto compõem o saldo físico, mas não o disponível para saída. | Invariante | O-04, O-05 | RF-05, RF-22 |

## 2. Rastreabilidade e imutabilidade

| ID | Regra | Tipo | Origem | RFs |
|----|-------|------|--------|-----|
| RN-06 | Toda movimentação registra obrigatoriamente o usuário autor, a data e a hora do lançamento. Nenhuma movimentação pode existir sem autor identificado. | Invariante | E-08 | RF-12, RF-32 |
| RN-07 | Movimentação registrada é imutável. Correções ocorrem exclusivamente por lançamento de estorno, que referencia a movimentação original e mantém ambas visíveis no histórico. | Invariante | E-08 | RF-13 |
| RN-08 | O sistema não permite contas compartilhadas: cada credencial corresponde a uma pessoa física identificada. Sem isso, a regra RN-06 perde valor. | Invariante | E-08 | RF-32 |
| RN-09 | Produto com pelo menos uma movimentação não pode ser excluído, apenas inativado. Produto inativo não aceita novas movimentações, mas permanece consultável no histórico e nos relatórios. | Invariante | Derivada de E-04 | RF-06 |
| RN-10 | O código (SKU) de um produto é único e imutável após a primeira movimentação. | Invariante | Derivada de E-01 | RF-01 |

## 3. Estoque mínimo, alertas e reposição

| ID | Regra | Tipo | Origem | RFs |
|----|-------|------|--------|-----|
| RN-11 | O estoque mínimo sugerido de um produto é calculado como `consumo médio diário × prazo de entrega em dias × (1 + margem de segurança)`, onde o consumo médio diário considera as saídas dos últimos 90 dias e a margem de segurança padrão é de 20%. | Cálculo | O-01 (premissa) | RF-18 |
| RN-12 | O valor calculado é uma **sugestão**. O Gerente pode fixar manualmente o estoque mínimo de um produto; nesse caso o valor manual prevalece e o produto é marcado como "mínimo definido manualmente". | Autorização | O-01 (premissa) | RF-18, RF-25 |
| RN-13 | O alerta de estoque baixo é disparado quando `saldo disponível ≤ estoque mínimo`. | Processo | E-03, E-11 | RF-15 |
| RN-14 | Um produto em estado de alerta gera no máximo uma notificação por e-mail a cada 24 horas, mesmo que sofra novas movimentações. O painel interno, ao contrário, reflete o estado sempre atualizado. | Processo | O-08 (premissa) | RF-15, RF-17 |
| RN-15 | O alerta é encerrado automaticamente quando o saldo disponível volta a superar o estoque mínimo; não requer baixa manual. | Processo | O-08 (premissa) | RF-15 |
| RN-16 | Ruptura de estoque (`saldo disponível = 0`) é tratada como evento crítico: gera notificação imediata a Compras e Gerência, sem a supressão de 24 horas prevista em RN-14. | Processo | O-08 (premissa) | RF-16, RF-17 |
| RN-17 | A quantidade sugerida de compra é `ponto de reposição − saldo disponível − quantidade em pedidos de compra pendentes`, arredondada para cima até o múltiplo da embalagem de compra do fornecedor. Sugestão com resultado menor ou igual a zero não é gerada. | Cálculo | E-12 | RF-19 |
| RN-18 | Sugestão de compra nunca se converte em pedido automaticamente: exige ação explícita de um usuário do perfil Compras. | Autorização | Derivada de E-12 | RF-20 |

## 4. Lotes, validade e unidades de medida

| ID | Regra | Tipo | Origem | RFs |
|----|-------|------|--------|-----|
| RN-19 | Cada produto possui exatamente uma unidade de medida base, na qual o saldo é armazenado. Unidades alternativas são convertidas para a base no momento do lançamento, por fator de conversão fixo cadastrado no produto. | Invariante | O-03 (premissa) | RF-04 |
| RN-20 | O fator de conversão de uma unidade alternativa não pode ser alterado se houver movimentações que o utilizaram; a mudança exige o cadastro de uma nova unidade. | Invariante | Derivada de RN-19 | RF-04 |
| RN-21 | Para produto controlado por lote, toda entrada exige a identificação do lote e, quando o produto for controlado por validade, a data de validade. | Invariante | O-05 (premissa) | RF-05, RF-07 |
| RN-22 | A saída de produto controlado por validade segue a política **FEFO** (*First Expired, First Out*): o sistema sugere o lote de validade mais próxima com saldo disponível. O operador pode escolher outro lote, mas a escolha exige justificativa. | Processo | O-05 (premissa) | RF-05, RF-08 |
| RN-23 | Lote com data de validade anterior à data corrente é bloqueado para saída de venda ou consumo, permanecendo elegível apenas para movimentação de perda por vencimento. | Invariante | E-05, O-05 | RF-05, RF-09 |

## 5. Perdas, ajustes e inventário

| ID | Regra | Tipo | Origem | RFs |
|----|-------|------|--------|-----|
| RN-24 | Todo registro de perda exige categoria (vencimento, avaria ou extravio) e justificativa textual com no mínimo 10 caracteres. | Invariante | E-05 | RF-09 |
| RN-25 | Ajuste manual de estoque só pode ser registrado por usuário do perfil Gerente e exige justificativa textual obrigatória. | Autorização | E-10, O-02 (premissa) | RF-24, RF-25 |
| RN-26 | Enquanto um inventário estiver aberto, os produtos nele incluídos não aceitam entradas nem saídas. Tentativas de movimentação são recusadas com indicação do inventário responsável pelo bloqueio. | Processo | O-04 (premissa) | RF-22 |
| RN-27 | O encerramento de um inventário exige aprovação de um Gerente e converte cada divergência em uma movimentação de ajuste, vinculada ao inventário e justificada pela contagem. O Gerente aprovador não pode ser o mesmo usuário que registrou a contagem. | Autorização | E-09, E-10, O-04 (premissa) | RF-21, RF-23 |
| RN-28 | A acurácia de um inventário é `(itens contados sem divergência ÷ total de itens contados) × 100`. | Cálculo | Derivada de E-09 | RF-26 |

## 6. Compras, devoluções e acesso externo

| ID | Regra | Tipo | Origem | RFs |
|----|-------|------|--------|-----|
| RN-29 | Devolução de cliente é registrada como entrada de tipo "devolução de cliente" e retorna ao saldo disponível somente após conferência de integridade; itens avariados na devolução são lançados como perda por avaria. | Processo | O-06 (premissa) | RF-14, RF-09 |
| RN-30 | Devolução a fornecedor é registrada como saída de tipo "devolução a fornecedor" e deve referenciar o pedido de compra que originou a entrada. | Processo | O-06 (premissa) | RF-14, RF-36 |
| RN-31 | A quantidade total recebida em um pedido de compra não pode exceder a quantidade pedida por item. Excedentes exigem alteração formal do pedido. | Invariante | Derivada de E-13 | RF-38 |
| RN-32 | Um pedido de compra transita entre os estados `Pendente → Parcialmente recebido → Recebido`, ou `Pendente → Cancelado`. Pedido recebido ou cancelado não aceita novas entradas. | Processo | Derivada de E-13, E-14 | RF-38 |
| RN-33 | Usuário do perfil Fornecedor acessa exclusivamente os pedidos de compra emitidos para o seu próprio cadastro, e apenas em modo de leitura. Nenhum dado de saldo, custo interno ou outro fornecedor é visível a ele. | Autorização | E-14 (premissa) | RF-39, RF-33 |

---

## 7. Matriz de permissões por perfil

Consolidação operacional das regras de autorização (RN-12, RN-18, RN-25, RN-27, RN-33).

| Operação | Estoquista | Gerente | Compras | Fornecedor | Admin TI |
|----------|:----------:|:-------:|:-------:|:----------:|:--------:|
| Cadastrar / editar produto | — | ✔ | — | — | ✔ |
| Definir estoque mínimo manual | — | ✔ | — | — | — |
| Registrar entrada / saída | ✔ | ✔ | — | — | — |
| Registrar perda | ✔ | ✔ | — | — | — |
| Registrar ajuste manual | — | ✔ | — | — | — |
| Registrar contagem de inventário | ✔ | ✔ | — | — | — |
| Aprovar / encerrar inventário | — | ✔ | — | — | — |
| Consultar saldo e histórico | ✔ | ✔ | ✔ | — | ✔ |
| Aprovar sugestão / emitir pedido | — | ✔ | ✔ | — | — |
| Consultar próprios pedidos | — | — | — | ✔ (leitura) | — |
| Consultar trilha de auditoria | — | ✔ | — | — | ✔ |
| Gerenciar usuários e perfis | — | — | — | — | ✔ |

> **Atenção:** as regras marcadas como `(premissa)` derivam de pontos em aberto da elicitação e estão
> registradas em [lacunas-e-ambiguidades.md](lacunas-e-ambiguidades.md) aguardando validação com os
> stakeholders. Elas foram adotadas para permitir a especificação prosseguir, e não porque tenham
> sido confirmadas.
