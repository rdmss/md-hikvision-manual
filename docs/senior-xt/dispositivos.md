# Dispositivos e leitoras — Senior XT

!!! warning "Telas de cadastro pendentes de confirmação"
    A sequência de telas do Senior XT para cadastro de controladora, leitora
    facial e leitoras de cartão não foi verificada em ambiente real e não está
    descrita aqui. Pendências `PEND-03` e `IMG-XT-05` em

## O que o driver faz com o dispositivo

1. **Provisiona** o equipamento pela interface ISAPI
2. **Registra o endereço de retorno** — instrui o equipamento a postar eventos em
   `http://<endereço-do-driver>:<porta>/api/v1/isapi`
3. **Sonda** periodicamente, reportando online/offline
4. **Carrega credenciais** conforme as pendências recebidas

!!! danger "O equipamento precisa alcançar o driver"
    Se o firewall liberar só o sentido driver → equipamento, o dispositivo
    aparece online, a carga conclui e **nenhuma passagem é validada**. É a falha
    mais comum. Ver [Rede e portas](../antes-de-instalar/rede-e-portas.md).

## Credenciais do equipamento

Os campos opcionais `seniorxt.ext.username` e `seniorxt.ext.password` são usados
quando o equipamento exige autenticação. Se o provisionamento falhar com
`ConfigureDevices failed for device {DeviceId}`, revise-os.

## Conferindo

| Onde | O que confirmar |
|---|---|
| `/diagnostic` | `totalDevices` bate; `offlineDevices` em zero |
| Teste de dispositivo | `POST /diagnostic/test/device/{id}` passa |
| `/diagnostic/events` | Uma passagem de teste aparece |

Próximo passo: [Validação](validacao.md).
