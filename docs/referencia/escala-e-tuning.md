# Escala e tuning

Referência para dimensionar instalações com muitos dispositivos. Os defaults
atendem bem a instalações pequenas e médias; ajuste quando o comportamento
observado justificar, não preventivamente.

## O que muda o comportamento em escala

| Parâmetro | Default | Efeito ao aumentar | Efeito ao reduzir |
|---|---|---|---|
| `seniorx.keepalive.device.parallelism` | `32` | Ciclo mais rápido | Menos rajadas de socket |
| `seniorx.keepalive.device.interval` | `30` | Menos carga | Detecção mais rápida |
| `seniorx.keepalive.device.timeout` | `10` | Mais tolerante a rede lenta | Ciclo mais rápido com muitos dispositivos parados |
| `seniorx.keepalive.device.retries` | `2` | Menos falso offline | Detecção mais rápida |
| `seniorx.keepalive.device.failthreshold` | `3` | Menos oscilação | Detecção mais rápida, mais ruído |
| `middleware.device.configure.parallelism` | `16` | Provisionamento inicial mais rápido | Menos rajada de rede |
| `middleware.threadpool.minthreads` | `max(64, núcleos × 8)` | Absorve rajadas melhor | Menos memória de pilha |

Descrição completa de cada um em [Referência de parâmetros](parametros.md).

## Ordem de grandeza

### Validação de acesso

O custo dominante é a ida e volta até a Senior — rede do cliente, não do driver.
O driver acrescenta milissegundos: a conexão com o equipamento é reutilizada e o
log é assíncrono.

Catracas simultâneas processam em paralelo. **Não há fila única**, então mais
catracas não significam espera crescente.

### Keepalive

Duração aproximada do ciclo:

```
ciclo ≈ (nº de dispositivos ÷ parallelism) × custo da sonda
```

Com 200 dispositivos online, sonda de ~200 ms e `parallelism` 32, o ciclo leva
cerca de 1,3 s.

!!! warning "Queda em massa estica o ciclo"
    Dispositivo offline não responde — a sonda gasta o `timeout` inteiro. Numa
    queda generalizada:

    ```
    ciclo ≈ (nº offline ÷ parallelism) × timeout
    ```

    Com 200 dispositivos offline, `parallelism` 32 e `timeout` 10 s, o ciclo
    passa de ~1,3 s para mais de 60 s — e a detecção de **novas** quedas fica
    proporcionalmente mais lenta.

    Se isso importar na instalação, reduza `timeout` e/ou aumente `parallelism`.

### Pendências

Processadas em paralelo entre dispositivos e **serializadas por dispositivo**,
garantindo ordem. A carga facial é o item mais pesado: download e envio de fotos,
sequencial por pessoa.

### Eventos

O banco em modo WAL aguenta centenas de inserções por segundo. O gargalo prático
é o disco da máquina.

## Sinais de saturação

O que observar em `/diagnostic/data`:

| Sinal | Significa | Ação |
|---|---|---|
| `offlineDevices` alto e constante | Sondagem mal dimensionada, ou rede ruim | Reduza `timeout` e revise `retries` |
| `retryQueueSize` crescendo | Senior indisponível ou recusando | Ver [dead-letter](../operacao/dead-letter.md) |
| `deadLetterSize` crescendo | Rejeição permanente | Ação manual necessária |
| `inboxPending` acima de zero fora do boot | Webhooks atrasados | Investigue CPU e disco |
| `inboxAddFailures` acima de zero | Disco ou banco degradado | **Incidente** — a durabilidade parou de valer |

No log, nas linhas de validação:

- `senior=Xms` alto → gargalo na Senior
- `respostaEquipamento=Yms` alto → rede local ou equipamento

## Runtime

O *Server GC* vem habilitado. Em máquina muito pequena (2 vCPU, pouca RAM), se o
consumo de memória incomodar, defina a variável de ambiente `DOTNET_gcServer=0`
no serviço.

## Limitações conhecidas

!!! info "Estas são limitações de arquitetura, não configuração"
    - Os handlers de pendência usam chamadas bloqueantes. O piso do thread pool
      mitiga o efeito; a correção definitiva é assíncrona ponta a ponta.
    - A fila de mensagens do Senior XT é em memória e **não sobrevive a
      reinício**.
    - Instância única: não há particionamento de frota entre múltiplas
      instalações.
