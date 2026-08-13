# Validação — Senior X

Roteiro para confirmar que a integração está funcionando ponta a ponta. Faça na
ordem: cada passo depende do anterior.

## 1. O serviço subiu

```bat
sc query HIKVISION-DRIVER
```

Esperado: `RUNNING`.

!!! warning "`RUNNING` não significa integrado"
    O serviço sobe mesmo sem configuração válida. Continue.

## 2. O health responde

```bat
curl -i http://localhost:5000/health
```

Esperado: `200`. Um `503` indica que `driver`, `seniorx-api` ou
`seniorx-websocket` está fora.

Em Senior X esse check é confiável — as três verificações realmente testam.

## 3. O painel mostra o esperado

Abra `/diagnostic` e confirme:

- [ ] Versão correta
- [ ] Plataforma `seniorx`
- [ ] `websocketState` igual a `Open`
- [ ] `totalDevices` batendo com os equipamentos cadastrados
- [ ] `offlineDevices` em zero

## 4. A conectividade passa

Pelo painel:

- [ ] Teste com a Senior — `POST /diagnostic/test/seniorx`
- [ ] Teste com cada dispositivo — `POST /diagnostic/test/device/{id}`

!!! note "Estes testes cobrem só o sentido de ida"
    Eles provam que o driver alcança a Senior e os equipamentos. Não provam que
    os equipamentos alcançam o driver — é o passo 5 que faz isso.

## 5. A passagem de teste — o único que prova tudo

1. Faça uma passagem real numa catraca, com pessoa autorizada
2. Confirme que **liberou**
3. Confirme que o evento aparece em `/diagnostic/events`
4. Confirme que o evento aparece **na Senior**

Se liberou mas não apareceu, o evento não está chegando: reveja o sentido
equipamento → driver.

## 6. A negativa também funciona

Faça uma passagem com pessoa **sem permissão** e confirme que nega. Uma
integração que libera todo mundo não está validando — está falhando aberto.

## 7. A contingência é conhecida

- [ ] Cliente ciente de que, sem a Senior, **o acesso é negado**
- [ ] Monitoramento configurado sobre `/health`
- [ ] `deadLetterSize` incluído no monitoramento

Ver [Checklist de homologação](../antes-de-instalar/checklist.md).
