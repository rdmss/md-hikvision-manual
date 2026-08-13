# Tecnologia biométrica

!!! warning "Capítulo pendente de confirmação"
    O cadastro de tecnologia biométrica no Senior XT não foi verificado em
    ambiente real e não está descrito aqui.
    Pendências `PEND-03` e `IMG-XT-04` em [Pendências](../anexos/pendencias.md).

## Para que serve

Declara à Senior que o equipamento faz reconhecimento facial. Sem essa
declaração, a plataforma não envia as pendências de carga facial — e o driver
nunca recebe as fotos.

## Comportamento da carga facial no Senior XT

Diferente do Senior X: aqui a carga é **incremental reconciliada**.

| Aspecto | Comportamento |
|---|---|
| Pessoa nova | Enviada ao equipamento |
| Pessoa alterada | Atualizada |
| **Pessoa que saiu da lista** | **Removida do equipamento** |

!!! success "A remoção acontece nesta plataforma"
    Em Senior XT, o *template* facial de quem sai da lista é removido do
    equipamento. Isso é melhor do ponto de vista de LGPD do que o comportamento
    do Senior X, que não remove. Ver [LGPD](../anexos/lgpd.md).

## Se a carga facial não acontece

| Sintoma | Verificar |
|---|---|
| Nenhuma pendência de carga facial chega | Tecnologia biométrica declarada no cadastro |
| Toda a carga falha | Servlet CSM acessível — ver [Config Center](config-center.md) |
| Falha em pessoas isoladas | O `{Motivo}` no log: foto ausente, formato ou rosto não detectado |
