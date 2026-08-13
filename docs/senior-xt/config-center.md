# Senior Config Center

!!! warning "Capítulo pendente de confirmação"
    As etapas de configuração no Senior Config Center não foram verificadas em
    ambiente real e **não estão descritas aqui**. Nenhum nome de campo ou
    caminho de menu foi deduzido.


## O que precisa estar configurado

Do lado da Senior XT, a integração depende de:

| Item | Reflete no driver |
|---|---|
| Endereço da Concentradora acessível ao driver | `seniorxt.server` e `seniorxt.port` |
| Servlet CSM habilitado para consulta de fotos | `seniorxt.csmservlet` |
| Driver cadastrado, com identificador numérico | `seniorxt.driver` |
| Uso de fotos habilitado | Carga facial funcionando |

## Como validar sem as telas

| Verificação | Como |
|---|---|
| Driver alcança a Concentradora | `Test-NetConnection <server> -Port <porta>` |
| Autenticação estabelece | Log **sem** `Attempting SeniorXT reconnection` em laço |
| Servlet CSM acessível | Carga facial conclui — falha total aponta para o servlet |
| Driver ID correto | Pendências chegando ao driver |

Se a autenticação não estabelece, verifique nesta ordem: certificado no lugar,
`seniorxt.driver` correto, endereço e porta corretos, rede liberada.

Ver [Mensagens de log](../problemas/mensagens-de-log.md).
