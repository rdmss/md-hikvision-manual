# Rede e portas

!!! danger "O erro mais comum de instalação está aqui"
    A integração exige comunicação **nos dois sentidos**. Não basta o driver
    alcançar a Senior e os equipamentos: **cada equipamento precisa alcançar o
    driver** para entregar os eventos de acesso.

    Quando só o sentido driver → equipamento está liberado, tudo parece
    funcionar — os dispositivos aparecem online, a carga de cartões conclui —
    mas **nenhuma passagem é validada**, porque o evento nunca chega. É um
    sintoma que engana, e quase sempre é firewall ou VLAN.

## Portas necessárias

| Origem | Destino | Porta | Protocolo | Para quê |
|---|---|---|---|---|
| Equipamento | **Driver** | `5000` | HTTP | **Entrega dos eventos de acesso** (webhook) |
| Driver | Equipamento | 80 / 443 | HTTP(S) ISAPI | Configuração, carga de credenciais, sondagem de saúde |
| Driver | Senior X | 443 | HTTPS + WSS | API REST e canal de pendências |
| Driver | Concentradora | conforme `seniorxt.port` | TCP | Protocolo CSM |
| Operador | Driver | `5000` | HTTP | Painel de configuração e diagnóstico |

A porta do driver é a definida em `middleware.api.port` — `5000` por padrão. Se
você alterar, altere junto a regra de firewall e o cadastro nos equipamentos.

## Por plataforma

=== "Senior X"

    O driver precisa de saída para a internet, em HTTPS e WebSocket seguro:

    | Destino | Porta |
    |---|---|
    | `sam-api.senior.com.br` | 443 |

    Ambos os endereços são configuráveis, mas só pelo arquivo — ver
    [Referência de parâmetros](../referencia/parametros.md).

    !!! note "Proxy que corta WebSocket"
        O canal de pendências usa `wss://`. Proxies corporativos que inspecionam
        HTTP às vezes derrubam o *upgrade* para WebSocket. O sintoma é a API
        funcionar e as pendências nunca chegarem, com reconexões seguidas no log.

=== "Senior XT"

    O driver precisa alcançar a **Concentradora** na rede do cliente:

    | Destino | Porta |
    |---|---|
    | `seniorxt.server` | `seniorxt.port` |
    | Servlet CSM (`seniorxt.csmservlet`) | conforme a URL informada |

    O servlet CSM é usado na consulta de fotos para a carga facial. Se ele
    estiver inacessível, o cadastro de faces falha mesmo com a Concentradora
    respondendo.

## IP do driver

O driver registra nos equipamentos o endereço para onde eles devem postar os
eventos. **Se o IP da máquina mudar, os equipamentos continuam apontando para o
endereço antigo** e os eventos param de chegar.

Use IP fixo, ou reserva de DHCP vinculada ao MAC.

## Como validar antes de instalar

Do **equipamento** em direção ao driver — este é o teste que quase ninguém faz e
o que mais evita retrabalho. Se o equipamento tiver console ou acesso via
navegador, confirme que ele alcança `http://<ip-do-driver>:5000/health`.

Da **máquina do driver**:

```bat
REM Alcança o equipamento?
curl -i http://<ip-do-equipamento>/ISAPI/System/deviceInfo

REM Alcança a Senior X?
curl -i https://sam-api.senior.com.br/sdk/v1

REM Alcança a Concentradora? (Senior XT)
Test-NetConnection <seniorxt.server> -Port <seniorxt.port>
```

Depois de instalado, o painel tem testes prontos de conectividade — ver
[Tela de diagnóstico](../operacao/diagnostico.md).
