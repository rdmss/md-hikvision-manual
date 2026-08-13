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

## O que monitorar em cada plataforma

O escopo do `/health` muda conforme a plataforma configurada. Monte o
monitoramento de acordo.

=== "Senior X"

    O `/health` cobre a integração inteira: além do driver, ele verifica a API
    REST e o canal WebSocket. Um monitor sobre o código HTTP é suficiente.

=== "Senior XT"

    O `/health` cobre o serviço do driver. A conexão com a Concentradora é
    acompanhada pelos indicadores operacionais, e o monitoramento deve incluí-los:

    - **`offlineDevices` em `/diagnostic/data`** — uma frota inteira ficando
      offline de uma vez indica perda da integração.
    - **`Attempting SeniorXT reconnection` no log** — evidência direta de perda
      de conexão com a Concentradora.

    Monte o alerta sobre esses dois sinais, e não apenas sobre o código HTTP do
    `/health`.

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
