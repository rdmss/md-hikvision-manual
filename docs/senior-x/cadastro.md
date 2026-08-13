# Cadastro na plataforma — Senior X

Do lado da Senior, o driver precisa estar cadastrado para que a plataforma envie
pendências e receba eventos.

!!! warning "Sequência de telas pendente de confirmação"
    Os nomes de menu e a navegação exata da interface do Senior X **não estão
    documentados aqui** porque não foram verificados em ambiente real. Nenhum
    caminho de menu foi deduzido.

    Pendências `PEND-03` e `IMG-SX-01` em [Pendências](../anexos/pendencias.md).

## O que precisa existir na Senior

Independentemente do caminho de telas, estes são os objetos que a integração
exige:

| Objeto | Para quê | Reflete em |
|---|---|---|
| **Driver** | Representa esta instalação. Emite a *driver key* | `seniorx.driver_key` |
| **Dispositivos** | Cada controladora Hikvision | Lista da [tela de diagnóstico](../operacao/diagnostico.md) |
| **Associação dispositivo ↔ driver** | Diz à Senior por qual driver falar com cada equipamento | Pendências chegando ao driver certo |
| **Permissões de acesso** | Quem pode passar, onde e quando | Resultado da validação |

## Como saber que o cadastro está correto

Sem depender das telas da Senior, os sintomas dizem muito:

| Sintoma | O que indica |
|---|---|
| `totalDevices` menor que o esperado | Dispositivo não cadastrado ou não associado |
| Log: `Device {ManagerDeviceId} not found for pendency {PendencyId}` | A Senior mandou trabalho para um dispositivo que o driver não conhece |
| Pendências nunca chegam | Driver não cadastrado, ou *driver key* incorreta |
| Passagem sempre negada | Permissão ausente, ou credencial não carregada |

Ver [Solução de problemas](../problemas/sintomas.md).

Próximo passo: [Fotos e reconhecimento facial](fotos-e-facial.md).
