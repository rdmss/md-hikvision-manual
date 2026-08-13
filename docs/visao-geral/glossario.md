# Glossário

Termos que aparecem neste manual, no log do driver e nas conversas de suporte.

### Anti-oscilação (*anti-flapping*)

Mecanismo que exige um número de falhas consecutivas antes de declarar um
equipamento offline. Impede que uma perda momentânea de pacote vire alarme e
gere um vaivém de notificações à Senior. Controlado por
`seniorx.keepalive.device.failthreshold`.

### Concentradora (CSM)

Componente do Senior XT que fica na rede do cliente e concentra a comunicação
com os drivers, por protocolo TCP-IP. É o equivalente, no XT, ao papel que a
nuvem cumpre no Senior X.

### Dead-letter

Pasta onde vão os eventos que desistiram de ser entregues à Senior — por
rejeição permanente ou por estouro de tentativas/validade. **Não se resolve
sozinho:** exige ação manual. Ver [dead-letter](../operacao/dead-letter.md).

### Driver ID

Identificador numérico do driver no Senior XT, configurado em `seniorxt.driver`.
Não confundir com a *driver key* do Senior X.

### Driver key

Segredo que identifica a instalação no Senior X (`seniorx.driver_key`). É por
instalação — não deve ser reaproveitado entre clientes. Sem ela, o canal de
pendências não sobe.

### Inbox de webhooks

Camada de durabilidade que persiste o evento bruto do equipamento **antes** de
confirmar o recebimento. Garante que um crash no meio do processamento não perca
o evento.

### ISAPI

Interface HTTP dos equipamentos Hikvision, usada pelo driver para configurar o
dispositivo, carregar credenciais e sondar saúde. É também por ela que o
equipamento é instruído a enviar eventos ao driver.

### Keepalive

Sondagem periódica que verifica se cada equipamento está respondendo. Alimenta o
estado online/offline reportado à Senior.

### Partner key

Identificador do fabricante do driver, constante no produto. Diferente da
*driver key*, que é por instalação e é segredo.

### Pendência

Unidade de trabalho que a Senior envia ao driver: carregar cartões, carregar
faces, bloquear dispositivo, coletar eventos, sincronizar data e hora, entre
outras. Chegam pelo canal WebSocket, no Senior X.

!!! note "`Not implemented` no log"
    Significa que a Senior enviou um tipo de pendência que este driver não trata.
    Não é erro de configuração — é funcionalidade ausente.

### Replace total

Estratégia de carga em que o driver limpa a lista do equipamento e reescreve
inteira, em vez de aplicar diferenças. É como os **cartões** são carregados.

### `remoteCheck` (verificação remota)

Modo em que o equipamento não decide o acesso: consulta o servidor a cada
passagem. É o modo em que este driver opera, e a razão de não haver liberação
local durante quedas.

### Resync

Reconciliação periódica entre o que o driver acredita sobre um equipamento e o
estado real dele, corrigindo divergências acumuladas.

### Senior X

Plataforma da Senior em nuvem. O driver se comunica por REST e por um canal
WebSocket de pendências.

### Senior XT

Plataforma da Senior com Concentradora CSM na rede do cliente, por protocolo
TCP-IP.

### Servlet CSM

Endpoint do Senior XT usado para consultar fotos na carga facial
(`seniorxt.csmservlet`). Se estiver inacessível, a carga facial falha mesmo com
a Concentradora respondendo.

### *Template* facial

Representação matemática do rosto, gravada no equipamento. Não é a foto
original. É dado biométrico e, portanto, dado pessoal sensível — ver
[LGPD](../anexos/lgpd.md).

### *Upsert*

Estratégia de carga que insere o que não existe e atualiza o que existe, mas
**não remove** o que saiu da lista. É como as **faces** são carregadas no
Senior X.
