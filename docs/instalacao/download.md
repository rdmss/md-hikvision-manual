# Download

O instalador é distribuído como executável do Windows:

```
Hikvision Driver-Setup-2.2.1.exe
```

!!! info "Onde obter"
    O canal oficial de distribuição ainda não está definido nesta documentação —
    instalador pelo canal de atendimento acordado com o fornecedor.

    Nenhum link foi publicado aqui porque nenhum foi confirmado. Um endereço
    inventado levaria alguém a baixar um arquivo de origem desconhecida.

## O que vem no pacote

| Item | Observação |
|---|---|
| Aplicação | Publicada *self-contained* — o runtime vai embutido |
| `logging.json` | Configuração de log, atualizada junto com o executável |
| `middleware.properties` | Modelo, escrito **apenas** se ainda não existir |

!!! warning "O certificado do Senior XT não vem no pacote"
    O `HIK.CER` precisa ser obtido e colocado manualmente. Ver
    [Certificado](../senior-xt/certificado.md).

## Pacote de atualização

Existe também um pacote reduzido, contendo apenas o `HIK_Driver.exe`, para
atualizar sem passar pelo instalador. Ele **não** atualiza o `logging.json` —
ver [Atualização](atualizacao.md) antes de escolher esse caminho.

## Verificação antes de instalar

- Confirme a versão do arquivo com quem o forneceu
- Instale a partir de cópia obtida pelo canal oficial, não de repasse informal
- Confira os [pré-requisitos](../antes-de-instalar/pre-requisitos.md) — em
  especial o ASP.NET Core Runtime 9.0, sem o qual o instalador não avança
