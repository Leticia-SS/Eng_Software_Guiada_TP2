# Análise de Degradação por Fragmentação de Contexto

## Código Gerado com Contexto Fragmentado (Exercício 4)

**Fragmentos fornecidos (isolados, sem contexto):**

1. `class Comentario: id: int; ticket_id: int; autor_id: int; texto: str` (sem a classe `Ticket` e sem o ORM).
2. `def adicionar_comentario(ticket_id, autor_id, texto): ...` (sem contexto de persistência nem de validação).

**Código gerado pela IA (v2 - fragmentado):**

```python
# Suposição: a IA criou um dicionário global para simular persistência.
_comentarios_db = []

def adicionar_comentario(ticket_id, autor_id, texto):
    # Suposição: não validou se o ticket existe.
    novo_comentario = {
        "id": len(_comentarios_db) + 1,
        "ticketId": ticket_id,   # Nome inconsistente com o padrão snake_case
        "autorId": autor_id,     # Nome inconsistente
        "texto": texto,
        "data": "2024-01-01"     # Suposição errada: data fixa, não ISO 8601
    }
    _comentarios_db.append(novo_comentario)
    return novo_comentario
```

---

## Comparação e Degradação

| Aspecto | Código com Contexto Enxuto (Exercício 3) | Código com Contexto Fragmentado (Exercício 4) | Degradação |
|---------|------------------------------------------|-----------------------------------------------|------------|
| Nomes | `ticket_id`, `autor_id` (snake_case) | `ticketId`, `autorId` (camelCase) | Inconsistência de nomenclatura com o resto do projeto. |
| Persistência | Delega ao serviço/ORM existente. | Cria uma lista global (`_comentarios_db`) e um dicionário. | Reimplementação de algo que já existia (a camada de persistência). A IA não sabia que o projeto usava um ORM. |
| Validação | Assume que `buscar_ticket_por_id` é usado para validar a existência. | Não valida a existência do ticket. | Suposição errada sobre o domínio: um comentário pode ser adicionado a um ticket inexistente. |
| Tipos de Dados | `data_criacao: datetime` (modelo) e formato ISO 8601. | `"data": "2024-01-01"` (string fixa). | Suposição errada sobre tipos de dados e regras de data. |

---

## Conclusão

A fragmentação de contexto levou a IA a "resolver o caso geral": cada fragmento gerou um código que parece correto isoladamente, mas não se encaixa no sistema. As três degradações concretas são nomes inconsistentes, reimplementação de algo existente e suposições erradas sobre tipos de dados. A correção seria sempre montar o pacote de contexto enxuto do Exercício 3 antes de pedir a geração.
