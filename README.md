# Descartes

A skill é uma entrevista estruturada que roda **antes** de criar qualquer função ou método novo, para não perder decisões pelo caminho nem escrever código que não precisava existir.

## Por que existe

A motivação por trás da criação da skill é para que ela seja criada a partir dos princípios de um bom código, limpo, bem comentado, elegante, funcional, e entendível tanto pra você, quanto para as pessoas que lerão a mesma função posteriormente.
Se você tem pensamento acelerado e perde o fio de decisões já tomadas no meio da implementação. Em vez de já sair codando, a skill primeiro questiona se a função precisa mesmo ser criada e, se precisar, força uma decisão explícita sobre contrato, localização, efeitos colaterais, casos, erros e testes, partindo do pressuposto de uma pergunta por vez, sempre com uma resposta recomendada para só confirmar ou corrigir.

## Como usar

```
/descartes [nome ou descrição da função]
```

- Quando não é dado um argumento, a skill pergunta primeiro o que construir.

## O que ela faz

1. **Bloco 0 — vale a pena criar?** Sobe uma escada: YAGNI → já existe no projeto → stdlib → recurso nativo → dependência já instalada → uma linha; Antes de abrir a entrevista. Só continua se nada disso resolver.
2. **Entrevista em 6 blocos** — contrato, onde mora, efeitos colaterais, casos de borda, erros, testes, aplicando princípios técnicos (tratamento de erro, nomes, KISS/YAGNI, DRY, baixo acoplamento, legibilidade do corpo, mudança cirúrgica) ao longo do caminho.
3. **Rascunho em disco** (`.claude/rascunhos/<nome-da-funcao>.md`) como memória externa da entrevista, atualizado a cada resposta.
4. **Entrega final** — recap do contrato, "teste de elegância" (do contrato e depois do corpo), permissão explícita antes de escrever, ordem de decisão de comentários, e verificação do código contra os cenários de teste definidos.

## Arquivos

- `SKILL.md` — a skill em si (regras, roteiro, princípios técnicos).
- `.claude/rascunhos/` — gerado em tempo de uso, um arquivo por função entrevistada. Não é apagado depois — fica como registro.
