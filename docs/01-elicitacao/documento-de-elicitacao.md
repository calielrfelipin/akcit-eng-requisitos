# Documento de Elicitação — Sistema de Controle de Estoque (StockPlus)

> **Natureza deste documento:** artefato **de entrada**, produzido na etapa de elicitação.
> É a única fonte primária do projeto. Todo requisito, regra de negócio ou história de usuário
> registrada nas pastas `02-analise/` e `03-especificacao/` deve ser rastreável até um trecho
> deste documento — ou, quando não for, deve estar explicitamente marcada como **premissa**
> em [lacunas-e-ambiguidades.md](../02-analise/lacunas-e-ambiguidades.md).

---

## 1. Contexto do projeto

A empresa **StockPlus** atua na comercialização de produtos para varejo e atacado. Atualmente, o
controle de estoque é realizado por meio de planilhas eletrônicas e registros manuais, dificultando
o acompanhamento das entradas e saídas de produtos, a reposição de mercadorias e a identificação de
perdas. Com o crescimento do volume de operações, a empresa decidiu desenvolver um sistema para
centralizar o gerenciamento do estoque, proporcionando maior controle sobre os produtos, redução de
erros e apoio à tomada de decisões.

### Problema central

O controle descentralizado em planilhas produz três efeitos indesejados que o sistema deve atacar:

| # | Efeito atual | Consequência para o negócio |
|---|--------------|-----------------------------|
| P1 | Saldo de estoque não confiável | Vendas de itens indisponíveis e compras desnecessárias |
| P2 | Ausência de rastro de quem movimentou o quê | Perdas não explicadas e impossibilidade de auditoria |
| P3 | Reposição reativa, baseada em percepção | Ruptura de estoque e capital parado em itens de baixo giro |

---

## 2. Stakeholders identificados

| Stakeholder | Interesse | Papel na elicitação |
|-------------|-----------|---------------------|
| Estoquistas | Registrar entradas e saídas de produtos, consultar quantidades e localizar itens no estoque | Usuário operacional — entrevistado |
| Gerentes | Acompanhar níveis de estoque, aprovar ajustes de inventário e gerar relatórios gerenciais | Aprovador / patrocinador — entrevistado |
| Setor de Compras | Monitorar produtos com estoque baixo e realizar reposições | Usuário tático — entrevistado |
| Fornecedores | Fornecer produtos e acompanhar pedidos de compra quando necessário | Ator externo — entrevistado indiretamente |
| Equipe de TI | Desenvolver, manter e garantir o funcionamento do sistema | Executor — não entrevistado nesta etapa |

---

## 3. Informações obtidas nas entrevistas

As falas abaixo estão transcritas literalmente. Cada uma recebeu um identificador (`E-nn`) para
permitir rastreabilidade a partir dos requisitos.

### 3.1 Estoquistas

| ID | Fala do stakeholder |
|----|---------------------|
| E-01 | "Gostaria de localizar rapidamente qualquer produto pelo nome ou código." |
| E-02 | "Precisamos registrar entradas e saídas de mercadorias de forma simples." |
| E-03 | "Seria útil receber um aviso quando um produto estiver com estoque baixo." |
| E-04 | "Gostaria de consultar o histórico de movimentações de cada produto." |
| E-05 | "Precisamos registrar perdas por vencimento, avarias ou extravios." |

### 3.2 Gerentes

| ID | Fala do stakeholder |
|----|---------------------|
| E-06 | "Precisamos acompanhar o estoque em tempo real." |
| E-07 | "Gostaríamos de visualizar relatórios de produtos mais vendidos e de baixa movimentação." |
| E-08 | "É importante controlar quem realizou cada movimentação no estoque." |
| E-09 | "Precisamos realizar inventários periódicos para conferir o estoque físico." |
| E-10 | "Os ajustes de estoque devem ser registrados com justificativa." |

### 3.3 Setor de Compras

| ID | Fala do stakeholder |
|----|---------------------|
| E-11 | "Gostaríamos de receber alertas automáticos quando um produto atingir o estoque mínimo." |
| E-12 | "Seria interessante gerar sugestões de compra automaticamente." |
| E-13 | "Precisamos consultar o histórico de compras de cada produto." |

### 3.4 Fornecedores

| ID | Fala do stakeholder |
|----|---------------------|
| E-14 | "Seria útil receber informações sobre pedidos realizados e seus respectivos prazos de entrega." |

---

## 4. Observações registradas ao final da elicitação

Durante as entrevistas, alguns pontos permaneceram sem definição e deverão ser analisados. Cada
observação recebeu um identificador (`O-nn`) e é tratada em
[lacunas-e-ambiguidades.md](../02-analise/lacunas-e-ambiguidades.md).

| ID | Ponto em aberto |
|----|-----------------|
| O-01 | Não foi definido como será calculado o estoque mínimo de cada produto. |
| O-02 | Não está claro quais usuários poderão realizar ajustes manuais de estoque. |
| O-03 | Não foi informado como o sistema tratará produtos com diferentes unidades de medida. |
| O-04 | Não foi definido como será realizado o processo de inventário físico. |
| O-05 | Não está claro se o sistema permitirá o cadastro de produtos com controle por lote ou validade. |
| O-06 | Não foi informado como serão tratadas devoluções de clientes e devoluções a fornecedores. |
| O-07 | Não foi definido se haverá integração com sistemas de vendas ou de compras. |
| O-08 | Não foram estabelecidas regras para notificações de estoque baixo ou ruptura de estoque. |
| O-09 | Não foram levantados requisitos relacionados à segurança, desempenho, disponibilidade, acessibilidade e privacidade dos dados. |

---

## 5. Limites conhecidos da elicitação

Três limitações desta etapa condicionam a análise subsequente e devem ser lembradas na leitura dos
demais artefatos:

1. **A Equipe de TI não foi entrevistada.** Nenhuma restrição tecnológica, de infraestrutura ou de
   integração foi capturada na fonte. Todos os requisitos não funcionais em
   `02-analise/requisitos-nao-funcionais.md` são, portanto, **propostos**, não elicitados.
2. **O Fornecedor foi ouvido por meio de uma única fala.** Não há informação suficiente para
   decidir se ele será usuário autenticado do sistema ou apenas destinatário de notificações.
3. **Não houve observação do processo físico no armazém.** O documento descreve o que os
   stakeholders *dizem* que fazem, o que tipicamente difere do fluxo real de conferência,
   endereçamento e contagem.
