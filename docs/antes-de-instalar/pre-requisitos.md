# Pré-requisitos

Confira estes itens **antes** de rodar o instalador. Quase toda instalação que
falha em campo falha por um destes cinco pontos.

## Máquina

| Item | Requisito |
|---|---|
| Sistema operacional | Windows com suporte a serviços (`sc create`) |
| Arquitetura | x64 |
| Privilégio | O instalador exige **administrador** |
| Diretório | `C:\HikvisionDriver` por padrão, editável na instalação |

## Runtime .NET

!!! warning "O instalador exige o ASP.NET Core Runtime 9.0, mesmo sem o driver precisar dele"
    O driver é publicado como **self-contained** `win-x64` — o runtime vai
    embutido no executável e a aplicação roda sem .NET instalado na máquina.

    O instalador, porém, verifica o runtime na última tela e **não deixa
    continuar** sem ele. Se não encontrar, exibe um diálogo em laço:

    > O ASP.NET Core Runtime 9.0 não está instalado.
    > Este aplicativo requer o ASP.NET Core Runtime 9.0 para funcionar.
    > Baixe e instale em: https://dotnet.microsoft.com/download/dotnet/9.0
    > (Selecione "ASP.NET Core Runtime" para Windows x64)

    Só há duas saídas: instalar o runtime e clicar em **Repetir**, ou
    **Cancelar** e abortar a instalação.

    **Na prática, portanto: instale o ASP.NET Core Runtime 9.0 x64 antes**, ainda
    que o driver não vá utilizá-lo. Essa verificação é uma trava do instalador,
    não uma dependência da aplicação.

## Rede

O ponto mais esquecido é o sentido da comunicação: **o equipamento precisa
alcançar o driver**, não só o contrário. Ver [Rede e portas](rede-e-portas.md).

## Plataforma Senior

=== "Senior X"

    - Ambiente Senior X acessível pela internet a partir da máquina do driver.
    - **Driver key** emitida para esta instalação. É segredo — não reaproveite
      entre clientes.

=== "Senior XT"

    - Endereço e porta da **Concentradora** CSM.
    - Endereço do **servlet CSM**, usado na consulta de fotos.
    - **Driver ID** numérico, conforme cadastrado na Senior.
    - Arquivo de **certificado** (`HIK.CER`) — o instalador **não** o empacota.
      Ver [Certificado](../senior-xt/certificado.md).

## Equipamentos

- Controladoras Hikvision linha DS-K com **ISAPI habilitado**.
- Credenciais de acesso ao equipamento (usuário e senha), se ele exigir.
- IP fixo ou reserva de DHCP: o driver registra o endereço de callback nos
  equipamentos, e um IP que muda quebra a entrega de eventos.

!!! info "Modelos e firmware homologados"
    A lista fechada ainda não foi consolidada. Ver [Pendências](../anexos/pendencias.md).
