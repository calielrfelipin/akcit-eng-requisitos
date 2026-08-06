# Critérios de Aceitação — Sistema de Controle de Estoque (StockPlus)

Critérios em formato **Dado / Quando / Então** (*Given / When / Then*) para as histórias de
[historias-de-usuario.md](historias-de-usuario.md).

**Convenção de identificação:** `CA-<US>.<n>` — por exemplo, `CA-07.3` é o terceiro critério da
história US-07.

**Cobertura:** as 18 histórias `Must` estão detalhadas com cenários de sucesso, alternativos e de
exceção. As histórias `Should` e `Could` trazem apenas os cenários essenciais, a serem expandidos no
refinamento da *sprint* correspondente — expandir agora produziria especificação que provavelmente
mudará quando as questões de
[lacunas-e-ambiguidades.md](../02-analise/lacunas-e-ambiguidades.md) forem respondidas.

---

## US-01 — Cadastrar produto

**CA-01.1 — Cadastro válido**
- **Dado** que estou autenticado com o perfil Gerente
- **E** não existe produto com o código `SKU-1001`
- **Quando** cadastro um produto com código `SKU-1001`, nome "Parafuso sextavado 8mm", unidade base "UN", categoria "Fixadores" e estoque mínimo 100
- **Então** o produto é criado com saldo inicial zero
- **E** aparece nos resultados de busca

**CA-01.2 — Código duplicado (RN-10)**
- **Dado** que já existe um produto com o código `SKU-1001`
- **Quando** tento cadastrar outro produto com o mesmo código
- **Então** o cadastro é recusado com a mensagem "Já existe um produto com este código"
- **E** nenhum registro é criado

**CA-01.3 — Campos obrigatórios**
- **Quando** tento salvar um produto sem código, sem nome ou sem unidade base
- **Então** o cadastro é recusado
- **E** cada campo ausente é sinalizado individualmente

**CA-01.4 — Perfil sem permissão (RN — matriz de permissões)**
- **Dado** que estou autenticado com o perfil Estoquista
- **Quando** tento acessar o cadastro de produtos
- **Então** a operação é negada
- **E** a tentativa é registrada na trilha de auditoria

---

## US-02 — Localizar produto

**CA-02.1 — Busca por nome parcial**
- **Dado** que existem os produtos "Parafuso sextavado 8mm" e "Parafuso Philips 6mm"
- **Quando** busco por `parafuso`
- **Então** ambos são listados com código, saldo atual e localização física

**CA-02.2 — Busca insensível a acento e caixa**
- **Dado** que existe o produto "Cabo de aço trançado"
- **Quando** busco por `ACO TRANCADO`
- **Então** o produto é retornado

**CA-02.3 — Busca por código exato**
- **Quando** busco pelo código `SKU-1001`
- **Então** apenas o produto correspondente é retornado

**CA-02.4 — Nenhum resultado**
- **Quando** busco por um termo sem correspondência
- **Então** vejo a mensagem "Nenhum produto encontrado" e um atalho para cadastrar novo produto

**CA-02.5 — Desempenho (RNF-01)**
- **Dado** que a base contém 50.000 produtos
- **Quando** executo uma busca por nome
- **Então** o resultado é exibido em até 2 segundos no percentil 95

---

## US-05 — Inativar produto

**CA-05.1 — Inativação preserva histórico (RN-09)**
- **Dado** que o produto `SKU-1001` possui 15 movimentações registradas
- **Quando** o inativo
- **Então** o produto deixa de aparecer nas telas de movimentação
- **E** seu histórico permanece consultável
- **E** ele continua presente nos relatórios do período em que estava ativo

**CA-05.2 — Exclusão bloqueada (RN-09)**
- **Dado** que o produto possui ao menos uma movimentação
- **Quando** tento excluí-lo
- **Então** a exclusão é recusada com a mensagem "Produto com movimentações não pode ser excluído; utilize a inativação"

**CA-05.3 — Movimentação de produto inativo**
- **Dado** que o produto `SKU-1001` está inativo
- **Quando** tento registrar uma entrada para ele
- **Então** a movimentação é recusada com a indicação de que o produto está inativo

---

## US-06 — Registrar entrada de mercadoria

**CA-06.1 — Entrada válida**
- **Dado** que o produto `SKU-1001` tem saldo 50
- **Quando** registro entrada de 100 UN com documento de origem "NF 4521"
- **Então** o saldo passa a 150
- **E** a movimentação é gravada com meu usuário, data e hora (RN-06)

**CA-06.2 — Quantidade inválida (RN-04)**
- **Quando** tento registrar entrada com quantidade zero ou negativa
- **Então** o lançamento é recusado com a mensagem "A quantidade deve ser maior que zero"

**CA-06.3 — Produto com controle de lote (RN-21)**
- **Dado** que o produto `SKU-2002` é controlado por lote e validade
- **Quando** tento registrar entrada sem informar lote ou validade
- **Então** o lançamento é recusado
- **E** os campos lote e validade são sinalizados como obrigatórios

**CA-06.4 — Produto sob inventário aberto (RN-26)**
- **Dado** que o produto `SKU-1001` está incluído em um inventário aberto
- **Quando** tento registrar entrada
- **Então** o lançamento é recusado com a identificação do inventário que bloqueia o produto

**CA-06.5 — Desempenho (RNF-02)**
- **Quando** confirmo o registro de uma entrada
- **Então** recebo a confirmação em até 3 segundos no percentil 95

---

## US-07 — Registrar saída de mercadoria

**CA-07.1 — Saída válida**
- **Dado** que o produto `SKU-1001` tem saldo 150
- **Quando** registro saída de 30 UN com motivo "Venda"
- **Então** o saldo passa a 120
- **E** a movimentação é gravada com meu usuário, data e hora

**CA-07.2 — Saldo insuficiente (RN-01, RN-02)**
- **Dado** que o produto `SKU-1001` tem saldo 20
- **Quando** tento registrar saída de 30 UN
- **Então** o lançamento é recusado com a mensagem "Saldo disponível insuficiente: 20 UN"
- **E** o saldo permanece 20

**CA-07.3 — Saída exata do saldo dispara ruptura (RN-16)**
- **Dado** que o produto `SKU-1001` tem saldo 20
- **Quando** registro saída de 20 UN
- **Então** o saldo passa a 0
- **E** uma notificação de ruptura é enviada imediatamente a Compras e Gerência

**CA-07.4 — Sugestão FEFO (RN-22)**
- **Dado** que o produto `SKU-2002` é controlado por validade
- **E** possui o lote A com validade em 30 dias e o lote B com validade em 90 dias, ambos com saldo
- **Quando** registro uma saída
- **Então** o sistema sugere o lote A
- **E** ao escolher o lote B sou obrigado a informar justificativa

**CA-07.5 — Lote vencido bloqueado (RN-23)**
- **Dado** que o lote C do produto `SKU-2002` está vencido
- **Quando** tento registrar saída de venda desse lote
- **Então** o lançamento é recusado
- **E** o sistema informa que o lote está elegível apenas para perda por vencimento

**CA-07.6 — Concorrência sobre o mesmo saldo (RN-01)**
- **Dado** que o produto `SKU-1001` tem saldo 10
- **Quando** dois usuários confirmam simultaneamente saídas de 10 UN cada
- **Então** apenas uma das saídas é efetivada
- **E** a outra é recusada por saldo insuficiente
- **E** em nenhum momento o saldo persistido fica negativo

---

## US-08 — Registrar perda

**CA-08.1 — Perda válida (RN-24)**
- **Dado** que o produto `SKU-1001` tem saldo 120
- **Quando** registro perda de 5 UN na categoria "Avaria" com a justificativa "Caixa danificada no transporte"
- **Então** o saldo passa a 115
- **E** a movimentação é classificada como perda por avaria

**CA-08.2 — Justificativa obrigatória (RN-24)**
- **Quando** tento registrar perda sem justificativa ou com menos de 10 caracteres
- **Então** o lançamento é recusado com a indicação do mínimo exigido

**CA-08.3 — Categoria obrigatória (RN-24)**
- **Quando** tento registrar perda sem selecionar vencimento, avaria ou extravio
- **Então** o lançamento é recusado

**CA-08.4 — Perda de lote vencido (RN-23)**
- **Dado** que o lote C do produto `SKU-2002` está vencido
- **Quando** registro perda por vencimento desse lote
- **Então** o lançamento é aceito
- **E** o saldo do lote é zerado

---

## US-09 — Consultar saldo atual

**CA-09.1 — Saldo derivado das movimentações (RN-03)**
- **Dado** que o produto `SKU-1001` teve entrada de 100, saída de 30 e perda de 5
- **Quando** consulto seu saldo
- **Então** vejo 65

**CA-09.2 — Saldo não editável (RN-03)**
- **Quando** acesso a tela de detalhe do produto
- **Então** o campo de saldo é somente leitura, em qualquer perfil, inclusive Gerente e Administrador

**CA-09.3 — Saldo disponível distinto do físico (RN-05)**
- **Dado** que o produto `SKU-2002` tem 100 UN, das quais 20 em lote vencido
- **Quando** consulto o produto
- **Então** vejo saldo físico 100 e saldo disponível 80
- **E** a diferença é explicada na própria tela

**CA-09.4 — Latência (RNF-03)**
- **Dado** que outro usuário acaba de registrar uma movimentação
- **Quando** consulto o saldo do produto
- **Então** o valor atualizado é exibido em até 5 segundos após a confirmação daquele lançamento

---

## US-10 — Consultar histórico de movimentações

**CA-10.1 — Histórico completo com autoria (RN-06)**
- **Quando** consulto o histórico do produto `SKU-1001`
- **Então** vejo, para cada movimentação, o tipo, a quantidade, a data e hora, o usuário autor e o saldo resultante

**CA-10.2 — Filtro por período e tipo**
- **Quando** filtro o histórico por um intervalo de datas e pelo tipo "Perda"
- **Então** apenas as perdas do intervalo são listadas
- **E** vejo o total do conjunto filtrado

**CA-10.3 — Ordenação cronológica**
- **Quando** consulto o histórico
- **Então** as movimentações aparecem em ordem cronológica decrescente por padrão

**CA-10.4 — Estorno visível (RN-07)**
- **Dado** que uma movimentação foi estornada
- **Quando** consulto o histórico
- **Então** vejo tanto o lançamento original quanto o estorno, com indicação visual do vínculo entre eles

---

## US-11 — Estornar movimentação

**CA-11.1 — Estorno cria contra-lançamento (RN-07)**
- **Dado** que existe uma entrada de 100 UN no produto `SKU-1001`, cujo saldo atual é 150
- **Quando** estorno essa entrada informando o motivo
- **Então** é criada uma movimentação de estorno de 100 UN vinculada à original
- **E** o saldo passa a 50
- **E** a movimentação original permanece inalterada no histórico

**CA-11.2 — Movimentação imutável (RN-07)**
- **Quando** abro o detalhe de uma movimentação já registrada
- **Então** nenhum campo é editável
- **E** não existe opção de exclusão, em nenhum perfil

**CA-11.3 — Estorno que tornaria o saldo negativo (RN-01)**
- **Dado** que uma entrada de 100 UN foi seguida de saídas que reduziram o saldo a 20
- **Quando** tento estornar aquela entrada
- **Então** o estorno é recusado, pois resultaria em saldo negativo
- **E** o sistema orienta a registrar um ajuste manual, sujeito a RN-25

**CA-11.4 — Estorno duplicado**
- **Dado** que uma movimentação já foi estornada
- **Quando** tento estorná-la novamente
- **Então** a operação é recusada com a indicação do estorno existente

---

## US-13 — Ser alertado sobre estoque baixo

**CA-13.1 — Disparo do alerta (RN-13)**
- **Dado** que o produto `SKU-1001` tem estoque mínimo 100 e saldo 105
- **Quando** uma saída reduz o saldo a 100
- **Então** o produto passa a constar no painel de alertas
- **E** um e-mail é enviado aos perfis Gerente e Compras

**CA-13.2 — Supressão de reenvio (RN-14)**
- **Dado** que o produto já gerou alerta por e-mail há 3 horas
- **Quando** ele sofre nova saída permanecendo abaixo do mínimo
- **Então** nenhum novo e-mail é enviado
- **E** o painel reflete o saldo atualizado

**CA-13.3 — Encerramento automático (RN-15)**
- **Dado** que o produto está em estado de alerta
- **Quando** uma entrada eleva o saldo acima do estoque mínimo
- **Então** o produto deixa o painel de alertas sem qualquer ação manual

**CA-13.4 — Falha no e-mail não bloqueia a operação (RNF-18)**
- **Dado** que o serviço de e-mail está indisponível
- **Quando** uma saída dispara a condição de alerta
- **Então** a movimentação é registrada normalmente
- **E** a notificação é enfileirada para reenvio

---

## US-14 — Ser notificado sobre ruptura

**CA-14.1 — Notificação imediata (RN-16)**
- **Dado** que o produto `SKU-1001` tem saldo 5
- **Quando** uma saída de 5 UN zera o saldo
- **Então** Compras e Gerência recebem notificação imediata de ruptura
- **E** a supressão de 24 horas de RN-14 não se aplica

**CA-14.2 — Destaque no painel**
- **Quando** existem produtos com saldo zero
- **Então** eles aparecem em seção própria do painel, distinta dos produtos em alerta

---

## US-15 — Obter estoque mínimo sugerido

**CA-15.1 — Cálculo da sugestão (RN-11)**
- **Dado** que o produto `SKU-1001` teve saída média de 10 UN por dia nos últimos 90 dias
- **E** o prazo de entrega do fornecedor é de 7 dias
- **Quando** o sistema calcula o estoque mínimo sugerido
- **Então** o valor apresentado é 84 UN — `10 × 7 × 1,20`

**CA-15.2 — Sobreposição manual (RN-12)**
- **Dado** que estou autenticado com o perfil Gerente
- **Quando** fixo o estoque mínimo do produto em 150
- **Então** o valor 150 prevalece sobre o calculado
- **E** o produto é marcado como "mínimo definido manualmente"

**CA-15.3 — Produto sem histórico suficiente**
- **Dado** que o produto foi cadastrado há 5 dias
- **Quando** o sistema tenta calcular o estoque mínimo
- **Então** nenhuma sugestão é apresentada
- **E** o sistema informa que são necessários pelo menos 30 dias de histórico

**CA-15.4 — Parâmetros configuráveis (RNF-29)**
- **Dado** que a margem de segurança é alterada de 20% para 30% na configuração
- **Quando** o cálculo é executado novamente
- **Então** o novo percentual é aplicado sem alteração de código

---

## US-17 — Realizar contagem de inventário

**CA-17.1 — Abertura do inventário**
- **Dado** que estou autenticado com perfil Estoquista ou Gerente
- **Quando** abro um inventário selecionando uma categoria de produtos
- **Então** o inventário é criado no estado "Aberto" com a lista de itens a contar

**CA-17.2 — Bloqueio de movimentação (RN-26)**
- **Dado** que existe inventário aberto contendo o produto `SKU-1001`
- **Quando** qualquer usuário tenta registrar entrada ou saída desse produto
- **Então** a movimentação é recusada com a identificação do inventário responsável

**CA-17.3 — Cálculo da divergência**
- **Dado** que o saldo do sistema para `SKU-1001` é 120
- **Quando** registro a contagem física de 117
- **Então** a divergência apurada é de −3 UN

**CA-17.4 — Saldo do sistema oculto durante a contagem**
- **Quando** registro a contagem de um item
- **Então** o saldo do sistema não é exibido antes da confirmação da quantidade contada

> A ordem importa: exibir o saldo esperado antes da digitação induz o contador a confirmá-lo em vez
> de contar, o que anula o propósito do inventário.

**CA-17.5 — Item não contado**
- **Quando** tento encerrar o inventário com itens sem contagem registrada
- **Então** o encerramento é recusado
- **E** os itens pendentes são listados

---

## US-18 — Aprovar e encerrar inventário

**CA-18.1 — Aprovação converte divergências em ajustes (RN-27)**
- **Dado** que um inventário aberto tem divergências apuradas
- **E** estou autenticado com o perfil Gerente
- **Quando** aprovo o encerramento
- **Então** cada divergência gera uma movimentação de ajuste vinculada ao inventário
- **E** os saldos passam a refletir a contagem física
- **E** o bloqueio de movimentação dos produtos é liberado

**CA-18.2 — Segregação de função (RN-27)**
- **Dado** que registrei pessoalmente as contagens do inventário
- **Quando** tento aprovar o seu encerramento com meu próprio usuário, ainda que eu tenha perfil Gerente
- **Então** a aprovação é recusada com a mensagem "A aprovação exige um Gerente distinto de quem realizou a contagem"

**CA-18.3 — Perfil sem permissão para aprovar (RN-27)**
- **Dado** que estou autenticado com o perfil Estoquista
- **Quando** tento aprovar o encerramento de um inventário
- **Então** a operação é negada e registrada na auditoria

**CA-18.4 — Acurácia apurada (RN-28)**
- **Dado** que um inventário contou 50 itens, dos quais 46 sem divergência
- **Quando** o inventário é encerrado
- **Então** a acurácia apresentada é 92%

---

## US-19 — Registrar ajuste manual

**CA-19.1 — Ajuste válido (RN-25)**
- **Dado** que estou autenticado com o perfil Gerente
- **E** o produto `SKU-1001` tem saldo 115
- **Quando** registro ajuste para 118 com a justificativa "Recontagem após conferência de recebimento"
- **Então** é criada uma movimentação de ajuste de +3 UN
- **E** o saldo passa a 118

**CA-19.2 — Justificativa obrigatória (RN-25)**
- **Quando** tento registrar ajuste sem justificativa
- **Então** o lançamento é recusado

**CA-19.3 — Restrição de perfil (RN-25)**
- **Dado** que estou autenticado com o perfil Estoquista
- **Quando** tento acessar a função de ajuste manual
- **Então** a operação é negada
- **E** a tentativa é registrada na trilha de auditoria

**CA-19.4 — Ajuste não pode resultar em saldo negativo (RN-01)**
- **Quando** tento registrar ajuste com valor final negativo
- **Então** o lançamento é recusado

---

## US-20 — Acompanhar o painel de estoque

**CA-20.1 — Composição do painel**
- **Quando** acesso o painel
- **Então** vejo o total de produtos ativos, a quantidade de produtos em alerta, a quantidade em ruptura e as últimas movimentações registradas

**CA-20.2 — Navegação a partir do painel**
- **Quando** clico no indicador de produtos em alerta
- **Então** sou levado à lista desses produtos, com saldo atual e estoque mínimo

**CA-20.3 — Atualização a cada carregamento (RF-27)**
- **Dado** que uma movimentação foi registrada após eu abrir o painel
- **Quando** recarrego a tela
- **Então** os indicadores refletem a movimentação

---

## US-24 — Autenticar com credencial individual

**CA-24.1 — Autenticação bem-sucedida**
- **Dado** que possuo credencial ativa
- **Quando** informo usuário e senha corretos
- **Então** acesso o sistema
- **E** vejo apenas as funções do meu perfil

**CA-24.2 — Credencial inválida**
- **Quando** informo senha incorreta
- **Então** o acesso é negado com mensagem genérica, sem revelar se o usuário existe
- **E** a tentativa é registrada

**CA-24.3 — Limite de tentativas (RNF-11)**
- **Quando** erro a senha 5 vezes em 15 minutos
- **Então** a credencial é bloqueada temporariamente
- **E** o evento é registrado na auditoria

**CA-24.4 — Expiração por inatividade (RNF-09)**
- **Dado** que estou autenticado
- **Quando** permaneço 30 minutos sem interagir
- **Então** minha sessão expira e a reautenticação é exigida

**CA-24.5 — Autoria vinculada à credencial (RN-06, RN-08)**
- **Quando** registro qualquer movimentação
- **Então** o autor gravado é o usuário da sessão autenticada, sem possibilidade de informá-lo manualmente

---

## US-25 — Gerenciar perfis e permissões

**CA-25.1 — Atribuição de perfil**
- **Dado** que estou autenticado como Administrador de TI
- **Quando** atribuo o perfil Gerente a um usuário
- **Então** ele passa a acessar as operações previstas na matriz de permissões de [regras-de-negocio.md](../02-analise/regras-de-negocio.md)

**CA-25.2 — Verificação no servidor (RNF-08)**
- **Dado** que estou autenticado com o perfil Estoquista
- **Quando** submeto diretamente uma requisição para a operação de ajuste manual, sem passar pela interface
- **Então** a requisição é rejeitada pelo servidor

**CA-25.3 — Isolamento do perfil Fornecedor (RN-33, RNF-10)**
- **Dado** que estou autenticado com o perfil Fornecedor
- **Quando** altero o identificador de pedido na requisição para um pedido de outro fornecedor
- **Então** o acesso é negado
- **E** nenhum dado de saldo ou de custo interno é retornado

**CA-25.4 — Registro de mudança de permissão (RF-34)**
- **Quando** altero o perfil de um usuário
- **Então** a alteração é registrada na auditoria com valor anterior e posterior

---

## Histórias `Should` e `Could` — cenários essenciais

| História | Critério essencial |
|----------|--------------------|
| US-03 Unidades alternativas | **Dado** produto com unidade base UN e unidade "CX" de fator 12, **quando** registro entrada de 5 CX, **então** o saldo aumenta em 60 UN e a movimentação exibe as duas grandezas (RN-19). Alterar o fator após uso é recusado (RN-20). |
| US-04 Lote e validade | **Dado** produto controlado por validade, **quando** habilito o controle em produto que já possui saldo, **então** o sistema exige a distribuição do saldo existente em lotes antes de concluir a mudança (RN-21). |
| US-12 Devoluções | **Dado** devolução de cliente, **quando** registro a entrada, **então** a quantidade compõe o saldo físico mas só entra no disponível após a conferência de integridade (RN-29); itens avariados são lançados como perda. |
| US-16 Sugestão de compra | **Dado** produto com ponto de reposição 100, saldo 30 e 40 em pedido pendente, **quando** a sugestão é gerada, **então** a quantidade sugerida é 30, arredondada para o múltiplo da embalagem de compra (RN-17). Nenhum pedido é emitido sem ação do perfil Compras (RN-18). |
| US-21 Giro de produtos | **Dado** um período informado, **quando** gero o relatório, **então** os produtos são ordenados por quantidade de **saída** e o relatório declara explicitamente que mede saídas de estoque, não vendas (Q-11). |
| US-22 Relatório de perdas | **Quando** gero o relatório, **então** vejo as perdas agrupadas por categoria e por período, com quantidade e participação percentual. |
| US-23 Exportação | **Quando** exporto um relatório, **então** obtenho arquivo CSV com os mesmos registros exibidos na tela e PDF com cabeçalho, filtros aplicados e data de geração. |
| US-26 Trilha de auditoria | **Dado** o perfil Gerente ou Administrador, **quando** filtro por usuário e período, **então** vejo autor, data, hora, operação, valor anterior e posterior (RF-34); o próprio acesso à trilha é registrado (RNF-25). |
| US-27 Pedido de compra | **Quando** registro um pedido, **então** ele nasce no estado "Pendente" com prazo de entrega previsto obrigatório (RN-32). |
| US-28 Entrada por pedido | **Dado** pedido de 100 UN, **quando** dou entrada de 60, **então** o status passa a "Parcialmente recebido" e o saldo aumenta em 60; tentativa de receber mais de 100 no total é recusada (RN-31). |
| US-29 Histórico de compras | **Quando** consulto um produto, **então** vejo as compras com fornecedor, data, quantidade e preço unitário, ordenadas por data decrescente. |
| US-30 Portal do fornecedor | **Dado** o perfil Fornecedor, **quando** acesso a consulta de pedidos, **então** vejo somente os pedidos do meu próprio cadastro, em modo leitura (RN-33). |
| US-31 Pedido atrasado | **Dado** pedido pendente com prazo vencido, **quando** a rotina diária é executada, **então** o perfil Compras é notificado com a lista de pedidos em atraso. |

---

## Definição de pronto

Uma história só é considerada concluída quando:

1. todos os seus critérios de aceitação passam em teste automatizado ou manual documentado;
2. as regras de negócio referenciadas estão implementadas na camada de domínio, com teste que cita o
   identificador `RN-nn` (RNF-27);
3. os requisitos não funcionais aplicáveis foram verificados pelo critério declarado em
   [requisitos-nao-funcionais.md](../02-analise/requisitos-nao-funcionais.md);
4. as tentativas de operação não autorizada geram registro de auditoria (RF-34);
5. nenhuma premissa nova foi assumida sem ter sido registrada em
   [lacunas-e-ambiguidades.md](../02-analise/lacunas-e-ambiguidades.md).
