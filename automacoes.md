# Automações — IA4business

Registro do que foi ligado, por que, e a auditoria manter/consertar/matar. Atualizar a cada aula que ligar algo novo.

## Conectores

| Conector | Status | Justificativa |
|---|---|---|
| Notion | **MANTER** | Usado de verdade: relatório mensal FakeERP, relatório diário, e agora a página [Alertas — IA4business](https://app.notion.com/p/3d7fb134714b81ca9eeaef83df2a9cce), destino da Regra 1 de `regras.md`. |
| Gmail | **CONSERTAR** | Ligado desde a Aula 9, mas nenhum fluxo foi construído em cima ainda — não existe página "Caixa de entrada" no Notion. Ideia boa (reduzir dúvida de atendimento), execução não aconteceu. Ou vira fluxo real na próxima aula, ou é candidato a desligar. |

## Fluxos

| Fluxo | Status | Justificativa |
|---|---|---|
| Relatório FakeERP (manual) | **MANTER** | Login + `/report/{year}/{month}` documentados em `fake-erp.md`, testado direto pelo terminal com jan/fev/mar/jul de 2026. Reproduzível com uma linha. |

## Rotinas

| Rotina | Agenda | Status | Justificativa |
|---|---|---|---|
| FakeERP — Relatório diário (`trig_01FzD9HPLRwfNPeUnNQWyx3S`) | todo dia, 8h (São Paulo) | **CONSERTAR** | Lógica correta e testada com "Run now": ao encontrar a API bloqueada, não inventou número nenhum nem escreveu no Notion, só avisou por notificação — comportamento certo de falha. Mas está de fato bloqueada: a política de rede do ambiente de nuvem rejeitou a conexão de saída para `fake-erp.isilab.com.br` (403 no CONNECT). Preciso liberar esse domínio nas configurações do ambiente antes da próxima execução (amanhã 8h). Dono: Luísa. |

## Regras ligadas

Arquivo das regras: [regras.md](regras.md) (5 regras — venda cancelada, receita paga abaixo da meta, pagamento pendente há mais de 15 dias, desconto fora do padrão, concentração de faturamento em um pedido)

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

## Auditoria desta semana

Primeira semana com automação de verdade — nada para **matar** ainda. Ponto de atenção real: o conector Gmail, ligado sem uso, e a rotina FakeERP, ligada mas bloqueada por rede. Sem isso resolvido, "todo dia às 8h" ainda não devolve tempo nenhum — é dívida, não ativo, até o bloqueio de rede ser corrigido.
