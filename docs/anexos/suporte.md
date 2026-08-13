# Suporte

!!! danger "Canal de suporte ainda não definido"
    Esta seção está deliberadamente em branco. Nenhum e-mail, telefone ou portal
    foi publicado aqui porque **nenhum foi confirmado**.

    Publicar um canal inventado é pior do que não ter a seção: o cliente escreve
    para um endereço que ninguém lê e conclui que o fornecedor não responde.

    Preencher esta página é o item `PEND-01` em [Pendências](pendencias.md).

## O que enviar ao abrir um chamado

Independentemente do canal, estes itens resolvem a maioria dos atendimentos sem
uma segunda rodada de perguntas:

| Item | Onde obter |
|---|---|
| Versão do driver | Topo da [tela de diagnóstico](../operacao/diagnostico.md), ou campo `version` em `/diagnostic/data` |
| Plataforma em uso | Campo `thirdpart` |
| Log do dia | `log\log-AAAAMMDD.txt` no diretório de instalação |
| Estado do sistema | Saída completa de `/diagnostic/data` |
| Modelo do equipamento | Etiqueta do equipamento ou interface web dele |
| Horário da ocorrência | Aproximado, com fuso |
| O que se esperava e o que aconteceu | Descrição em uma ou duas frases |

!!! tip "Segredos já vêm mascarados no log"
    Chave de integração, senha e token são substituídos por marcadores antes de
    serem gravados. Ainda assim, confira o arquivo antes de enviá-lo para fora
    da organização.

## Antes de acionar

Boa parte dos casos está coberta em [Solução de problemas](../problemas/sintomas.md).
Vale especialmente conferir:

- O sentido **equipamento → driver** está liberado no firewall? É a causa mais
  comum de "nada acontece".
- O `/health` responde `200`? Em Senior XT, lembre-se de que ele
  [não detecta queda da Concentradora](../operacao/health-e-monitoramento.md).
- `deadLetterSize` está acima de zero? Isso exige
  [ação manual](../operacao/dead-letter.md).
