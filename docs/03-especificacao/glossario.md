# Glossário — Sistema de Controle de Estoque (StockPlus)

Vocabulário comum entre stakeholders, analistas e equipe de desenvolvimento. Vários dos conflitos
registrados em [lacunas-e-ambiguidades.md](../02-analise/lacunas-e-ambiguidades.md) nasceram de termos
usados com sentidos diferentes por pessoas diferentes — este glossário existe para reduzir a
reincidência.

Termos marcados com ⚠️ tiveram seu sentido **fixado pela análise**, e não pela elicitação. Precisam de
confirmação do stakeholder.

---

## A

**Acurácia de inventário**
Percentual de itens contados sem divergência em relação ao total contado: `(itens sem divergência ÷
total contado) × 100` (RN-28). Mede a confiabilidade do saldo do sistema, não o desempenho do
contador.

**Ajuste manual**
Movimentação que altera o saldo sem corresponder a um evento físico de entrada ou saída, usada para
corrigir divergências. Exige justificativa e é restrita ao perfil Gerente (RN-25). Distingue-se do
**estorno**, que anula um lançamento específico.

**Alerta de estoque baixo**
Estado em que `saldo disponível ≤ estoque mínimo` (RN-13). É um **estado**, não um evento: encerra-se
automaticamente quando o saldo volta a superar o mínimo (RN-15), sem baixa manual.

---

## C

**Consumo médio diário** ⚠️
Média de saídas por dia calculada sobre os últimos 90 dias (RN-11). A janela de 90 dias é premissa da
análise — [Q-01](../02-analise/lacunas-e-ambiguidades.md).

---

## D

**Devolução a fornecedor**
Saída de mercadoria retornada ao fornecedor, obrigatoriamente vinculada ao pedido de compra que
originou a entrada (RN-30).

**Devolução de cliente**
Entrada de mercadoria retornada por cliente. Compõe o saldo físico imediatamente, mas só entra no
**saldo disponível** após conferência de integridade; itens avariados tornam-se **perda por avaria**
(RN-29).

**Divergência de inventário**
Diferença entre a quantidade contada fisicamente e o saldo registrado no sistema. Positiva indica
sobra, negativa indica falta. Convertida em ajuste na aprovação do inventário (RN-27).

---

## E

**Endereçamento**
Identificação da posição física de um produto no armazém (por exemplo, "Rua B / Prateleira 12").
Nesta versão, um produto possui uma única localização textual (RF-03) — ver
[Q-12](../02-analise/lacunas-e-ambiguidades.md).

**Estorno**
Movimentação de sinal contrário que anula o efeito de um lançamento anterior, mantendo ambos visíveis
no histórico. É o único mecanismo de correção admitido, pois movimentações são imutáveis (RN-07).

**Estoque mínimo**
Quantidade abaixo da qual o produto entra em estado de alerta. Pode ser calculado pelo sistema
(RN-11) ou fixado manualmente pelo Gerente (RN-12). **Não é o mesmo que ponto de reposição.**

---

## F

**FEFO** (*First Expired, First Out*)
Política de saída que prioriza o lote de validade mais próxima entre os que têm saldo disponível
(RN-22). Escolher outro lote é permitido, mas exige justificativa. Difere de **FIFO** (*First In,
First Out*), que prioriza o lote de entrada mais antiga — para produtos perecíveis, FEFO é a política
correta, porque entrada mais antiga não implica validade mais próxima.

---

## L

**Lote**
Conjunto de unidades de um produto recebidas em uma mesma remessa, identificado para fins de
rastreabilidade e controle de validade (RN-21). Só é exigido em produtos marcados como controlados
por lote (RF-05).

---

## M

**Movimentação**
Todo registro que altera o saldo de um produto. Tipos previstos: entrada, saída, perda, ajuste,
estorno, devolução de cliente e devolução a fornecedor. Toda movimentação é imutável e possui autor,
data e hora (RN-06, RN-07).

---

## P

**Perda**
Redução de estoque sem contrapartida de saída comercial, classificada em vencimento, avaria ou
extravio, com justificativa obrigatória (RN-24). Registrar perdas corretamente é o que impede que
elas apareçam depois como divergência inexplicada de inventário.

**Ponto de reposição** ⚠️
Nível de saldo que aciona a geração de sugestão de compra. Nesta especificação equivale ao **estoque
mínimo**, o que torna alerta e sugestão simultâneos. Manter os dois conceitos separados — mínimo como
piso de segurança e ponto de reposição um pouco acima dele — permitiria comprar antes de entrar em
zona crítica. A distinção depende de [Q-01](../02-analise/lacunas-e-ambiguidades.md).

---

## R

**Ruptura de estoque**
Situação em que o saldo disponível de um produto é zero (RN-16). Tratada como evento crítico, com
notificação imediata e sem a supressão de 24 horas aplicável ao alerta de estoque baixo (RN-14).

---

## S

**Saldo disponível**
Quantidade elegível para saída. Exclui lotes vencidos, produtos sob inventário aberto e devoluções não
conferidas (RN-05). **É este o saldo validado nas saídas** (RN-02).

**Saldo físico**
Quantidade total existente no armazém, incluindo o que não está disponível para saída. Usado na
conferência de inventário.

> A distinção entre saldo físico e disponível é a fonte mais provável de mal-entendido operacional
> do sistema: um estoquista que vê "100 em estoque" e recebe recusa de saída de 90 unidades precisa
> entender de imediato o motivo. Daí a exigência de CA-09.3 — a diferença deve ser explicada na
> própria tela.

**SKU** (*Stock Keeping Unit*)
Código único e imutável que identifica um produto no catálogo (RN-10). Nas entrevistas os
stakeholders referem-se a ele simplesmente como "código".

**Sugestão de compra**
Recomendação de quantidade a comprar, calculada como `ponto de reposição − saldo disponível −
quantidade em pedidos pendentes` (RN-17). É **sugestão**: nunca se converte em pedido sem ação
explícita do perfil Compras (RN-18).

---

## T

**Tempo real** ⚠️
Expressão usada pelos Gerentes em E-06. Nesta especificação significa saldo consistente a cada
consulta, com latência máxima de 5 segundos entre o registro de uma movimentação e sua visibilidade
(RNF-03). **Não** significa atualização automática de tela por *push* — ver
[Q-10](../02-analise/lacunas-e-ambiguidades.md).

---

## U

**Unidade de medida base**
Unidade na qual o saldo de um produto é armazenado. Cada produto tem exatamente uma (RN-19).

**Unidade alternativa**
Unidade de lançamento diferente da base, convertida por fator fixo cadastrado no produto (RN-19). O
fator não pode ser alterado após ter sido usado em movimentações (RN-20).

---

## V

**Vendas** ⚠️
Termo usado pelos Gerentes em E-07 ("produtos mais vendidos"). O sistema especificado **não conhece
vendas** — conhece saídas de estoque, que incluem consumo interno, transferências e devoluções. Os
relatórios de RF-28 e RF-29 medem **saídas**, não vendas. A troca de termo é deliberada e está
registrada em [Q-11](../02-analise/lacunas-e-ambiguidades.md).
