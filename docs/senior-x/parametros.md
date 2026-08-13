# Parâmetros — Senior X

Configuração do driver para operar com o Senior X. Preencha pela
[tela de configuração](../instalacao/primeira-configuracao.md).

| Campo na tela | Chave | Obrigatório |
|---|---|:--:|
| Driver Key | `seniorx.driver_key` | ✓ |

Além dos campos comuns — porta, usuário, senha e allowlist do painel.

!!! note "As duas URLs não aparecem na tela"
    `seniorx.api.url` e `seniorx.websocket.url` são propriedades ocultas no
    painel. Os defaults apontam para a produção da Senior:

    | Chave | Default |
    |---|---|
    | `seniorx.api.url` | `https://sam-api.senior.com.br/sdk/v1` |
    | `seniorx.websocket.url` | `wss://sam-api.senior.com.br/websocket/pendency` |

    Para apontar a outro ambiente, edite o `middleware.properties` e reinicie o
    serviço.

!!! danger "A driver key é segredo por instalação"
    Ela identifica **esta** instalação no Senior X. Não reaproveite entre
    clientes: isso mistura as integrações. Sem ela, o canal de pendências não
    sobe e o log registra:

    > Configuração ausente: 'seniorx.driver_key'. Cliente WS não será iniciado.

Descrição completa de cada parâmetro em
[Referência de parâmetros](../referencia/parametros.md).

## Particularidades do Senior X

| Comportamento | Detalhe |
|---|---|
| Carga de faces | *Upsert* — **não remove** quem saiu da lista |
| Fila de reenvio de eventos | Disponível (só existe nesta plataforma) |
| `/health` | Verifica de fato API e WebSocket |

Próximo passo: [Cadastro na plataforma](cadastro.md).
