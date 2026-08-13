# Hikvision Driver

Driver que conecta controladoras de acesso **Hikvision** (linha DS-K, via ISAPI)
às plataformas de gestão de acesso da **Senior**. Roda como serviço Windows na
rede do cliente.

A cada passagem, o equipamento consulta o driver, o driver consulta a Senior, e a
resposta volta liberando ou negando o acesso.

---

## Escolha a sua plataforma

Cada guia leva do zero até a catraca funcionando. Leia só o da sua instalação.

<div class="grid cards" markdown>

-   ### [Guia Senior X](guia-senior-x.md)

    Plataforma em nuvem, comunicação por REST e WebSocket.

-   ### [Guia Senior XT](guia-senior-xt.md)

    Concentradora CSM na rede do cliente, por TCP-IP.

</div>

---

## Compatibilidade

| Item | Valor |
|---|---|
| Versão do driver | **2.2.1** |
| Sistema operacional | Windows, instalado como serviço |
| Runtime | ASP.NET Core Runtime 9.0 x64 |
| Equipamentos | Hikvision linha DS-K, com ISAPI habilitado |
| Diretório padrão | `C:\HikvisionDriver` |
| Nome padrão do serviço | `HIKVISION-DRIVER` |

---

## As três coisas que mais causam chamado

Se você ler só isto antes de instalar, já evita a maioria dos problemas.

!!! danger "1. O equipamento precisa alcançar o driver"
    Não basta o driver alcançar o equipamento. O evento de acesso vai do
    **equipamento para o driver**, na porta 5000. Sem esse sentido liberado no
    firewall, tudo parece certo — os dispositivos aparecem online, a carga de
    cartões conclui — e **nenhuma passagem é validada**.

!!! danger "2. A propriedade `driverAddress` é obrigatória"
    É ela, no cadastro do dispositivo na Senior, que informa ao equipamento o
    endereço do driver. Sem ela o equipamento nem chega a ser configurado.
    Ver [Propriedades](propriedades.md#propriedades-extensiveis).

!!! danger "3. Sem a Senior, o acesso é negado"
    Os equipamentos operam em verificação remota: a decisão é sempre do
    servidor. Não existe liberação local durante quedas. Combine isso com o
    cliente **antes** da instalação.

---

## Outras páginas

- **[Propriedades](propriedades.md)** — todos os parâmetros de configuração e as
  propriedades extensíveis do cadastro na Senior
- **[Problemas](problemas.md)** — sintoma, causa e ação; mensagens de log
