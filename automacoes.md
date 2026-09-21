# Automações — IA4business

Registro do que foi ligado, por que, e a auditoria manter/consertar/matar. Atualizar a cada aula que ligar algo novo.

## Conectores

| Conector | Status | Justificativa |
|---|---|---|
| Notion | **MANTER** | Usado de verdade: relatório mensal FakeERP, relatório diário, e agora a página [Alertas — IA4business](https://app.notion.com/p/3d7fb134714b81ca9eeaef83df2a9cce), destino da Regra 1 de `regras.md`. |
| Gmail | **CONSERTAR** | Ganhou primeiro uso real na Regra 6 de `regras.md` (busca por palavra-chave, correlacionando com pedido cancelado do FakeERP), mas só como segunda fonte de uma regra que nunca dispara — a caixa é pessoal e de treino, sem pedido de cliente de verdade. Vira fluxo real quando a fonte 2 for o e-mail de atendimento da empresa, ligado ao Kommo. |

## Fluxos

| Fluxo | Status | Justificativa |
|---|---|---|
| Relatório FakeERP (manual) | **MANTER** | Login + `/report/{year}/{month}` documentados em `fake-erp.md`, testado direto pelo terminal com jan/fev/mar/jul de 2026. Reproduzível com uma linha. |

## Rotinas

| Rotina | Agenda | Status | Justificativa |
|---|---|---|---|
| FakeERP — Relatório diário (`trig_01FzD9HPLRwfNPeUnNQWyx3S`) | todo dia, 8h (São Paulo) | **CONSERTAR** | Lógica correta e testada com "Run now": ao encontrar a API bloqueada, não inventou número nenhum nem escreveu no Notion, só avisou por notificação — comportamento certo de falha. Mas está de fato bloqueada: a política de rede do ambiente de nuvem rejeitou a conexão de saída para `fake-erp.isilab.com.br` (403 no CONNECT). Preciso liberar esse domínio nas configurações do ambiente antes da próxima execução (amanhã 8h). Dono: Luísa. |

## Regras ligadas

Arquivo das regras: [regras.md](regras.md) (6 regras — venda cancelada, receita paga abaixo da meta, pagamento pendente há mais de 15 dias, desconto fora do padrão, concentração de faturamento em um pedido, pedido cancelado com reclamação por e-mail)

Como roda: à mão no Claude Code, testado contra janeiro/2026 (dispara) e julho/2026 (calada). Ainda não agendada como rotina — depende primeiro do bloqueio de rede do FakeERP ser resolvido (ver rotina abaixo).

## Execuções

| Data | Regra | Fonte | Disparou? | Número que decidiu |
|---|---|---|---|---|
| 10/09 | venda-cancelada | FakeERP 01/2026 | sim | 1 pedido CANCELLED (orderId 1003, R$225,00) — escrito na página [Alertas — IA4business](https://app.notion.com/p/3d7fb134714b81ca9eeaef83df2a9cce) do Notion (degrau 1, ação saiu do repositório) |
| 10/09 | venda-cancelada | FakeERP 07/2026 | não | 0 pedidos CANCELLED entre 2 pedidos olhados |
| 10/09 | receita-abaixo-meta | FakeERP 01/2026 | sim | receita paga R$1.430,00 (< R$2.000,00) |
| 10/09 | receita-abaixo-meta | FakeERP 07/2026 | não | receita paga R$4.300,00 (≥ R$2.000,00) |
| 10/09 | pendente-15-dias | FakeERP 01/2026 | sim | orderId 1004, pendente desde 28/01 (>15 dias) |
| 10/09 | pendente-15-dias | FakeERP 07/2026 | não | 0 pedidos PENDING entre 2 pedidos olhados |
| 10/09 | desconto-fora-padrao | FakeERP 02/2026 | sim | orderId 1007, desconto 10% (R$250,00 de R$2.500,00) |
| 10/09 | desconto-fora-padrao | FakeERP 01/2026 | não | maior desconto 9,12% (orderId 1004), abaixo dos 10% |
| 10/09 | concentracao-faturamento | FakeERP 07/2026 | sim | orderId 1012, 93% do faturamento pago do mês (R$4.000,00 de R$4.300,00) |
| 10/09 | concentracao-faturamento | FakeERP 02/2026 | não | maior concentração 71,4% (orderId 1007), abaixo dos 75%, entre 3 pedidos pagos |
| 10/09 | cancelado-com-email | FakeERP 01/2026 + Gmail 18–23/01/2026 | não | 1 pedido CANCELLED (orderId 1003) na fonte 1; 0 e-mails com palavra-chave na fonte 2 |
| 10/09 | cancelado-com-email | FakeERP 03/2026 + Gmail 28/03–02/04/2026 | não | 1 pedido CANCELLED (orderId 1010) na fonte 1; 0 e-mails com palavra-chave na fonte 2 |

## Painel

`painel.html`, na raiz do repositório. Mostra os 3 números declarados em [dados/fonte.md](dados/fonte.md) — pedidos no mês, receita paga no mês, ticket médio pago — calculados a partir de [dados/amostra.csv](dados/amostra.csv) (janeiro/2026, FakeERP). Arquivo único, dados embutidos no HTML: abre com dois cliques, em qualquer computador, sem servidor e sem internet.

**Como é atualizado hoje:** à mão. Sempre que `dados/amostra.csv` mudar (novo mês, dado real substituindo o de treino), rodar de novo o prompt do passo 3 da aula ("lê o fonte.md e o amostra.csv, gera o painel.html") para o painel se refazer a partir do dado novo — nunca editar o HTML célula por célula.

**Ainda não é rotina.** Ligar isso a "todo dia às 8h" (faixa rápida da aula, degrau 2) depende do mesmo bloqueio da rotina do FakeERP logo acima.

| Data | Fonte usada | Números gerados | Observação |
|---|---|---|---|
| 14/09 | dados/amostra.csv (FakeERP, jan/2026) | 4 pedidos · receita paga R$ 1.430,00 · ticket médio pago R$ 715,00 | Primeira tentativa: API fora do ar (502 em todos os endpoints, 8 tentativas em ~2 min). 3 dos 4 pedidos vieram de execuções já documentadas acima, e o 4º (PAID, R$ 480,00) foi deduzido dos agregados já confirmados, sem inventar `orderId`/data/valor bruto. ~20 min depois a API voltou (`HTTP 200`) e `GET /report/2026/1` reconfirmou o pedido de verdade: 1002, 12/01/2026, `value` 480,00, `discount` 0,00 — a dedução bateu exatamente. `dados/amostra.csv` e `painel.html` foram atualizados com o dado direto |

## Auditoria desta semana

Primeira semana com automação de verdade — nada para **matar** ainda. Ponto de atenção real: o conector Gmail, ligado sem uso, e a rotina FakeERP, ligada mas bloqueada por rede. Sem isso resolvido, "todo dia às 8h" ainda não devolve tempo nenhum — é dívida, não ativo, até o bloqueio de rede ser corrigido.

## Testes de falha (21/09)

Rodei de verdade os 3 cenários de falha da Aula 14 contra `dados/amostra.csv` (renomeei o arquivo, sujei 3 linhas, restaurei tudo depois) — resultado completo em [testes.md](testes.md). Achado principal: sem uma regra escrita, nada impedia a IA de reaproveitar um número já visto antes na conversa como se fosse uma leitura nova da fonte. Corrigi isso acrescentando a seção "Regra de segurança da fonte" em `regras.md` (21/09, 09h12) — "FONTE INDISPONÍVEL" obrigatório quando a fonte falha, proibido reaproveitar número antigo, e obrigação de informar linhas lidas/ignoradas e por quê. Repeti o teste de fonte fora do ar com a regra nova e o comportamento mudou de "sem instrução nenhuma" para recusa explícita.
