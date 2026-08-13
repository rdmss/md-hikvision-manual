# Permissões na Senior

Para operar, o driver depende de um conjunto de permissões na plataforma Senior.

!!! warning "Lista pendente de confirmação"
    A relação de permissões e os códigos de tela **não estão publicados aqui**
    porque não foram verificados. O manual de referência do concorrente lista 17
    itens com códigos; publicar uma lista deduzida seria pior do que não ter:
    levaria alguém a conceder permissões erradas ou a abrir um chamado por uma
    permissão inexistente.

    Pendência `PEND-03` em [Pendências](../anexos/pendencias.md).

## O que o driver precisa fazer na plataforma

Enquanto a lista formal não é confirmada, estas são as **capacidades** que a
integração exige — verificáveis pelo comportamento do produto:

| Capacidade | Por quê |
|---|---|
| Autenticar como driver | Estabelecer a sessão com a plataforma |
| Receber pendências | Cargas de credenciais, comandos e coletas |
| Consultar pessoas e credenciais | Montar a lista de cartões e faces |
| Consultar fotos | Carga facial (via servlet CSM, no Senior XT) |
| Validar acesso em tempo real | Decidir liberar ou negar cada passagem |
| Notificar eventos de acesso | Registrar as passagens na plataforma |
| Reportar estado de dispositivo | Informar online/offline |

## Como identificar permissão faltando

Falta de permissão costuma aparecer como falha específica, não como queda geral:

| Sintoma | Capacidade provavelmente ausente |
|---|---|
| Pendências nunca chegam | Recebimento de pendências |
| Carga de cartões falha inteira | Consulta de pessoas e credenciais |
| Carga facial falha inteira, cartões funcionam | Consulta de fotos |
| Validação sempre nega, mesmo com pessoa autorizada | Validação de acesso |
| Eventos não aparecem na Senior | Notificação de eventos |
| Dispositivo sempre indisponível na Senior | Reporte de estado |

O log traz o erro da chamada recusada — ver
[Mensagens de log](../problemas/mensagens-de-log.md).
