# Cadastro de catálogo

!!! warning "Capítulo pendente de confirmação"
    O cadastro de catálogo e a aba de acoplados no Senior XT não foram
    verificados em ambiente real. Os nomes de campo e a navegação **não estão
    descritos aqui** — não serão deduzidos.


## Para que serve

O catálogo descreve à Senior o modelo de equipamento e o que ele suporta —
tecnologias de leitura, acoplados e capacidades. É o que permite à plataforma
enviar as pendências certas para cada dispositivo.

## Sintomas de catálogo incorreto

Sem acesso às telas, o comportamento denuncia:

| Sintoma | Provável causa |
|---|---|
| Log com `Not implemented` para um tipo de pendência | A Senior envia trabalho incompatível com o que o driver trata |
| Carga facial nunca solicitada | Tecnologia biométrica não declarada — ver [Biometria](biometria.md) |
| Leitora não reconhecida | Acoplados não cadastrados |

Ver [Mensagens de log](../problemas/mensagens-de-log.md).
