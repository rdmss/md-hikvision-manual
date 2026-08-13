# Dispositivos e leitoras — Senior X

Cada controladora Hikvision precisa estar cadastrada na Senior e associada a
este driver.

!!! warning "Telas de cadastro pendentes de confirmação"
    A sequência exata de telas do Senior X não foi verificada em ambiente real e
    não está descrita aqui. Pendências `PEND-03` e `IMG-SX-02` em
    [Pendências](../anexos/pendencias.md).

## O que o driver faz com o dispositivo

Ao conhecer um dispositivo, o driver:

1. **Provisiona** o equipamento pela interface ISAPI
2. **Registra o endereço de retorno** — instrui o equipamento a postar os eventos
   em `http://<endereço-do-driver>:<porta>/api/v1/isapi`
3. **Passa a sondá-lo** periodicamente, reportando online/offline à Senior
4. **Carrega credenciais** quando a Senior enviar a pendência correspondente

!!! danger "O passo 2 é o que costuma quebrar"
    O equipamento precisa **alcançar** o endereço registrado. Se o firewall
    liberar só o sentido driver → equipamento, o dispositivo aparece online, a
    carga conclui, e nenhuma passagem é validada.

    Ver [Rede e portas](../antes-de-instalar/rede-e-portas.md).

!!! warning "IP do driver não pode mudar"
    O endereço fica gravado no equipamento. Se o IP da máquina mudar, os
    equipamentos continuam apontando para o antigo e os eventos param de chegar.
    Use IP fixo ou reserva de DHCP.

## Conferindo

| Onde | O que confirmar |
|---|---|
| `/diagnostic` | `totalDevices` bate com o número de equipamentos |
| `/diagnostic` | `offlineDevices` em zero |
| Teste de dispositivo | `POST /diagnostic/test/device/{id}` passa |
| `/diagnostic/events` | Uma passagem de teste aparece |

O último item é o único que prova o caminho de volta.

Próximo passo: [Validação](validacao.md).
