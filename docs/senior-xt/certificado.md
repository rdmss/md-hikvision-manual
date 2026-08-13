# Certificado

Deploys **Senior XT** exigem um arquivo de certificado no diretório de
instalação. O nome padrão é `HIK.CER`, configurável em `seniorxt.certificate`.

!!! danger "O instalador não empacota o certificado"
    O arquivo foi removido do versionamento do projeto e **não vai junto no
    instalador**. Ele precisa ser colocado manualmente no diretório de
    instalação.

    Sem ele, a autenticação com a Concentradora falha — e o sintoma não é
    óbvio: o serviço sobe normalmente, o painel abre, e a integração
    simplesmente não estabelece.

## Onde colocar

Na raiz do diretório de instalação, ao lado do executável:

```
C:\HikvisionDriver\HIK.CER
```

Se você usar outro nome de arquivo, informe-o em `seniorxt.certificate` — a
configuração espera o **nome do arquivo**, não um caminho completo.

## Ordem correta

1. Instale o driver
2. **Copie o `HIK.CER`** para o diretório de instalação
3. Só então configure a plataforma pela tela `/config`

Configurar antes de o certificado estar no lugar faz a integração falhar no
primeiro start, exigindo reinício depois.

## Trocando o certificado

O arquivo é lido na inicialização. Depois de substituí-lo, **reinicie o
serviço**:

```bat
sc stop HIKVISION-DRIVER
sc start HIKVISION-DRIVER
```

## Atualização e desinstalação

| Situação | O que acontece com o certificado |
|---|---|
| Atualização pelo instalador | Preservado — não é sobrescrito |
| Pacote de atualização (só o executável) | Preservado |
| **Desinstalação** | **Apagado** junto com o diretório inteiro |

!!! warning "Guarde uma cópia fora da máquina"
    A desinstalação remove a pasta inteira. Mantenha o certificado no repositório
    de artefatos do cliente, não apenas no servidor.

## Verificando

Não há teste dedicado ao certificado no painel. A evidência é indireta:

- O log **não** mostra `Attempting SeniorXT reconnection` em laço
- Os dispositivos aparecem online na [tela de diagnóstico](../operacao/diagnostico.md)
- Uma passagem de teste é validada

Ver [Validação](validacao.md).
