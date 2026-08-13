# Endpoints da API

Todos servidos na porta definida por `middleware.api.port` — `5000` por padrão.

## Rotas

| Método | Rota | Para quê | Autenticação |
|---|---|---|---|
| `POST` | `/api/v1/isapi` | Webhook de eventos do equipamento | Aberto |
| `GET` | `/health` | Estado agregado, para monitoramento | Aberto |
| `GET` | `/diagnostic` | Painel de diagnóstico | Painel |
| `GET` | `/diagnostic/data` | Indicadores em JSON | Painel |
| `GET` | `/diagnostic/events` | Eventos de acesso recentes | Painel |
| `GET` | `/diagnostic/logs` | Leitura de log pela web | Painel |
| `POST` | `/diagnostic/test/device/{id}` | Teste de conectividade com um dispositivo | Painel |
| `POST` | `/diagnostic/test/seniorx` | Teste de conectividade com a plataforma | Painel |
| `GET` `POST` | `/configuration` | Ler e salvar configuração | Painel |
| `GET` | `/` e `/config` | Tela de configuração | Painel |

## Autenticação

As rotas marcadas como **Painel** usam `middleware.api.user` e
`middleware.api.password`. O acesso também respeita `middleware.api.allowlist`,
quando preenchida.

!!! danger "Senha vazia desliga a autenticação"
    Com `middleware.api.password` em branco — que é o padrão de fábrica — as
    rotas do painel ficam acessíveis a qualquer origem que alcance a porta.
    Inclui `/configuration`, que **altera** a configuração do driver.

### Por que webhook e health ficam abertos

O equipamento não faz login antes de entregar um evento, e o monitoramento
precisa ler o health sem credencial. Os dois permanecem acessíveis mesmo com
senha configurada — por isso a proteção real dessa porta é de rede
(`middleware.api.allowlist` e firewall), não só de senha.

## Webhook de eventos

```
POST /api/v1/isapi
```

É por aqui que cada passagem chega. O equipamento é registrado automaticamente
pelo driver para postar em `http://<endereço-do-driver>:<porta>/api/v1/isapi`,
via a configuração de *HTTP hosts* de notificação do ISAPI.

!!! warning "Este é o caminho que o firewall costuma bloquear"
    O sentido é **equipamento → driver**. Liberar apenas driver → equipamento faz
    tudo parecer funcionar sem nenhuma validação acontecer. Ver
    [Rede e portas](../antes-de-instalar/rede-e-portas.md).

## Health

```
GET /health
```

| Código | Significado |
|---|---|
| `200` | Todos os itens verificados estão no ar |
| `503` | Pelo menos um item está fora |

Três verificações: `driver`, `seniorx-api` e `seniorx-websocket`.

!!! warning "As duas últimas só valem em Senior X"
    Quando a plataforma não é Senior X, essas verificações passam
    automaticamente. Ver [Health check e monitoramento](../operacao/health-e-monitoramento.md).

## Diagnóstico em JSON

```
GET /diagnostic/data
```

Traz versão, tempo desde o start, plataforma, porta, estado da integração,
estatísticas de dispositivos e filas, a lista de dispositivos e a situação da
fila de reenvio. O significado de cada indicador está em
[Tela de diagnóstico](../operacao/diagnostico.md).

## Eventos recentes

```
GET /diagnostic/events?deviceId=<id>&limit=<n>
```

Ambos os parâmetros são opcionais; `limit` vale 200 por padrão.

## Configuração

```
GET  /configuration
POST /configuration
```

Permite ler e gravar a configuração sem passar pela tela. Salvar com campos
obrigatórios em branco é recusado com
`Preencha as propriedades obrigatorias: <lista>`.

!!! danger "Esta rota expõe e altera a integração"
    Trate-a como superfície sensível: senha definida e allowlist restrita.
