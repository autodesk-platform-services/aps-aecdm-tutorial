---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

layout: home
title: Home
permalink: /
nav_order: 1
---

# Tutorial 

## Introdução

Neste tutorial, será abordada a conexão entre dados de sensores IoT com o modelo BIM, utilizando a extensão Data Visualization do Viewer.
O resultado do tutorial será um aplicativo semelhante ao exemplo abaixo, no qual é apresentado o modelo BIM "pintado" representando dados de desempenho medidos em tempo real, bem como gráficos que exibem as séries temporais da rede de sensores instalada. Este é um exemplo de Gêmeo Digital Informativo (para saber mais sobre níveis de maturidade de um Gêmeo Digital [clique aqui](https://redshift.autodesk.com/articles/what-is-a-digital-twin/)).

Os recursos de interatividade são diversos e capazes de abarcar filtros e localizações rápidas de sensores e dados de forma contextualizada. A partir deste resultado, é possível obter insights de monitoramento e controle do ambiente construído e subsidiar análises preditivas, principalmente, para Operação e Manutenção (O&M).

![Final Result](/assets/images/iot_mocked.gif)

Basicamente, o modelo (gerenciado pelo seu aplicativo APS, Autodesk Docs ou outro repositório em nuvem da Autodesk, conforme detalhado [aqui](https://forge.autodesk.com/en/docs/data/v2/developers_guide/basics/)) será renderizado no navegador com o Viewer, e a este adicionado [sprites](https://aps.autodesk.com/en/docs/dataviz/v1/developers_guide/examples/sprites/) que representarão sensores integrados com a cena do modelo, de modo que tenha-se os [mapas de calor](https://aps.autodesk.com/en/docs/dataviz/v1/developers_guide/examples/heatmap/) caracterizando os dados desses sensores de acordo com o gradiente de cores definido.

Para desenvolver essa solução há etapas cruciais, como a definição do modelo base, a base de dados dos sensores, exemplo base e customização.
É recomendável experiência com desenvolvimento Web para explorar os maiores benefícios do presente tutorial.
As ferramentas utilizadas e recomendadas para reproduzir as próximas etapas são: (i) o [Visual Studio Code](https://code.visualstudio.com/) como ambiente de desenvolvimento; (ii) e o [Node js](https://nodejs.org/en/), para desenvolver o exemplo. Tendo em vista a simplificação de algumas etapas, será adotada a [extensão do VS Code para APS(anteriormente Forge)](https://marketplace.visualstudio.com/items?itemName=petrbroz.vscode-forge-tools).

Para aqueles que estão dando os primeiro passos com a Autodesk Platform Services, recomendamos sempre este [tutorial](http://aps.autodesk.com/tutorials) como caminho introdutório!

O tutorial é dividido em 3 macro etapas:

2. [Requisitos]({{ site.baseurl }}/requisites/home/)

3. [Clonando o app base]({{ site.baseurl }}/cloning/home/)

4. [Adaptando aos seus dados]({{ site.baseurl }}/adapting/home/)
