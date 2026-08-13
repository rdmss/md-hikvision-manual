# LGPD e dados biométricos

O driver trata **dado pessoal sensível**: a LGPD classifica biometria nessa
categoria, com exigências mais rigorosas que dado pessoal comum.

Esta página descreve **o que o produto tecnicamente faz** com esses dados. As
definições de base legal, finalidade e prazo de retenção são do controlador —
normalmente o cliente que opera o controle de acesso — e estão marcadas como
campo a preencher.

## Que dados passam pelo driver

| Dado | Origem | Para onde vai | Onde persiste |
|---|---|---|---|
| Número do cartão | Senior | Equipamento | Gravado no equipamento |
| Foto da pessoa | Senior | Equipamento | Trafega no driver; o equipamento guarda o *template* facial |
| Identificação da pessoa | Senior | Equipamento e eventos | Banco local de eventos |
| Evento de acesso | Equipamento | Senior | Banco local e fila de reenvio |

!!! note "O driver é intermediário, não repositório de cadastro"
    O cadastro de pessoas vive na Senior. O driver recebe o que precisa para
    provisionar o equipamento e para registrar a passagem. Ele não é a fonte
    primária desses dados.

## Onde os dados ficam em repouso

No diretório de instalação:

| Local | Conteúdo | Observação |
|---|---|---|
| Banco de eventos | Eventos de acesso processados | Contém identificação de pessoa e horário de passagem |
| `events\` | Eventos aguardando entrega à Senior | Idem |
| `events\deadletter\` | Eventos que desistiram de ser entregues | **Podem ficar indefinidamente** se ninguém agir |
| `log\` | Log diário | Pode conter identificadores; segredos são mascarados |
| Equipamento | Cartões e *templates* faciais | Fora do driver — vive no dispositivo |

!!! warning "Dead-letter é acúmulo silencioso de dado pessoal"
    Eventos parados em `events\deadletter\` não expiram sozinhos. Além do
    problema operacional, isso significa dado pessoal retido além do necessário.
    Inclua a verificação na rotina — ver
    [Fila de reenvio e dead-letter](../operacao/dead-letter.md).

## Proteções que o produto oferece

- **Mascaramento em log:** chave de integração, senha, token e secret são
  substituídos antes da gravação.
- **Autenticação do painel:** `middleware.api.user` e `middleware.api.password`.
- **Restrição por origem:** `middleware.api.allowlist`.
- **Transporte cifrado com a Senior X:** HTTPS e WebSocket seguro.

!!! warning "Configure a proteção do painel"
    Numa instalação que trata dado biométrico, definir `middleware.api.password`
    e restringir `middleware.api.allowlist` são medidas de segurança sobre dado
    sensível, não ajustes opcionais. Faça as duas na configuração inicial.

## Remoção de dados

| Situação | O que acontece |
|---|---|
| Pessoa desligada, Senior XT | Carga facial incremental **remove** quem saiu da lista |
| Pessoa desligada, Senior X | Carga facial é *upsert* — **não remove** quem saiu |
| Desinstalação do driver | O diretório inteiro é apagado, incluindo banco, filas e logs |

!!! warning "Em Senior X, planeje a remoção no desligamento"
    A carga facial no Senior X é *upsert*: ela insere e atualiza, mas a remoção
    do *template* de quem sai da lista precisa ser feita por outro meio.

    Em termos de LGPD, isso deve estar previsto no processo de desligamento do
    cliente. É diferença de comportamento entre plataformas.

## Campos a preencher pelo controlador

Itens que dependem da política do cliente e não podem ser afirmados aqui:

| Item | Quem define |
|---|---|
| Base legal para o tratamento de biometria | Controlador |
| Finalidade declarada | Controlador |
| Prazo de retenção dos eventos de acesso | Controlador |
| Procedimento de atendimento ao titular | Controlador |
| Encarregado (DPO) e canal de contato | Controlador |
| Registro das operações de tratamento | Controlador |
