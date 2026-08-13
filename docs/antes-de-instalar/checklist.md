# Checklist

Duas listas: uma para conferir **antes** de instalar, outra para fechar a
**homologação** com a Senior. Ambas feitas para serem impressas ou copiadas para
o chamado.

---

## Antes de instalar

### Máquina

- [ ] Windows x64, com acesso de administrador
- [ ] **ASP.NET Core Runtime 9.0 x64 instalado** — o instalador barra sem ele,
      ainda que o driver não use
- [ ] Espaço em disco para log, banco de eventos e fila
- [ ] IP fixo, ou reserva de DHCP vinculada ao MAC

### Rede

- [ ] Driver alcança os equipamentos na porta ISAPI
- [ ] **Cada equipamento alcança o driver** na porta do driver (5000 por padrão)
- [ ] Porta do driver liberada no firewall do Windows
- [ ] Senior X: saída para `sam-api.senior.com.br` em 443, HTTPS **e WebSocket**
- [ ] Senior XT: driver alcança a Concentradora na porta configurada
- [ ] Senior XT: driver alcança o servlet CSM

!!! danger "O item que mais falha em campo"
    O sentido **equipamento → driver**. Sem ele, tudo parece funcionar e nenhuma
    passagem é validada.

### Plataforma Senior

=== "Senior X"

    - [ ] Driver key emitida para **esta** instalação
    - [ ] Driver cadastrado na plataforma
    - [ ] Dispositivos cadastrados e associados

=== "Senior XT"

    - [ ] Endereço e porta da Concentradora
    - [ ] Endereço do servlet CSM
    - [ ] Driver ID numérico
    - [ ] **Arquivo `HIK.CER` em mãos** — o instalador não o empacota
    - [ ] Driver, catálogo e tecnologia biométrica cadastrados
    - [ ] Dispositivos e leitoras cadastrados

### Equipamentos

- [ ] Modelos Hikvision linha DS-K
- [ ] ISAPI habilitado
- [ ] Credenciais de acesso em mãos
- [ ] Firmware verificado

---

## Homologação

Roteiro para demonstrar que a integração funciona ponta a ponta.

### Instalação

- [ ] Serviço criado e em `RUNNING`
- [ ] Nome do serviço registrado na documentação do cliente
- [ ] Senha do painel definida — **não deixar em branco**
- [ ] `middleware.api.allowlist` restrita aos IPs de gestão

### Integração

- [ ] `/health` responde `200`
- [ ] Painel mostra a plataforma correta
- [ ] Teste de conectividade com a Senior passa
- [ ] Teste de conectividade com cada dispositivo passa
- [ ] Todos os dispositivos aparecem **online**

!!! warning "Senior XT: o `/health` não prova integração"
    Nele os checks de plataforma passam automaticamente. Confirme pela lista de
    dispositivos e pela ausência de `Attempting SeniorXT reconnection` no log.

### Funcional

- [ ] Carga de cartões concluída, com passagem por cartão liberada
- [ ] Carga facial concluída, com passagem por face liberada
- [ ] Passagem **negada** para pessoa sem permissão
- [ ] Evento de cada teste visível em `/diagnostic/events`
- [ ] Evento de cada teste visível **na Senior**

### Contingência

- [ ] Com a Senior indisponível, o acesso é negado — comportamento esperado e
      acordado com o cliente
- [ ] Eventos ocorridos durante a queda são reenviados ao restabelecer
- [ ] Reinício do serviço não perde evento pendente

### Operação

- [ ] Monitoramento configurado sobre `/health` (Senior X) ou sobre
      `offlineDevices` e o log (Senior XT)
- [ ] `deadLetterSize` incluído no monitoramento
- [ ] Equipe do cliente sabe onde ficam os logs
- [ ] Procedimento de atualização combinado
- [ ] **Cliente ciente de que não há liberação local durante quedas**

### Conformidade

- [ ] Cliente ciente de que, em Senior X, a face de quem sai da lista permanece
      no equipamento
- [ ] Prazo de retenção de eventos definido pelo cliente
- [ ] Backup do `middleware.properties` guardado como material sensível
