# Fotos e reconhecimento facial — Senior X

Para que o equipamento reconheça rostos, a Senior precisa enviar as fotos das
pessoas ao driver, que as converte em cadastro no dispositivo.

!!! warning "Ativação pendente de confirmação"
    A tela e o caminho de ativação do uso de fotos no Senior X não foram
    verificados em ambiente real e **não estão descritos aqui**.

## Como a carga funciona

A carga facial é o processamento mais pesado do driver: para cada pessoa, baixa
a foto e a envia ao equipamento, sequencialmente.

Uma falha individual **não interrompe** as demais. O log registra pessoa a
pessoa:

```
Carga facial | Device {ManagerDeviceId} Pendency {PendencyId} | [{Index}/{Total}] ERRO {Person} — {Motivo}
```

O `{Motivo}` traz a causa: foto ausente, formato recusado pelo equipamento ou
rosto não detectado.

## Comportamento na remoção

!!! warning "Planeje a remoção no processo de desligamento"
    A carga facial no Senior X é *upsert*: insere e atualiza, e a remoção do
    *template* de quem sai da lista é feita por outro meio. No Senior XT a carga
    é incremental reconciliada e remove automaticamente.

    Tem implicação de LGPD — defina o procedimento de desligamento junto ao
    cliente. Ver [LGPD e dados biométricos](../anexos/lgpd.md).

## Se a carga falha inteira

Falha em **todas** as pessoas, e não em algumas, aponta para causa comum:

| Causa | Verificar |
|---|---|
| Equipamento inalcançável | Estado online na [tela de diagnóstico](../operacao/diagnostico.md) |
| Equipamento sem suporte ou capacidade | Modelo e limite de faces do dispositivo |
| Falha ao obter as fotos | Log, procurando erros de `GET` |

Próximo passo: [Dispositivos e leitoras](dispositivos.md).
