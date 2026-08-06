# Matriz de Rastreabilidade — Sistema de Controle de Estoque (StockPlus)

Vínculo bidirecional entre a fala do stakeholder e o artefato que a implementa. Permite responder a
duas perguntas que a documentação, isolada em arquivos separados, não responde:

- **Rastreabilidade progressiva:** dada uma necessidade da elicitação, onde ela foi tratada?
- **Rastreabilidade regressiva:** dado um requisito, quem o pediu — ou ele apareceu sem origem?

A segunda pergunta é a que revela escopo inflado. Requisito sem origem rastreável é candidato natural
a corte.

---

## 1. Da elicitação ao critério de aceitação

| Origem | Necessidade | RF | RN | História | Critérios |
|--------|-------------|----|----|----------|-----------|
| E-01 | Localizar produto por nome ou código | RF-02, RF-03 | — | US-02 | CA-02.1 a CA-02.5 |
| E-02 | Registrar entradas e saídas de forma simples | RF-07, RF-08 | RN-01, RN-02, RN-04, RN-19 | US-06, US-07 | CA-06.1 a CA-06.5, CA-07.1 a CA-07.6 |
| E-03 | Aviso de estoque baixo | RF-15, RF-17 | RN-13, RN-14, RN-15 | US-13 | CA-13.1 a CA-13.4 |
| E-04 | Histórico de movimentações por produto | RF-11, RF-12 | RN-06, RN-09 | US-10 | CA-10.1 a CA-10.4 |
| E-05 | Registrar perdas por vencimento, avaria e extravio | RF-09, RF-30 | RN-24, RN-23 | US-08, US-22 | CA-08.1 a CA-08.4 |
| E-06 | Estoque em tempo real | RF-10, RF-27 | RN-03, RN-05 | US-09, US-20 | CA-09.1 a CA-09.4, CA-20.1 a CA-20.3 |
| E-07 | Relatórios de mais vendidos e de baixa movimentação | RF-28, RF-29 | — | US-21 | Cenário essencial US-21 |
| E-08 | Controlar quem realizou cada movimentação | RF-12, RF-13, RF-32, RF-34 | RN-06, RN-07, RN-08 | US-11, US-24, US-26 | CA-11.1 a CA-11.4, CA-24.1 a CA-24.5 |
| E-09 | Inventários periódicos | RF-21, RF-23, RF-26 | RN-26, RN-27, RN-28 | US-17, US-18 | CA-17.1 a CA-17.5, CA-18.1 a CA-18.4 |
| E-10 | Ajustes com justificativa | RF-24, RF-25 | RN-25 | US-19 | CA-19.1 a CA-19.4 |
| E-11 | Alertas automáticos no estoque mínimo | RF-15, RF-17, RF-18 | RN-11, RN-13 | US-13, US-15 | CA-13.1, CA-15.1 a CA-15.4 |
| E-12 | Sugestões de compra automáticas | RF-19, RF-20 | RN-17, RN-18 | US-16 | Cenário essencial US-16 |
| E-13 | Histórico de compras por produto | RF-36, RF-37, RF-38 | RN-31, RN-32 | US-27, US-28, US-29 | Cenários essenciais US-27 a US-29 |
| E-14 | Fornecedor informado sobre pedidos e prazos | RF-36, RF-39, RF-40 | RN-33 | US-27, US-30, US-31 | CA-25.3, cenário essencial US-30 |

**Cobertura:** as 14 falas registradas na elicitação estão tratadas. Nenhuma necessidade elicitada
ficou sem requisito correspondente.

---

## 2. Das observações às premissas e questões abertas

| Observação | Questão | Premissa adotada | RF | RN | História |
|------------|---------|------------------|----|----|----------|
| O-01 | Q-01 | Fórmula de estoque mínimo com margem de 20% | RF-18 | RN-11, RN-12 | US-15 |
| O-02 | Q-02 | Ajuste manual exclusivo do Gerente | RF-25, RF-33 | RN-25 | US-19, US-25 |
| O-03 | Q-03 | Unidade base única com fator fixo | RF-04 | RN-19, RN-20 | US-03 |
| O-04 | Q-04 | Inventário por conjunto, com bloqueio e dupla assinatura | RF-21, RF-22, RF-23 | RN-26, RN-27 | US-17, US-18 |
| O-05 | Q-05 | Lote e validade opcionais, saída por FEFO | RF-05 | RN-21, RN-22, RN-23 | US-04 |
| O-06 | Q-06 | Devoluções como tipos próprios de movimentação | RF-14 | RN-29, RN-30 | US-12 |
| O-07 | Q-07 | Sem integração nesta versão; API exposta | RF-41, RF-42 (`Won't`) | — | — |
| O-08 | Q-08 | Painel mais e-mail, com supressão de 24 h | RF-16, RF-17 | RN-14, RN-16 | US-13, US-14 |
| O-09 | Q-09 | Conjunto completo de RNFs propostos | — | — | Transversal |

---

## 3. Requisitos sem origem direta na elicitação

Requisitos derivados pela análise. Cada um precisa justificar sua existência, sob pena de ser escopo
não pedido.

| RF | Requisito | Derivado de | Justificativa da derivação |
|----|-----------|-------------|----------------------------|
| RF-01 | Cadastro de produtos | Pré-condição de todas as falas | Nenhuma necessidade elicitada é realizável sem catálogo |
| RF-06 | Inativação em vez de exclusão | E-04 | Excluir produto destruiria o histórico que E-04 exige |
| RF-13 | Estorno em vez de edição | E-08 | Editar movimentação inutiliza o controle de autoria de E-08 |
| RF-20 | Aprovação da sugestão de compra | E-12 | Sugestão que compra sozinha não é sugestão; retira a decisão do comprador |
| RF-26 | Indicador de acurácia | E-09 | Conferir sem medir não fecha o ciclo de melhoria |
| RF-31 | Exportação de relatórios | E-07 | Relatório gerencial circula fora do sistema |
| RF-35 | Consulta da trilha de auditoria | RF-34 | Registro que não pode ser consultado não serve à investigação |
| RF-40 | Alerta de pedido atrasado | E-14 | Prazo registrado sem acompanhamento não gera ação |

Nenhum requisito ficou sem justificativa. Os quatro itens classificados como `Won't` em
[requisitos-funcionais.md](requisitos-funcionais.md) foram explicitamente recusados, e não
silenciosamente omitidos.

---

## 4. Regras de negócio por requisito

Verificação inversa: toda regra é aplicada em algum lugar?

| RN | Requisitos que a aplicam | História |
|----|--------------------------|----------|
| RN-01 | RF-08, RF-10, RF-24 | US-07, US-11, US-19 |
| RN-02 | RF-08 | US-07 |
| RN-03 | RF-10, RF-24 | US-09, US-19 |
| RN-04 | RF-07, RF-08, RF-09 | US-06, US-07, US-08 |
| RN-05 | RF-05, RF-22 | US-04, US-09, US-17 |
| RN-06 | RF-12, RF-32 | US-06, US-07, US-10, US-24 |
| RN-07 | RF-13 | US-11 |
| RN-08 | RF-32 | US-24 |
| RN-09 | RF-06 | US-05 |
| RN-10 | RF-01 | US-01 |
| RN-11 | RF-18 | US-15 |
| RN-12 | RF-18, RF-25 | US-15 |
| RN-13 | RF-15 | US-13 |
| RN-14 | RF-15, RF-17 | US-13 |
| RN-15 | RF-15 | US-13 |
| RN-16 | RF-16, RF-17 | US-14 |
| RN-17 | RF-19 | US-16 |
| RN-18 | RF-20 | US-16 |
| RN-19 | RF-04 | US-03 |
| RN-20 | RF-04 | US-03 |
| RN-21 | RF-05, RF-07 | US-04, US-06 |
| RN-22 | RF-05, RF-08 | US-04, US-07 |
| RN-23 | RF-05, RF-09 | US-04, US-07, US-08 |
| RN-24 | RF-09 | US-08 |
| RN-25 | RF-24, RF-25 | US-19 |
| RN-26 | RF-22 | US-17 |
| RN-27 | RF-21, RF-23 | US-18 |
| RN-28 | RF-26 | US-18 |
| RN-29 | RF-09, RF-14 | US-12 |
| RN-30 | RF-14, RF-36 | US-12 |
| RN-31 | RF-38 | US-28 |
| RN-32 | RF-38 | US-27, US-28 |
| RN-33 | RF-33, RF-39 | US-25, US-30 |

Todas as 33 regras têm ponto de aplicação. Nenhuma regra ficou órfã.

---

## 5. Requisitos não funcionais por requisito funcional afetado

| RNF | Categoria | Afeta |
|-----|-----------|-------|
| RNF-01 | Desempenho | RF-02 |
| RNF-02 | Desempenho | RF-07, RF-08, RF-09 |
| RNF-03 | Desempenho | RF-10, RF-27 |
| RNF-04 | Desempenho | RF-28, RF-29, RF-30 |
| RNF-05 | Desempenho | Transversal |
| RNF-06 a RNF-11 | Segurança | RF-32, RF-33, RF-39 |
| RNF-12 a RNF-14 | Auditabilidade | RF-12, RF-13, RF-34 |
| RNF-15 a RNF-18 | Disponibilidade | Transversal; RNF-18 afeta RF-17 |
| RNF-19 a RNF-23 | Usabilidade | RF-02, RF-07, RF-08, RF-21 |
| RNF-24 a RNF-26 | Privacidade | RF-34, RF-35, RF-36 |
| RNF-27 a RNF-30 | Manutenibilidade | Transversal; RNF-29 afeta RF-18; RNF-30 mitiga Q-07 |

---

## 6. Cobertura por caso de uso

| Caso de uso | Requisitos cobertos | Regras exercitadas | Histórias |
|-------------|---------------------|--------------------|-----------|
| UC-01 Registrar saída | RF-08, RF-10, RF-12, RF-16 | RN-01, RN-02, RN-04, RN-06, RN-07, RN-19, RN-22, RN-23, RN-26 | US-07 |
| UC-02 Realizar inventário | RF-21, RF-22, RF-23, RF-26 | RN-05, RN-26, RN-27, RN-28 | US-17, US-18 |
| UC-03 Repor a partir de alerta | RF-15, RF-17, RF-18, RF-19, RF-20, RF-36, RF-38 | RN-11, RN-13, RN-14, RN-15, RN-17, RN-18, RN-31, RN-32 | US-13, US-15, US-16, US-27, US-28 |

Os três casos de uso, somados, exercitam 22 das 33 regras de negócio. As 11 restantes concentram-se em
cadastro, permissão e devoluções — fluxos lineares, adequadamente cobertos por história e critério de
aceitação.

---

## 7. Leitura crítica da matriz

Três observações que só ficam visíveis quando os artefatos são cruzados:

1. **RF-03 (localização física) tem origem frágil.** Deriva de uma leitura possível de E-01, cuja
   ambiguidade está registrada em Q-12. É o requisito de sustentação mais fraca entre os que
   permaneceram no escopo.

2. **E-07 não é atendida como foi pedida.** O stakeholder pediu "produtos mais vendidos"; a matriz
   entrega relatório de saídas. A linha da matriz está preenchida, mas a necessidade original só será
   plenamente atendida se Q-07 for resolvida com integração. Rastreabilidade formal não é o mesmo que
   satisfação do stakeholder — e é justamente esse tipo de lacuna que a matriz existe para expor.

3. **E-14 foi atendida por excesso.** A fala pede apenas ser informado sobre pedidos e prazos; a
   especificação produziu um portal autenticado para ator externo (RF-39), com todo o custo de
   segurança que isso implica (RNF-10, RN-33). Manter o requisito como `Could` foi a mitigação
   adotada, mas a resposta a Q-13 pode permitir substituí-lo por uma notificação por e-mail — solução
   de custo e risco muito menores para a mesma necessidade.
