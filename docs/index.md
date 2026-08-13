# Hikvision Driver

Driver de integração que conecta controladoras de acesso **Hikvision** (linha DS-K,
via ISAPI) às plataformas de gestão de acesso da **Senior**.

O driver roda como **serviço Windows** na rede do cliente. Ele fica entre os
equipamentos e a plataforma Senior: recebe os eventos de acesso do equipamento,
valida cada passagem contra a Senior em tempo real, sincroniza as credenciais
(cartões e faces) nos dispositivos e reporta a saúde da frota.

---

## Compatibilidade

| Item | Versão / valor |
|---|---|
| Versão do driver | **2.2.1** |
| Plataformas suportadas | Senior X (nuvem) · Senior XT (Concentradora CSM) |
| Sistema operacional | Windows (serviço) |
| Runtime | Não requer .NET instalado — o pacote é *self-contained* |
| Equipamentos | Hikvision linha DS-K, com ISAPI habilitado |

!!! info "Modelos homologados"
    A lista fechada de modelos e versões de firmware homologados ainda não está
    consolidada nesta documentação. Consulte [Pendências](anexos/pendencias.md).

---

## Por onde começar

Escolha a plataforma Senior que a instalação vai usar. As duas jornadas são
independentes — você não precisa ler a outra.

<div class="grid cards" markdown>

-   ### Senior X

    Plataforma em nuvem. O driver fala REST e WebSocket com a Senior.

    [Instalar com Senior X](senior-x/parametros.md)

-   ### Senior XT

    Concentradora CSM na rede do cliente, via protocolo TCP-IP.

    [Instalar com Senior XT](senior-xt/parametros.md)

</div>

Se ainda não sabe qual é o caso, comece pela [Visão geral](visao-geral/produto.md).

---

## Atalhos frequentes

| Preciso de… | Vá para |
|---|---|
| Instalar do zero | [Antes de instalar](antes-de-instalar/pre-requisitos.md) |
| Saber se está funcionando | [Validação e diagnóstico](operacao/diagnostico.md) |
| Resolver um problema | [Solução de problemas](problemas/sintomas.md) |
| Entender um parâmetro | [Referência de parâmetros](referencia/parametros.md) |
| Saber o que acontece se a Senior cair | [Queda da Senior](operacao/queda-da-senior.md) |
