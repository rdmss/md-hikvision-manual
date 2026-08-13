# Desinstalação

!!! danger "A desinstalação apaga o diretório inteiro"
    O último passo do desinstalador executa `rmdir /S /Q` sobre a pasta de
    instalação. Isso remove **tudo** que estiver lá dentro, inclusive:

    - `middleware.properties` — sua configuração, com a chave de integração
    - `HIK.CER` — o certificado, em deploys Senior XT
    - a pasta `log\` — todo o histórico de log
    - o banco de eventos e a fila `events\`, incluindo `events\deadletter\`

    O `middleware.properties` é marcado para não ser removido pelo
    desinstalador, mas a remoção da pasta acontece depois e leva o arquivo junto.
    **Na prática, não há preservação.**

    **Faça backup antes de desinstalar** se houver qualquer chance de reinstalar
    nesta máquina ou de precisar dos eventos ainda não entregues.

## Backup recomendado

Antes de iniciar a desinstalação, com o serviço ainda parado:

```bat
sc stop HIKVISION-DRIVER
mkdir C:\backup-hikvision
xcopy C:\HikvisionDriver\middleware.properties C:\backup-hikvision\ /Y
xcopy C:\HikvisionDriver\HIK.CER C:\backup-hikvision\ /Y
xcopy C:\HikvisionDriver\events C:\backup-hikvision\events\ /E /I /Y
xcopy C:\HikvisionDriver\log C:\backup-hikvision\log\ /E /I /Y
```

!!! warning "O backup contém segredo"
    O `middleware.properties` guarda a chave de integração com a Senior. Trate a
    pasta de backup como material sensível e apague-a quando não precisar mais.

## Verifique a fila antes

Eventos ainda não entregues à Senior somem com a pasta. Antes de desinstalar,
abra o diagnóstico e confirme que `retryQueueSize` e `deadLetterSize` estão em
zero. Ver [Fila de reenvio e dead-letter](../operacao/dead-letter.md).

## Como desinstalar

Use **Aplicativos e Recursos** do Windows, ou o desinstalador no diretório de
instalação. O processo executa, nesta ordem:

1. Para o serviço — `sc stop <nome do serviço>`
2. Aguarda 5 segundos
3. Encerra o processo à força — `taskkill /F /IM HIK_Driver.exe /T`
4. Remove o serviço — `sc delete <nome do serviço>`
5. **Apaga a pasta de instalação inteira** — `rmdir /S /Q`

!!! note "Se você trocou o nome do serviço na instalação"
    O desinstalador usa o nome informado naquela ocasião. Se ele não for
    removido, descubra o nome real e apague manualmente:

    ```bat
    sc queryex type=service | findstr /i hik
    sc delete <nome encontrado>
    ```

## Depois de desinstalar

Remova o cadastro do driver na plataforma Senior, senão a Senior continuará
esperando um driver que não existe mais e reportando os dispositivos como
indisponíveis.
