# Fila de reenvio e dead-letter

Quando o driver não consegue entregar um evento à Senior, ele guarda em vez de
descartar. Este capítulo explica as duas pastas envolvidas e o que fazer quando
a segunda começa a encher.

!!! info "Específico do Senior X"
    Esta mecânica e seus parâmetros se aplicam a instalações `thirdpart=seniorx`.

## As duas pastas

Relativas ao diretório de instalação:

| Pasta | O que contém | Ação necessária |
|---|---|---|
| `events\` | Eventos aguardando reenvio | Nenhuma — o driver reenvia sozinho a cada 30 s |
| `events\deadletter\` | Eventos que desistiram | **Manual** — nada acontece sem intervenção |

## Como um evento vai parar em dead-letter

Dois caminhos:

1. **Rejeição permanente.** A Senior respondeu com erro do tipo "não tente de
   novo" (HTTP 4xx). Reenviar o mesmo conteúdo daria o mesmo resultado.
2. **Estouro de limite.** O evento excedeu `seniorx.eventretry.maxretries`
   (2880 tentativas, cerca de 24 h) ou `seniorx.eventretry.ttl.hours` (72 h).

No log: `Evento movido para dead-letter`.

## Como monitorar

Em `/diagnostic/data`:

| Indicador | Leitura |
|---|---|
| `retryQueueSize` | Cresce e depois esvazia → indisponibilidade temporária, normal |
| `retryQueueSize` | Cresce e não esvazia → a Senior segue fora, ou está recusando |
| `deadLetterSize` | **Qualquer valor acima de zero exige alguém olhar** |

!!! warning "Dead-letter não se resolve sozinho"
    Um evento em dead-letter fica lá indefinidamente. Se ninguém agir, aquele
    registro de acesso nunca chega à Senior. Inclua `deadLetterSize` no
    monitoramento — ver [Health check e monitoramento](health-e-monitoramento.md).

## Como reprocessar

Diagnostique **antes** de reenviar: se a causa for rejeição permanente, devolver
o arquivo à fila só produz outra rodada de falhas.

1. **Descubra o motivo.** Procure no log a linha que moveu o evento para
   dead-letter e veja a resposta da Senior.
2. **Corrija a causa.** Cadastro faltando na Senior, dispositivo não associado,
   pessoa inexistente — o que a rejeição apontar.
3. **Devolva os arquivos à fila.** Com o serviço rodando:

   ```bat
   move C:\HikvisionDriver\events\deadletter\*.json C:\HikvisionDriver\events\
   ```

4. **Acompanhe.** Em até 30 s o driver tenta de novo. Confirme que
   `deadLetterSize` não voltou a subir.

!!! tip "Faça em lote pequeno primeiro"
    Com muitos arquivos, mova um só e confirme que ele foi aceito antes de mover
    o resto. Evita repetir um erro em massa.

## Arquivos inválidos

Se um arquivo da fila estiver corrompido, o driver o põe de lado com a extensão
`.invalid` e registra:

```
Arquivo de retry invalido {File}; movendo para .invalid
```

Um caso isolado é tolerável. Recorrência indica problema no disco — investigue
junto com `inboxAddFailures`.

## Antes de desinstalar ou migrar

Estas pastas **são apagadas** na desinstalação, junto com todo o diretório.
Confirme que `retryQueueSize` e `deadLetterSize` estão em zero, ou faça backup.
Ver [Desinstalação](../instalacao/desinstalacao.md).
