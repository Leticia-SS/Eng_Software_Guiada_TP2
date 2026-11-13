# Casos de Teste: Atribuição Automática de Tickets

## Especificação de Referência

- Um novo ticket deve ser atribuído ao agente de suporte com menor `carga_atual`.
- Em caso de empate, escolher o agente de menor `id`.
- Se não houver agentes disponíveis, lançar `NenhumAgenteDisponivelError`.
- Se o ticket não existir, lançar `TicketNaoEncontradoError`.

## Casos de Teste Gerados

| # | Cenário | Entrada | Saída Esperada |
| :--- | :--- | :--- | :--- |
| 1 | Mais de um agente disponível | Agentes: A (carga 3, id 1), B (carga 1, id 2), C (carga 2, id 3). Ticket válido. | Atribuído ao agente B (menor carga). |
| 2 | Apenas um agente disponível | Agentes: A (carga 5, id 1). Ticket válido. | Atribuído ao agente A. |
| 3 | Nenhum agente disponível | Lista de agentes vazia. Ticket válido. | Lança `NenhumAgenteDisponivelError`. |
| 4 | Empate de carga | Agentes: A (carga 2, id 2), B (carga 2, id 1). Ticket válido. | Atribuído ao agente B (menor id). |
| 5 | Ticket inexistente | Lista de agentes com um disponível. `ticket_id` inválido. | Lança `TicketNaoEncontradoError`. |

## Execução dos Testes

| # | Resultado | Motivo |
| :--- | :--- | :--- |
| 1 | **Passou** | `selecionar_agente` retornou o agente com menor carga corretamente. |
| 2 | **Passou** | Com um único agente, a função o retorna diretamente. |
| 3 | **Passou** | A lista vazia disparou `NenhumAgenteDisponivelError` conforme especificado. |
| 4 | **Passou** | O critério de desempate por `id` funcionou corretamente. |
| 5 | **Passou** | O `ticket_id` inválido disparou `TicketNaoEncontradoError`. |

## Análise de Falhas e Ajustes

Durante a execução, dois problemas apareceram fora dos casos acima e exigiram correção:

**Falha 1: Agente com carga acima do limite ainda era considerado disponível.**
A função `listar_agentes_disponiveis` do projeto retornava todos os agentes, sem filtrar os que já estavam com carga máxima. O teste 1 passou porque os agentes de teste estavam abaixo do limite, mas um teste adicional com agente sobrecarregado revelou o problema. **Correção:** ajustar `listar_agentes_disponiveis` para filtrar `carga_atual < LIMITE_MAXIMO`.

**Falha 2: Carga negativa não era rejeitada.**
Um teste exploratório com `carga_atual = -1` mostrou que a função aceitava o valor e o agente era selecionado como "menor carga". A especificação não previa esse caso. **Correção:** adicionar validação na criação do agente para rejeitar carga negativa e registrar o caso de borda na especificação.

## Conclusão

Os cinco casos derivados da especificação passaram após os ajustes. Os dois problemas encontrados mostram que a especificação inicial era incompleta: ela não definia o limite máximo de carga nem rejeitava valores inválidos. A execução dos testes contra a implementação expôs essas lacunas e permitiu corrigi-las antes do merge.
