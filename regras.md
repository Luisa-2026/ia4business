# Regras — IA4business

Regras que a rotina usa para decidir quando falar e quando ficar calada. Fonte de dado: FakeERP (ver `fake-erp.md`), usado aqui como proxy de "pedidos" — cancelamento, receita paga e pendência em aberto são os três tipos de exceção que hoje só chegam ao meu pai porque alguém percebeu manualmente.

Em nenhuma regra a ação é executar algo sozinha: o máximo que a automação faz é escrever o alerta. Quem decide e quem age continua sendo uma pessoa — [problema.md](problema.md) e o `CLAUDE.md` do projeto exigem revisão humana como etapa final.

## Regra 1 — Venda cancelada no mês

| Campo | Valor |
|---|---|
| Nome | Venda cancelada no mês |
| Gatilho | Tempo. Todo dia às 8h (ou à mão, toda segunda) |
| Fonte | FakeERP, pedidos do mês corrente — `GET /report/{year}/{month}`, ver `fake-erp.md` |
| Condição | Existe pelo menos 1 pedido com `status: "CANCELLED"` no mês consultado |
| Ação | Escrever alerta com `orderId`, valor (`total`) e data de cada pedido cancelado |
| Quem recebe | Eu (Luisa), no arquivo `alertas/`. Repasso ao meu pai só se a causa não for óbvia |
| Se não disparar | Gravar em `automacoes.md` a data, o mês consultado e quantos pedidos foram olhados |

## Regra 2 — Receita paga do mês abaixo da meta

| Campo | Valor |
|---|---|
| Nome | Receita paga do mês abaixo da meta |
| Gatilho | Tempo. Todo dia às 8h, a partir do dia 20 do mês |
| Fonte | FakeERP, pedidos do mês corrente — `GET /report/{year}/{month}`, ver `fake-erp.md` |
| Condição | Receita paga (soma do campo `total` só dos pedidos com `status: "PAID"`, ignorando `CANCELLED` e `PENDING`) abaixo de R$ 2.000, faltando 10 dias ou menos para fechar o mês |
| Ação | Escrever alerta com a receita paga atual, quanto falta para R$ 2.000 e os pedidos `PENDING` que poderiam fechar a diferença |
| Quem recebe | Eu (Luisa), no arquivo `alertas/`. Repasso ao meu pai, já que é uma decisão estratégica dele |
| Se não disparar | Gravar em `automacoes.md` a data, a receita paga apurada e quantos pedidos foram olhados |

## Regra 3 — Pagamento pendente há mais de 15 dias

| Campo | Valor |
|---|---|
| Nome | Pagamento pendente há mais de 15 dias |
| Gatilho | Tempo. Todo dia às 8h |
| Fonte | FakeERP, pedidos do mês corrente — `GET /report/{year}/{month}`, ver `fake-erp.md` |
| Condição | Existe pedido com `status: "PENDING"` cujo `orderDateTime` é de 15 dias atrás ou mais, contando a partir de hoje |
| Ação | Escrever alerta com `orderId`, valor e há quantos dias está pendente — pronto para eu enviar ao responsável financeiro conferir antes que vire prazo perdido |
| Quem recebe | Eu (Luisa), no arquivo `alertas/`. Repasso ao funcionário financeiro por WhatsApp, mantendo a revisão antes de qualquer cobrança |
| Se não disparar | Gravar em `automacoes.md` a data, quantos pedidos pendentes existiam e o mais antigo deles |

## Regra 4 — Desconto fora do padrão em um pedido

| Campo | Valor |
|---|---|
| Nome | Desconto fora do padrão em um pedido |
| Gatilho | Tempo. Todo dia às 8h |
| Fonte | FakeERP, pedidos do mês corrente — `GET /report/{year}/{month}`, ver `fake-erp.md` |
| Condição | Existe pedido com `status: "PAID"` ou `"PENDING"` cujo desconto (`discount`/`value`) é 10% ou mais do valor bruto. Pedidos `CANCELLED` ficam de fora — desconto de venda que não se concretizou não conta |
| Ação | Escrever alerta com `orderId`, `value`, `discount` e o percentual calculado |
| Quem recebe | Eu (Luisa), no arquivo `alertas/`. Repasso ao meu pai — desconto acima do padrão é decisão dele, não da equipe |
| Se não disparar | Gravar em `automacoes.md` a data, o mês consultado e o maior percentual de desconto encontrado |

Testada contra os 4 meses reais do FakeERP: dispara em fevereiro/2026 (pedido 1007, 10% de desconto), fica calada em janeiro/2026 (maior desconto é 9,12%).

## Regra 5 — Concentração de faturamento em um único pedido

| Campo | Valor |
|---|---|
| Nome | Concentração de faturamento em um único pedido |
| Gatilho | Tempo. Todo dia às 8h |
| Fonte | FakeERP, pedidos do mês corrente — `GET /report/{year}/{month}`, ver `fake-erp.md` |
| Condição | O mês tem 2 ou mais pedidos com `status: "PAID"` **e** existe um deles cujo `total` é 75% ou mais da soma de `total` de todos os pedidos `PAID` do mês |
| Ação | Escrever alerta com `orderId`, `total` do pedido e o percentual que ele representa do faturamento pago do mês |
| Quem recebe | Eu (Luisa), no arquivo `alertas/`. Repasso ao meu pai — um único pedido concentrando a maior parte do faturamento é risco se ele cancelar ou atrasar |
| Se não disparar | Gravar em `automacoes.md` a data, o mês consultado e a maior concentração encontrada |

Testada contra os 4 meses reais do FakeERP: dispara em julho/2026 (pedido 1012, 93% do faturamento pago do mês), fica calada em fevereiro/2026 (maior concentração é 71,4%, mesmo com 3 pedidos pagos). Esta regra já nasce da observação registrada em [fake-erp.md](fake-erp.md) — "o pedido 1007 sozinho foi 71% do faturamento de fevereiro/2026" — só que agora vira alerta automático em vez de achado manual.

## Por que essas cinco

Regra 3 é a mais próxima do problema real descrito em `problema.md`: lá, o erro recorrente é prazo perdido no pagamento de boletos pelo Funcionário 2. Aqui, "pedido pendente há mais de 15 dias" é a mesma lógica aplicada ao dado que já tenho testado (FakeERP) — a mesma checagem, com um `customerId`/`items` de verdade, é candidata a virar a regra real de boletos quando o sistema financeiro da empresa estiver conectado.

Regras 4 e 5 exploram os dois campos do `OrderDTO` que as três primeiras não usam: `discount` e a distribuição de `total` entre pedidos do mês. Não mapeiam para um processo específico do `problema.md` ainda — servem para mostrar que a mesma lógica de condição-com-número funciona em qualquer campo numérico da fonte, mesmo sem `customerId`. A Regra 5 em especial nasceu de um achado que já estava documentado em texto (`fake-erp.md`) e virou alerta automático.
