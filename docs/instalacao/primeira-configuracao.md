# Primeira configuração

Depois de instalar, o driver está rodando mas **ainda não integrado**. Falta
apontar a plataforma Senior e preencher as credenciais.

```
http://<host>:5000/config
```

O endereço `/` leva à mesma tela.

!!! warning "Por padrão, a tela não pede login"
    Com `middleware.api.password` vazio, qualquer pessoa com acesso de rede à
    porta abre esta tela — que exibe e permite alterar os parâmetros de
    integração. **Defina uma senha nesta primeira configuração** e restrinja a
    allowlist aos IPs de gestão.

## A tela

Dois blocos:

**Propriedades comuns** — porta da API, usuário e senha do painel, IPs
permitidos.

**Propriedades do Terceiro** — muda conforme a plataforma escolhida.

=== "Senior X"

    | Campo | Preencher com |
    |---|---|
    | Driver Key | A chave emitida pela Senior para esta instalação — **é segredo** |

    !!! note "As URLs não aparecem na tela"
        `seniorx.api.url` e `seniorx.websocket.url` são propriedades ocultas no
        painel. Os defaults apontam para a produção da Senior. Para outro
        ambiente, edite o `middleware.properties` e reinicie o serviço.

=== "Senior XT"

    | Campo | Preencher com |
    |---|---|
    | Endereço da Concentradora | Host ou IP da Concentradora |
    | Porta da Concentradora | Porta TCP |
    | CSM Servlet (Consulta de fotos) | Endereço do servlet usado na carga facial |
    | Driver ID | Identificador numérico cadastrado na Senior |
    | Certificado | Nome do arquivo — `HIK.CER` por padrão |
    | Usuário do Equipamento | Opcional |
    | Senha para o usuário | Opcional |
    | Endereço Middleware | Opcional |

    !!! danger "Coloque o certificado antes de salvar"
        O instalador **não** empacota o `HIK.CER`. O arquivo precisa estar no
        diretório de instalação. Ver [Certificado](../senior-xt/certificado.md).

## Salvando

Campos obrigatórios em branco impedem a gravação, com a mensagem:

```
Preencha as propriedades obrigatorias: <lista das chaves>
```

Nada é gravado quando isso acontece — corrija e salve de novo.

Gravando com sucesso, aparece a confirmação **Driver Configurado**. O driver se
reinicia sozinho para aplicar; não é preciso mexer no serviço.

## Confirmando que funcionou

Na ordem:

1. **`/health`** responde `200`.

    Em Senior XT, lembre-se de que este check não verifica a Concentradora — ver
    [Health check e monitoramento](../operacao/health-e-monitoramento.md).

2. **`/diagnostic`** mostra a plataforma correta e os dispositivos aparecendo.

3. **Teste de conectividade** com a Senior, pelo painel.

4. **Passagem de teste** numa catraca — e o evento aparecendo em
   `/diagnostic/events`.

O passo 4 é o único que prova a integração ponta a ponta: ele exercita o
caminho **equipamento → driver**, que os anteriores não cobrem.

## Alterando depois

Mudanças pela tela reiniciam o driver automaticamente. Mudanças feitas direto no
`middleware.properties` exigem reinício manual do serviço — ver
[Serviço Windows](servico-windows.md).
