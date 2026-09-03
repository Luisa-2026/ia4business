# Automações — IA4business

Registro do que foi ligado, por que, e a auditoria manter/consertar/matar. Atualizar a cada aula que ligar algo novo.

## Conectores

| Conector | Status | Justificativa |
|---|---|---|
| Notion | **MANTER** | Usado de verdade: relatório mensal FakeERP, relatório diário, rotina escreve nele sozinha. |
| Gmail | **CONSERTAR** | Ligado desde a Aula 9, mas nenhum fluxo foi construído em cima ainda — não existe página "Caixa de entrada" no Notion. Ideia boa (reduzir dúvida de atendimento), execução não aconteceu. Ou vira fluxo real na próxima aula, ou é candidato a desligar. |

## Fluxos

| Fluxo | Status | Justificativa |
|---|---|---|
| Relatório FakeERP (manual) | **MANTER** | Login + `/report/{year}/{month}` documentados em `fake-erp.md`, testado direto pelo terminal com jan/fev/mar/jul de 2026. Reproduzível com uma linha. |

## Rotinas

| Rotina | Agenda | Status | Justificativa |
|---|---|---|---|
| FakeERP — Relatório diário (`trig_01FzD9HPLRwfNPeUnNQWyx3S`) | todo dia, 8h (São Paulo) | **CONSERTAR** | Lógica correta e testada com "Run now": ao encontrar a API bloqueada, não inventou número nenhum nem escreveu no Notion, só avisou por notificação — comportamento certo de falha. Mas está de fato bloqueada: a política de rede do ambiente de nuvem rejeitou a conexão de saída para `fake-erp.isilab.com.br` (403 no CONNECT). Preciso liberar esse domínio nas configurações do ambiente antes da próxima execução (amanhã 8h). Dono: Luísa. |

## Auditoria desta semana

Primeira semana com automação de verdade — nada para **matar** ainda. Ponto de atenção real: o conector Gmail, ligado sem uso, e a rotina FakeERP, ligada mas bloqueada por rede. Sem isso resolvido, "todo dia às 8h" ainda não devolve tempo nenhum — é dívida, não ativo, até o bloqueio de rede ser corrigido.
