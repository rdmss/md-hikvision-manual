# Desinstalação

A desinstalação remove a aplicação **e o diretório de instalação**, com tudo o
que estiver nele:

- `middleware.properties` — a configuração, com a chave de integração
- `HIK.CER` — o certificado, em deploys Senior XT
- a pasta `log\` — o histórico de log
- o banco de eventos e a fila `events\`, incluindo `events\deadletter\`

!!! warning "Faça backup antes"
    Se houver qualquer chance de reinstalar nesta máquina, ou se ainda existirem
    eventos não entregues à Senior, copie esses arquivos antes de iniciar. O
    procedimento está logo abaixo.

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
5. Remove a pasta de instalação

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
