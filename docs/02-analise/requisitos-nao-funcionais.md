# Requisitos Não Funcionais — Sistema de Controle de Estoque (StockPlus)

> **Aviso de procedência:** a observação **O-09** da elicitação registra que *"não foram levantados
> requisitos relacionados à segurança, desempenho, disponibilidade, acessibilidade e privacidade dos
> dados"*. Consequentemente, **nenhum requisito desta página foi elicitado** — todos são propostos a
> partir do contexto do negócio e das boas práticas aplicáveis. Cada um traz a coluna **Situação**:
>
> - `A validar` — proposto, aguardando confirmação de um stakeholder ou da Equipe de TI.
> - `Derivado` — consequência lógica de um requisito funcional ou regra de negócio já aceita.
>
> Requisitos não funcionais sem métrica são inverificáveis. Por isso a coluna **Critério de
> verificação** é obrigatória em todos os itens.

---

## 1. Desempenho

| ID | Requisito | Critério de verificação | Situação |
|----|-----------|-------------------------|----------|
| RNF-01 | A busca de produto por nome ou código deve responder em até 2 segundos no percentil 95, com base de até 50.000 produtos. | Teste de carga com 50.000 SKUs e 30 usuários concorrentes; medir p95 do tempo de resposta. | A validar (quantifica o "rapidamente" de E-01) |
| RNF-02 | O registro de uma movimentação deve ser confirmado em até 3 segundos no percentil 95. | Teste de carga com 30 usuários concorrentes lançando movimentações. | A validar (quantifica o "de forma simples" de E-02) |
| RNF-03 | O saldo exibido deve refletir toda movimentação confirmada em no máximo 5 segundos. | Lançar movimentação e medir a latência até o valor aparecer no painel de outro usuário. | A validar (quantifica o "tempo real" de E-06 — ver [Q-10](lacunas-e-ambiguidades.md)) |
| RNF-04 | Relatórios gerenciais sobre até 12 meses de histórico devem ser gerados em até 10 segundos; acima disso, o processamento deve ser assíncrono com notificação de conclusão. | Executar RF-28 e RF-29 sobre base com 12 meses de movimentações. | A validar |
| RNF-05 | O sistema deve suportar 50 usuários simultâneos e 10.000 movimentações por dia sem degradação superior a 20% nos tempos de RNF-01 e RNF-02. | Teste de carga comparando linha de base e carga plena. | A validar (dimensionamento não informado na elicitação) |

## 2. Segurança

| ID | Requisito | Critério de verificação | Situação |
|----|-----------|-------------------------|----------|
| RNF-06 | Todo tráfego entre cliente e servidor deve ocorrer sobre HTTPS com TLS 1.2 ou superior. | Varredura de configuração TLS; rejeição de conexões em texto claro. | A validar |
| RNF-07 | Senhas devem ser armazenadas com função de derivação de chave resistente a força bruta (bcrypt, scrypt ou Argon2), nunca em texto claro nem com hash simples. | Inspeção do esquema de persistência e revisão de código. | Derivado (RN-08 exige credencial individual confiável) |
| RNF-08 | O controle de acesso deve ser aplicado no servidor, por operação, e não apenas por ocultação de elementos na interface. | Teste de tentativa de acesso direto a endpoints restritos com credencial de perfil inferior. | Derivado (RF-33, matriz de permissões de RN) |
| RNF-09 | A sessão do usuário deve expirar após 30 minutos de inatividade, exigindo reautenticação. | Teste funcional de expiração de sessão. | A validar |
| RNF-10 | O acesso do perfil Fornecedor deve ser isolado dos dados internos, sem possibilidade de enumerar pedidos de terceiros por manipulação de identificadores. | Teste de referência direta insegura a objeto (IDOR) alternando identificadores de pedido. | Derivado (RN-33) |
| RNF-11 | Tentativas de autenticação falhadas devem ser limitadas a 5 por credencial em 15 minutos, com bloqueio temporário e registro do evento. | Teste de força bruta controlado. | A validar |

## 3. Auditabilidade

| ID | Requisito | Critério de verificação | Situação |
|----|-----------|-------------------------|----------|
| RNF-12 | Os registros de auditoria devem ser somente-inserção: nenhuma operação da aplicação pode alterá-los ou removê-los. | Revisão de permissões de banco e ausência de comandos de atualização/remoção sobre a tabela de auditoria. | Derivado (E-08, RN-07) |
| RNF-13 | O histórico de movimentações e a trilha de auditoria devem ser retidos por no mínimo 5 anos. | Verificação da política de retenção e da rotina de arquivamento. | A validar (prazo legal/fiscal não confirmado) |
| RNF-14 | Todo registro temporal deve ser gravado em UTC, com exibição convertida para o fuso do usuário. | Inspeção dos dados persistidos e da camada de apresentação. | Derivado (RN-06 — ordem cronológica confiável) |

## 4. Disponibilidade e continuidade

| ID | Requisito | Critério de verificação | Situação |
|----|-----------|-------------------------|----------|
| RNF-15 | O sistema deve estar disponível 99,5% do tempo na janela operacional (dias úteis, 06:00 às 22:00), o que admite até ~4 horas de indisponibilidade por mês. | Monitoramento de disponibilidade com apuração mensal. | A validar (janela operacional não elicitada) |
| RNF-16 | Deve haver rotina diária de cópia de segurança com retenção mínima de 30 dias e teste de restauração trimestral documentado. | Evidência de execução dos backups e relatório do teste de restauração. | A validar |
| RNF-17 | O objetivo de tempo de recuperação (RTO) é de 4 horas e o de ponto de recuperação (RPO) é de 1 hora. | Exercício de recuperação de desastre medindo RTO e RPO efetivos. | A validar |
| RNF-18 | Indisponibilidade do serviço de e-mail não deve impedir o registro de movimentações; as notificações devem ser enfileiradas e reenviadas. | Teste com serviço de e-mail indisponível durante lançamentos. | Derivado (RF-17 — alerta não pode bloquear operação) |

## 5. Usabilidade e acessibilidade

| ID | Requisito | Critério de verificação | Situação |
|----|-----------|-------------------------|----------|
| RNF-19 | O registro de uma entrada ou saída deve ser concluído em no máximo 5 interações (campos e cliques) a partir da tela inicial. | Análise de tarefa e teste com usuário do perfil Estoquista. | A validar (operacionaliza o "de forma simples" de E-02) |
| RNF-20 | Um estoquista sem treinamento formal deve concluir o registro de uma saída em até 3 minutos na primeira tentativa, com taxa de sucesso de 90% em teste com 10 participantes. | Teste de usabilidade com 10 participantes representativos. | A validar |
| RNF-21 | As telas operacionais devem atender ao WCAG 2.1 nível AA: contraste mínimo de 4.5:1, navegação completa por teclado e rótulos associados a todos os campos. | Auditoria automatizada mais verificação manual de navegação por teclado. | A validar (O-09) |
| RNF-22 | Mensagens de erro devem indicar a causa e a ação corretiva, sem exibir códigos técnicos ou rastreamento de pilha ao usuário final. | Revisão do catálogo de mensagens. | A validar |
| RNF-23 | As telas de consulta de saldo e de registro de movimentação devem ser utilizáveis em tela de 7 polegadas ou maior, prevendo uso em tablet no armazém. | Teste responsivo nas resoluções alvo. | A validar |

## 6. Privacidade e conformidade

| ID | Requisito | Critério de verificação | Situação |
|----|-----------|-------------------------|----------|
| RNF-24 | Os dados pessoais tratados devem se limitar ao necessário para identificar o autor de cada operação (nome, matrícula ou e-mail corporativo e perfil), em observância ao princípio da necessidade da LGPD. | Inventário de dados pessoais e revisão de finalidade. | A validar (O-09) |
| RNF-25 | O acesso à trilha de auditoria, por conter dados pessoais de desempenho, deve ser restrito aos perfis Gerente e Administrador de TI e ele próprio registrado. | Teste de permissão e verificação do log de acesso ao log. | Derivado (RF-35, RNF-08) |
| RNF-26 | Dados de contato de fornecedores devem ser acessíveis apenas aos perfis Compras, Gerente e Administrador de TI. | Teste de permissão por perfil. | Derivado (RN-33) |

## 7. Manutenibilidade e evolução

| ID | Requisito | Critério de verificação | Situação |
|----|-----------|-------------------------|----------|
| RNF-27 | As regras de negócio documentadas em [regras-de-negocio.md](regras-de-negocio.md) devem ser implementadas em camada de domínio independente da interface, coberta por testes automatizados que referenciem o identificador da regra (`RN-nn`). | Relatório de cobertura com rastreio regra → teste. | A validar |
| RNF-28 | A cobertura de testes automatizados do domínio de movimentação e saldo deve ser de no mínimo 80%. | Relatório de cobertura da suíte de testes. | A validar |
| RNF-29 | O cálculo de estoque mínimo (RN-11) e a margem de segurança devem ser parametrizáveis por configuração, sem necessidade de alteração de código. | Alterar parâmetros em ambiente de teste e verificar o efeito. | Derivado (O-01 permanece em aberto — a fórmula tende a mudar) |
| RNF-30 | O sistema deve expor os saldos e as movimentações por interface programável documentada, preparando a integração futura com vendas e compras sem reescrita do núcleo. | Existência de contrato de API versionado e documentado. | A validar (mitiga o risco de [Q-07](lacunas-e-ambiguidades.md)) |

---

## 8. Prioridade e recomendação

Nem todos os requisitos acima cabem no MVP. A recomendação de sequenciamento é:

| Faixa | Requisitos | Justificativa |
|-------|------------|---------------|
| **Bloqueiam o MVP** | RNF-06, RNF-07, RNF-08, RNF-12, RNF-14, RNF-19 | Sem eles, os requisitos funcionais `Must` de auditoria e autoria (RF-12, RF-32, RF-34) não se sustentam: um sistema que registra autor sem controle de acesso confiável produz um rastro sem valor probatório. |
| **Primeira release após o MVP** | RNF-01 a RNF-05, RNF-09 a RNF-11, RNF-16, RNF-18, RNF-27, RNF-28 | Exigem infraestrutura de teste e de operação que pode amadurecer junto com o produto. |
| **Dependem de decisão externa** | RNF-13, RNF-15, RNF-17, RNF-21, RNF-23, RNF-24, RNF-30 | Envolvem definições de negócio, jurídicas ou de infraestrutura ainda não obtidas — ver [lacunas-e-ambiguidades.md](lacunas-e-ambiguidades.md). |

**Próximo passo indicado:** submeter esta página à Equipe de TI e à Gerência em uma sessão de
validação dedicada. Enquanto os itens `A validar` não forem confirmados, os números aqui registrados
devem ser lidos como propostas de partida, não como contrato.
