# Topologia

Como os componentes se comunicam, por plataforma. Os diagramas usam a cor do
texto da página, funcionando em tema claro e escuro.

## Senior X — nuvem

O driver fica na rede do cliente e fala com a plataforma pela internet.

<figure markdown>
<svg viewBox="0 0 720 260" role="img" aria-label="Diagrama: equipamentos e driver na rede do cliente, plataforma Senior X na nuvem" style="width:100%;height:auto;color:currentColor">
  <g fill="none" stroke="currentColor" stroke-width="1.5">
    <rect x="12" y="40" width="330" height="200" rx="6" stroke-dasharray="5 4" opacity="0.45"/>
    <rect x="430" y="80" width="270" height="120" rx="6" stroke-dasharray="5 4" opacity="0.45"/>
    <rect x="36" y="76" width="120" height="46" rx="4"/>
    <rect x="36" y="150" width="120" height="46" rx="4"/>
    <rect x="212" y="112" width="110" height="76" rx="4" stroke-width="2"/>
    <rect x="470" y="116" width="190" height="52" rx="4"/>
  </g>
  <g font-family="ui-monospace,SFMono-Regular,Menlo,monospace" font-size="11" fill="currentColor">
    <text x="20" y="32" opacity="0.6">REDE DO CLIENTE</text>
    <text x="438" y="72" opacity="0.6">NUVEM</text>
    <text x="52" y="104">Controladora</text>
    <text x="52" y="178">Controladora</text>
    <text x="234" y="146" font-size="12" font-weight="600">Driver</text>
    <text x="234" y="164" opacity="0.7">porta 5000</text>
    <text x="492" y="140" font-size="12" font-weight="600">Senior X</text>
    <text x="492" y="158" opacity="0.7">REST + WebSocket</text>
  </g>
  <g stroke="currentColor" stroke-width="1.5" fill="none">
    <path d="M156 92 L206 128" marker-end="url(#a)"/>
    <path d="M156 180 L206 172" marker-end="url(#a)"/>
    <path d="M206 140 L160 108" marker-end="url(#a)" opacity="0.55"/>
    <path d="M322 150 L464 142" marker-end="url(#a)"/>
    <path d="M464 128 L326 136" marker-end="url(#a)" opacity="0.55"/>
  </g>
  <defs>
    <marker id="a" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="5" markerHeight="5" orient="auto">
      <path d="M0 0 L10 5 L0 10 z" fill="currentColor"/>
    </marker>
  </defs>
  <g font-family="ui-monospace,SFMono-Regular,Menlo,monospace" font-size="9.5" fill="currentColor" opacity="0.75">
    <text x="150" y="118">eventos →</text>
    <text x="150" y="132">← ISAPI</text>
    <text x="352" y="130">← pendências</text>
    <text x="352" y="166">validação →</text>
  </g>
</svg>
<figcaption>Senior X: o driver precisa de saída HTTPS e WebSocket para a internet.</figcaption>
</figure>

| Sentido | Porta | Para quê |
|---|---|---|
| Equipamento → Driver | `5000` | **Entrega dos eventos de acesso** |
| Driver → Equipamento | 80 / 443 | Configuração, credenciais, sondagem |
| Driver → Senior X | 443 | Validação, eventos, pendências |

## Senior XT — Concentradora CSM

Tudo permanece na rede do cliente. A Concentradora faz o papel da nuvem.

<figure markdown>
<svg viewBox="0 0 720 260" role="img" aria-label="Diagrama: equipamentos, driver e Concentradora CSM, todos na rede do cliente" style="width:100%;height:auto;color:currentColor">
  <g fill="none" stroke="currentColor" stroke-width="1.5">
    <rect x="12" y="40" width="688" height="200" rx="6" stroke-dasharray="5 4" opacity="0.45"/>
    <rect x="36" y="76" width="120" height="46" rx="4"/>
    <rect x="36" y="150" width="120" height="46" rx="4"/>
    <rect x="212" y="112" width="110" height="76" rx="4" stroke-width="2"/>
    <rect x="450" y="104" width="210" height="92" rx="4"/>
  </g>
  <g font-family="ui-monospace,SFMono-Regular,Menlo,monospace" font-size="11" fill="currentColor">
    <text x="20" y="32" opacity="0.6">REDE DO CLIENTE</text>
    <text x="52" y="104">Controladora</text>
    <text x="52" y="178">Controladora</text>
    <text x="234" y="146" font-size="12" font-weight="600">Driver</text>
    <text x="234" y="164" opacity="0.7">porta 5000</text>
    <text x="472" y="134" font-size="12" font-weight="600">Concentradora</text>
    <text x="472" y="152" opacity="0.7">TCP-IP (CSM)</text>
    <text x="472" y="170" opacity="0.7">servlet de fotos</text>
  </g>
  <g stroke="currentColor" stroke-width="1.5" fill="none">
    <path d="M156 92 L206 128" marker-end="url(#b)"/>
    <path d="M156 180 L206 172" marker-end="url(#b)"/>
    <path d="M206 140 L160 108" marker-end="url(#b)" opacity="0.55"/>
    <path d="M322 150 L444 150" marker-end="url(#b)"/>
  </g>
  <defs>
    <marker id="b" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="5" markerHeight="5" orient="auto">
      <path d="M0 0 L10 5 L0 10 z" fill="currentColor"/>
    </marker>
  </defs>
  <g font-family="ui-monospace,SFMono-Regular,Menlo,monospace" font-size="9.5" fill="currentColor" opacity="0.75">
    <text x="150" y="118">eventos →</text>
    <text x="150" y="132">← ISAPI</text>
    <text x="340" y="142">socket</text>
  </g>
</svg>
<figcaption>Senior XT: sem dependência de internet, mas com dois destinos — Concentradora e servlet CSM.</figcaption>
</figure>

| Sentido | Porta | Para quê |
|---|---|---|
| Equipamento → Driver | `5000` | **Entrega dos eventos de acesso** |
| Driver → Equipamento | 80 / 443 | Configuração, credenciais, sondagem |
| Driver → Concentradora | `seniorxt.port` | Protocolo CSM |
| Driver → Servlet CSM | conforme a URL | Consulta de fotos para a carga facial |

## Fluxo de uma validação

O caminho percorrido a cada passagem, igual nas duas plataformas.

<figure markdown>
<svg viewBox="0 0 720 150" role="img" aria-label="Diagrama de sequência: pessoa, equipamento, driver e plataforma Senior" style="width:100%;height:auto;color:currentColor">
  <g fill="none" stroke="currentColor" stroke-width="1.5">
    <rect x="14" y="46" width="96" height="40" rx="4"/>
    <rect x="176" y="46" width="120" height="40" rx="4"/>
    <rect x="362" y="46" width="110" height="40" rx="4" stroke-width="2"/>
    <rect x="538" y="46" width="140" height="40" rx="4"/>
  </g>
  <g font-family="ui-monospace,SFMono-Regular,Menlo,monospace" font-size="11" fill="currentColor">
    <text x="34" y="71">Pessoa</text>
    <text x="192" y="71">Equipamento</text>
    <text x="382" y="71" font-weight="600">Driver</text>
    <text x="558" y="71">Senior</text>
  </g>
  <g stroke="currentColor" stroke-width="1.5" fill="none">
    <path d="M110 60 L170 60" marker-end="url(#c)"/>
    <path d="M296 60 L356 60" marker-end="url(#c)"/>
    <path d="M472 60 L532 60" marker-end="url(#c)"/>
    <path d="M532 78 L476 78" marker-end="url(#c)" opacity="0.6"/>
    <path d="M356 78 L300 78" marker-end="url(#c)" opacity="0.6"/>
    <path d="M170 78 L114 78" marker-end="url(#c)" opacity="0.6"/>
  </g>
  <defs>
    <marker id="c" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="5" markerHeight="5" orient="auto">
      <path d="M0 0 L10 5 L0 10 z" fill="currentColor"/>
    </marker>
  </defs>
  <g font-family="ui-monospace,SFMono-Regular,Menlo,monospace" font-size="9.5" fill="currentColor" opacity="0.75">
    <text x="112" y="40">cartão / face</text>
    <text x="300" y="40">webhook</text>
    <text x="478" y="40">valida</text>
    <text x="120" y="104">← libera ou nega, em até 15 s</text>
  </g>
</svg>
<figcaption>A decisão é sempre da Senior. O equipamento aguarda a resposta pela janela de <code>seniorx.remotecheck.timeout</code>.</figcaption>
</figure>

!!! danger "O sentido que costuma faltar"
    A seta **equipamento → driver** é a que o firewall bloqueia com mais
    frequência. Sem ela, os dispositivos aparecem online e nenhuma passagem é
    validada. Ver [Rede e portas](../antes-de-instalar/rede-e-portas.md).
