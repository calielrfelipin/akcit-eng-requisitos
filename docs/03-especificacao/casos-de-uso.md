# Casos de Uso — Sistema de Controle de Estoque (StockPlus)

## Por que casos de uso, se já existem histórias de usuário?

História de usuário e caso de uso não competem: respondem a perguntas diferentes. A história diz
**o que** alguém quer e **por quê**; o caso de uso descreve **como** a interação transcorre, incluindo
os desvios. Para a maior parte do *backlog*, a história com critérios de aceitação basta.

Três fluxos deste projeto, porém, têm característica que a história não captura bem: **múltiplos
atores, decisão condicional e um número de exceções maior que o de caminhos normais**. Nesses casos, a
história vira uma lista de critérios desconexos e a equipe perde a visão da sequência. São eles:

| Caso de uso | Por que exige detalhamento em caso de uso |
|-------------|-------------------------------------------|
| UC-01 Registrar saída de mercadoria | Seis exceções distintas (saldo, lote, validade, inventário, concorrência, permissão) sobre um fluxo de quatro passos |
| UC-02 Realizar inventário físico | Dois atores em momentos diferentes, estado de bloqueio, segregação de função e conversão de divergência em ajuste |
| UC-03 Repor estoque a partir de alerta | Encadeia três atores e atravessa quatro épicos — é o fluxo que resolve o problema P3 da elicitação |

Os demais requisitos permanecem especificados por história e critério de aceitação. Escrever caso de
uso para todos eles seria duplicação de esforço sem ganho de clareza.

---

## UC-01 — Registrar saída de mercadoria

| Campo | Conteúdo |
|-------|----------|
| **Identificador** | UC-01 |
| **Ator primário** | Estoquista |
| **Atores secundários** | Gerente, Comprador (destinatários de notificação) |
| **História relacionada** | [US-07](historias-de-usuario.md) |
| **Requisitos** | RF-08, RF-10, RF-12, RF-16 |
| **Regras** | RN-01, RN-02, RN-04, RN-06, RN-07, RN-19, RN-22, RN-23, RN-26 |
| **Objetivo** | Reduzir o saldo de um produto registrando a razão e o responsável pela baixa |
| **Frequência estimada** | Dezenas a centenas de vezes por dia — é a operação mais executada do sistema |

**Pré-condições**

1. O Estoquista está autenticado com credencial individual (RN-08).
2. O produto está ativo (RN-09).
3. O produto possui saldo disponível maior que zero.

**Pós-condições de sucesso**

1. O saldo do produto foi reduzido pela quantidade informada, convertida para a unidade base.
2. Existe uma movimentação de saída imutável, com autor, data e hora (RN-06, RN-07).
3. Se o saldo atingiu o estoque mínimo ou zero, as notificações correspondentes foram disparadas.

**Pós-condições de falha**

1. O saldo permanece inalterado.
2. Nenhuma movimentação parcial foi persistida.

### Fluxo principal

| # | Ator | Ação |
|---|------|------|
| 1 | Estoquista | Localiza o produto por nome ou código (UC estende-se de US-02) |
| 2 | Sistema | Exibe saldo físico, saldo disponível, unidade base e unidades alternativas |
| 3 | Estoquista | Informa quantidade, unidade e motivo da saída |
| 4 | Sistema | Converte a quantidade para a unidade base (RN-19) |
| 5 | Sistema | Valida quantidade maior que zero (RN-04) e saldo disponível suficiente (RN-02) |
| 6 | Sistema | Persiste a movimentação com autor, data e hora (RN-06) |
| 7 | Sistema | Recalcula e exibe o novo saldo |
| 8 | Sistema | Avalia as condições de alerta e ruptura (RN-13, RN-16) |
| 9 | Sistema | Confirma a operação ao Estoquista em até 3 segundos (RNF-02) |

### Fluxos alternativos

**A1 — Produto controlado por validade (RN-22)**
Após o passo 2, o sistema sugere o lote de validade mais próxima com saldo disponível. Se o
Estoquista aceitar, o fluxo segue no passo 3. Se escolher outro lote, o sistema exige justificativa
antes de prosseguir, e a justificativa é gravada na movimentação.

**A2 — Saída em unidade alternativa (RN-19)**
No passo 3, o Estoquista seleciona uma unidade alternativa. No passo 4 o sistema aplica o fator de
conversão e, no passo 7, exibe o saldo nas duas grandezas.

**A3 — Saída vinculada a devolução a fornecedor (RN-30)**
No passo 3, o Estoquista escolhe o motivo "Devolução a fornecedor". O sistema passa a exigir a
referência ao pedido de compra que originou a entrada, e o fluxo segue no passo 4.

### Fluxos de exceção

| ID | Condição | Tratamento |
|----|----------|------------|
| E1 | Saldo disponível insuficiente (RN-01, RN-02) | O sistema recusa no passo 5, informa o saldo disponível e mantém os dados digitados para correção. Nada é persistido. |
| E2 | Quantidade zero ou negativa (RN-04) | O sistema recusa no passo 5 com mensagem específica. |
| E3 | Lote selecionado está vencido (RN-23) | O sistema recusa e orienta o registro de perda por vencimento (UC alternativo: US-08). |
| E4 | Produto incluído em inventário aberto (RN-26) | O sistema recusa no passo 5, identificando o inventário que bloqueia o produto. |
| E5 | Saída concorrente sobre o mesmo saldo (RN-01) | O sistema serializa a verificação de saldo com a persistência. A segunda transação é recusada por saldo insuficiente. O saldo persistido nunca fica negativo. |
| E6 | Perfil sem permissão para movimentar | O sistema nega a operação no passo 3 e registra a tentativa na trilha de auditoria (RF-34). |
| E7 | Falha no envio de notificação (RNF-18) | A movimentação permanece registrada. A notificação é enfileirada para reenvio. A falha de notificação nunca reverte a movimentação. |

> **Nota de projeto sobre E5:** este é o ponto de maior risco técnico do caso de uso. A verificação
> de saldo do passo 5 e a persistência do passo 6 devem ocorrer na mesma transação, com bloqueio
> sobre o registro de saldo do produto. Validar o saldo em tela, ou fora da transação, permite que
> duas saídas simultâneas violem RN-01 — o defeito clássico dessa classe de sistema, e exatamente o
> tipo de erro que a planilha atual comete.

---

## UC-02 — Realizar inventário físico

| Campo | Conteúdo |
|-------|----------|
| **Identificador** | UC-02 |
| **Ator primário** | Estoquista (contagem) |
| **Ator secundário** | Gerente (aprovação) |
| **Histórias relacionadas** | [US-17](historias-de-usuario.md), [US-18](historias-de-usuario.md) |
| **Requisitos** | RF-21, RF-22, RF-23, RF-26 |
| **Regras** | RN-05, RN-26, RN-27, RN-28 |
| **Objetivo** | Reconciliar o saldo registrado no sistema com a contagem física, de forma rastreável |
| **Frequência estimada** | Periódica — mensal ou trimestral, conforme a definição pendente em Q-04 |

**Pré-condições**

1. Existem produtos ativos com saldo registrado.
2. O Estoquista está autenticado.

**Pós-condições de sucesso**

1. O inventário está no estado "Encerrado".
2. Cada divergência gerou uma movimentação de ajuste vinculada ao inventário.
3. O bloqueio de movimentação dos produtos foi liberado.
4. A acurácia da contagem está registrada (RN-28).

### Fluxo principal

| # | Ator | Ação |
|---|------|------|
| 1 | Estoquista | Abre um inventário selecionando produtos por categoria ou localização |
| 2 | Sistema | Cria o inventário no estado "Aberto", congela o saldo de referência de cada item e bloqueia movimentações desses produtos (RN-26) |
| 3 | Estoquista | Percorre o armazém e registra a quantidade contada de cada item |
| 4 | Sistema | Registra a contagem **sem exibir** o saldo de referência antes da confirmação |
| 5 | Sistema | Calcula a divergência de cada item após a confirmação da quantidade |
| 6 | Estoquista | Solicita o encerramento do inventário |
| 7 | Sistema | Verifica que todos os itens foram contados; encaminha o inventário para aprovação |
| 8 | Gerente | Analisa o relatório de divergências e aprova o encerramento |
| 9 | Sistema | Valida que o aprovador é distinto de quem registrou as contagens (RN-27) |
| 10 | Sistema | Converte cada divergência em movimentação de ajuste, vinculada ao inventário |
| 11 | Sistema | Libera o bloqueio de movimentação e apura a acurácia (RN-28) |

> **Nota sobre o passo 4:** ocultar o saldo esperado antes da digitação é decisão deliberada de
> especificação. Exibi-lo transforma a contagem em conferência de expectativa: o operador tende a
> confirmar o número que vê, o que produz inventários com 100% de acurácia aparente e nenhuma
> informação real. É um requisito de comportamento, não de interface.

### Fluxos alternativos

**A1 — Recontagem de item divergente**
Após o passo 5, o Estoquista pode solicitar recontagem de um item. O sistema mantém o registro da
primeira contagem e adiciona a segunda, exibindo ambas ao Gerente no passo 8.

**A2 — Inventário rotativo por localização**
No passo 1, a seleção é feita por localização física em vez de categoria. O bloqueio de RN-26 recai
apenas sobre os produtos daquela localização, e as demais áreas do armazém seguem operando.

**A3 — Aprovação parcial**
No passo 8, o Gerente pode rejeitar divergências específicas, devolvendo-as para recontagem, e
aprovar as demais. O inventário permanece aberto até que todos os itens estejam resolvidos.

### Fluxos de exceção

| ID | Condição | Tratamento |
|----|----------|------------|
| E1 | Itens sem contagem no encerramento | O sistema recusa no passo 7 e lista os itens pendentes. |
| E2 | Aprovador é o mesmo usuário que contou (RN-27) | O sistema recusa no passo 9 e exige outro Gerente. O inventário permanece aguardando aprovação. |
| E3 | Aprovador sem perfil Gerente | Operação negada e registrada na auditoria (RF-34). |
| E4 | Tentativa de movimentar produto bloqueado (RN-26) | A movimentação é recusada com identificação do inventário. Ver UC-01, exceção E4. |
| E5 | Inventário abandonado sem encerramento | O sistema alerta o Gerente após 48 horas de inatividade. O bloqueio persiste até encerramento ou cancelamento explícito — bloquear indefinidamente paralisa a operação. |
| E6 | Ajuste geraria saldo negativo (RN-01) | Situação impossível por construção, pois a contagem física é sempre maior ou igual a zero e substitui o saldo. Registrado aqui para explicitar que a regra foi considerada. |

---

## UC-03 — Repor estoque a partir de alerta

| Campo | Conteúdo |
|-------|----------|
| **Identificador** | UC-03 |
| **Ator primário** | Comprador |
| **Atores secundários** | Sistema (iniciador), Gerente, Estoquista, Fornecedor |
| **Histórias relacionadas** | [US-13](historias-de-usuario.md), [US-15](historias-de-usuario.md), [US-16](historias-de-usuario.md), [US-27](historias-de-usuario.md), [US-28](historias-de-usuario.md) |
| **Requisitos** | RF-15, RF-17, RF-18, RF-19, RF-20, RF-36, RF-38 |
| **Regras** | RN-11, RN-13, RN-14, RN-17, RN-18, RN-31, RN-32 |
| **Objetivo** | Converter um alerta de estoque baixo em recebimento efetivo, sem ruptura |
| **Particularidade** | O caso de uso é **iniciado pelo sistema**, não por um ator humano |

**Pré-condições**

1. O produto possui estoque mínimo definido, calculado (RN-11) ou fixado manualmente (RN-12).
2. Existe fornecedor cadastrado para o produto, com prazo de entrega informado.

**Pós-condições de sucesso**

1. Existe pedido de compra registrado, ou a sugestão foi explicitamente rejeitada com registro.
2. Havendo recebimento, o saldo foi elevado acima do estoque mínimo e o alerta se encerrou (RN-15).

### Fluxo principal

| # | Ator | Ação |
|---|------|------|
| 1 | Sistema | Detecta `saldo disponível ≤ estoque mínimo` após uma movimentação de saída (RN-13) |
| 2 | Sistema | Inclui o produto no painel de alertas e notifica Compras e Gerência por e-mail (RN-14) |
| 3 | Sistema | Gera sugestão de compra calculando `ponto de reposição − saldo − pedidos pendentes` (RN-17) |
| 4 | Comprador | Analisa a sugestão no painel |
| 5 | Comprador | Aprova a sugestão, eventualmente ajustando a quantidade (RN-18) |
| 6 | Sistema | Registra pedido de compra no estado "Pendente", com prazo de entrega previsto (RN-32) |
| 7 | Fornecedor | Entrega a mercadoria |
| 8 | Estoquista | Registra a entrada vinculada ao pedido de compra |
| 9 | Sistema | Atualiza o saldo, valida o limite de RN-31 e atualiza o status do pedido (RN-32) |
| 10 | Sistema | Verifica que o saldo superou o estoque mínimo e encerra o alerta (RN-15) |

### Fluxos alternativos

**A1 — Comprador rejeita a sugestão**
No passo 5, o Comprador rejeita a sugestão informando o motivo. O produto permanece em alerta e a
rejeição é registrada, para que a mesma sugestão não seja reapresentada indefinidamente.

**A2 — Recebimento parcial (RN-32)**
No passo 8, a quantidade recebida é inferior à pedida. O status do pedido passa a "Parcialmente
recebido" e o pedido continua elegível a novas entradas. Se o saldo ainda estiver abaixo do mínimo, o
alerta permanece ativo.

**A3 — Ruptura antes do recebimento (RN-16)**
Entre os passos 6 e 7, o saldo chega a zero. O sistema dispara notificação imediata de ruptura, sem a
supressão de 24 horas, e destaca no painel que existe pedido pendente com prazo previsto — informação
que o Gerente precisa para decidir por uma compra emergencial.

**A4 — Pedido atrasado (RF-40)**
Se o prazo previsto no passo 6 for ultrapassado sem recebimento, o sistema notifica o Comprador para
cobrança do fornecedor.

### Fluxos de exceção

| ID | Condição | Tratamento |
|----|----------|------------|
| E1 | Produto sem histórico suficiente para calcular o mínimo (RN-11) | Nenhuma sugestão é gerada. O sistema solicita ao Gerente que defina o mínimo manualmente (RN-12). |
| E2 | Quantidade sugerida menor ou igual a zero (RN-17) | Nenhuma sugestão é criada — há pedidos pendentes suficientes para cobrir a necessidade. |
| E3 | Produto sem fornecedor cadastrado | O alerta é gerado, mas não a sugestão. O sistema sinaliza a ausência de fornecedor como impedimento. |
| E4 | Recebimento superior ao pedido (RN-31) | A entrada é recusada. O excedente exige alteração formal do pedido. |
| E5 | Serviço de e-mail indisponível (RNF-18) | O alerta permanece visível no painel e o e-mail é enfileirado. A ausência de e-mail não suprime o alerta. |

> **Observação de escopo:** este caso de uso pressupõe que as saídas cheguem ao sistema. Se as vendas
> forem registradas em outro sistema sem integração — questão [Q-07](../02-analise/lacunas-e-ambiguidades.md)
> —, o passo 1 nunca é acionado no momento certo e todo o fluxo perde eficácia. UC-03 é o argumento
> mais concreto para tratar Q-07 como decisão prioritária.
