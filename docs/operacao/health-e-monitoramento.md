# Health check e monitoramento

O endpoint `/health` existe para ser consultado por ferramenta de monitoramento
(Zabbix, PRTG, Nagios, o que o cliente já usar).

!!! info "O driver não avisa ninguém"
    Não há envio de e-mail, push ou alerta ativo quando algo cai. Quem precisa
    perguntar é o monitoramento. Sem um check configurado, uma queda passa
    despercebida até alguém reclamar da catraca.

## O endpoint

```
GET http://<host>:5000/health
```

Aberto — **não exige autenticação**, mesmo com senha configurada no painel. O
monitoramento precisa conseguir ler sem credencial.

### Códigos de resposta

| Código | Significado | Ação do monitoramento |
|---|---|---|
| `200` | Tudo verificado está no ar | — |
| `503` | Pelo menos um item está fora | Alertar |

Use o **código HTTP** como gatilho do alerta: é mais confiável do que interpretar
o corpo.

### Corpo da resposta

Três verificações, cada uma com estado próprio:

| Item | O que verifica |
|---|---|
| `driver` | O serviço interno do driver terminou de subir |
| `seniorx-api` | A API REST da Senior X respondeu |
| `seniorx-websocket` | O canal WebSocket está aberto |

---

## Limitação importante em Senior XT

!!! danger "Em Senior XT, o health não verifica a conexão com a Concentradora"
    As verificações `seniorx-api` e `seniorx-websocket` são **neutralizadas**
    quando a plataforma configurada não é Senior X: elas passam
    automaticamente, sem testar nada.

    Ou seja, numa instalação Senior XT o `/health` responde `200` desde que o
    serviço do driver tenha subido — **mesmo com a Concentradora inacessível**.

    **Consequência prática:** não use `/health` como único monitor em Senior XT.
    Ele detecta "o processo morreu", não "a integração parou".

    **O que usar no lugar, em Senior XT:**

    - Monitore `offlineDevices` em `/diagnostic/data` — se todos os
      dispositivos ficarem offline de uma vez, a integração caiu.
    - Vigie o log por `Attempting SeniorXT reconnection`, que indica perda de
      conexão com a Concentradora.

    Fechar essa lacuna no `/health` é uma correção de produto, registrada em
    [Pendências](../anexos/pendencias.md).

---

## Exemplos de configuração

### Zabbix

Item do tipo *HTTP agent*:

| Campo | Valor |
|---|---|
| URL | `http://{HOST.CONN}:5000/health` |
| Tipo de informação | Texto |
| Códigos aceitos | `200` |

Gatilho, disparando quando a última verificação falhar:

```
last(/HikvisionDriver/hik.health.status)<>200
```

### PRTG

Sensor **HTTP Advanced**:

| Campo | Valor |
|---|---|
| URL | `http://<host>:5000/health` |
| Timeout | 10 s |
| Comportamento | Tratar códigos ≥ 400 como *Down* |

### Verificação manual

```bat
curl -i http://localhost:5000/health
```

---

## O que mais vale monitorar

`/health` responde "está de pé". Para "está saudável", os indicadores de
`/diagnostic/data` dizem mais:

| Indicador | Alertar quando | Significa |
|---|---|---|
| `offlineDevices` | Alto e constante | Dispositivos inalcançáveis, ou keepalive mal dimensionado |
| `retryQueueSize` | Crescendo | A Senior está indisponível ou recusando os eventos |
| `deadLetterSize` | Maior que zero | Eventos desistiram de ser entregues e exigem ação manual |
| `inboxPending` | Maior que zero fora do boot | Processamento de webhooks atrasado |
| `inboxAddFailures` | Maior que zero | Problema de disco ou banco — a durabilidade está degradada |

Ver [Tela de diagnóstico](diagnostico.md) e
[Fila de reenvio e dead-letter](dead-letter.md).
