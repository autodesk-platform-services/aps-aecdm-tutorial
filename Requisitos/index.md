---
layout: page
title: Requisitos
nav_order: 2
has_children: true
permalink: /requisites/home/
---

# Requisitos

Como dito em nossa introdução, nessa primeira parte cuidaremos dos requisitos necessários para conectar nosso modelo com sensores por meio da APS.
Ao término dessa etapa devemos ter nosso modelo base pronto para conexão, disponível em nosso [bucket](https://aps.autodesk.com/en/docs/data/v2/developers_guide/basics/#object-storage-service-oss). 

1. Primeiramente vamos ao modelo: Para esse tutorial, utilizaremos o exemplo [enviado pela Fernanda](). Fique à vontade para fazer referência ao seu próprio ou usar um dos nossos [exemplos](https://knowledge.autodesk.com/support/revit/getting-started/caas/CloudHelp/cloudhelp/2022/ENU/Revit-GetStarted/files/GUID-7B9C7A69-1083-406D-A01F-53D405C167F3-htm.html)(Um arquivo RVT é fortemente recomendado). Note que para esse tutorial, será necessário que os modelos tenham os ambientes definidos nos locar onde os sensores ficarão posicionados. Com isso podemos seguir para o próximo requisito

2. O próximo requisito consiste em disponibilizar nosso modelo na nuvem para conversão e visualização. Há diversas formas de conseguir isso, como descrito [nesse passo a passo](https://aps.autodesk.com/en/docs/data/v2/tutorials/app-managed-bucket/), porém para simplificar nosso trabalho (agora e em futuros desenvolvimentos) vamo usar uma extensão desenvolvida para o VS Code. Basta seguir o passo a passo desse blog: https://aps.autodesk.com/blog/forge-visual-studio-code para instalar essa extensão. 

- Uma vez com a extensão instalada, vamos criar um bucket
![create bucket](/assets/images/create_bucket)
- e fazer o upload do nosso modelo
![upload file](/assets/images/upload_file)
- Para concluir essa parte, devemos iniciar a tradução do arquivo. Aqui é de extrema importância traduzirmos o modelo rvt gerando os ambientes. Você pode conferir mais detalhes [nesse passo-a-passo](https://aps.autodesk.com/en/docs/model-derivative/v2/tutorials/prep-roominfo4viewer/) e [nessa lightning talk](https://youtu.be/GgW9gBCRrWg?t=232). Para facilitar, podemos usar o método customizado da nossa extensão

Legal! Com isso já temos nosso modelo pronto para ser utilizado.
![model ready](/assets/images/model_ready)

[Próximo passo - Phasing Extension]({{ site.baseurl }}/building/phasingextension/){: .btn}
