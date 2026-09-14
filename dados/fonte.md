# Fonte canônica — IA4business

Onde os 3 números do meu negócio moram, com a regra de cálculo de cada um escrita aqui — não na cabeça de ninguém, não dentro de uma ferramenta.

## Onde vive

**FakeERP**, o mesmo ERP de treino usado desde a aula 10 (ver [fake-erp.md](../fake-erp.md)), acessado por `GET /report/{year}/{month}`. É a mesma fonte já usada em `regras.md` e `automacoes.md` — hoje ela só ganha uma amostra congelada em CSV e a regra de cálculo escrita, em vez de ficar implícita em cada regra.

Caminho escolhido (das três opções da aula): **C — dado de treino**, porque o dado real da construtora (boletos, contratos, prazos de cartório — ver [problema.md](../problema.md)) ainda está em planilha/WhatsApp, sem exportação estruturada pronta. O FakeERP entra aqui como proxy de "pedidos", igual já vinha sendo usado.

Amostra congelada: `dados/amostra.csv`, pedidos de **janeiro de 2026**.

## As três perguntas sobre esta fonte

1. **Quem escreve nela?** Nesta amostra, ninguém — é dado de treino fixo, gerado pelo FakeERP, não por uma pessoa da equipe digitando. **FALTA DEFINIR:** quando a fonte real da construtora existir (ex.: planilha de boletos pagos, ou export do Kommo), quem é o dono que atualiza.
2. **Com que frequência?** A amostra em `dados/amostra.csv` é um retrato de um dia (14/09/2026), não recalculada automaticamente. **FALTA DEFINIR:** frequência real de atualização quando a fonte deixar de ser treino.
3. **Como eu percebo que ela está desatualizada?** Hoje, comparando a data no rodapé do `painel.html` com a data de hoje — se for diferente, os números do painel são de uma versão antiga da amostra. **FALTA DEFINIR:** um alerta automático para isso (a Regra 6 de `regras.md` já mostra o padrão de "avisar quando uma fonte não responde"; a mesma lógica poderia avisar quando uma fonte está velha).

## Os 3 números

Tipo de negócio usado como referência: "Loja ou e-commerce" (pedidos), por ser o que o FakeERP representa.

### 1 · Volume — Pedidos no mês

**Regra de cálculo:** contagem de todos os pedidos retornados por `GET /report/{year}/{month}` no período, **qualquer que seja o `status`** (`PAID`, `CANCELLED` ou `PENDING` contam igual aqui — este número mede quantos pedidos existiram, não quantos valeram).

**Janeiro de 2026:** 4 pedidos.

### 2 · Dinheiro — Receita paga no mês

**Regra de cálculo:** soma do campo `total` **apenas** dos pedidos com `status: "PAID"`. Pedidos `CANCELLED` e `PENDING` não entram — é dinheiro que efetivamente entrou, não o que foi vendido nem o que ainda pode entrar.

**Janeiro de 2026:** R$ 1.430,00.

### 3 · Qualidade — Ticket médio pago

**Regra de cálculo:** receita paga no mês (número 2) dividida pela **quantidade de pedidos com `status: "PAID"`** — não pela quantidade total de pedidos do número 1. Um ticket médio dividido pelo total de pedidos misturaria dinheiro que entrou com pedidos que nunca vão entrar.

**Janeiro de 2026:** R$ 715,00 (R$ 1.430,00 ÷ 2 pedidos pagos).

## Por que comercial e financeiro veem números diferentes sem que nenhum esteja errado

Se alguém contar "quanto vendemos" pelos 4 pedidos do mês (visão comercial, pedidos fechados), chega a um número puxado pelo `value` bruto de todos eles. Se alguém contar pelo dinheiro que efetivamente caiu na conta (visão financeira, só `PAID`), chega a R$ 1.430,00. Dos 4 pedidos, 1 está cancelado e 1 está pendente — nenhum dos dois lados está errado, cada um está respondendo uma pergunta diferente. A regra de cálculo escrita acima é o que evita a reunião discutir planilha em vez de decisão.

## Nota sobre a amostra de hoje (14/09/2026): a API caiu e depois voltou

Na primeira tentativa de montar `dados/amostra.csv`, a API do FakeERP respondeu `502` (erro de origem, via Cloudflare) em **todos** os endpoints, incluindo `/v3/api-docs`, que não exige login — 8 tentativas ao longo de ~2 minutos, sempre fora do ar. Para não inventar dado, a amostra ficou com 3 dos 4 pedidos confirmados por sessões anteriores (`fake-erp.md`, `regras.md`, `alertas/2026-09-10.md`) e o 4º só com o `total` (480,00), deduzido matematicamente de dois agregados já confirmados — `orderId`, data e a quebra `value`/`discount` desse pedido ficaram em branco, não inventados.

Cerca de 20 minutos depois, a API voltou (`HTTP 200`) e `GET /report/2026/1` foi chamado de novo, de verdade. **A dedução se confirmou exatamente**: o pedido é o **1002**, `PAID`, `total` 480,00 — batendo com o valor que já havia sido calculado por diferença. A amostra abaixo já está com o dado direto da API, não mais com a dedução:

| orderId | orderDateTime | value | discount | total | status |
|---|---|---|---|---|---|
| 1001 | 2026-01-05T09:30:00 | 1.000,00 | 50,00 | 950,00 | PAID |
| 1002 | 2026-01-12T14:10:00 | 480,00 | 0,00 | 480,00 | PAID |
| 1003 | 2026-01-20T18:45:00 | 250,00 | 25,00 | 225,00 | CANCELLED |
| 1004 | 2026-01-28T11:00:00 | 1.320,50 | 120,50 | 1.200,00 | PENDING |

Os 3 números não mudam (a dedução já estava certa), mas agora todo campo de `dados/amostra.csv` vem de uma chamada real, sem nenhuma lacuna.
