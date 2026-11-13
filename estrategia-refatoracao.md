# Pair Programming e Estratégia de Refatoração: Histórico de Interações

## Funcionalidade

"Registrar um histórico de interações de cada ticket: toda mudança de status ou comentário deve gerar uma entrada de histórico consultável depois."

---

## Sessão de Pair Programming

### Rodada 1: Estrutura de Dados

**O que pedi:**
"Estamos no Nimbus Desk, em Python 3.11. Preciso de uma estrutura de dados para representar uma entrada de histórico de um ticket. Ela deve registrar o tipo de evento (mudança de status ou comentário), o valor anterior, o valor novo, o autor e a data. Não escreva código ainda: apenas descreva a estrutura e os tipos."

**O que a IA gerou:**
Sugeriu uma classe `EntradaHistorico` com os campos `id` (int), `ticket_id` (int), `tipo_evento` (str), `valor_anterior` (str | None), `valor_novo` (str | None), `autor_id` (int) e `data_evento` (datetime).

**O que ajustei:**
Aceitei a estrutura, mas pedi para `tipo_evento` ser um `Enum` (`MudancaStatus`, `Comentario`) em vez de string livre, para evitar valores inconsistentes. Também pedi que `data_evento` fosse gerada automaticamente no momento do registro, não recebida como parâmetro.

---

### Rodada 2: Registro de Mudança de Status

**O que pedi:**
"Escreva uma função `registrar_mudanca_status(ticket_id: int, status_anterior: str, status_novo: str, autor_id: int) -> EntradaHistorico` que crie e persista uma entrada de histórico do tipo `MudancaStatus`. Use a função `salvar_historico` que já existe no projeto."

**O que a IA gerou:**

```python
def registrar_mudanca_status(ticket_id, status_anterior, status_novo, autor_id):
    entrada = EntradaHistorico(
        ticket_id=ticket_id,
        tipo_evento=TipoEvento.MUDANCA_STATUS,
        valor_anterior=status_anterior,
        valor_novo=status_novo,
        autor_id=autor_id,
        data_evento=datetime.now()
    )
    salvar_historico(entrada)
    return entrada
```

**O que ajustei:**
A lógica está correta, mas faltou a assinatura de tipos. Pedi para adicionar `int`, `str` e `-> EntradaHistorico`, seguindo a convenção do projeto.

---

### Rodada 3: Integração com o Fluxo de Comentário

**O que pedi:**
"Integre o registro de histórico ao fluxo de adicionar comentário do Exercício 3. Sempre que um comentário for adicionado, uma entrada de histórico do tipo `Comentario` deve ser registrada, com o texto do comentário em `valor_novo`. Reutilize a função `adicionar_comentario` já existente."

**O que a IA gerou:**

```python
def adicionar_comentario(ticket_id, autor_id, texto):
    comentario = Comentario(
        ticket_id=ticket_id,
        autor_id=autor_id,
        texto=texto,
        data_criacao=datetime.now()
    )
    salvar_comentario(comentario)
    registrar_entrada_historico(
        ticket_id=ticket_id,
        tipo=TipoEvento.COMENTARIO,
        valor_novo=texto,
        autor_id=autor_id
    )
    return comentario
```

**O que ajustei:**
A IA inventou uma função `registrar_entrada_historico` que não existe. Pedi para reutilizar a estrutura da rodada 2, criando uma função genérica `registrar_historico(ticket_id, tipo_evento, valor_anterior, valor_novo, autor_id)` e chamando-a tanto no fluxo de status quanto no de comentário. Isso elimina a duplicação entre os dois fluxos.

---

### Rodada 4: Consulta do Histórico por Ticket

**O que pedi:**
"Escreva uma função `consultar_historico(ticket_id: int) -> list[EntradaHistorico]` que retorne as entradas de histórico de um ticket, ordenadas da mais antiga para a mais recente. Se o ticket não existir, lance `TicketNaoEncontradoError`."

**O que a IA gerou:**

```python
def consultar_historico(ticket_id: int) -> list[EntradaHistorico]:
    """Retorna o histórico de um ticket, ordenado cronologicamente."""
    ticket = buscar_ticket_por_id(ticket_id)
    if ticket is None:
        raise TicketNaoEncontradoError(ticket_id)
    return listar_historico_por_ticket(ticket_id)
```

**O que ajustei:**
Aceitei o código, mas pedi para garantir a ordenação na consulta (`ORDER BY data_evento ASC`) na função de repositório `listar_historico_por_ticket`, para que o retorno seja sempre cronológico.

---

## Estratégia de Refatoração Assistida por IA

### Momentos de Acionamento

- **Após os testes passarem:** a primeira refatoração acontece quando o código já funciona e os testes de unidade e integração estão verdes. Nesse momento, o comportamento está travado e a refatoração pode focar em legibilidade e duplicação sem risco de mascarar um defeito.
- **Antes do pull request:** uma segunda passada, com foco em aderência ao guia de estilo do projeto e remoção de código desnecessário gerado pela IA. É o momento de rodar o linter e o formatador.
- **Após a primeira revisão de código:** a refatoração final acontece depois que outro desenvolvedor revisou o PR e apontou trechos confusos. Nesse momento, o foco é a clareza para quem vai manter o código, não apenas a correção.

### Trechos Prioritários para Revisão

- **Lógica de persistência do histórico:** é o ponto de maior risco. Um erro aqui pode gerar entradas duplicadas, fora de ordem ou perdidas. Deve ser revisado com testes que cubram concorrência e falha de persistência.
- **Integração com o fluxo de comentário:** a chamada ao registro de histórico dentro de `adicionar_comentario` precisa ser revisada para garantir que uma falha no registro não impeça o comentário de ser salvo (ou que ambos sejam revertidos em uma transação).
- **Consulta por ticket:** a ordenação e o tratamento de ticket inexistente devem ser validados com testes de borda, incluindo ticket sem histórico e ticket com muitas entradas.

### Critério de Refatoração

A refatoração deve ser acionada quando o custo de manter o código como está supera o custo de alterá-lo. No caso do histórico, os sinais de que a refatoração é necessária são: duplicação entre o registro de status e o de comentário, nomes genéricos na estrutura de dados e ausência de testes cobrindo a integração entre os fluxos. Quando esses sinais aparecem, a refatoração é prioritária; quando não, o código pode seguir para o merge e ser refatorado em uma próxima sprint.
