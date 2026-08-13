# Eventos e motivos

## Como um evento percorre o sistema

```
Equipamento → POST /api/v1/isapi → Inbox (gravado antes do aceite)
                                        │
                                        ▼
                              Processamento no driver
                                        │
                    ┌───────────────────┴───────────────────┐
                    ▼                                       ▼
       Validação online (remoteCheck)            Notificação à Senior
                    │                                       │
                    ▼                                       ▼
       Resposta ao equipamento                   Sucesso? → concluído
       (libera ou nega)                          Falha?   → fila de reenvio
                                                              │
                                                    estourou limite?
                                                              ▼
                                                        dead-letter
```

## Onde consultar

| Fonte | Conteúdo |
|---|---|
| `GET /diagnostic/events` | Eventos de acesso recentes; aceita `deviceId` e `limit` |
| Banco local | Todos os eventos processados; recoletável pela Senior |
| `events\` | Aguardando reenvio |
| `events\deadletter\` | Desistiram de ser entregues |

## Estados possíveis de um evento

| Estado | Onde está | Ação |
|---|---|---|
| Recebido, não processado | Inbox | Nenhuma — será processado, inclusive após reinício |
| Processado e entregue | Banco | Nenhuma |
| Entrega falhou | `events\` | Nenhuma — reenvio automático a cada 30 s |
| Desistiu | `events\deadletter\` | **Manual** — ver [dead-letter](../operacao/dead-letter.md) |
| Arquivo corrompido | `.invalid` | Investigar disco se recorrente |

!!! note "Validações pendentes não são refeitas"
    Validações online que ficaram pendentes de execuções anteriores são
    descartadas no replay do inbox: a janela de resposta do equipamento já
    expirou, e reprocessar registraria um acesso que pode não ter ocorrido.

## Motivos de negativa

!!! warning "Catálogo pendente"
    A relação fechada de códigos e motivos de negativa devolvidos pelo
    equipamento e pela plataforma não foi consolidada nesta documentação. Os
    motivos aparecem no log e em `/diagnostic/events` com o texto de origem.

    Registrado em [Pendências](../anexos/pendencias.md).

Quando o equipamento recusa a resposta enviada pelo driver, o log traz o motivo
literal dado por ele:

```
Erro na resposta do acesso para o dispositivo {DeviceId}: {Response}
```

Na carga facial, a recusa por pessoa aparece como:

```
Carga facial | ... [{Index}/{Total}] ERRO {Person} — {Motivo}
```
