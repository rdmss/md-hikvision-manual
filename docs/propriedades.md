# Propriedades

Duas coisas diferentes se chamam "propriedade" nesta integração, e confundi-las
é fonte comum de erro:

| Onde vive | O que é | Vale para |
|---|---|---|
| **`middleware.properties`**, no servidor | Configuração do driver | A instalação inteira |
| **Propriedades extensíveis**, no cadastro da Senior | Configuração de cada equipamento | Um dispositivo |

---

## Propriedades extensíveis

Ficam na configuração extensível de **cada dispositivo**, no cadastro da Senior.
O driver as lê ao provisionar o equipamento.

| Propriedade | Obrigatória | Plataforma | O que faz |
|---|:--:|---|---|
| `driverAddress` | **✓** | X e XT | Endereço do driver que será gravado no equipamento, para onde ele envia os eventos de acesso |
| `model` | | X e XT | Modelo do equipamento; aparece no painel de diagnóstico |
| `username` | | XT | Usuário daquele equipamento; sobrepõe a configuração global |
| `password` | | XT | Senha daquele equipamento; sobrepõe a configuração global |

### `driverAddress`

!!! danger "Sem ela, o equipamento nunca é configurado"
    O provisionamento falha com `Extensible Property driverAddress not found` e
    o equipamento não passa a reportar passagens. É a causa de "instalei tudo e
    nada acontece" que sobrevive mesmo com o firewall correto.

Formato: `http://<endereço-do-driver>:<porta>`, por exemplo
`http://10.20.0.5:5000`.

Regras que costumam ser violadas:

- Use o endereço que **o equipamento** enxerga, não o que você enxerga. Nunca
  `localhost`.
- Se driver e equipamento estão em redes diferentes, use o endereço roteável
  entre elas.
- A porta tem de bater com `middleware.api.port`.
- O IP precisa ser fixo: ele fica gravado no equipamento, e se mudar os eventos
  param de chegar.

### `username` e `password`

Em Senior XT, quando presentes, valem para aquele dispositivo. Ausentes, o driver
usa `seniorxt.ext.username` e `seniorxt.ext.password` da configuração global.
Útil quando a frota tem credenciais diferentes.

---

## Configuração do driver

Fica em `middleware.properties`, no diretório de instalação, no formato
`chave=valor`. A maior parte é editável pela tela `/config`.

!!! info "Esta tabela é verificada automaticamente"
    Um teste na esteira compara esta página com as chaves realmente lidas pelo
    código. Chave nova sem documentação, ou documentação de chave inexistente,
    reprova o build.

### Essenciais

| Chave | Obrig. | Default | O que faz |
|---|:--:|---|---|
| `thirdpart` | ✓ | *(vazio)* | Plataforma: `seniorx` ou `seniorxt`. Sem valor válido o driver sobe sem integração |
| `middleware.api.port` | ✓ | `5000` | Porta em que o driver escuta. **É a que os equipamentos precisam alcançar** |
| `middleware.api.user` | | `admin` | Usuário do painel |
| `middleware.api.password` | | *(vazio)* | Senha do painel. Vazia = sem login |
| `middleware.api.allowlist` | | *(vazio)* | IPs autorizados, separados por vírgula. Vazio = todos |

### Senior X

| Chave | Obrig. | Default | O que faz |
|---|:--:|---|---|
| `seniorx.driver_key` | ✓ | *(vazio)* | **Segredo.** Identifica esta instalação. Sem ela o canal de pendências não sobe |
| `seniorx.api.url` | ✓ | `https://sam-api.senior.com.br/sdk/v1` | Endpoint REST. Editável só pelo arquivo |
| `seniorx.websocket.url` | ✓ | `wss://sam-api.senior.com.br/websocket/pendency` | Canal de pendências. Editável só pelo arquivo |

!!! note "As duas URLs não aparecem na tela"
    São filtradas do painel e apontam para a produção da Senior. Para outro
    ambiente, edite o arquivo e reinicie o serviço.

### Senior XT

| Chave | Obrig. | Default | O que faz |
|---|:--:|---|---|
| `seniorxt.server` | ✓ | *(vazio)* | Endereço da Concentradora |
| `seniorxt.port` | ✓ | *(vazio)* | Porta TCP da Concentradora |
| `seniorxt.csmservlet` | ✓ | *(vazio)* | Servlet CSM, usado na consulta de fotos |
| `seniorxt.driver` | ✓ | *(vazio)* | Driver ID, o mesmo cadastrado na Senior |
| `seniorxt.certificate` | ✓ | `HIK.CER` | Nome do arquivo de certificado no diretório de instalação |
| `seniorxt.ext.username` | | *(vazio)* | Usuário dos equipamentos, global |
| `seniorxt.ext.password` | | *(vazio)* | Senha dos equipamentos, global |
| `seniorxt.ext.server.address` | | *(vazio)* | Endereço do middleware informado ao equipamento |

### Ajuste fino

Nenhuma é obrigatória. Ficam comentadas no `middleware.properties.example` —
descomente só o que precisar sobrescrever. Ajuste apenas quando o comportamento
observado justificar.

| Chave | Default | O que faz |
|---|---|---|
| `seniorx.remotecheck.timeout` | `15` | Segundos que o equipamento aguarda a resposta da validação |
| `seniorx.keepalive.device.interval` | `30` | Segundos entre ciclos de sondagem dos dispositivos |
| `seniorx.keepalive.device.parallelism` | `32` | Sondas simultâneas por ciclo |
| `seniorx.keepalive.device.timeout` | `10` | Segundos por tentativa de sonda |
| `seniorx.keepalive.device.sendtimeout` | `30` | Segundos para enviar o status à Senior |
| `seniorx.keepalive.device.retries` | `2` | Tentativas extras para dispositivo online; evita falso offline |
| `seniorx.keepalive.device.failthreshold` | `3` | Falhas consecutivas antes de reportar offline |
| `seniorx.keepalive.device.resync` | `300` | Segundos entre execuções do resync |
| `seniorx.keepalive.driver.interval` | `60` | Segundos entre keepalives do driver com a Senior |
| `seniorx.eventretry.maxretries` | `2880` | Tentativas por evento na fila de reenvio. Só Senior X |
| `seniorx.eventretry.ttl.hours` | `72` | Validade do evento na fila. Só Senior X |
| `middleware.device.configure.parallelism` | `16` | Dispositivos configurados em paralelo |
| `middleware.threadpool.minthreads` | `max(64, núcleos × 8)` | Piso de threads. `0` usa o default do runtime |

!!! note "O prefixo `seniorx.` do keepalive não restringe a plataforma"
    As chaves de keepalive atendem também instalações `thirdpart=seniorxt`.

---

## Aplicando mudanças

| Como alterou | Reinício |
|---|---|
| Pela tela `/config` | Automático |
| Editando o arquivo | **Manual:** `sc stop` e `sc start` |
| Trocando o `HIK.CER` | **Manual** |
