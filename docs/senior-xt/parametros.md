# Parâmetros — Senior XT

Configuração do driver para operar com a Concentradora CSM. Preencha pela
[tela de configuração](../instalacao/primeira-configuracao.md).

| Campo na tela | Chave | Obrigatório |
|---|---|:--:|
| Endereço da Concentradora | `seniorxt.server` | ✓ |
| Porta da Concentradora | `seniorxt.port` | ✓ |
| CSM Servlet (Consulta de fotos) | `seniorxt.csmservlet` | ✓ |
| Driver ID | `seniorxt.driver` | ✓ |
| Certificado | `seniorxt.certificate` | ✓ |
| Usuário do Equipamento | `seniorxt.ext.username` | |
| Senha para o usuário | `seniorxt.ext.password` | |
| Endereço Middleware | `seniorxt.ext.server.address` | |

!!! danger "Coloque o certificado antes de configurar"
    O instalador **não** empacota o `HIK.CER`. Ver [Certificado](certificado.md).

!!! warning "Cadastro do driver na Senior pendente de confirmação"
    A tela e os campos de cadastro do driver no Senior XT não foram verificados
    em ambiente real. Pendências `PEND-03` e `IMG-XT-02` em
    [Pendências](../anexos/pendencias.md).

    O `Driver ID` preenchido aqui precisa ser **o mesmo** cadastrado na Senior.

Descrição completa em [Referência de parâmetros](../referencia/parametros.md).

## Particularidades do Senior XT

| Comportamento | Detalhe |
|---|---|
| Carga de faces | Incremental reconciliada — **remove** quem saiu da lista |
| Fila de reenvio de eventos | **Não existe** — `seniorx.eventretry.*` não tem efeito |
| `/health` | **Não verifica** a Concentradora |
| Fila de mensagens | Em memória — não sobrevive a reinício |

!!! danger "O `/health` dá falso positivo aqui"
    As verificações de API e WebSocket passam automaticamente quando a plataforma
    não é Senior X. O `/health` responde `200` com a Concentradora inacessível.

    Monitore `offlineDevices` e o log. Ver
    [Health check e monitoramento](../operacao/health-e-monitoramento.md).

!!! note "As chaves `seniorx.keepalive.*` valem aqui"
    Apesar do prefixo, o serviço de keepalive roda para as duas plataformas.

Próximo passo: [Senior Config Center](config-center.md).
