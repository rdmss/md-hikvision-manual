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

### Preciso instalar o .NET na máquina?

Sim: o instalador requer o **ASP.NET Core Runtime 9.0 x64** e confere sua
presença antes de prosseguir. Deixe-o instalado antes de começar. Ver
[Pré-requisitos](../antes-de-instalar/pre-requisitos.md).

---

### Ao atualizar, preciso reconfigurar?

Não. O `middleware.properties` só é escrito se ainda não existir, então sua
configuração é preservada. Ver [Atualização](../instalacao/atualizacao.md).

---

### Desinstalar apaga minha configuração?

Sim — a desinstalação remove o diretório de instalação, com configuração,
certificado, logs e eventos ainda não entregues. Faça backup antes; o
procedimento está em [Desinstalação](../instalacao/desinstalacao.md).

---

### Como monitoro a integração em Senior XT?

Pelos indicadores operacionais: `offlineDevices` em `/diagnostic/data` e a
ocorrência de `Attempting SeniorXT reconnection` no log. Em Senior XT o escopo do
`/health` é o serviço do driver, então o alerta deve incluir esses dois sinais.
Ver [Health check e monitoramento](../operacao/health-e-monitoramento.md).

---

### Quando alguém é desligado, a face sai do equipamento?

Depende da plataforma. Em **Senior XT** a carga facial é incremental
reconciliada e remove quem saiu da lista. Em **Senior X** a carga é *upsert*:
insere e atualiza, e a remoção precisa ser feita por outro meio.

Considere isso ao definir o processo de desligamento — ver
[LGPD e dados biométricos](../anexos/lgpd.md).

---

### O driver avisa quando algo cai?

Não. Não há e-mail, push ou alerta ativo. O monitoramento do cliente precisa
consultar `/health` e os indicadores de `/diagnostic/data`.

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
