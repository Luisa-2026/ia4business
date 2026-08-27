---
name: fecha-conversa
description: Transforma notas brutas de uma conversa ou reunião em uma ata curta, com ações que sempre têm dono e prazo, e sugere atualização do contexto do projeto quando o episódio confirma um padrão já conhecido.
---

# fecha-conversa

Recebe o relato bruto de uma conversa (WhatsApp, reunião, lembrança falada) e
transforma em uma ata estruturada.

## Estrutura da ata

1. **Assunto** e **data** — se a data não estiver clara ("esses dias", "ontem"),
   perguntar a data aproximada em vez de assumir.
2. **Envolvidos**.
3. **O que aconteceu** — resumo objetivo, sem opinião.
4. **Já resolvido nesta conversa** — o que já foi solucionado, sem virar ação pendente.
5. **Causa raiz**, se identificável no relato.
6. **Ações pendentes** — tabela com Ação | Dono | Prazo.

## A regra que não pode quebrar

**Nunca fechar a ata com uma ação sem dono ou sem prazo.** Se a conversa não
trouxer essa informação, perguntar antes de fechar — não preencher com "a
definir" sem antes checar com a pessoa. Se o usuário confirmar um prazo
aproximado (ex: "esta semana"), usar o que foi confirmado.

## Ligação com o contexto do projeto

Se o episódio relatado for uma nova ocorrência de um problema já documentado em
`problema.md` ou `contexto/`, apontar isso explicitamente na ata — é evidência
real que reforça (ou contradiz) o que já está escrito, e vale considerar
atualizar o arquivo correspondente.
