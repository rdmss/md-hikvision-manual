# Notas de versão

## 2.2.1 — versão atual

Versão de referência desta documentação.

**Desempenho**

- Correção de lentidão causada por log em nível Debug **síncrono**. A correção
  depende do `logging.json` novo, por isso esse arquivo é substituído pelo
  instalador junto com o executável.
- Conexão com o equipamento reutilizada entre validações.
- Log assíncrono.
- Piso configurável do thread pool (`middleware.threadpool.minthreads`), padrão
  `max(64, núcleos × 8)`, para absorver rajadas de validação durante cargas
  faciais.

**Durabilidade**

- **Inbox de webhooks:** o evento bruto passa a ser persistido antes de o driver
  confirmar o recebimento. Se o processo morrer no meio do processamento, o
  evento é reprocessado no próximo start.
- Limites explícitos na fila de reenvio: `seniorx.eventretry.maxretries`
  (2880) e `seniorx.eventretry.ttl.hours` (72).

**Saúde da frota**

- Sonda rápida para dispositivo já offline, evitando que uma queda em massa
  trave o ciclo.
- Anti-oscilação por limiar de falhas consecutivas.

!!! note "Atualizando a partir de versão anterior"
    Prefira o instalador completo. O pacote que substitui apenas o executável
    **não** atualiza o `logging.json` e, portanto, não entrega a correção de
    lentidão desta versão. Ver [Atualização](../instalacao/atualizacao.md).

---

## Sobre o histórico anterior

Este driver substitui um middleware Java legado. O histórico daquele projeto foi
preservado no repositório, mas as versões anteriores a esta linha não são
cobertas por este manual.

!!! info "Changelog completo pendente"
    A relação detalhada de versões anteriores não foi consolidada aqui.
