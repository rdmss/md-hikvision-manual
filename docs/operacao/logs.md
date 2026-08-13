# Logs

## Onde ficam

No diretório de instalação, um arquivo por dia:

```
C:\HikvisionDriver\log\log-AAAAMMDD.txt
```

Também dá para ler pela web, sem acesso ao disco da máquina:

```
GET http://<host>:5000/diagnostic/logs
```

## Como ler

As linhas trazem data e hora, nível e a mensagem. Trechos entre chaves nos
modelos de mensagem — `{DeviceId}`, `{PendencyId}` — aparecem preenchidos com
valores reais.

O caminho mais rápido para diagnosticar: pegue um trecho da mensagem e procure
em [Mensagens de log](../problemas/mensagens-de-log.md), que traz significado e
ação para cada uma.

### Níveis

| Nível | O que significa |
|---|---|
| `Information` | Curso normal — conexões, handshakes, pendências processadas |
| `Warning` | Algo fora do esperado, mas o driver seguiu — reconexões, configuração faltando |
| `Error` | Uma operação falhou. Costuma vir acompanhado da exceção |

Numa investigação, comece filtrando por `Error` e suba para `Warning` se não
achar nada.

### Métricas nas linhas de validação

As linhas de validação de acesso trazem tempos que separam responsabilidades:

| Campo | Alto significa |
|---|---|
| `senior=Xms` | O gargalo está na Senior ou no caminho até ela |
| `respostaEquipamento=Yms` | O gargalo está na rede local ou no equipamento |

É a forma mais direta de responder "a lentidão é nossa ou deles?".

## Configuração de log

O nível e os destinos ficam em `logging.json`, no diretório de instalação.

!!! warning "O `logging.json` é atualizado junto com o executável"
    Ele é tratado como parte da aplicação, não como configuração do cliente. A
    versão 2.2.1 corrige uma lentidão causada por log em nível Debug síncrono, e
    a correção depende do arquivo novo.

    Se você personalizar o `logging.json`, guarde uma cópia: o instalador o
    substitui.

!!! danger "Cuidado ao elevar o nível para Debug"
    Log em nível Debug foi a causa de lentidão corrigida na 2.2.1. Use
    temporariamente, para diagnosticar, e volte ao normal em seguida — não
    deixe ligado em produção.

## Segredos

Valores de `driver_key`, `partner_key`, senha, token e secret são **mascarados**
antes da gravação. Um log pode ser anexado a um chamado sem expor credenciais.

!!! note "Mascaramento cobre segredo, não dado pessoal"
    O log pode conter identificadores de pessoa e de dispositivo, além de
    horários de passagem. Confira antes de enviar para fora da organização, e
    trate o arquivo conforme a política de dados do cliente. Ver
    [LGPD](../anexos/lgpd.md).

## Retenção

Os arquivos se acumulam por dia no diretório `log\`. Não há expurgo automático
documentado — inclua a pasta na rotina de limpeza do servidor, considerando o
prazo de retenção definido pelo cliente.

!!! danger "A desinstalação apaga todo o histórico"
    A pasta `log\` vai junto com o diretório de instalação. Se o histórico
    importar, faça backup antes. Ver [Desinstalação](../instalacao/desinstalacao.md).
