# Sessão de Pair Programming com IA: Atribuição Automática de Tickets

## Funcionalidade

"Atribuir automaticamente um novo ticket ao agente de suporte com menor carga de trabalho no momento."

---

## Rodada 1: Estrutura de Dados

**O que pedi:**
"Estamos no Nimbus Desk, em Python 3.11. Preciso de uma estrutura de dados para representar um agente de suporte e sua carga de trabalho atual. Não escreva código ainda: apenas descreva a estrutura, os campos e os tipos."

**O que a IA gerou:**
Sugeriu uma classe `Agente` com os campos `id` (int), `nome` (str), `tickets_abertos` (int) e `disponivel` (bool).

**O que ajustei:**
Aceitei a sugestão, mas pedi para renomear `tickets_abertos` para `carga_atual`, para ficar mais alinhado ao vocabulário do domínio. Também pedi que `disponivel` fosse derivado da carga, não um campo separado, para evitar inconsistência.

---

## Rodada 2: Função de Seleção do Agente

**O que pedi:**
"Agora escreva uma função Python `selecionar_agente(agentes: list[Agente]) -> Agente` que retorne o agente com menor `carga_atual`. Em caso de empate, escolha o de menor `id`. Se a lista estiver vazia, lance uma exceção `NenhumAgenteDisponivelError`."

**O que a IA gerou:**

```python
def selecionar_agente(agentes):
    if not agentes:
        raise NenhumAgenteDisponivelError()
    return min(agentes, key=lambda a: (a.carga_atual, a.id))
```

**O que ajustei:**
A lógica está correta, mas faltou a assinatura de tipos. Pedi para adicionar `list[Agente]` e `-> Agente`, seguindo a convenção do projeto.

---

## Rodada 3: Orquestração

**O que pedi:**
"Escreva a função `atribuir_ticket(ticket_id: int) -> Agente` que busca o ticket, busca os agentes disponíveis, seleciona o agente com a função da rodada 2 e atualiza a carga. Use as funções de repositório `buscar_ticket_por_id` e `listar_agentes_disponiveis` que já existem no projeto."

**O que a IA gerou:**

```python
def atribuir_ticket(ticket_id):
    ticket = buscar_ticket_por_id(ticket_id)
    agentes = listar_agentes_disponiveis()
    agente = selecionar_agente(agentes)
    agente.carga_atual += 1
    return agente
```

**O que ajustei:**
A IA não persistiu a alteração do agente. Pedi para chamar `salvar_agente(agente)` após o incremento, seguindo o padrão de persistência do projeto.

---

## Rodada 4: Tratamento de Erros

**O que pedi:**
"Adicione o tratamento para o caso de o ticket não existir. Use a exceção `TicketNaoEncontradoError`, que já existe no projeto, e documente com uma docstring."

**O que a IA gerou:**

```python
def atribuir_ticket(ticket_id: int) -> Agente:
    """Atribui um ticket ao agente com menor carga."""
    ticket = buscar_ticket_por_id(ticket_id)
    if ticket is None:
        raise TicketNaoEncontradoError(ticket_id)
    agentes = listar_agentes_disponiveis()
    agente = selecionar_agente(agentes)
    agente.carga_atual += 1
    salvar_agente(agente)
    return agente
```

**O que ajustei:**
Aceitei o código. A docstring está breve, mas atende ao padrão do projeto.

---

## Diferencial da Colaboração Passo a Passo

Pedir a funcionalidade inteira de uma só vez delegaria à IA todas as decisões de estrutura, nomes, persistência e tratamento de erros. O resultado poderia "funcionar" no caminho feliz, mas seria um "castelo de cartas": sem garantia de que as escolhas da IA coincidem com as do projeto.

Com o passo a passo, cada trecho é revisado e validado antes de servir de base para o próximo. O desenvolvedor mantém o controle das decisões de projeto, e o código final é compreendido, testável e aderente ao Nimbus Desk.
