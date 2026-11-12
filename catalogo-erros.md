# Catálogo de Erros em Código Gerado por IA - Nimbus Desk

## Erro 1: Condição de Corrida (Race Condition) em `gerar_protocolo_ticket()`

**Prompt original:** "Gere uma função Python gerar_protocolo_ticket() que retorne um número de protocolo sequencial para cada novo ticket."

**Código gerado pela IA (v1):**

```python
_contador_protocolo = 1000

def gerar_protocolo_ticket():
    global _contador_protocolo
    _contador_protocolo += 1
    return _contador_protocolo
```

**Tipo de erro:** Condição de Corrida (Race Condition) / Estado Compartilhado.

**Simulação:** Duas threads chamam a função simultaneamente. Ambas leem `_contador_protocolo` (ex: 1000), ambas incrementam para 1001. Dois tickets diferentes recebem o mesmo protocolo.

**Revisão Crítica:**
A leitura superficial mostra uma função simples e correta. A revisão crítica pergunta: "Algum valor é compartilhado entre chamadas ou usuários?". Sim, o `_contador_protocolo` global é compartilhado. Em um ambiente concorrente (servidor web), isso é uma "mina terrestre" que não aparece em testes de uma única chamada.

**Correção:**
Uso de um `threading.Lock` para garantir exclusão mútua ou, preferencialmente, delegar a geração do ID para o banco de dados (ex: `AUTO_INCREMENT`), que é atômico por natureza.

```python
import threading
_lock = threading.Lock()
_contador_protocolo = 1000

def gerar_protocolo_ticket():
    with _lock:
        global _contador_protocolo
        _contador_protocolo += 1
        return _contador_protocolo
```

---

## Erro 2: Tratamento de Exceção Ausente (Suposição de Ambiente)

**Código gerado pela IA (exemplo de leitura de anexo):**

```python
def ler_anexo(caminho_arquivo):
    with open(caminho_arquivo, 'r') as f:
        return f.read()
```

**Tipo de erro:** Caso de Borda / Suposição Errada sobre o Ambiente.

**Análise:** O código assume que o arquivo sempre existe, é legível e está no formato correto. Ignora o "caminho infeliz".

**Revisão Crítica:**
A IA não considera entradas incomuns (arquivo inexistente, permissão negada, encoding errado) a menos que instruída. O código é um "castelo de cartas" que desaba na primeira falha de I/O.

**Correção:**
Tratamento explícito de exceções e validação de entrada.

```python
import os

def ler_anexo(caminho_arquivo):
    if not os.path.exists(caminho_arquivo):
        raise FileNotFoundError(f"Anexo não encontrado: {caminho_arquivo}")
    try:
        with open(caminho_arquivo, 'r', encoding='utf-8') as f:
            return f.read()
    except IOError as e:
        # Logar o erro e relançar uma exceção de domínio
        raise RuntimeError(f"Falha ao ler anexo: {e}")
```

---

## Erro 3: Alucinação de API

**Código gerado pela IA (exemplo de envio de e-mail):**

```python
import smtplib

def enviar_notificacao_email(destinatario, assunto, corpo):
    server = smtplib.SMTP('smtp.nimbusdesk.com')
    server.send_email(destinatario, assunto, corpo) # Método inventado
    server.quit()
```

**Tipo de erro:** Alucinação.

**Análise:** O método `send_email` não existe na biblioteca `smtplib`. O nome é plausível, mas o código falhará em tempo de execução.

**Revisão Crítica:**
Conferir a documentação oficial da biblioteca. A IA inventou um método que "parece" certo.

**Correção:**
Uso da API correta (`sendmail`), com construção da mensagem via `email.message`.

```python
import smtplib
from email.message import EmailMessage

def enviar_notificacao_email(destinatario, assunto, corpo):
    msg = EmailMessage()
    msg.set_content(corpo)
    msg['Subject'] = assunto
    msg['From'] = 'noreply@nimbusdesk.com'
    msg['To'] = destinatario

    with smtplib.SMTP('smtp.nimbusdesk.com') as server:
        server.send_message(msg)
```
