---
layout: page
title: Requisitos
nav_order: 2
has_children: true
permalink: /requisites/home/
---

# Requisitos

Como apresentado na introdução, nessa primeira parte serão abordados os requisitos necessários para conectar o modelo BIM com sensores IoT por meio da APS. Ao término dessa etapa, o modelo base estará pronto para conexão e disponível no seu [bucket](https://aps.autodesk.com/en/docs/data/v2/developers_guide/basics/#object-storage-service-oss).

1. Inicialmente trataremos o modelo. Para esse tutorial, será utilizado o exemplo da [Vila dos Idosos](https://github.com/JoaoMartins-callmeJohn/iot-sample-tutorial/tree/main/assets/files). Sinta-se a vontade para fazer referência ao seu próprio modelo ou utilizar outros [exemplos](https://knowledge.autodesk.com/support/revit/getting-started/caas/CloudHelp/cloudhelp/2024/ENU/Revit-GetStarted/files/GUID-7B9C7A69-1083-406D-A01F-53D405C167F3-htm.html) (um arquivo RVT é recomendado). Note que para esse tutorial, será necessário que os modelos tenham os ambientes definidos nos locais onde os sensores ficarão posicionados.

2. O próximo requisito consiste em disponibilizar o modelo na nuvem para conversão e visualização. Há diversas formas de realizar essa tarefa, como descrito [nesse passo a passo](https://aps.autodesk.com/en/docs/data/v2/tutorials/app-managed-bucket/), porém para simplificar este tutorial (agora e em futuros desenvolvimentos) recomenda-se o uso de uma extensão desenvolvida para o VS Code. Para tanto, é apenas seguir o passo a passo desse blog: https://aps.autodesk.com/blog/forge-visual-studio-code para instalar essa extensão.

- Uma vez a extensão instalada, cria-se um bucket, conforme imagem abaixo:

![create bucket](../../assets/images/create_bucket.gif)

- E faz-se o upload do modelo:

  ![upload file](../../assets/images/upload_file.gif)

- Para concluir essa parte, deve-se iniciar a tradução do arquivo. Aqui é de extrema importância traduzir o modelo BIM gerando os ambientes. Você pode conferir mais detalhes [nesse passo-a-passo](https://aps.autodesk.com/en/docs/model-derivative/v2/tutorials/prep-roominfo4viewer/) e [nessa lightning talk](https://youtu.be/GgW9gBCRrWg?t=232). Para facilitar, pode-se utilizar o método customizado da extensão

![upload file](../../assets/images/start_translation.gif)

Agora sim! Dessa forma o modelo estará pronto para ser utilizado.

![model ready](../../assets/images/model_ready.gif)

[Próximo passo - Clonando o app base]({{ site.baseurl }}/cloning/home/){: .btn}
