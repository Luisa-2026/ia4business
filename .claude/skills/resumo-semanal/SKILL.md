---
name: resumo-semanal
description: Lê o histórico do repositório e escreve o status da semana do projeto — o que andou, o que travou e os próximos passos — para reportar sem precisar interromper o gestor.
---

# resumo-semanal

Lê o `git log` da última semana e o conteúdo atual de `problema.md`, `contexto/` e
`prompts.md` para escrever um status curto e objetivo.

## Estrutura fixa do resumo

1. **O que andou** — mudanças reais da semana, citando o commit ou arquivo, não
   generalidades.
2. **O que travou** — pendências concretas. Se houver achados recentes de uma
   revisão do repositório (skill `revisa-repo`), citar esses achados aqui em vez
   de reescrever pendências genéricas.
3. **Próximos passos** — sempre numerados e com data, nunca uma lista vaga sem
   prazo (ex: nunca "continuar avançando", sempre "X até dd/mm").

## Por que essa régua

O objetivo é que o proprietário consiga entender o status em poucos minutos, sem
precisar perguntar detalhes — reduzindo as interrupções que o problema.md
descreve. Um resumo com passos vagos ou sem prazo obriga o proprietário a
perguntar de novo, o que anula o propósito da skill.
