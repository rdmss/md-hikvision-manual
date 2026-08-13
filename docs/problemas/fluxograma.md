# Fluxograma de diagnóstico

Ponto de partida quando alguém diz que "não está funcionando". Siga de cima para
baixo: cada nível elimina uma classe de causa.

## O acesso não libera

```
O serviço está RUNNING?
│
├─ NÃO → Ver o log: "Erro ao iniciar middleware"
│         Porta ocupada? Configuração ilegível?
│         → Serviço Windows
│
└─ SIM
   │
   O painel abre em http://<host>:5000/config ?
   │
   ├─ NÃO → Firewall, allowlist ou porta divergente
   │         → Rede e portas
   │
   └─ SIM
      │
      A integração está de pé?
      │  Senior X:  /health responde 200
      │  Senior XT: log SEM "Attempting SeniorXT reconnection" em laço
      │
      ├─ NÃO → Configuração incompleta ou plataforma inacessível
      │         Ver "Thirdpart ... propriedades obrigatorias faltando"
      │         Senior XT: certificado no lugar? Driver ID correto?
      │
      └─ SIM
         │
         Os dispositivos aparecem ONLINE?
         │
         ├─ NÃO → "Erro no keepalive do device"
         │         Equipamento ligado? ISAPI habilitado? Credenciais?
         │
         └─ SIM
            │
            Uma passagem de teste aparece em /diagnostic/events?
            │
            ├─ NÃO → O EVENTO NÃO ESTÁ CHEGANDO
            │         Libere o sentido EQUIPAMENTO → DRIVER
            │         É a causa mais comum de todas
            │
            └─ SIM
               │
               O evento aparece na Senior?
               │
               ├─ NÃO → retryQueueSize crescendo? Senior indisponível
               │         deadLetterSize acima de zero? Rejeição permanente
               │
               └─ SIM → A integração está funcionando.
                        Se ainda nega, é permissão ou credencial não
                        carregada — verifique o cadastro na Senior.
```

## O ponto de corte que mais economiza tempo

O nó da **passagem de teste** separa dois mundos:

- **Não aparece** → problema de **rede de entrada**. Nada adianta mexer em
  configuração, cadastro ou credencial.
- **Aparece** → a rede está certa. O problema é de **dados**: permissão,
  credencial ou cadastro.

Fazer esse teste primeiro evita a maior parte das investigações erradas.

Ver [Sintoma, causa e ação](sintomas.md) e
[Mensagens de log](mensagens-de-log.md).
