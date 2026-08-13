# O que o driver faz

O Hikvision Driver conecta controladoras de acesso Hikvision (linha DS-K, via
ISAPI) às plataformas de gestão de acesso da Senior. Ele roda como serviço
Windows na rede do cliente, entre os equipamentos e a plataforma.

Quatro responsabilidades, todas ativas ao mesmo tempo.

## 1. Validação de acesso em tempo real

Quando alguém apresenta cartão ou face, o equipamento **não decide sozinho**.
Ele envia o evento ao driver, que consulta a Senior e devolve a resposta —
liberar ou negar.

```
Pessoa → Equipamento → [webhook] → Driver → Senior
                                      ↓
Equipamento ← resposta (libera/nega) ←┘
```

O equipamento aguarda essa resposta por uma janela configurável
(`seniorx.remotecheck.timeout`, 15 s por padrão). Passado esse prazo, aplica o
comportamento de timeout do próprio firmware.

!!! danger "A decisão é sempre do servidor"
    As credenciais gravadas no equipamento têm validade local **desabilitada de
    propósito**. Isso garante que horário, bloqueio e demais regras sejam sempre
    os da Senior — nunca uma cópia desatualizada no equipamento.

    A contrapartida: **sem a Senior, o acesso é negado**. Não existe liberação
    local de contingência. Ver [Queda da Senior](../operacao/queda-da-senior.md).

## 2. Carga de credenciais

O driver sincroniza nos equipamentos quem pode passar.

| Credencial | Estratégia | O que significa na prática |
|---|---|---|
| **Cartões** | Substituição total | O driver limpa a lista do equipamento e reescreve inteira |
| **Faces — Senior XT** | Incremental reconciliada | Envia só o que mudou e **remove quem saiu** da lista |
| **Faces — Senior X** | *Upsert* | Adiciona e atualiza, mas **não remove** quem saiu |

!!! note "A diferença na remoção facial importa no desligamento"
    Em Senior X, a remoção do *template* facial de quem sai da lista precisa ser
    feita por outro meio. Considere isso ao definir o processo de desligamento —
    ver [LGPD e dados biométricos](../anexos/lgpd.md).

A carga facial é o processamento mais pesado do driver: envolve baixar a foto e
enviá-la ao equipamento, pessoa a pessoa. Uma falha individual não interrompe as
demais.

## 3. Monitoramento de saúde da frota

O driver sonda periodicamente cada equipamento e reporta online/offline à Senior.

- Ciclo a cada 30 s por padrão, com sondas em paralelo
- **Anti-oscilação:** só reporta offline após 3 falhas consecutivas, evitando
  que uma perda momentânea de pacote vire alarme
- Equipamento já offline usa sonda rápida, para não travar o ciclo
- **Resync reconciliador** periódico, corrigindo divergências acumuladas

Os parâmetros estão em [Referência de parâmetros](../referencia/parametros.md).

## 4. Durabilidade

Nenhum evento de acesso é descartado em silêncio. Três camadas:

| Camada | Função |
|---|---|
| **Inbox de webhooks** | O evento bruto é gravado **antes** de o driver confirmar o recebimento. Se o processo morrer, ele é reprocessado no próximo start |
| **Banco local** | Todo evento processado fica registrado e pode ser recoletado pela Senior |
| **Fila de reenvio** | Notificações que falharam são reenviadas a cada 30 s; o que estoura os limites vai para dead-letter em vez de sumir |

!!! note "A fila de reenvio só existe em Senior X"
    O serviço é registrado apenas quando `thirdpart=seniorx`.

## O que o driver não faz

Vale ser explícito, porque estas são perguntas recorrentes:

- **Não libera acesso offline.** Ver acima.
- **Não emite alerta ativo.** O monitoramento do cliente consulta o driver; não
  há envio de e-mail ou push. Ver
  [Health check e monitoramento](../operacao/health-e-monitoramento.md).
- **Não é o cadastro de pessoas.** Esse vive na Senior.
- **Não roda em múltiplas instâncias particionando a frota.** Uma instalação
  atende a frota inteira.
