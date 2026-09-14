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

## Nota sobre a amostra de hoje (14/09/2026): a API do FakeERP caiu

Antes de montar `dados/amostra.csv`, tentei reconfirmar os 4 pedidos de janeiro/2026 chamando a API de novo (login + `/report/2026/1`). A resposta foi `502` (erro de origem, via Cloudflare) em **todos** os endpoints, incluindo `/v3/api-docs`, que não exige login — ou seja, o servidor do FakeERP está fora do ar agora, não é um bloqueio de rede do meu lado. Tentei 8 vezes ao longo de ~2 minutos, sempre `502`.

Para não inventar dado, a amostra usa apenas o que já foi confirmado por chamadas reais em sessões anteriores (documentadas em `fake-erp.md` e `automacoes.md`):

- **1001** — confirmado por completo (exemplo real documentado em `fake-erp.md`): 05/01/2026, `value` 1000,00, `discount` 50,00, `total` 950,00, `PAID`.
- **1003** — `orderId`, data (20/01/2026) e `total` (225,00, `CANCELLED`) confirmados em `regras.md`/`alertas/2026-09-10.md`. `value` e `discount` individuais não foram documentados nas sessões anteriores e não puderam ser reconfirmados hoje.
- **1004** — `orderId`, data (28/01/2026) e `total` (1.200,00, `PENDING`) confirmados nos mesmos arquivos. `value`/`discount` idem: não confirmados.
- **Um 4º pedido, PAID, total 480,00** — não está documentado individualmente em nenhuma sessão anterior, mas seu valor é uma consequência matemática necessária de dois números já confirmados de forma independente: `totalAmount` de janeiro é 2.855,00 (1001+1003+1004 = 2.375,00, sobra 480,00) **e** a receita paga de janeiro é 1.430,00 (950,00 do pedido 1001, sobra 480,00). As duas contas batem no mesmo valor, o que dá confiança de que ele existe e é `PAID`. O `orderId` (assumi 1002, por sequência) e a data exata não são confirmados — por isso ficam em branco no CSV, em vez de inventados.

Quando a API voltar, o próximo passo é rodar `GET /report/2026/1` de novo e confirmar o pedido 1002 de verdade (id, data, `value`, `discount`), substituindo a dedução por dado direto.
