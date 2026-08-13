# Guia Senior X

Do zero até a catraca funcionando. Siga na ordem — cada etapa depende da
anterior.

---

## Etapa 1 — Antes de começar

### Tenha em mãos

- [ ] **Driver key** emitida pela Senior para esta instalação
- [ ] Acesso administrador ao servidor Windows
- [ ] IP fixo (ou reserva de DHCP) para o servidor do driver
- [ ] Credenciais dos equipamentos Hikvision, se exigirem
- [ ] **ASP.NET Core Runtime 9.0 x64** instalado — o instalador confere e não
      avança sem ele

### Libere a rede

| Origem | Destino | Porta | Para quê |
|---|---|---|---|
| **Equipamento** | **Driver** | **5000** | **Entrega dos eventos de acesso** |
| Driver | Equipamento | 80 / 443 | Configuração, credenciais, sondagem |
| Driver | `sam-api.senior.com.br` | 443 | API REST e WebSocket |

!!! danger "Confirme o sentido equipamento → driver"
    É o que mais falha. Teste do equipamento, não só do servidor. Se o
    equipamento tiver navegador, abra `http://<ip-do-driver>:5000/health` a
    partir dele.

!!! note "Proxy que corta WebSocket"
    O canal de pendências usa `wss://`. Proxies que inspecionam HTTP às vezes
    derrubam o *upgrade*. O sintoma é a API funcionar e as pendências nunca
    chegarem.

---

## Etapa 2 — Instalar o driver

Execute `Hikvision Driver-Setup-2.2.1.exe` **como administrador**.

O assistente tem quatro telas:

**1. Boas-vindas** — identifica o produto (Hikvision Driver, versão 2.2.1).
Clique em Avançar.

**2. Configuração Personalizada** — pede o **Nome do Serviço**, com o padrão
`HIKVISION-DRIVER`. É o nome usado em `sc start` e `sc stop`. Mantenha o padrão,
salvo se houver mais de uma instância na mesma máquina. Campo obrigatório.

**3. Diretório de destino** — padrão `C:\HikvisionDriver`.

**4. Pronto para instalar** — o instalador confere o ASP.NET Core Runtime 9.0.
Se não encontrar, informa o endereço de download e oferece Repetir; instale o
runtime em outra janela e clique em Repetir.

Ao confirmar, o instalador copia os arquivos, cria o serviço com início
automático e o inicia. Sua configuração anterior, se houver, é preservada.

Confirme:

```bat
sc query HIKVISION-DRIVER
```

Esperado: `RUNNING`.

---

## Etapa 3 — Configurar o driver

Abra no navegador:

```
http://<ip-do-servidor>:5000/config
```

![Tela de configuração do driver](assets/telas/config.jpg){ loading=lazy }

Preencha:

| Campo | Valor |
|---|---|
| **Terceiro** | `SeniorX` |
| **Porta da API** | `5000` |
| **Usuario do Painel** | `admin` |
| **Senha do Painel** | defina uma senha |
| **IPs permitidos** | IPs de gestão, ou deixe vazio |
| **Driver Key** | a chave emitida pela Senior |

!!! tip "Defina a senha do painel agora"
    O painel só passa a exigir login depois que a senha é preenchida. Como esta
    tela altera a configuração da integração, defina-a nesta primeira passagem.

Clique em **Salvar**. Se faltar campo obrigatório, aparece
`Preencha as propriedades obrigatorias:` com a lista, e nada é gravado.

Gravando com sucesso:

![Confirmação de driver configurado](assets/telas/config-salvo.jpg){ loading=lazy }

O driver reinicia sozinho.

---

## Etapa 4 — Cadastrar na Senior X

É aqui que a integração se completa. O driver já está no ar, mas ainda não
conhece nenhum equipamento.

!!! warning "Sequência de telas pendente de captura"
    As telas do Senior X ainda não foram capturadas nesta documentação. Os
    objetos e propriedades abaixo, porém, são os que o driver realmente lê — e
    são o que precisa existir do lado da Senior.

### O que precisa existir

| Objeto | Para quê |
|---|---|
| **Driver** | Representa esta instalação; emite a *driver key* |
| **Dispositivo** | Cada controladora Hikvision |
| **Associação dispositivo ↔ driver** | Diz à Senior por qual driver falar com cada equipamento |
| **Leitoras** | Os pontos de leitura de cada dispositivo |
| **Permissões de acesso** | Quem pode passar, onde e quando |

### Propriedades extensíveis do dispositivo

No cadastro de cada dispositivo, na configuração extensível, informe:

| Propriedade | Obrigatória | Valor |
|---|:--:|---|
| `driverAddress` | **✓** | `http://<ip-do-driver>:5000` — o endereço que o equipamento vai usar |
| `model` | | Modelo do equipamento, ex.: `DS-K1T671M` |

!!! danger "`driverAddress` é o que faz a integração funcionar"
    O driver usa essa propriedade para configurar o equipamento a enviar os
    eventos de acesso. **Sem ela o provisionamento falha** com
    `Extensible Property driverAddress not found`, e o equipamento nunca passa a
    reportar passagens.

    Use o endereço que o **equipamento** enxerga. Se driver e equipamento
    estiverem em redes distintas, é o endereço roteável entre elas — não
    `localhost`, não o IP interno de outra VLAN.

!!! warning "IP fixo é requisito, não recomendação"
    Esse endereço fica gravado no equipamento. Se o IP do servidor mudar, os
    equipamentos continuam apontando para o antigo e os eventos param de chegar.

### Fotos e reconhecimento facial

Para carga facial, habilite o uso de fotos na plataforma e garanta que as
pessoas tenham foto cadastrada.

!!! warning "Em Senior X, planeje a remoção no desligamento"
    A carga facial no Senior X é *upsert*: insere e atualiza, e a remoção do
    *template* de quem sai da lista precisa ser feita por outro meio. Preveja
    isso no processo de desligamento — é exigência de LGPD sobre dado
    biométrico.

---

## Etapa 5 — Validar

Na ordem. Só o passo 5 prova a integração inteira.

**1. O serviço está no ar**

```bat
sc query HIKVISION-DRIVER
```

**2. O health responde**

```bat
curl -i http://localhost:5000/health
```

Esperado `200`. Um `503` indica driver, API ou WebSocket fora.

**3. O painel mostra o esperado**

Abra `http://<ip>:5000/diagnostic`:

![Painel de diagnóstico](assets/telas/diagnostico-devices.jpg){ loading=lazy }

- [ ] Plataforma `seniorx`, `WebSocket` verde
- [ ] Número de dispositivos bate com o cadastrado
- [ ] Nenhum offline

**4. Os testes de conectividade passam**

Botão **Testar** em cada dispositivo, e o teste com a Senior.

**5. Uma passagem de teste chega**

Passe um cartão ou face numa catraca, com pessoa autorizada. Confirme que
liberou e que o evento aparece na aba **Eventos**:

![Aba de eventos](assets/telas/diagnostico-eventos.jpg){ loading=lazy }

!!! danger "Se liberou mas não apareceu"
    O evento não está chegando ao driver. Revise o sentido
    **equipamento → driver** no firewall e a propriedade `driverAddress`.

**6. A negativa também funciona**

Passe alguém **sem** permissão e confirme que nega. Uma integração que libera
todo mundo não está validando.

---

## Etapa 6 — Deixar operando

### Monitoramento

O driver **não emite alerta** — o monitoramento do cliente precisa consultar.

| O que | Onde | Alertar quando |
|---|---|---|
| Saúde geral | `GET /health` | Código diferente de `200` |
| Dispositivos fora | `/diagnostic/data` → `offlineDevices` | Alto e constante |
| Eventos represados | `retryQueueSize` | Crescendo sem esvaziar |
| Eventos desistidos | `deadLetterSize` | **Qualquer valor acima de zero** |

Exemplo de gatilho no Zabbix, sobre um item *HTTP agent* apontando para
`http://{HOST.CONN}:5000/health`:

```
last(/HikvisionDriver/hik.health.status)<>200
```

### Logs

Ficam em `C:\HikvisionDriver\log\log-AAAAMMDD.txt`, e também na aba **Logs** do
painel:

![Aba de logs](assets/telas/diagnostico-logs.jpg){ loading=lazy }

Segredos são mascarados automaticamente.

### Eventos represados

Eventos que a Senior não aceitou vão para `events\deadletter\` e **exigem ação
manual**. Diagnostique a causa, corrija, e devolva à fila:

```bat
move C:\HikvisionDriver\events\deadletter\*.json C:\HikvisionDriver\events\
```

### Queda da Senior

O driver reconecta sozinho. Durante a queda, **o acesso é negado** — não há
liberação local. Os eventos ocorridos são guardados e reenviados quando a
conexão volta.

---

## Atualizar e desinstalar

**Atualizar:** rode o instalador novo por cima, com o mesmo nome de serviço. A
configuração é preservada.

**Desinstalar:** remove o diretório de instalação inteiro, com configuração,
logs e eventos não entregues. Faça backup antes:

```bat
sc stop HIKVISION-DRIVER
xcopy C:\HikvisionDriver\middleware.properties C:\backup-hikvision\ /Y
xcopy C:\HikvisionDriver\events C:\backup-hikvision\events\ /E /I /Y
```

---

Problemas? Vá para **[Problemas](problemas.md)**.
Detalhe de cada parâmetro em **[Propriedades](propriedades.md)**.
