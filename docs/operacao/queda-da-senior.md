# Queda da Senior

O que acontece quando o driver perde comunicação com a plataforma. É a pergunta
que o suporte mais recebe.

## Resumo

| Situação | Comportamento |
|---|---|
| Driver perde conexão com a Senior | Continua rodando e reconecta sozinho, com espera crescente de 5 s até 120 s |
| Validação de acesso durante a queda | **Acesso negado** — não há liberação local |
| Eventos ocorridos durante a queda | Gravados localmente e reenviados quando a conexão volta |
| Driver reiniciado no meio do processamento | Eventos pendentes são reprocessados no próximo start |

---

## Por que o acesso é negado

!!! danger "Não existe liberação local de contingência"
    Os equipamentos são provisionados em **modo de verificação remota**: toda
    decisão de liberar ou negar é da Senior, em tempo real. As credenciais
    gravadas no equipamento ficam com validade local **desabilitada de
    propósito**.

    É o desenho do modo online — garante que horário, bloqueio, crédito e demais
    regras sejam sempre os do servidor, nunca uma cópia desatualizada no
    equipamento.

Com a Senior inacessível:

- O driver responde **acesso negado** à validação. No log aparece
  `Erro ao validar acesso no Senior`.
- Se o **driver** estiver fora do ar, o equipamento aguarda a janela de
  `seniorx.remotecheck.timeout` (15 s por padrão) e aplica o comportamento de
  timeout do próprio firmware.

**Operar com liberação local durante quedas é evolução de produto**, não
configuração: exigiria habilitar validade local nas credenciais e definir uma
janela de tolerância. Se o cliente precisa disso, trate como demanda à parte.

---

## O que acontece com os eventos

Nenhum evento é descartado em silêncio. São três camadas:

### 1. Banco local

Todo evento processado é gravado e pode ser recoletado pela Senior por meio de
pendência de coleta.

### 2. Fila de reenvio

Notificações que falharam por indisponibilidade viram arquivos na pasta
`events\` e são reenviadas a cada 30 segundos.

Limites: `seniorx.eventretry.maxretries` (2880, cerca de 24 h) e
`seniorx.eventretry.ttl.hours` (72 h). O que estoura vai para dead-letter.

!!! warning "A fila de reenvio só existe em Senior X"
    O serviço de reenvio é registrado apenas quando `thirdpart=seniorx`. Em
    Senior XT estes parâmetros não têm efeito.

### 3. Inbox de webhooks

O evento bruto do equipamento é persistido **antes** de o driver confirmar o
recebimento. Se o processo morrer no meio do processamento, o evento é
reprocessado no próximo start.

!!! note "Validações pendentes não são refeitas no replay"
    Validações online que ficaram pendentes de execuções anteriores são
    **descartadas** no replay: a janela de resposta do equipamento já expirou, e
    reprocessar registraria um acesso que pode não ter acontecido.

---

## Como perceber que caiu

=== "Senior X"

    `/health` responde `503` — os itens `seniorx-api` e `seniorx-websocket`
    detectam a queda.

    No log: `Erro inesperado no cliente WS`, `Erro GET`/`Erro POST`, e as
    tentativas de reconexão.

=== "Senior XT"

    Acompanhe pelos indicadores operacionais:

    - O log, procurando por `Attempting SeniorXT reconnection`
    - `offlineDevices` em `/diagnostic/data` — uma queda em massa é sinal forte

    Ver [Health check e monitoramento](health-e-monitoramento.md).

---

## Quando a conexão volta

A reconexão é automática — ninguém precisa intervir. A fila de reenvio esvazia
sozinha, com uma tentativa a cada 30 s por evento.

Confirme na [tela de diagnóstico](diagnostico.md) que `retryQueueSize` está
caindo. Se estiver parado e `deadLetterSize` subindo, a Senior está **recusando**
os eventos, não indisponível — ver
[Fila de reenvio e dead-letter](dead-letter.md).
