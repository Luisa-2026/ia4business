# FakeERP — acesso via API (sem conector)

ERP de treino usado na Aula 10. Não tem conector/MCP — o acesso é direto por chamadas HTTP, feitas pelo terminal (Claude Code), não pelo claude.ai (lá a IA recusa por diretriz interna).

## Endereço

- Base: `https://fake-erp.isilab.com.br`
- Documentação (OpenAPI 3.1, JSON): `https://fake-erp.isilab.com.br/v3/api-docs`

## Autenticação

`POST /auth/login`, corpo JSON — atenção: o campo é `login`, não `username` (erro comum de 401/403).

```json
{"login": "user", "password": "user"}
```

Devolve um token JWT (`type: "Bearer"`), válido por 1h (`expiresInMs: 3600000`). Depois de expirar, é só logar de novo — não tem refresh token.

```bash
curl -s -X POST "https://fake-erp.isilab.com.br/auth/login" \
  -H "Content-Type: application/json" \
  -d '{"login":"user","password":"user"}'
```

## Endpoint de relatório

`GET /report/{year}/{month}` — pedidos de um mês inteiro. **Não existe endpoint por dia.** Requer `Authorization: Bearer <token>`.

```bash
curl -s "https://fake-erp.isilab.com.br/report/2026/1" \
  -H "Authorization: Bearer <token>"
```

Resposta:

```json
{
  "year": 2026, "month": 1, "count": 4,
  "totalValue": 3050.50, "totalDiscount": 195.50, "totalAmount": 2855.00,
  "orders": [
    {"orderId": 1001, "orderDateTime": "2026-01-05T09:30:00", "value": 1000.00, "discount": 50.00, "total": 950.00, "status": "PAID"}
  ]
}
```

**Pegadinha:** `totalAmount` soma todos os pedidos, inclusive `CANCELLED` e `PENDING`. "Quanto vendemos de fato" = soma de `total` só dos pedidos com `status: "PAID"`. A API não filtra isso — quem sabe da regra de negócio é a gente, não o endpoint.

## Meses com dados na base de treino (2026)

Só janeiro, fevereiro, março e julho têm pedidos. Qualquer outro mês devolve `count: 0` e `orders: []` — não é bug, é a base de teste.

## Erros comuns

| Sintoma | Causa | Solução |
|---|---|---|
| 401/403 no login | campo errado no corpo | usar `login`, não `username` |
| 401/403 no `/report` | token ausente, errado ou expirado (>1h) | logar de novo, checar header `Authorization: Bearer <token>` |
| relatório vazio sem erro | mês sem pedidos na base | normal fora de jan/fev/mar/jul de 2026 |

## Como reusar

Peça: **"me traz o relatório de fevereiro de 2026"** — este arquivo tem tudo que é preciso para autenticar, chamar `/report/2026/2` e devolver a análise em português (total vendido só com pedidos pagos, nº de pedidos, ticket médio, fora da curva), sem mostrar o JSON bruto.
