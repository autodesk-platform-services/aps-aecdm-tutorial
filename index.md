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

Neste tutorial, vamos nos concentrar na conexão entre dados de sensores com o modelo usando a extensão Data Visualization do Viewer.
Ao final do tutorial, teremos nosso app semelhante ao exemplo abaixo.

![Final Result](/assets/images/iot_mocked.gif)

Basicamente vamos renderizar um modelo (gerenciado pelo seu aplicativo APS, Autodesk Docs ou outro repositório em núvem da Autodesk. Detalhes [aqui](https://forge.autodesk.com/en/docs/data/v2/developers_guide/basics/)) no navegador com o Viewer, adicionando [sprites](https://aps.autodesk.com/en/docs/dataviz/v1/developers_guide/examples/sprites/) que representarão sensores integrados com a nossa cena do modelo, de modo que tenhamos os [mapas de calor](https://aps.autodesk.com/en/docs/dataviz/v1/developers_guide/examples/heatmap/) representando os dados desses sensores de acordo com o gradiente de cores definido.

Para montar essa solução iremos passar por algumas etapas cruciais como definição do modelo base, dados dos sensores, exemplo base e custmização.
É recomendável experiência com desenvolvimento WEB para tirar maior proveito desse tutorial.
Nesse tutorial iremos utilizar o [Visual Studio Code](https://code.visualstudio.com/) como ambiente de desenvolvimento. O exemplo foi desenvolvido usando [Node js](https://nodejs.org/en/). Para simplificar algumas etapas, vamos usar a [extensão do VS Code para APS(anteriormente Forge)](https://marketplace.visualstudio.com/items?itemName=petrbroz.vscode-forge-tools).

Temos outro tutorial para quem está dando os primeiros passos com a Autodesk Platform Services [aqui](http://aps.autodesk.com/tutorials). Recomendamos sempre ele como primeiro destino ;)

O tutorial é dividido em 3 macro etapas:

2. [Requisitos]({{ site.baseurl }}/requisites/home/)

3. [Clonando o app base]({{ site.baseurl }}/cloning/home/)

4. [Adaptando aos seus dados]({{ site.baseurl }}/adapting/home/)
