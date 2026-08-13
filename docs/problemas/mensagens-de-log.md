# Mensagens de log

As mensagens abaixo são as que o driver realmente grava, com o texto literal que
você vai encontrar em `log\log-AAAAMMDD.txt`. Use **Ctrl+F** com um trecho da
mensagem.

Trechos entre chaves — `{DeviceId}`, `{PendencyId}` — são substituídos por
valores reais no log.

---

## Inicialização

| Mensagem | Sev. | Significado | Ação |
|---|---|---|---|
| `Erro ao iniciar middleware` | Erro | Falha geral na subida da integração. A exceção acompanha a linha. | Leia a exceção logo abaixo. Quase sempre é configuração inválida ou porta ocupada. |
| `Thirdpart selecionado nao sera iniciado porque ha propriedades obrigatorias faltando: {MissingProperties}` | Aviso | O serviço subiu, mas **sem integração** — faltam campos obrigatórios. | Preencha as propriedades listadas na [tela de configuração](../instalacao/primeira-configuracao.md). |
| `Thirdpart '{Thirdpart}' nao suportado para inicializacao do middleware.` | Aviso | O valor de `thirdpart` não é `seniorx` nem `seniorxt`. | Corrija a plataforma na configuração. |
| `ClientCSMCommunication nao foi registrado para o fluxo SeniorXT.` | Erro | O driver tentou usar o canal da Concentradora sem ele ter sido registrado — sinal de que a configuração Senior XT está incompleta. | Verifique se `thirdpart=seniorxt` e se todos os campos obrigatórios de XT estão preenchidos. |

---

## Senior X — API e WebSocket

| Mensagem | Sev. | Significado | Ação |
|---|---|---|---|
| `Configuração ausente: 'seniorx.driver_key'. Cliente WS não será iniciado.` | Aviso | Sem a chave, o canal de pendências não sobe. Cargas e comandos da Senior nunca chegam. | Preencha `seniorx.driver_key`. |
| `Conectando ao WebSocket` | Info | Tentativa de conexão em andamento. | — |
| `Conectado ao WebSocket: {State}` | Info | Canal aberto. | — |
| `Erro inesperado no cliente WS` | Erro | Falha no canal de pendências. | Verifique a saída para `wss://` e se há proxy cortando o *upgrade*. Ver [Rede e portas](../antes-de-instalar/rede-e-portas.md). |
| `Erro ao processar mensagem WS` | Erro | Chegou uma mensagem que o driver não conseguiu tratar. | Se for recorrente, colete o log e acione o suporte. |
| `Erro GET {Endpoint}` / `Erro POST {Endpoint}` | Erro | Chamada à API da Senior falhou. | Confirme `seniorx.api.url` e a conectividade. |

---

## Senior XT — Concentradora

| Mensagem | Sev. | Significado | Ação |
|---|---|---|---|
| `Attempting SeniorXT reconnection. Attempt {Attempt}.` | Aviso | Conexão com a Concentradora caiu; o driver está reconectando. | Se repetir sem parar, verifique `seniorxt.server`, `seniorxt.port` e a rede. Em Senior XT esta é a principal evidência de queda da integração. |
| `Enviando autenticacao SeniorXT. Driver={DriverId}, MessageNumber={MessageNumber}` | Info | Handshake em curso. | — |
| `Erro ao enviar mensagem {MessageType} #{MessageNumber}.` | Erro | Falha ao transmitir para a Concentradora. | Verifique a conexão. |
| `Erro ao enviar ACK para a mensagem #{MessageNumber}.` / `NACK` | Erro | Falha ao confirmar recebimento. | Idem. |
| `ACK/NACK sem requisicao pendente #{MessageNumber}` | Aviso | Chegou confirmação de uma mensagem que o driver não estava esperando — normalmente após reconexão. | Isolado, é inofensivo. Em volume, indica instabilidade de rede. |
| `Error replying to IsAliveCommand.` | Erro | O driver não conseguiu responder ao teste de vida da Concentradora. | A Senior pode marcar o driver como indisponível. Verifique a rede. |

---

## Dispositivos e saúde

| Mensagem | Sev. | Significado | Ação |
|---|---|---|---|
| `Erro no keepalive do device {DeviceId}` | Erro | O dispositivo não respondeu à sondagem. | Confirme que o equipamento está ligado e alcançável na porta ISAPI. |
| `DevicesKeepAlive \| Error` | Erro | Falha no ciclo de sondagem como um todo, não em um dispositivo. | Investigue rede ou saturação. Ver [Referência de parâmetros](../referencia/parametros.md). |
| `Erro no resync do device {DeviceId}` | Erro | A reconciliação periódica falhou para esse dispositivo. | Idem. |
| `Erro ao enviar status do device {DeviceId}` | Erro | O driver não conseguiu reportar online/offline à Senior. | Verifique a conexão com a plataforma. |
| `ConfigureDevices failed for device {DeviceId}` | Erro | Falha ao provisionar o equipamento. | Confirme credenciais do equipamento e se o ISAPI está habilitado. |
| `Erro na resposta do acesso para o dispositivo {DeviceId}: {Response}` | Erro | O equipamento recusou a resposta de validação enviada pelo driver. | O texto em `{Response}` traz o motivo dado pelo equipamento. |

---

## Pendências e cargas

| Mensagem | Sev. | Significado | Ação |
|---|---|---|---|
| `Device {ManagerDeviceId} not found for pendency {PendencyId}` | Erro | A Senior mandou trabalho para um dispositivo que o driver não conhece. | O equipamento provavelmente não está cadastrado ou associado corretamente na Senior. |
| `<Tipo> \| Pendency: {PendencyId} - Device: {ManagerDeviceId} - Not implemented` | Erro | O tipo de pendência recebido não é tratado nesta versão. | Anote o `<Tipo>` e o `PendencyId` e acione o suporte. |
| `Erro processando pendência {PendencyId} do device {DeviceId}` | Erro | Falha genérica no processamento. | Leia a exceção que acompanha. |
| `Carga facial \| Device {ManagerDeviceId} Pendency {PendencyId} \| [{Index}/{Total}] ERRO {Person} — {Motivo}` | Erro | Uma pessoa específica falhou na carga facial; as demais seguem. | `{Motivo}` diz o porquê — foto ausente, formato recusado pelo equipamento, rosto não detectado. |
| `CollectEventsByDate \| Device {ManagerDeviceId} - Error {PendencyId}` | Erro | Falha ao coletar eventos por período. | Verifique se o equipamento responde. |
| `DateTime \| Area {ManagerDeviceId} not found for device {Id}` | Erro | Sincronização de data/hora sem área correspondente. | Revise o cadastro do dispositivo na Senior. |

---

## Fila de eventos

| Mensagem | Sev. | Significado | Ação |
|---|---|---|---|
| `Arquivo de retry invalido {File}; movendo para .invalid` | Erro | Um arquivo da fila está corrompido e foi posto de lado. | Se recorrente, investigue o disco. |
| `Evento movido para dead-letter` | Aviso | O evento desistiu de ser entregue. | Ver [Fila de reenvio e dead-letter](../operacao/dead-letter.md). |

---

!!! tip "Segredos não aparecem no log"
    Valores de `driver_key`, `partner_key`, senha, token e secret são mascarados
    automaticamente. Um log pode ser anexado a um chamado sem expor credenciais —
    ainda assim, confira antes de enviar para fora.
