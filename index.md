---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

layout: home
title: Home
permalink: /
nav_order: 1
---

# Construction Phasing Tutorial

## Introdução

Neste tutorial, vamos nos concentrar na construção de uma extensão para faseamento de construção dentro do Viewer.

![Final Result](/assets/images/complete.gif)

Basicamente vamos renderizar um modelo (gerenciado pelo seu aplicativo Forge, Autodesk Docs ou outro repositório em núvem da Autodesk. Detalhes [aqui](https://forge.autodesk.com/en/docs/data/v2/developers_guide/basics/)) no navegador com o Viewer, adicionando um [gráfico de Gantt](https://en.wikipedia.org/wiki/Gantt_chart) conectado com o modelo, de modo que tenhamos os elementos vinculados a diversas atividades construtivas.

Legal! Para atingir esse objetivo precisaremos de alguns pré-requisitos:

1. Primeiramente, se você está dando seus primeiros passos com Forge, vá até o nosso tutorial [Getting Started](https://forge-tutorials.autodesk.io) and [Environment Setup](https://forge-tutorials.autodesk.io/setup/).

2. Este tutorial será focado na criação de uma [Extensão do Viewer](https://forge.autodesk.com/en/docs/viewer/v7/developers_guide/viewer_basics/extensions/), tendo como base um aplicativo já definido. Se você não tem um, pode usar o exemplo [Simple Viewer](https://forge-tutorials.autodesk.io/tutorials/simple-viewer/) ou [Hubs Browser](https://forge-tutorials.autodesk.io/tutorials/hubs-browser/) em Node.js ou .NET.

3. Para construir essa extensão, usaremos o exemplo [Basic Extension](https://forge-tutorials.autodesk.io/tutorials/dashboard/basic) do [tutorial de Forge](https://forge-tutorials.autodesk.io), mas não é necessário ir até lá. O arquivo `BaseExtension.js` será disado na próxima etapa desse tutorial.

4. Se ainda não tem um modelo para teste, use um da nossa seção de [exemplos](https://knowledge.autodesk.com/support/revit/getting-started/caas/CloudHelp/cloudhelp/2022/ENU/Revit-GetStarted/files/GUID-7B9C7A69-1083-406D-A01F-53D405C167F3-htm.html) (Um arquivo RVT é fortemente recomendado).

Uma com os pré-requisitos garantidos, passe para a próxima etapa do tutorial.

O tutorial é dividido em 4 macro etapas:

1. [Introdução]({{ site.baseurl }}/input/index)

2. [Adicionando o Gráfico de Gantt]({{ site.baseurl }}/building/home/)

3. [Conectando o gráfico com o modelo]({{ site.baseurl }}/connecting/home/)

4. [Otimizando a experiência]({{ site.baseurl }}/improving/home/)
