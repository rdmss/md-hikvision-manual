# Serviço Windows

O driver roda como serviço com início automático. O nome padrão é
`HIKVISION-DRIVER`, definido na [tela 2 do instalador](instalador.md) — se você
alterou lá, use o seu nome nos comandos abaixo.

Todos os comandos exigem **Prompt de Comando como administrador**.

## Comandos do dia a dia

```bat
REM Estado atual
sc query HIKVISION-DRIVER

REM Iniciar
sc start HIKVISION-DRIVER

REM Parar
sc stop HIKVISION-DRIVER
```

Reiniciar é parar e iniciar. Não há comando único:

```bat
sc stop HIKVISION-DRIVER
timeout /t 5 /nobreak > nul
sc start HIKVISION-DRIVER
```

!!! tip "Esqueceu o nome do serviço"
    ```bat
    sc queryex type=service | findstr /i hik
    ```

## Quando reiniciar é necessário

| Mudança | Reinício |
|---|---|
| Configuração salva pela tela `/config` | Automático — o driver se reinicia |
| Edição manual do `middleware.properties` | **Manual** |
| Substituição do executável (atualização) | **Manual** |
| Troca do `HIK.CER` | **Manual** |

## Interpretando o estado

`sc query` devolve o campo `STATE`:

| Estado | Significa |
|---|---|
| `RUNNING` | No ar |
| `STOPPED` | Parado — ver o log para saber se foi falha ou parada intencional |
| `START_PENDING` | Subindo. A inicialização da integração é assíncrona: o serviço fica `RUNNING` antes de a conexão com a Senior estar pronta |

!!! warning "`RUNNING` não significa integrado"
    O serviço sobe mesmo com configuração inválida ou ausente. Quando faltam
    propriedades obrigatórias, o driver registra no log:

    > Thirdpart selecionado nao sera iniciado porque ha propriedades obrigatorias faltando: `<lista>`

    e segue rodando **sem integração**. Para saber se está realmente
    funcionando, use [`/health`](../operacao/health-e-monitoramento.md) e a
    [tela de diagnóstico](../operacao/diagnostico.md) — não o `sc query`.

## Onde ficam os arquivos

Relativos ao diretório de instalação (`C:\HikvisionDriver` por padrão):

| Caminho | O que é |
|---|---|
| `HIK_Driver.exe` | O executável |
| `middleware.properties` | Sua configuração — **contém segredo** |
| `logging.json` | Configuração de log; atualizada junto com o executável |
| `HIK.CER` | Certificado, em deploys Senior XT |
| `log\log-AAAAMMDD.txt` | Log diário |
| `events\` | Fila de reenvio |
| `events\deadletter\` | Eventos que desistiram de ser entregues |

## Se o serviço não sobe

1. Confirme que o executável existe no caminho registrado:
   `sc qc HIKVISION-DRIVER`
2. Abra o log mais recente em `log\` e procure por `Erro ao iniciar middleware`.
3. Verifique se a porta já está ocupada por outro processo:
   `netstat -ano | findstr :5000`

Ver [Solução de problemas](../problemas/sintomas.md).
