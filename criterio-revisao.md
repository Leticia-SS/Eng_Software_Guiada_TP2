# Critério de Revisão: Geração Confiável vs. Revisão Humana Aprofundada

## Classificação das Tarefas do Nimbus Desk

| Tarefa | Classificação | Justificativa |
| :--- | :--- | :--- |
| **a. Formatação de data para interface** | **Revisão Leve** | Tarefa padronizada. O risco é baixo; um erro de formato é visual e facilmente corrigido. A lógica é simples e bem coberta por bibliotecas padrão. |
| **b. Cálculo do valor a ser cobrado** | **Revisão Humana Aprofundada** | Regra de negócio complexa e de alto risco financeiro. Um erro de cálculo impacta diretamente a receita e a confiança do cliente. Exige validação de regras de impostos, descontos e arredondamentos. |
| **c. Geração de ID único para ticket** | **Revisão Humana Aprofundada** | Aparentemente simples, mas é um ponto crítico de concorrência (ver Erro 1 do catálogo). Um erro pode causar duplicidade de protocolos. Exige análise de arquitetura (banco de dados vs. aplicação). |
| **d. Exclusão definitiva de ticket e anexos** | **Revisão Humana Aprofundada** | Operação irreversível e de alto risco (perda de dados). Envolve regras de negócio (o que pode ser excluído?), integridade referencial e segurança. Um erro pode ser catastrófico. |
| **e. Tradução de mensagem de erro** | **Revisão Leve** | Tarefa padronizada e de baixo risco. Um erro de tradução é um problema de UX, não de sistema. A revisão humana pode ser uma simples leitura para garantir o tom e a clareza. |

## Critério Geral Aplicável a Novas Tarefas

O critério central é o **Custo de Estar Errado**, que se desdobra em duas perguntas:

1.  **A tarefa é padronizada?**
    *   **Sim:** A IA tende a acertar, pois segue caminhos trilhados. A revisão pode ser leve.
    *   **Não:** A tarefa é específica do domínio ou envolve lógica complexa. A IA pode alucinar ou simplificar demais. Exige revisão aprofundada.

2.  **Qual o impacto de um erro?**
    *   **Baixo (UX, logs, texto):** O erro é detectável e corrigível sem grandes danos. Revisão leve.
    *   **Alto (dinheiro, segurança, dados, usuários):** O erro é caro, silencioso ou irreversível. Exige revisão humana aprofundada.

**Regra prática:** Quanto mais a tarefa se afastar do "caminho feliz" e quanto maior o impacto de uma falha, mais humana e aprofundada deve ser a revisão.
