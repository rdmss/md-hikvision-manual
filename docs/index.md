# Hikvision Driver

Driver que conecta controladoras de acesso **Hikvision** (linha DS-K) às
plataformas de gestão de acesso da **Senior**. Roda como serviço Windows, num
servidor dentro da rede do cliente.

---

## Como a integração funciona

Vale entender isto antes de instalar — quase todo problema de campo faz sentido
depois que este desenho está claro.

Numa instalação comum, a catraca decide sozinha quem passa: ela guarda a lista
de pessoas autorizadas e compara. **Aqui não.** Neste modelo, chamado
*verificação remota*, o equipamento não decide nada — ele **pergunta** a cada
passagem.

O caminho de uma passagem é este:

1. A pessoa apresenta o cartão ou o rosto na catraca.
2. A catraca envia o evento ao **driver**, pela rede.
3. O driver pergunta à **Senior** se aquela pessoa pode passar agora.
4. A Senior responde, o driver repassa, e a catraca libera ou nega.

Tudo isso em menos de um segundo, e a catraca fica esperando essa resposta.

O driver, portanto, é um **intermediário**. Ele não é o cadastro de pessoas —
esse vive na Senior — e não decide acessos. Ele traduz: fala ISAPI (a linguagem
dos equipamentos Hikvision) de um lado, e o protocolo da Senior do outro.

Além de validar passagens, o driver cuida de três coisas em segundo plano:

- **Carrega as credenciais** nos equipamentos — cartões e fotos que a Senior
  manda.
- **Vigia a saúde da frota**, avisando a Senior quando um equipamento fica
  inacessível.
- **Guarda os eventos** que não conseguiu entregar, reenviando depois.

---

## Escolha a sua plataforma

Cada guia leva do zero até a catraca funcionando. Leia só o da sua instalação —
os dois são independentes.

<div class="grid cards" markdown>

-   ### [Guia Senior X](guia-senior-x.md)

    A Senior está **na nuvem**. O driver conversa com ela pela internet.

-   ### [Guia Senior XT](guia-senior-xt.md)

    Há uma **Concentradora** na rede do cliente. O driver conversa com ela
    localmente.

</div>

Na dúvida sobre qual é o caso, pergunte a quem contratou a Senior: quem usa
Senior XT sabe que tem uma Concentradora instalada.

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
    A parte que engana: liberar o firewall **do servidor para a catraca** parece
    suficiente, mas não é. O evento de acesso viaja no sentido contrário — da
    **catraca para o servidor**, na porta 5000.

    Sem esse caminho aberto, tudo aparenta estar certo: os equipamentos ficam
    online, a carga de cartões conclui, o painel fica verde. E **nenhuma
    passagem é validada**, porque o pedido nunca chega.

!!! danger "2. A propriedade `driverAddress` é obrigatória"
    É ela, preenchida no cadastro do dispositivo dentro da Senior, que diz ao
    equipamento **para qual endereço** mandar os eventos. É a outra metade do
    item 1: o firewall abre o caminho, e essa propriedade informa o destino.

    Sem ela, o equipamento sequer chega a ser configurado.
    Ver [Propriedades](propriedades.md#propriedades-extensiveis).

!!! danger "3. Sem a Senior, o acesso é negado"
    Como a decisão é sempre do servidor, uma queda da Senior significa catraca
    negando todo mundo. Não existe modo de contingência que libere localmente.

    **Combine isso com o cliente antes de instalar.** É a conversa que ninguém
    quer ter no meio de uma indisponibilidade.

---

## Outras páginas

- **[Propriedades](propriedades.md)** — o que cada parâmetro faz, tanto na
  configuração do driver quanto no cadastro da Senior
- **[Problemas](problemas.md)** — sintoma, causa e ação; o que cada mensagem de
  log quer dizer
