# Referência de parâmetros

Todas as configurações do driver vivem em **`middleware.properties`**, no
diretório de instalação (`C:\HikvisionDriver` por padrão). O formato é
`chave=valor`, uma por linha; linhas iniciadas por `#` são comentário.

A maior parte das chaves também é editável pela [tela de configuração](../instalacao/primeira-configuracao.md).
As exceções estão marcadas na coluna *Onde edita*.

!!! info "Esta tabela é verificada automaticamente"
    Um teste no CI compara esta página com as chaves realmente lidas pelo
    código-fonte e com o `middleware.properties.example`. Chave nova sem
    documentação — ou documentação de chave que não existe mais — reprova o
    build. Se você encontrou uma divergência aqui, é um defeito.

---

## Seleção de plataforma

| Chave | Obrigatória | Default | Onde edita | Descrição |
|---|:--:|---|---|---|
| `thirdpart` | ✓ | *(vazio)* | Tela + arquivo | Plataforma Senior em uso: `seniorx` ou `seniorxt`. Sem um valor válido o driver sobe, mas **não inicia a integração** — o log registra `Thirdpart '<valor>' nao suportado para inicializacao do middleware.` |

---

## Middleware / API local

Controlam a API HTTP do próprio driver — a que serve o painel de configuração, o
diagnóstico e o webhook que os equipamentos chamam.

| Chave | Obrigatória | Default | Onde edita | Descrição |
|---|:--:|---|---|---|
| `middleware.api.port` | ✓ | `5000` | Tela + arquivo | Porta TCP em que o driver escuta. É **esta** a porta que os equipamentos precisam alcançar para entregar eventos. |
| `middleware.api.user` | | `admin` | Tela + arquivo | Usuário do painel de configuração e do diagnóstico. |
| `middleware.api.password` | | *(vazio)* | Tela + arquivo | Senha do painel. **Vazio significa sem login.** Ver o aviso abaixo. |
| `middleware.api.allowlist` | | *(vazio)* | Tela + arquivo | Lista de IPs autorizados, separados por vírgula. Vazio libera qualquer origem. |

!!! tip "Defina a senha do painel na instalação"
    O painel só passa a exigir login depois que `middleware.api.password` é
    preenchida. Defina-a durante a configuração inicial e, quando possível,
    restrinja `middleware.api.allowlist` aos IPs de gestão — são as duas medidas
    que protegem o acesso à configuração da integração.

O webhook `/api/v1/isapi` e o `/health` permanecem acessíveis mesmo com senha
configurada, por desenho: o equipamento não faz login antes de entregar um
evento, e o monitoramento precisa ler o health sem credencial.

---

## Senior X (nuvem)

Obrigatórias quando `thirdpart=seniorx`.

| Chave | Obrigatória | Default | Onde edita | Descrição |
|---|:--:|---|---|---|
| `seniorx.api.url` | ✓ | `https://sam-api.senior.com.br/sdk/v1` | **Somente arquivo** | Endpoint REST da Senior. |
| `seniorx.websocket.url` | ✓ | `wss://sam-api.senior.com.br/websocket/pendency` | **Somente arquivo** | Canal WebSocket por onde chegam as pendências. |
| `seniorx.driver_key` | ✓ | *(vazio)* | Tela + arquivo | **Segredo.** Chave que identifica esta instalação na Senior. Sem ela o canal WebSocket não sobe: `Configuração ausente: 'seniorx.driver_key'. Cliente WS não será iniciado.` |

!!! note "Por que as duas URLs não aparecem na tela"
    `seniorx.api.url` e `seniorx.websocket.url` estão marcadas como propriedades
    ocultas no código e são filtradas do painel. Os defaults apontam para o
    ambiente de produção da Senior; para apontar a outro ambiente, edite o
    arquivo diretamente e reinicie o serviço.

---

## Senior XT (Concentradora CSM)

Obrigatórias quando `thirdpart=seniorxt`.

| Chave | Obrigatória | Default | Onde edita | Descrição |
|---|:--:|---|---|---|
| `seniorxt.server` | ✓ | *(vazio)* | Tela + arquivo | Endereço da Concentradora. |
| `seniorxt.port` | ✓ | *(vazio)* | Tela + arquivo | Porta TCP da Concentradora. |
| `seniorxt.csmservlet` | ✓ | *(vazio)* | Tela + arquivo | Servlet CSM usado na consulta de fotos. |
| `seniorxt.driver` | ✓ | *(vazio)* | Tela + arquivo | Identificador numérico do driver, conforme cadastrado na Senior. |
| `seniorxt.certificate` | ✓ | `HIK.CER` | Tela + arquivo | Nome do arquivo de certificado no diretório de instalação. Ver [Certificado](../senior-xt/certificado.md). |
| `seniorxt.ext.username` | | *(vazio)* | Tela + arquivo | Usuário do equipamento (opcional). |
| `seniorxt.ext.password` | | *(vazio)* | Tela + arquivo | Senha do usuário do equipamento (opcional). |
| `seniorxt.ext.server.address` | | *(vazio)* | Tela + arquivo | Endereço do middleware informado ao equipamento (opcional). |

!!! warning "O instalador não empacota o certificado"
    O `HIK.CER` foi removido do versionamento. Em deploys Senior XT o arquivo
    precisa ser colocado manualmente no diretório de instalação, senão a
    autenticação com a Concentradora falha.

---

## Ajuste fino

Nenhuma é obrigatória — todas têm default no código. Ficam **comentadas** no
`middleware.properties.example`: descomente apenas o que precisar sobrescrever.
Ajuste apenas quando o comportamento observado justificar.

### Validação de acesso

| Chave | Default | Descrição | Impacto |
|---|---|---|---|
| `seniorx.remotecheck.timeout` | `15` | Janela, em segundos, que o equipamento aguarda a resposta da validação. | Reduzir aumenta o risco de timeout em rede lenta; aumentar prende a catraca por mais tempo quando a Senior não responde. |

### Keepalive dos dispositivos

!!! note "Estas chaves valem para as duas plataformas"
    Apesar do prefixo `seniorx.`, o serviço de keepalive atende também
    instalações `thirdpart=seniorxt`.

| Chave | Default | Descrição | Impacto |
|---|---|---|---|
| `seniorx.keepalive.device.interval` | `30` | Segundos entre ciclos de sondagem. | Com centenas de dispositivos, `60` corta a carga pela metade. |
| `seniorx.keepalive.device.parallelism` | `32` | Sondas simultâneas por ciclo. | Frotas grandes: `32`–`64`. Valores altos geram rajadas de sockets. |
| `seniorx.keepalive.device.timeout` | `10` | Timeout, em segundos, por tentativa de sonda. | Reduzir para `5` acelera o ciclo quando há muitos dispositivos lentos ou offline. |
| `seniorx.keepalive.device.sendtimeout` | `30` | Timeout, em segundos, para enviar o status à Senior. | — |
| `seniorx.keepalive.device.retries` | `2` | Tentativas extras para dispositivo já ONLINE. | Evita falso offline. Dispositivo já OFFLINE usa sonda rápida de 1 tentativa, automaticamente. |
| `seniorx.keepalive.device.failthreshold` | `3` | Falhas consecutivas antes de reportar OFFLINE. | Anti-flapping. Reduzir acelera a detecção e aumenta o ruído. |
| `seniorx.keepalive.device.resync` | `300` | Segundos entre execuções do resync reconciliador. | — |
| `seniorx.keepalive.driver.interval` | `60` | Segundos entre keepalives do próprio driver com a Senior. | — |

### Fila de reenvio de eventos

!!! note "Somente Senior X"
    O serviço de reenvio é registrado apenas quando `thirdpart=seniorx`. Em
    Senior XT estas chaves não têm efeito.

| Chave | Default | Descrição | Impacto |
|---|---|---|---|
| `seniorx.eventretry.maxretries` | `2880` | Máximo de tentativas por evento. | A cada 30 s, `2880` equivale a cerca de 24 h. Estourado o limite, o evento vai para `events/deadletter/`. |
| `seniorx.eventretry.ttl.hours` | `72` | Validade do evento na fila, em horas. | Estourada, o evento vai para dead-letter. Ver [Fila de reenvio e dead-letter](../operacao/dead-letter.md). |

### Desempenho

| Chave | Default | Descrição | Impacto |
|---|---|---|---|
| `middleware.device.configure.parallelism` | `16` | Configurações de dispositivo executadas em paralelo. | Aumentar acelera o provisionamento inicial de frotas grandes ao custo de rajada de rede. |
| `middleware.threadpool.minthreads` | `max(64, núcleos × 8)` | Piso de threads do pool. | `0` usa o default do runtime. O valor é limitado a `1024`. Existe porque os handlers de pendência ainda bloqueiam threads: sem o piso, validações em rajada esperam pela rampa do runtime (~1 thread nova a cada 500 ms) quando cargas faciais rodam em paralelo. |

---

## Aplicando mudanças

Alterações feitas pela tela de configuração são gravadas no arquivo e o driver é
reiniciado. Alterações feitas direto no arquivo exigem **reiniciar o serviço**
manualmente — ver [Serviço Windows](../instalacao/servico-windows.md).

Ao salvar pela tela, campos obrigatórios em branco produzem a mensagem
`Preencha as propriedades obrigatorias: <lista>` e nada é gravado.
