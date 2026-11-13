# Avaliação de Refatoração: Notificação por E-mail (TP1)

## Código Original (antes da refatoração)

```python
def enviar_email_abertura(destinatario, titulo_ticket):
    if not destinatario:
        return False
    msg = "Assunto: Ticket aberto\n\nSeu ticket " + titulo_ticket + " foi aberto."
    server = smtplib.SMTP('smtp.nimbusdesk.com')
    server.sendmail('noreply@nimbusdesk.com', destinatario, msg)
    server.quit()
    return True

def enviar_email_resposta(destinatario, titulo_ticket):
    if not destinatario:
        return False
    msg = "Assunto: Resposta ao ticket\n\nSeu ticket " + titulo_ticket + " recebeu resposta."
    server = smtplib.SMTP('smtp.nimbusdesk.com')
    server.sendmail('noreply@nimbusdesk.com', destinatario, msg)
    server.quit()
    return True

def enviar_email_fechamento(destinatario, titulo_ticket):
    if not destinatario:
        return False
    msg = "Assunto: Ticket fechado\n\nSeu ticket " + titulo_ticket + " foi fechado."
    server = smtplib.SMTP('smtp.nimbusdesk.com')
    server.sendmail('noreply@nimbusdesk.com', destinatario, msg)
    server.quit()
    return True
```

---

## Refatoração Sugerida pela IA

```python
def montar_mensagem(assunto, corpo):
    return f"Assunto: {assunto}\n\n{corpo}"

def enviar_notificacao(destinatario, assunto, corpo):
    if not destinatario:
        raise ValueError("Destinatário vazio")
    msg = montar_mensagem(assunto, corpo)
    server = smtplib.SMTP('smtp.nimbusdesk.com')
    server.sendmail('noreply@nimbusdesk.com', destinatario, msg)
    server.quit()
    return True

def enviar_email_abertura(destinatario, titulo_ticket):
    return enviar_notificacao(destinatario, "Ticket aberto",
        f"Seu ticket {titulo_ticket} foi aberto.")

def enviar_email_resposta(destinatario, titulo_ticket):
    return enviar_notificacao(destinatario, "Resposta ao ticket",
        f"Seu ticket {titulo_ticket} recebeu resposta.")

def enviar_email_fechamento(destinatario, titulo_ticket):
    return enviar_notificacao(destinatario, "Ticket fechado",
        f"Seu ticket {titulo_ticket} foi fechado.")
```

---

## Avaliação da Qualidade

### Legibilidade

**Melhorou.** As três funções originais tinham nomes genéricos (`msg`) e lógica repetida. A versão refatorada tem `montar_mensagem` e `enviar_notificacao` com responsabilidades claras, e as funções de domínio delegam a elas. Um desenvolvedor novo no projeto entende o fluxo em uma leitura.

### Duplicação

**Eliminada.** Antes, o bloco de montagem e envio aparecia três vezes. Agora aparece uma vez, em `enviar_notificacao`. Se o servidor SMTP mudar, só um lugar precisa ser alterado.

### Aderência às Regras de Negócio Originais

**Regressão introduzida e corrigida.** O código original retornava `False` quando o destinatário era vazio. A versão refatorada passou a lançar `ValueError`, o que quebra o contrato com quem chama as funções (o chamador original não trata exceção). Corrigi restaurando o retorno `False` e mantendo o comportamento original:

```python
def enviar_notificacao(destinatario, assunto, corpo):
    if not destinatario:
        return False
    # ... resto igual
```

Adicionei o teste `test_destinatario_vazio_retorna_false` para cobrir o caso e impedir que a regressão volte.

---

## Conclusão

A refatoração melhorou a legibilidade e eliminou a duplicação, mas introduziu uma regressão de comportamento que não era visível na leitura superficial do código novo. A regressão foi detectada ao comparar o comportamento da versão refatorada com a original, e corrigida restaurando o contrato. A lição é que refatoração sem teste de comportamento é arriscada: o teste que faltava (destinatário vazio) foi justamente o que expôs a regressão.
