# Gestão de Contexto para a Funcionalidade de Comentários

## Pacote de Contexto Enxuto

**Objetivo:** Implementar a funcionalidade de adicionar um comentário a um ticket existente no Nimbus Desk.

### Camada 1: Objetivo

Criar um endpoint para adicionar um comentário a um ticket existente, persistindo o comentário e o autor, e retornar o comentário criado.

### Camada 2: Regras e Convenções

- Python 3.11, nomes em snake_case (português).
- Erros de domínio via exceção `TicketNaoEncontradoError`.
- Datas em ISO 8601.
- Persistência via ORM já existente no projeto (SQLAlchemy).

### Camada 3: Código Relevante (Incluído)

**Modelo de Dados (`models.py`):** Apenas a definição das classes `Ticket` e `Comentario`.

```python
class Ticket:
    id: int
    titulo: str
    # ... outros campos

class Comentario:
    id: int
    ticket_id: int
    autor_id: int
    texto: str
    data_criacao: datetime
```

**Camada de Serviço (`services.py`):** Apenas a assinatura do método de busca e um exemplo de método que persiste uma entidade.

```python
def buscar_ticket_por_id(ticket_id: int) -> Ticket: ...
def adicionar_comentario(ticket_id: int, autor_id: int, texto: str) -> Comentario: ...
# Exemplo de padrão de persistência:
def criar_ticket(titulo: str, descricao: str) -> Ticket:
    # ... lógica de criação e salvamento
```

**Endpoint (`api.py`):** Apenas a rota de exemplo de criação de ticket para manter o padrão de resposta HTTP.

```python
@app.post("/api/tickets")
def criar_ticket_endpoint(dados: TicketCreate):
    # ... chama o serviço e retorna 201
```

### Camada 4: Tarefa

Implemente o método `adicionar_comentario` na camada de serviço e o endpoint `POST /api/tickets/{ticket_id}/comentarios`.

### Camada 5: Formato da Resposta

Apenas o código Python para os dois trechos (serviço e endpoint), sem explicações.

---

## Trechos Descartados Deliberadamente

- **Corpo completo dos arquivos:** Descartado para não exceder a janela de contexto e diluir a informação relevante.
- **Outros métodos dos serviços (ex: `listar_tickets`, `fechar_ticket`):** Não são relevantes para a tarefa de adicionar comentário.
- **Configurações de banco de dados e logging:** A IA não precisa saber como o banco é configurado para gerar a lógica de negócio.
- **Testes existentes de outras funcionalidades:** Poderiam confundir o modelo com padrões de teste não relacionados.
- **Código de frontend:** A tarefa é exclusivamente de backend.

---

## Estratégia de Gestão de Contexto em Projeto Grande

Para um projeto maior que o Nimbus Desk, a estratégia seria baseada em três pilares:

1. **Documento de Contexto do Projeto (Fonte Única):** Manter um arquivo `CONTEXT.md` no repositório, versionado, com o objetivo do sistema, tecnologias, padrões de código, decisões de arquitetura e regras de negócio imutáveis. Este documento é a base para qualquer prompt.
2. **Mapa do Projeto e Resumos de Módulos:** Em vez de arquivos completos, manter um `PROJECT_MAP.md` com uma linha por módulo e as assinaturas das funções principais. A IA pode gerar e atualizar esse mapa periodicamente.
3. **Interfaces como Contratos:** Para cada tarefa, o contexto inclui apenas as interfaces (assinaturas de funções, modelos de dados) dos módulos que interagem com o código a ser alterado, não suas implementações. Isso mantém o contexto enxuto e focado no contrato.
