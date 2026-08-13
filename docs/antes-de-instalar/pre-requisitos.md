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

**Instale o ASP.NET Core Runtime 9.0 x64 antes de rodar o instalador.**

O instalador confere o runtime antes de prosseguir. Se ele não estiver presente,
a instalação não avança e o assistente orienta o download em
`https://dotnet.microsoft.com/download/dotnet/9.0` (opção *ASP.NET Core Runtime*,
Windows x64). Instalado o runtime, basta continuar dali mesmo.

!!! tip "Deixe pronto antes de ir ao cliente"
    Em servidor recém-provisionado o runtime raramente está instalado. Baixar no
    momento da visita costuma custar mais tempo do que a instalação inteira.

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
    Consulte o fornecedor para a lista de modelos homologados.
