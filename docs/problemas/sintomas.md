# Sintoma, causa e ação

Comece por aqui. Encontre o sintoma, siga a ação. Se precisar do texto exato de
uma linha de log, vá para [Mensagens de log](mensagens-de-log.md).

---

## A catraca nega todo mundo

| Causa provável | Como confirmar | Ação |
|---|---|---|
| A Senior está inacessível | `/health` responde `503`, ou o log mostra erro de API/WebSocket | Restabeleça a conexão. **Não há liberação local** — ver [Queda da Senior](../operacao/queda-da-senior.md) |
| `seniorx.driver_key` ausente | Log: `Configuração ausente: 'seniorx.driver_key'` | Preencha a chave e reinicie |
| Configuração incompleta | Log: `Thirdpart selecionado nao sera iniciado porque ha propriedades obrigatorias faltando` | Preencha os campos listados |
| A pessoa não está na lista do equipamento | O evento aparece nos eventos recentes com negativa | Rode a carga de credenciais pela Senior |

---

## Nada acontece — nenhum evento chega

Este é o sintoma mais enganoso: os dispositivos aparecem **online**, a carga de
cartões conclui, e mesmo assim nenhuma passagem é validada.

| Causa provável | Como confirmar | Ação |
|---|---|---|
| **O equipamento não alcança o driver** | `/diagnostic/events` fica vazio após uma passagem de teste | Libere o sentido **equipamento → driver** na porta do driver. É a causa mais comum. Ver [Rede e portas](../antes-de-instalar/rede-e-portas.md) |
| O IP do driver mudou | Os equipamentos apontam para o endereço antigo | Fixe o IP e reconfigure os dispositivos |
| A porta foi alterada sem ajustar o firewall | `middleware.api.port` diferente de 5000 | Alinhe firewall e cadastro dos equipamentos |

O driver enxergar o equipamento **não prova** que o equipamento enxerga o driver.
São sentidos independentes.

---

## O painel não abre

| Causa provável | Como confirmar | Ação |
|---|---|---|
| Serviço parado | `sc query HIKVISION-DRIVER` → `STOPPED` | `sc start HIKVISION-DRIVER`; se não subir, veja o log |
| Porta ocupada | `netstat -ano \| findstr :5000` mostra outro processo | Troque `middleware.api.port` ou libere a porta |
| Bloqueado por allowlist | `middleware.api.allowlist` não inclui seu IP | Inclua seu IP ou limpe o campo |
| Firewall local | O painel abre na máquina, não pela rede | Libere a porta no firewall do Windows |

---

## Dispositivo sempre offline

| Causa provável | Como confirmar | Ação |
|---|---|---|
| Equipamento inalcançável | Log: `Erro no keepalive do device {DeviceId}` | Verifique energia, rede e se o ISAPI está habilitado |
| Credenciais erradas | Falha na configuração do dispositivo | Revise `seniorxt.ext.username` / `.password` |
| Keepalive apertado demais para a rede | Muitos dispositivos oscilando entre online e offline | Aumente `seniorx.keepalive.device.timeout` e `.failthreshold`. Ver [Escala e tuning](../referencia/escala-e-tuning.md) |
| Frota grande com detecção lenta | Muitos offline ao mesmo tempo | Aumente `.parallelism` e reduza `.timeout` |

---

## Carga facial falha

| Causa provável | Como confirmar | Ação |
|---|---|---|
| Pessoa sem foto na Senior | Log: `Carga facial \| ... ERRO {Person} — {Motivo}` | Cadastre a foto |
| Foto recusada pelo equipamento | O `{Motivo}` traz a recusa | Reenvie uma foto dentro dos padrões do equipamento |
| Servlet CSM inacessível (Senior XT) | Falha em toda a carga, não em pessoas isoladas | Verifique `seniorxt.csmservlet`. Ver [Rede e portas](../antes-de-instalar/rede-e-portas.md) |

A carga é **por pessoa**: uma falha individual não interrompe as demais.

---

## Eventos não chegam à Senior

| Causa provável | Como confirmar | Ação |
|---|---|---|
| Senior indisponível | `retryQueueSize` crescendo em `/diagnostic/data` | Os eventos estão guardados e serão reenviados sozinhos |
| Senior recusando o conteúdo | `deadLetterSize` maior que zero | Ver [dead-letter](../operacao/dead-letter.md) — exige ação manual |
| Disco com problema | `inboxAddFailures` maior que zero | Investigue o disco: a durabilidade está degradada |

---

## O serviço não inicia

1. `sc qc HIKVISION-DRIVER` — o caminho do executável está correto?
2. Abra o log mais recente e procure por `Erro ao iniciar middleware`.
3. `netstat -ano | findstr :5000` — a porta está livre?
4. O `middleware.properties` existe e é legível pela conta do serviço?

---

## "Not implemented" no log

Mensagem no formato:

```
<Tipo> | Pendency: 123 - Device: 45 - Not implemented
```

A Senior enviou um tipo de pendência que **este driver não trata**. Não é
configuração nem rede — é funcionalidade ausente. Anote o `<Tipo>`, o
`PendencyId` e acione o suporte.

---

## Antes de abrir um chamado

Junte:

- O log do dia — `log\log-AAAAMMDD.txt` (segredos já vêm mascarados)
- A saída de `/diagnostic/data`
- Versão do driver, plataforma (`thirdpart`) e modelo do equipamento
- O horário aproximado da ocorrência
