# Pendências

O que ainda falta nesta documentação e por quê. A lista existe para que a lacuna
seja visível, em vez de ser preenchida por suposição.

!!! info "Princípio adotado"
    Nada entra neste manual sem origem verificável no código-fonte, no script do
    instalador ou em confirmação de quem conhece o ambiente. Fato não verificado
    aparece aqui, nunca como texto inventado no meio de um procedimento.

## Depende de informação do fabricante do driver

| ID | Item | Por que está parado |
|---|---|---|
| `PEND-01` | **Canal de suporte** | Não será publicado e-mail, telefone ou portal inventado. Um canal que não existe manda o cliente para o vazio — pior do que não ter a seção. |
| `PEND-02` | **Modelos e firmware homologados** | A lista fechada de equipamentos Hikvision suportados não foi consolidada. |
| `PEND-03` | **Permissões necessárias na Senior** | Os códigos de tela e a lista de permissões precisam de confirmação. Não serão deduzidos. |

## Depende de acesso a um ambiente Senior

Os passos estão descritos por escrito; falta a captura de tela.

| ID | Item | Onde entra |
|---|---|---|
| `IMG-SX-01` | Cadastro do driver no Senior X | [Cadastro na plataforma](../senior-x/cadastro.md) |
| `IMG-SX-02` | Cadastro de dispositivos e leitoras | [Dispositivos e leitoras](../senior-x/dispositivos.md) |
| `IMG-SX-03` | Ativação de fotos e facial | [Fotos e facial](../senior-x/fotos-e-facial.md) |
| `IMG-XT-01` | Senior Config Center | [Config Center](../senior-xt/config-center.md) |
| `IMG-XT-02` | Cadastro de driver | [Parâmetros — Senior XT](../senior-xt/parametros.md) |
| `IMG-XT-03` | Cadastro de catálogo e aba de acoplados | [Catálogo](../senior-xt/catalogo.md) |
| `IMG-XT-04` | Cadastro de tecnologia biométrica | [Biometria](../senior-xt/biometria.md) |
| `IMG-XT-05` | Cadastro de dispositivo e leitoras | [Dispositivos e leitoras](../senior-xt/dispositivos.md) |

!!! note "Por que não foram capturadas"
    Exigem acesso a um ambiente Senior, e a autenticação precisa ser feita por
    uma pessoa. Além disso, capturas de ambiente de produção trariam dado de
    cliente — as imagens devem sair de ambiente de homologação.

## Decisão consciente: sem imagem

| ID | Item | Motivo |
|---|---|---|
| `IMG-WIN-01..04` | Telas do instalador Windows | Substituídas por [descritivo campo a campo](../instalacao/instalador.md). Não há máquina Windows disponível, e o descritivo não envelhece quando o instalador muda de tema ou idioma. |
