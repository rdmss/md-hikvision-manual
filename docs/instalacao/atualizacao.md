# Atualização

Há dois caminhos. O instalador completo é o recomendado; o pacote de
atualização existe para quando só o executável precisa mudar.

!!! success "Sua configuração é preservada nos dois caminhos"
    O `middleware.properties` só é escrito pelo instalador **se ainda não
    existir**. Em atualização, o arquivo do cliente permanece intacto — não é
    preciso reconfigurar chave, URL nem porta.

---

## Caminho 1 — Instalador completo (recomendado)

Execute o `Hikvision Driver-Setup-<versão>.exe` como administrador, sobre a
instalação existente. Ele sobrescreve a aplicação e preserva a configuração.

Informe o **mesmo nome de serviço** da instalação original. Um nome diferente
cria um segundo serviço apontando para o mesmo diretório, e os dois competem
pela mesma porta.

O `logging.json` **é substituído** de propósito: ele é tratado como parte da
aplicação, não como configuração do cliente. A versão 2.2.1 corrige uma lentidão
causada por log em nível Debug síncrono, e a correção depende do arquivo novo.

---

## Caminho 2 — Pacote de atualização (só o executável)

Substitui apenas o `HIK_Driver.exe`, sem passar pelo instalador.

!!! warning "Nunca apague estes arquivos ao atualizar"
    - `middleware.properties` — sua configuração
    - `logging.json` — configuração de log
    - `HIK.CER` — certificado, em Senior XT
    - a pasta `log\` e o banco de eventos

Com **Prompt de Comando como administrador**:

```bat
REM 1. Parar o serviço
sc stop HIKVISION-DRIVER

REM 2. Garantir que o processo encerrou
REM    ("processo não encontrado" é normal — ignore)
taskkill /F /IM HIK_Driver.exe /T

REM 3. Backup do executável atual
cd C:\HikvisionDriver
copy HIK_Driver.exe HIK_Driver.exe.bak

REM 4. Copiar o HIK_Driver.exe do pacote para C:\HikvisionDriver\
REM    (sobrescrevendo o existente)

REM 5. Subir o serviço
sc start HIKVISION-DRIVER
```

Se o nome do serviço não for o padrão:

```bat
sc queryex type=service | findstr /i hik
```

!!! note "Este caminho não atualiza o `logging.json`"
    Se a versão de destino depender de mudança no `logging.json` — como a 2.2.1
    depende — o pacote de executável **não** entrega a correção completa. Nesse
    caso, use o instalador completo.

---

## Depois de atualizar

1. Confirme o estado: `sc query HIKVISION-DRIVER` → `RUNNING`
2. Abra `/health` e verifique que responde `200`
3. Abra a [tela de diagnóstico](../operacao/diagnostico.md) e confirme a versão
   exibida e os dispositivos voltando a online
4. Faça uma passagem de teste em uma catraca e confirme que ela aparece nos
   eventos recentes

!!! tip "Se algo der errado"
    O backup do passo 3 permite voltar: pare o serviço, restaure o
    `HIK_Driver.exe.bak` por cima do `HIK_Driver.exe` e suba o serviço.

## Eventos durante a janela

Com o serviço parado, os equipamentos não conseguem entregar eventos. O evento
de uma passagem ocorrida durante a atualização **não é recuperado** — a validação
online já terá expirado do lado do equipamento. Prefira janelas de baixo
movimento.
