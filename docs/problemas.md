# Problemas

Encontre o sintoma na lista, siga a ação.

!!! tip "Um teste que economiza muito tempo"
    Antes de investigar qualquer coisa, faça uma passagem de teste numa catraca e
    veja se ela aparece na aba **Eventos** do painel de diagnóstico.

    - **Apareceu?** A rede está certa. O problema é de dados: permissão,
      credencial ou cadastro na Senior.
    - **Não apareceu?** O evento não está chegando. O problema é de rede ou da
      propriedade `driverAddress` — e mexer em cadastro não vai resolver.

    Esse único teste separa dois mundos e evita a maior parte das investigações
    em direção errada.

---

## Instalei tudo e nenhuma passagem é validada

O sintoma mais comum e o mais enganoso. Os dispositivos aparecem **online**, a
carga de cartões conclui, o painel fica todo verde — e nenhuma passagem é
validada.

A explicação quase sempre é a mesma: o driver consegue falar com a catraca, mas
a catraca não consegue falar com o driver. São caminhos independentes, e só o
segundo entrega eventos. O painel verde reflete apenas o primeiro.

Verifique nesta ordem:

| # | Verificação | Como |
|---|---|---|
| 1 | A propriedade **`driverAddress`** está preenchida no cadastro do dispositivo? | Se faltar, o log traz `Extensible Property driverAddress not found` |
| 2 | O endereço em `driverAddress` é o que **o equipamento** enxerga? | Não pode ser `localhost` nem IP de outra VLAN |
| 3 | O firewall libera **equipamento → driver** na porta 5000? | Teste a partir do equipamento, não do servidor |
| 4 | O IP do servidor mudou depois da configuração? | O endereço antigo continua gravado no equipamento |

O driver enxergar o equipamento **não prova** que o equipamento enxerga o driver.
São sentidos independentes, e só o segundo entrega eventos.

---

## A catraca nega todo mundo

| Causa provável | Como confirmar | Ação |
|---|---|---|
| Senior inacessível | `/health` responde `503`, ou erros de API no log | Restabeleça a conexão. **Não há liberação local** |
| `seniorx.driver_key` ausente | Log: `Configuração ausente: 'seniorx.driver_key'` | Preencha a chave |
| Configuração incompleta | Log: `propriedades obrigatorias faltando` | Preencha os campos listados |
| Pessoa sem credencial no equipamento | O evento aparece com negativa | Rode a carga de credenciais pela Senior |

---

## O painel não abre

| Causa provável | Como confirmar | Ação |
|---|---|---|
| Serviço parado | `sc query HIKVISION-DRIVER` → `STOPPED` | `sc start`; se não subir, veja o log |
| Porta ocupada | `netstat -ano \| findstr :5000` | Troque a porta ou libere-a |
| Bloqueado por allowlist | Seu IP não está em `middleware.api.allowlist` | Inclua seu IP ou limpe o campo |
| Firewall local | Abre na máquina, não pela rede | Libere a porta no Windows |

---

## Dispositivo sempre offline

| Causa provável | Ação |
|---|---|
| Equipamento inalcançável | Verifique energia, rede e se o ISAPI está habilitado |
| Credenciais erradas | Revise `username`/`password` extensíveis ou `seniorxt.ext.*` |
| Rede lenta derrubando a sonda | Aumente `seniorx.keepalive.device.timeout` e `.failthreshold` |
| Frota grande, detecção lenta | Aumente `.parallelism` e reduza `.timeout` |

---

## Carga facial falha

| Sintoma | Causa provável |
|---|---|
| Nenhuma pendência de carga chega | Tecnologia biométrica não declarada no cadastro (Senior XT) |
| Falha em **todas** as pessoas | Servlet CSM inacessível, ou equipamento sem suporte |
| Falha em pessoas isoladas | Foto ausente, formato recusado ou rosto não detectado — o log traz o motivo |

A carga é por pessoa: uma falha individual não interrompe as demais.

---

## Eventos não chegam à Senior

| Indicador em `/diagnostic/data` | Significa | Ação |
|---|---|---|
| `retryQueueSize` crescendo | Senior indisponível | Nenhuma — reenvio automático |
| `deadLetterSize` acima de zero | Rejeição permanente | **Manual** — ver abaixo |
| `inboxAddFailures` acima de zero | Problema de disco | Incidente de infraestrutura |

### Reprocessar dead-letter

A pasta `deadletter` guarda eventos que o driver desistiu de entregar — porque a
Senior recusou de forma definitiva, ou porque passaram do prazo de tentativas.
**Eles ficam lá para sempre até alguém agir.**

Diagnostique **antes** de reenviar. Se a Senior recusou por um cadastro que
falta, devolver o arquivo à fila só produz outra recusa. Descubra o motivo no
log, corrija a causa, e só então reenvie.

```bat
move C:\HikvisionDriver\events\deadletter\*.json C:\HikvisionDriver\events\
```

Mova um arquivo primeiro e confirme que foi aceito antes de mover o resto.

---

## O serviço não inicia

1. `sc qc HIKVISION-DRIVER` — o caminho do executável está correto?
2. Log mais recente — procure `Erro ao iniciar middleware`
3. `netstat -ano | findstr :5000` — a porta está livre?
4. O `middleware.properties` existe e é legível?

---

## Mensagens de log

Ficam em `C:\HikvisionDriver\log\log-AAAAMMDD.txt`. Use Ctrl+F com um trecho.

### Configuração e inicialização

| Mensagem | Ação |
|---|---|
| `Extensible Property driverAddress not found` | Preencha `driverAddress` no cadastro do dispositivo |
| `Erro ao iniciar middleware` | Leia a exceção abaixo; costuma ser configuração inválida ou porta ocupada |
| `Thirdpart selecionado nao sera iniciado porque ha propriedades obrigatorias faltando` | Preencha os campos listados |
| `Configuração ausente: 'seniorx.driver_key'. Cliente WS não será iniciado.` | Preencha a driver key |
| `ClientCSMCommunication nao foi registrado para o fluxo SeniorXT.` | Configuração Senior XT incompleta |

### Conexão com a plataforma

| Mensagem | Ação |
|---|---|
| `Attempting SeniorXT reconnection. Attempt {n}.` | Confira certificado, Driver ID, endereço e porta. Em XT é a principal evidência de queda |
| `Erro inesperado no cliente WS` | Verifique a saída `wss://` e proxy cortando o *upgrade* |
| `Erro GET {Endpoint}` / `Erro POST {Endpoint}` | Confirme a URL da API e a conectividade |
| `Error replying to IsAliveCommand.` | A Senior pode marcar o driver como indisponível; verifique a rede |

### Dispositivos

| Mensagem | Ação |
|---|---|
| `Erro no keepalive do device {id}` | Equipamento inalcançável |
| `ConfigureDevices failed for device {id}` | Credenciais do equipamento ou ISAPI desabilitado |
| `Erro na resposta do acesso para o dispositivo {id}: {resposta}` | O equipamento recusou a resposta; o motivo vem no texto |
| `Device {id} not found for pendency {p}` | Dispositivo não cadastrado ou não associado na Senior |

### Cargas e eventos

| Mensagem | Ação |
|---|---|
| `Carga facial \| ... ERRO {pessoa} — {motivo}` | O motivo diz: foto ausente, formato ou rosto não detectado |
| `<Tipo> \| Pendency: ... - Not implemented` | Tipo de pendência não tratado nesta versão; anote o tipo e acione o suporte |
| `Evento movido para dead-letter` | Exige ação manual |
| `Arquivo de retry invalido {arquivo}` | Se recorrente, investigue o disco |

!!! tip "Segredos são mascarados"
    Chave de integração, senha e token são substituídos antes da gravação. O log
    pode conter identificadores de pessoa — confira antes de enviar para fora.

---

## Perguntas frequentes

??? question "O que acontece se a Senior cair? As catracas continuam liberando?"
    Não. O acesso é negado. Os equipamentos operam em verificação remota: a
    decisão é sempre do servidor. Liberação local durante quedas não é
    configuração — seria mudança de produto. Combine isso com o cliente antes da
    instalação.

??? question "Perco os eventos das passagens durante a queda?"
    Não. São gravados localmente e reenviados quando a conexão volta. A exceção é
    a passagem que ocorre com o **driver parado**: aí o equipamento não tem para
    quem entregar.

??? question "Preciso instalar o .NET na máquina?"
    Sim. O instalador requer o ASP.NET Core Runtime 9.0 x64 e confere sua
    presença antes de prosseguir.

??? question "Ao atualizar, preciso reconfigurar?"
    Não. O `middleware.properties` só é escrito se ainda não existir.

??? question "Desinstalar apaga minha configuração?"
    Sim. A desinstalação remove o diretório inteiro, com configuração,
    certificado, logs e eventos não entregues. Faça backup antes.

??? question "Quando alguém é desligado, a face sai do equipamento?"
    Em **Senior XT**, sim: a carga é incremental reconciliada. Em **Senior X**,
    não: a carga é *upsert*, e a remoção precisa ser feita por outro meio.
    Preveja isso no processo de desligamento.

??? question "Como monitoro a integração em Senior XT?"
    Por `offlineDevices` em `/diagnostic/data` e pela presença de
    `Attempting SeniorXT reconnection` no log. Em XT o `/health` cobre o serviço
    do driver.

??? question "O driver avisa quando algo cai?"
    Não. Não há e-mail nem push. O monitoramento do cliente precisa consultar.

??? question "Posso reaproveitar a mesma driver key em outro cliente?"
    Não. Ela identifica a instalação no Senior X e é segredo.

??? question "Onde vejo a versão instalada?"
    No topo do painel de diagnóstico, ou no campo `version` de
    `/diagnostic/data`.

---

## Antes de abrir um chamado

Junte:

- Log do dia — `log\log-AAAAMMDD.txt`
- Saída de `/diagnostic/data`
- Versão do driver, plataforma e modelo do equipamento
- Horário aproximado da ocorrência
