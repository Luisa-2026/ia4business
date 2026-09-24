# IA4business

Projeto da disciplina AI for Business (Link School of Business), aplicado à construtora/incorporadora da minha família. Resolve a dependência excessiva do meu pai (dono) para conferir e validar tarefas administrativas — pagamento de boletos, prazos de cartório, contratos — que hoje só ele confia de verdade. A operação usa um ERP de treino (FakeERP) como fonte de dados, um conjunto de regras que leem essa fonte e escrevem alertas quando algo foge do padrão, e documentação viva (regras, fonte de dados, testes de falha) para que a automação nunca invente número nem falhe em silêncio.

## Ordem de leitura sugerida

### 1. Contexto do negócio e do problema

- **[CLAUDE.md](CLAUDE.md)** — instruções que todo assistente de IA deve seguir nesta conversa/repositório: ler o contexto antes de responder, manter revisão humana obrigatória em qualquer automação sugerida.
- **[contexto/sobre-mim.md](contexto/sobre-mim.md)** — quem sou eu (Luisa), meu papel na empresa e como prefiro ser respondida.
- **[contexto/negocio.md](contexto/negocio.md)** — o que a empresa vende (lotes em Porto Seguro, apartamentos em São Paulo), como ganha dinheiro, estrutura da equipe (3 funcionários + meu pai) e o que a solução de IA não pode fazer (nenhuma ação autônoma sem aprovação humana).
- **[contexto/cliente.md](contexto/cliente.md)** — perfil de quem compra e por que o cliente externo não é o foco do problema atual.
- **[problema.md](problema.md)** — o problema central documentado em detalhe: quem sofre, como é hoje, o que mudaria, a métrica de sucesso (tabela Métrica/Alvo/Como confiro) e o primeiro experimento em andamento.

### 2. A automação em si

- **[regras.md](regras.md)** — as regras que decidem quando a automação deve falar (alertar) e quando deve ficar calada, todas lendo o FakeERP (e, na Regra 6, também o Gmail). Inclui a **"Regra de segurança da fonte"**: se a fonte não existir ou vier vazia, a resposta obrigatória é "FONTE INDISPONÍVEL" — nunca um número inventado.
- **[dados/fonte.md](dados/fonte.md)** — a fonte canônica dos dados (FakeERP) e a regra de cálculo de cada um dos "3 números do negócio" (pedidos no mês, receita paga, ticket médio pago), escrita para não depender da memória de ninguém.
- **[dados/amostra.csv](dados/amostra.csv)** — amostra congelada de pedidos de janeiro/2026, vinda de uma chamada real ao FakeERP. É o dado que alimenta o `painel.html`.
- **[fake-erp.md](fake-erp.md)** — documentação de acesso ao FakeERP (autenticação, endpoint de relatório, limitações da API) — usado tanto pelas regras quanto para gerar a amostra.
- **[painel.html](painel.html)** — painel visual com os 3 números do negócio, calculados a partir de `dados/amostra.csv` pela regra escrita em `dados/fonte.md`. Arquivo único, abre direto no navegador, sem servidor.
- **[automacoes.md](automacoes.md)** — registro de tudo que foi ligado (conectores, fluxos, rotinas), com auditoria manter/consertar/matar e uma tabela de execuções reais das regras acima, com data e resultado.

### 3. Testes de falha

- **[testes.md](testes.md)** — os 3 cenários de falha testados de verdade contra `dados/amostra.csv`: fonte fora do ar, dado sujo (linha vazia, valor negativo, data em formato errado) e uma condição que nunca dispara (Regra 6), com o que foi observado e consertado em cada caso.

### 4. Portão de escala

- **[escala.md](escala.md)** — as 5 perguntas do portão da Aula 15 (roda sem mim, avisa quando falha, alguém lê, mede, cliente ouvido), cada uma com evidência em arquivo, e a decisão: não abre ainda, e qual item vira "sim" primeiro, com prazo.

### 5. Radar de mercado (exercício da Aula 13)

- **[radar/radar-tendencias.md](radar/radar-tendencias.md)** — análise dos comentários de um post do Instagram (@caffeinearmy), classificados por categoria, com leitura de negócio sobre reclamações de entrega. Não é sobre a construtora — é um exercício de outra aula, mantido no mesmo repositório.
- **[radar/comentarios.md](radar/comentarios.md)** — os comentários brutos coletados, base do arquivo acima.

### 6. Histórico

- **[alertas/2026-09-10.md](alertas/2026-09-10.md)** — primeira execução manual das regras 1 a 3 (antes de existirem as regras 4, 5 e 6), com o resultado real de janeiro/2026 vs. julho/2026.
- **[prompts.md](prompts.md)** — biblioteca de prompts, incluindo o histórico de como um pedido foi refinado em 3 versões (V1 → V2 → V3) até chegar num briefing que funciona de verdade.
- **[relatorio-alternativas-solucao.docx](relatorio-alternativas-solucao.docx)** — documento Word com as alternativas de solução avaliadas para o problema, critérios de avaliação e recomendação, preparado em 27/08/2026.
- **Resumo_Contexto_Negocio.docx** — resumo do contexto do negócio preparado para revisão do meu pai. **Não aparece no GitHub**: está listado no `.gitignore`, então fica só na minha máquina.

### 7. Infraestrutura do projeto

- **[.gitignore](.gitignore)** — ignora arquivos de sistema (`.DS_Store`) e o `Resumo_Contexto_Negocio.docx` (documento de revisão pessoal, não faz parte do entregável).
- **[.claude/skills/](.claude/skills/)** — skills do Claude Code usadas neste projeto: `fecha-conversa`, `resumo-semanal` e `revisa-repo` (escritas para este projeto, uma por aula) e `doc-coauthoring`/`docx` (skills padrão da ferramenta, usadas para gerar os `.docx` acima — não foram escritas por mim).

---

Fonte canônica de dados: FakeERP (ver [dados/fonte.md](dados/fonte.md)). Nenhuma automação deste repositório executa ação final sozinha — toda decisão passa por revisão humana antes de qualquer pagamento, resposta ou confirmação, conforme [CLAUDE.md](CLAUDE.md) e [problema.md](problema.md).
