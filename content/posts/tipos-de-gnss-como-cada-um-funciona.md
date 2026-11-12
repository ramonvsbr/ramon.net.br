---
title: 'Tipos de GNSS: como cada um funciona'
date: 2026-11-11T21:59:00
summary: Guia ilustrado, organizado em torno de três perguntas que separam os conceitos. Depois delas, cada tecnologia é explicada com seu GIF, do mais simples (celular) ao mais sofisticado.
tags:
  - georreferenciamento
draft: false
---

Guia ilustrado, organizado em torno de **três perguntas** que separam os conceitos. Depois delas, cada tecnologia é explicada com seu GIF, do mais simples (celular) ao mais sofisticado.
## A ideia central

Todo receptor GNSS sofre dos mesmos erros: **atmosfera** (ionosfera e troposfera), **imprecisão de órbita** e **erro do relógio do satélite**. Para corrigi-los, existem só **duas grandes famílias de solução**:

1. **Diferencial (base + rover):** uma segunda estação, em coordenada conhecida, mede os erros e você os "subtrai". A correção vem de uma base, própria ou de rede.
2. **Absoluto refinado (PPP):** um único receptor, sem base nenhuma, compensa os erros usando **órbitas e relógios de satélite ultraprecisos**, calculados por instituições como o IGS.

Praticamente tudo se resume a três perguntas.

---

## Pergunta 1: usa base ou não?

**Usa base (método diferencial):**

- **RTK:** a base transmite correções ao rover **em tempo real** (rádio ou internet/NTRIP). O rover chega ao "FIX" (centímetros) em segundos.
- **PPK:** base e rover **gravam** os dados separadamente, e a "conta" é feita **depois, no escritório**. Não há link em campo.
- **DGPS:** mesma lógica do RTK, mas trabalhando só com o código (menos preciso, ~0,5 a 1 m).

**Não usa base (método absoluto):**

- **PPP:** um receptor sozinho mais produtos precisos de órbita e relógio. O preço é a **convergência**: centímetros só depois de dezenas de minutos a horas de rastreio.

## Pergunta 2: quando o processamento acontece?


| Método | Onde/quando a correção é aplicada |
|---|---|
| RTK | No campo, em tempo real (precisa de link o tempo todo) |
| PPK | No escritório, depois (não precisa de link nenhum em campo) |
| PPP via IBGE-PPP | Depois: você envia o arquivo RINEX ao site do IBGE |
| PPP em tempo real (ex.: RTX) | No campo, mas via serviço comercial pago |

Ou seja: **RTK e PPK são a mesma matemática**, diferindo só pelo momento. E o PPP pode ser feito dos dois jeitos: o que o define é a **ausência de base**, e não o momento.

## Pergunta 3: e a RBMC, onde entra?

A RBMC **não é uma técnica**, é **infraestrutura**. É uma rede de estações fixas do IBGE que rastreiam satélites 24 h por dia e publicam os dados em RINEX. Ela resolve o problema prático: "não tenho base própria, e agora?".

- **No PPK:** em vez de deixar um receptor seu parado num ponto conhecido, você baixa os dados da **estação RBMC mais próxima** e a usa como base no processamento. É uma base que você "empresta".
- **No PPP:** a RBMC nem entra, pois o PPP não precisa de base. O serviço do IBGE usado aqui é outro, o **IBGE-PPP**: você envia o RINEX do seu receptor e recebe as coordenadas em SIRGAS2000.
- **No RTK:** algumas estações transmitem correções via internet (RBMC-IP) e funcionam como base em tempo real, mas a cobertura é mais limitada que a das redes privadas.

---

## Resumo comparativo

| # | Tipo | Observável usado | Precisa de base/serviço? | Tempo real? | Precisão típica |
|---|---|---|---|---|---|
| 1 | GNSS autônomo (celular) | Código | Não | Sim | ~3 a 5 m |
| 2 | A-GPS (celular) | Código + dados de assistência | Rede celular/Wi-Fi | Sim | ~3 a 5 m (fix mais rápido) |
| 3 | SBAS (WAAS, EGNOS) | Código + correção regional | Satélite SBAS | Sim | ~1 a 3 m |
| 4 | DGPS | Código + correção local | Base em ponto conhecido | Sim | ~0,5 a 1 m |
| 5 | PPP | Fase + órbitas/relógios precisos | Produtos de órbita (sem base) | Pós ou tempo real | dm → cm, após convergência |
| 6 | RTK | Fase da portadora | Base própria ou rede | Sim | ~1 a 3 cm |
| 7 | PPK | Fase da portadora | Base própria ou RBMC | Não (pós-processado) | ~1 a 3 cm |
| 8 | RTX | PPP + modelos atmosféricos regionais | Serviço comercial (satélite/internet) | Sim | ~2 a 5 cm (varia por região) |

### RTK × PPK × PPP

| | **RTK** | **PPK** | **PPP** |
|---|---|---|---|
| Usa dados de base? | Sim | Sim | Não |
| Quando a correção é aplicada | Na hora | Depois | Depois ou na hora |
| Tipo de correção | Diferencial (base/rede) | Diferencial (base/RBMC) | Órbitas e relógios precisos |
| Convergência | Segundos | Rápida (a base ancora a solução) | 15 a 40 min (mais rápida no PPP-RTK) |

**Observações que amarram tudo:**

- **RTK/PPK** nas fichas de equipamentos é a **combinação** de duas técnicas no mesmo aparelho (tempo real + plano B), e não uma técnica nova.
- **PPP-RTK** é uma técnica própria: une a cobertura do PPP à rápida resolução de ambiguidades do RTK, usando correções de atmosfera e vieses.
- O **IBGE-PPP** é muito usado para **determinar a coordenada precisa de uma base própria**, que depois alimenta o RTK/PPK. As técnicas se complementam: o PPP dá a coordenada da base, e o RTK/PPK usam essa base no levantamento.

## Árvore das tecnologias

![Árvore das tecnologias GNSS](/img/tree_gnss.png)


---

# Detalhamento de cada tecnologia

## 1 · GNSS autônomo (celular)

![GNSS autônomo](/img/01_celular_gnss.gif)

O receptor mede o tempo que o sinal de cada satélite leva para chegar (**pseudodistância pelo código**). Com quatro ou mais satélites, calcula posição e erro do relógio. Não há nenhuma correção externa, então ionosfera, troposfera e imprecisão de órbita entram direto no erro.

## 2 · A-GPS (celular)

![A-GPS](/img/02_celular_agps.gif)

É o mesmo GNSS do item 1, mas a rede celular ou o Wi-Fi envia **dados de assistência** (efemérides, hora e posição aproximada). O celular acha os satélites em segundos, em vez de dezenas de segundos. A precisão final continua parecida com a do GNSS autônomo, e a melhoria está na **velocidade** do primeiro posicionamento.

## 3 · SBAS (WAAS, EGNOS e similares)

![SBAS](/img/03_sbas.gif)

Estações de referência medem os erros do sistema e enviam as correções a um satélite **geoestacionário**, que as retransmite ao receptor. São correções **gerais para a região**, e não específicas da sua posição. Exige receptor compatível.

## 4 · DGPS

![DGPS](/img/04_dgps.gif)

Uma **base em coordenada conhecida** compara a posição medida com a real, calcula o erro de cada satélite e o transmite por rádio ao rover. Como o rover vê quase os mesmos erros que a base, ele os subtrai. É a mesma lógica do RTK, só que com o **código** em vez da fase. A correção é local, por isso a precisão cai conforme a distância da base aumenta.

## 5 · PPP (Precise Point Positioning)

![PPP](/img/05_ppp.gif)

Um único receptor, **sem base**, usa órbitas e relógios de satélite muito precisos (produzidos, por exemplo, pelo IGS) e processa a **fase da portadora**. O preço é a **convergência**: a precisão de centímetros só aparece após dezenas de minutos. No Brasil, o IBGE-PPP faz o pós-processamento gratuitamente.

## 6 · RTK (Real Time Kinematic)

![RTK](/img/06_rtk.gif)

Uma base (própria ou de uma rede, no Network RTK) envia **correções de fase da portadora em tempo real**. O rover resolve as ambiguidades inteiras e chega ao estado **FIX** em segundos, com precisão de centímetros. Exige link de rádio ou internet e a base ligada durante todo o levantamento, e a precisão cai com a distância da base (geralmente até 20 a 30 km).

## 7 · PPK (Post-Processed Kinematic)

![PPK](/img/07_ppk.gif)

Mesma matemática do RTK, mas **sem link em campo**. Base e rover gravam dados brutos, e a correção é feita **depois, no escritório**, processando para frente e para trás no tempo. É comum em drones de mapeamento e em áreas sem sinal. A base pode ser uma estação da RBMC, desde que próxima e com dados cobrindo o período do levantamento.

## 8 · RTX (Trimble CenterPoint RTX)

![RTX](/img/08_rtx.gif)

Serviço comercial que segue a lógica do PPP **em tempo real**, somada a **modelos atmosféricos regionais** para acelerar a convergência. As correções chegam por satélite geoestacionário (L-band) ou internet, dispensando base e estação de rede. Funciona em áreas remotas, mas é pago e proprietário, e o desempenho muda conforme a região e o receptor. Há serviços equivalentes de outras empresas, como TerraStar (Hexagon) e StarFire (John Deere).

---

## Serviços do IBGE para o usuário brasileiro

Os dois serviços abaixo não são técnicas novas. São **infraestrutura oficial** que alimenta o PPK e o PPP no Brasil.

### 9 · RBMC (Rede Brasileira de Monitoramento Contínuo)

![RBMC](/img/09_rbmc.gif)

Rede de **estações GNSS fixas**, que rastreiam os satélites o tempo todo e têm coordenadas muito bem determinadas no **SIRGAS2000**. O IBGE disponibiliza os dados no formato **RINEX**, e a rede oferece ao usuário:

- **Dados para pós-processamento:** serve de **base** para PPK e para levantamentos estáticos, dispensando montar base própria. A estação precisa estar relativamente perto, e o arquivo deve cobrir o período do seu levantamento.
- **Tempo real (RBMC-IP):** algumas estações transmitem correções via internet (NTRIP), usáveis em RTK. A cobertura em tempo real é mais limitada do que a das redes privadas, então confira quais estações estão ativas na sua região.
- **Referência oficial:** é a base do georreferenciamento, pois amarra os trabalhos ao sistema geodésico brasileiro.

### 10 · IBGE-PPP

![IBGE-PPP](/img/10_ibge_ppp.gif)

Serviço **online e gratuito** de **PPP pós-processado**. O fluxo é:

1. Você grava dados com um receptor GNSS e converte para **RINEX**, se necessário.
2. Envia o arquivo ao serviço do IBGE, **sem precisar de base**.
3. O serviço aplica **órbitas e relógios precisos** (produtos do IGS) e devolve as coordenadas, em **SIRGAS2000** e também em ITRF.

Serve bem para **ponto isolado** e para **dar coordenada precisa a uma base** própria, que depois é usada em RTK ou PPK. A precisão depende do tempo de rastreio, do tipo de receptor (uma ou duas frequências) e da qualidade dos dados, e fica melhor com sessões longas. Para centímetros, sessões curtas não costumam bastar.

> Os serviços do IBGE podem sofrer instabilidade ou mudar de regras e formatos. Confira o portal do IBGE antes de planejar um levantamento.
