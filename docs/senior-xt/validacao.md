# Validação — Senior XT

Roteiro para confirmar a integração ponta a ponta. A ordem importa.

!!! danger "Não confie no `/health` nesta plataforma"
    Em Senior XT, as verificações de API e WebSocket passam automaticamente. O
    `/health` responde `200` mesmo com a Concentradora inacessível — ele só
    prova que o processo subiu.

    Por isso o roteiro abaixo se apoia no **log** e na **lista de
    dispositivos**, não no health.

## 1. O serviço subiu

```bat
sc query HIKVISION-DRIVER
```

Esperado: `RUNNING`.

## 2. O certificado está no lugar

Confirme que o arquivo indicado em `seniorxt.certificate` — `HIK.CER` por padrão
— existe no diretório de instalação. Sem ele, a autenticação falha em silêncio.
Ver [Certificado](certificado.md).

## 3. A autenticação com a Concentradora estabeleceu

Abra o log do dia e procure por:

| Encontrou | Significa |
|---|---|
| `Enviando autenticacao SeniorXT. Driver={DriverId}` | Handshake ocorreu |
| `Attempting SeniorXT reconnection` **em laço** | **Não estabeleceu** — verifique certificado, `seniorxt.driver`, endereço e porta |
| `ClientCSMCommunication nao foi registrado` | Configuração Senior XT incompleta |

Este passo substitui o `/health` como evidência de que a integração está viva.

## 4. O painel mostra o esperado

Em `/diagnostic`:

- [ ] Plataforma `seniorxt`
- [ ] `totalDevices` batendo com os equipamentos cadastrados
- [ ] `offlineDevices` em zero

!!! note "Ignore `websocketState` aqui"
    Esse indicador diz respeito ao Senior X e não reflete a Concentradora.

## 5. A conectividade com cada dispositivo passa

`POST /diagnostic/test/device/{id}` para cada equipamento.

Cobre apenas o sentido de ida — o passo 6 cobre a volta.

## 6. A passagem de teste

1. Passagem real com pessoa autorizada → deve **liberar**
2. Evento aparece em `/diagnostic/events`
3. Evento aparece **na Senior**

Liberou mas não apareceu: o evento não está chegando. Reveja o sentido
equipamento → driver.

## 7. A carga facial conclui

- [ ] Carga facial solicitada e concluída
- [ ] Passagem por face liberada
- [ ] Desligamento de teste **remove** a face do equipamento (comportamento
      esperado nesta plataforma)

## 8. A negativa funciona

Passagem com pessoa sem permissão deve **negar**.

## 9. Contingência e monitoramento

- [ ] Cliente ciente de que, sem a Senior, o acesso é negado
- [ ] Monitoramento sobre `offlineDevices` e sobre o log — **não** sobre o
      `/health` isoladamente
- [ ] `deadLetterSize` observado

!!! warning "Não há fila de reenvio em Senior XT"
    O serviço de reenvio só existe em Senior X. A garantia de reentrega
    documentada para aquela plataforma não se aplica aqui.

Ver [Checklist de homologação](../antes-de-instalar/checklist.md).
