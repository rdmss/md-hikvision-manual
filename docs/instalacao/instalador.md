# Instalador passo a passo

O instalador é gerado com Inno Setup. O arquivo se chama
`Hikvision Driver-Setup-2.2.1.exe`.

!!! note "Por que este capítulo não tem imagens"
    O passo a passo é descrito campo a campo, em vez de por captura de tela.
    Descritivo não envelhece quando o instalador muda de tema ou de idioma do
    Windows, e permanece legível para quem opera por acesso remoto lento.

---

## Antes de começar

- Execute como **administrador**. O instalador declara privilégio obrigatório e
  não abre sem elevação.
- Tenha o **ASP.NET Core Runtime 9.0 x64** instalado — o instalador barra sem
  ele, ainda que o driver não precise. Ver [Pré-requisitos](../antes-de-instalar/pre-requisitos.md).
- Em **atualização**, sua configuração é preservada. Ver [Atualização](atualizacao.md).

---

## Tela 1 — Boas-vindas

Tela padrão do Inno Setup, identificando o produto:

| Campo | Valor |
|---|---|
| Aplicação | Hikvision Driver |
| Versão | 2.2.1 |
| Publicador | Thidi |

Clique em **Avançar**.

---

## Tela 2 — Configuração Personalizada

Esta tela é específica deste instalador e é a mais fácil de passar batido.

**Título:** Configuração Personalizada
**Subtítulo:** Configurar serviço Windows
**Instrução:** Informe o nome do serviço.

| Campo | Valor padrão | Observação |
|---|---|---|
| **Nome do Serviço:** | `HIKVISION-DRIVER` | É o nome com que o serviço será criado no Windows |

O que você digitar aqui é o nome usado em `sc create`, `sc start`, `sc stop` e na
desinstalação. **Anote se alterar** — todos os comandos deste manual assumem o
padrão, e você precisará substituir o nome neles.

!!! danger "Campo obrigatório"
    Deixar em branco e clicar em Avançar produz:

    > O nome do serviço é obrigatório.

    O instalador não avança até você preencher.

!!! tip "Quando faz sentido mudar o nome"
    Só quando houver **mais de uma instância** do driver na mesma máquina. Dois
    serviços não podem ter o mesmo nome. Fora esse caso, mantenha o padrão.

---

## Tela 3 — Diretório de destino

| Campo | Valor padrão |
|---|---|
| Pasta de destino | `C:\HikvisionDriver` |

O campo é editável. Se alterar, lembre-se de que os caminhos citados neste
manual (`log\`, `events\`, `middleware.properties`, `HIK.CER`) passam a ser
relativos à pasta que você escolheu.

---

## Tela 4 — Pronto para instalar

É aqui que o instalador **verifica o ASP.NET Core Runtime 9.0**.

**Se o runtime for encontrado**, a instalação prossegue normalmente.

**Se não for encontrado**, aparece um diálogo de erro com os botões
**Repetir** e **Cancelar**:

> O ASP.NET Core Runtime 9.0 não está instalado.
> Este aplicativo requer o ASP.NET Core Runtime 9.0 para funcionar.
> Baixe e instale em: https://dotnet.microsoft.com/download/dotnet/9.0
> (Selecione "ASP.NET Core Runtime" para Windows x64)
> Após instalar, clique em "Repetir" para continuar ou "Cancelar" para abortar.

O diálogo **reaparece em laço** até o runtime ser detectado. Instale-o em outra
janela, volte e clique em **Repetir**. **Cancelar** aborta a instalação.

---

## O que o instalador faz

Ao confirmar, nesta ordem:

1. **Copia os arquivos da aplicação** para o diretório de destino, sobrescrevendo
   o que existir — incluindo subpastas.
2. **Preserva a sua configuração:** o `middleware.properties` é copiado
   **apenas se ainda não existir**. Em atualização e reinstalação, o arquivo do
   cliente permanece intacto e nada precisa ser reconfigurado.
3. **Atualiza o `logging.json`** junto com o executável. Este arquivo é
   deliberadamente tratado como parte da aplicação, não como configuração do
   cliente: a versão 2.2.1 corrige uma lentidão causada por log em nível Debug
   síncrono, e a correção exige substituir o `logging.json` antigo.
4. **Cria o serviço Windows**, com início automático:

   ```
   sc create HIKVISION-DRIVER binPath= "C:\HikvisionDriver\HIK_Driver.exe" start= auto displayname= "Hikvision Driver"
   ```

5. **Inicia o serviço:**

   ```
   sc start HIKVISION-DRIVER
   ```

---

## Depois de instalar

O driver sobe, mas ainda **não está integrado** — falta apontar a plataforma
Senior e preencher as credenciais. Siga para
[Primeira configuração](primeira-configuracao.md).

Em deploys **Senior XT**, coloque o `HIK.CER` no diretório de instalação antes de
configurar. Ver [Certificado](../senior-xt/certificado.md).
