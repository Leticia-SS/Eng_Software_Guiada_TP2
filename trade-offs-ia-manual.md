# Trade-offs entre Código Gerado por IA e Código Manual no Nimbus Desk

## Evidências dos Exercícios Anteriores

- **Exercício 1:** três erros catalogados no código gerado por IA (condição de corrida, tratamento de exceção ausente e alucinação de API). Todos passaram na leitura superficial, mas quebraram na análise crítica.
- **Exercício 2:** cinco tarefas classificadas, das quais três exigiram revisão humana aprofundada por envolver alto risco financeiro, concorrência ou perda irreversível de dados.
- **Exercício 7:** a refatoração do código de notificação por e-mail eliminou duplicação e melhorou a legibilidade, mas introduziu uma regressão de comportamento (destinatário vazio passou a lançar exceção em vez de retornar `False`) que só foi detectada na comparação com o código original.

## Análise de Trade-offs

### 1. Velocidade de Entrega
**IA:** gera a primeira versão em minutos, mesmo para funcionalidades completas. No Exercício 5, a estrutura inicial da atribuição automática de tickets saiu em uma rodada.
**Manual:** a primeira versão leva mais tempo, mas o desenvolvedor já conhece o código e as decisões de projeto.
**Trade-off:** a IA acelera a entrega inicial, mas o tempo economizado é parcialmente reinvestido em revisão e correção. No Nimbus Desk, o ganho é real em tarefas padronizadas (formatação, tradução) e menor em tarefas complexas (cobrança, exclusão).

### 2. Consistência com o Restante do Projeto
**IA:** tende a resolver o caso geral, sem conhecer as convenções do projeto. No Exercício 4, a fragmentação de contexto gerou nomes em camelCase, reimplementação de persistência e data fixa em string, tudo inconsistente com o Nimbus Desk.
**Manual:** o desenvolvedor conhece as convenções e as aplica naturalmente.
**Trade-off:** a IA só mantém consistência se o contexto for bem montado (Exercício 3). Sem isso, o código gerado aumenta o custo de manutenção. A escrita manual é mais consistente por padrão.

### 3. Necessidade de Revisão
**IA:** todo código gerado precisa de revisão, mas o nível varia. No Exercício 2, tarefas padronizadas exigiram revisão leve; tarefas de alto risco exigiram revisão aprofundada. No Exercício 7, a refatoração da IA introduziu uma regressão que passou despercebida na leitura superficial.
**Manual:** o código escrito pelo próprio desenvolvedor também precisa de revisão, mas o autor já conhece as decisões e o risco de regressão é menor.
**Trade-off:** a IA desloca o esforço do desenvolvedor da escrita para a revisão. O tempo total pode ser menor, mas só se a revisão for feita com rigor. Caso contrário, o custo aparece depois, em produção.

### 4. Cobertura de Casos de Borda
**IA:** não considera casos de borda espontaneamente. No Exercício 1, a função `gerar_protocolo_ticket` não tratava concorrência; no Exercício 6, a função de atribuição não filtrava agentes sobrecarregados nem rejeitava carga negativa. Esses casos só apareceram quando testados explicitamente.
**Manual:** o desenvolvedor que conhece o domínio tende a antecipar casos de borda, mesmo que não os implemente de imediato.
**Trade-off:** a IA cobre bem o caminho feliz, mas deixa lacunas nos extremos. A escrita manual (ou a revisão humana aprofundada) é necessária para garantir que os casos de borda sejam tratados.

## Recomendação Final

**Priorizar a geração por IA quando:**
- A tarefa é padronizada e bem delimitada (formatação, tradução, boilerplate, CRUD simples).
- O contexto do projeto pode ser fornecido de forma enxuta e completa (Exercício 3).
- O impacto de um erro é baixo e detectável rapidamente.
- Há testes automatizados que cubram o comportamento esperado e os casos de borda conhecidos.

**Priorizar a escrita manual (ou revisão humana aprofundada) quando:**
- A tarefa envolve regras de negócio complexas (cálculo de cobrança, políticas de desconto).
- Há concorrência, estado compartilhado ou operações irreversíveis (geração de ID, exclusão de dados).
- O código precisa ser consistente com convenções que a IA não conhece e o contexto é difícil de montar.
- O custo de um erro é alto (financeiro, segurança, perda de dados) e a detecção é difícil.

**Conclusão para o Nimbus Desk:** o time deve usar a IA como aceleradora nas tarefas padronizadas e como rascunho nas tarefas complexas, mas nunca deve abrir mão da revisão humana nas áreas sensíveis. A IA não substitui o julgamento do desenvolvedor; ela desloca o esforço da escrita para a revisão, e é essa revisão que garante a qualidade final.
