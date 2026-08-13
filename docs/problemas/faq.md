# Perguntas frequentes

---

### O que acontece se a Senior cair? As catracas continuam liberando?

**Não. O acesso é negado.**

Os equipamentos operam em verificação remota: cada passagem é decidida pela
Senior. As credenciais gravadas no equipamento têm validade local desabilitada
de propósito, justamente para que horário, bloqueio e demais regras sejam sempre
os do servidor.

Liberação local durante quedas **não é configuração** — seria uma mudança de
produto. Ver [Queda da Senior](../operacao/queda-da-senior.md).

---

### Perco os eventos das passagens durante a queda?

Não. Eventos são gravados localmente e reenviados quando a conexão volta.

A exceção é a passagem que ocorre com o **driver parado**: aí o equipamento não
tem para quem entregar, e aquela validação específica não é recuperada.

---

### Instalei tudo, os dispositivos aparecem online, mas nenhuma passagem é validada.

Quase certamente o firewall libera só um sentido.

O driver alcançar o equipamento **não prova** que o equipamento alcança o driver.
A entrega de eventos usa o sentido **equipamento → driver**, na porta do driver
(5000 por padrão). Ver [Rede e portas](../antes-de-instalar/rede-e-portas.md).

---

### O driver precisa de .NET instalado na máquina?

A aplicação, não — ela é publicada *self-contained*.

O **instalador**, sim: ele verifica o ASP.NET Core Runtime 9.0 e não deixa
continuar sem ele. Na prática, instale o runtime antes, ainda que o driver não
vá utilizá-lo. Ver [Pré-requisitos](../antes-de-instalar/pre-requisitos.md).

---

### Ao atualizar, preciso reconfigurar?

Não. O `middleware.properties` só é escrito se ainda não existir, então sua
configuração é preservada. Ver [Atualização](../instalacao/atualizacao.md).

---

### Desinstalar apaga minha configuração?

**Sim.** O desinstalador remove o diretório inteiro, levando junto configuração,
certificado, logs e eventos não entregues. Faça backup antes. Ver
[Desinstalação](../instalacao/desinstalacao.md).

---

### O `/health` responde 200 em Senior XT, mas a integração está caída. Por quê?

Porque em Senior XT as verificações de API e WebSocket **passam
automaticamente** — elas dizem respeito ao Senior X. O `/health` acaba
verificando apenas se o processo subiu.

Em Senior XT, monitore `offlineDevices` e vigie o log por
`Attempting SeniorXT reconnection`. Ver
[Health check e monitoramento](../operacao/health-e-monitoramento.md).

---

### Quando alguém é desligado, a face sai do equipamento?

Depende da plataforma:

- **Senior XT:** sim — a carga é incremental reconciliada e remove quem saiu.
- **Senior X:** **não** — a carga é *upsert* e não remove.

Em Senior X, o *template* facial permanece até ser removido por outro meio. Tem
implicação de LGPD — ver [LGPD e dados biométricos](../anexos/lgpd.md).

---

### O driver avisa quando algo cai?

Não. Não há e-mail, push ou alerta ativo. O monitoramento do cliente precisa
consultar `/health` e os indicadores de `/diagnostic/data`.

---

### O que significa "Not implemented" no log?

A Senior enviou um tipo de pendência que este driver não trata. Não é erro de
configuração nem de rede — é funcionalidade ausente. Anote o tipo e acione o
suporte.

---

### `deadLetterSize` está acima de zero. Isso se resolve sozinho?

Não. Eventos em dead-letter ficam lá indefinidamente até alguém intervir.
Diagnostique a causa antes de reenviar. Ver
[Fila de reenvio e dead-letter](../operacao/dead-letter.md).

---

### Posso rodar duas instâncias na mesma máquina?

O instalador permite, com nomes de serviço diferentes — mas cada instância
precisa de porta e diretório próprios. Não há suporte a particionamento de frota
entre instâncias.

---

### Posso reaproveitar a mesma driver key em outro cliente?

Não. Ela identifica a instalação no Senior X e é segredo. Reaproveitar mistura
as integrações.

---

### O log expõe dados sensíveis?

Chave de integração, senha, token e secret são mascarados automaticamente. O log
pode conter identificadores de pessoa e dispositivo — confira antes de enviar
para fora da organização.

---

### Onde vejo a versão instalada?

No topo da [tela de diagnóstico](../operacao/diagnostico.md), ou no campo
`version` de `/diagnostic/data`.
