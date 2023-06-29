---
layout: page
title: Requisitos
nav_order: 2
has_children: true
permalink: /requisites/home/
---

# Requisitos

Como mencionado na introdução do exercício, nessa primeira parte serão observados os requisitos necessários para conectar o modelo BIM com sensores IoT por meio da APS. Ao término dessa etapa, o modelo base estará pronto para conexão e disponível em nosso [bucket](https://aps.autodesk.com/en/docs/data/v2/developers_guide/basics/#object-storage-service-oss). 

1. Primeiramente, observa-se o modelo. Para esse tutorial, recomenda-se utilizar o dataset da [Vila dos Idosos](https://github.com/JoaoMartins-callmeJohn/iot-sample-tutorial/tree/main/assets/files). Sinta-se a vontade para fazer referência ao seu próprio modelo ou utilizar outros [exemplos](https://knowledge.autodesk.com/support/revit/getting-started/caas/CloudHelp/cloudhelp/2022/ENU/Revit-GetStarted/files/GUID-7B9C7A69-1083-406D-A01F-53D405C167F3-htm.html) (aconselha-se o uso de um arquivo de formato RVT). Note que será necessário que o modelo BIM possua os ambientes definidos nos locais os quais os sensores estarão posicionados ou instalados no mundo físico. Dessa forma, pode-se considerar o próximo requisito.

2. Então, configura-se a sua disponibilidade. Este requisito consiste em disponibilizar o modelo na nuvem para conversão e visualização. Há diversas formas de viabilizar o modelo na nuvem, como descrito [nesse passo a passo](https://aps.autodesk.com/en/docs/data/v2/tutorials/app-managed-bucket/). Para simplificar o exercício (agora e em futuros desenvolvimentos), pode-se utilizar uma extensão desenvolvida para o VS Code. Basta seguir o passo a passo deste [blog](https://aps.autodesk.com/blog/forge-visual-studio-code) para instalar essa extensão. 

- Uma vez a extensão instalada, pode-se criar um bucket

![create bucket](../../assets/images/create_bucket.gif)

- e fazer o upload do modelo

![upload file](../../assets/images/upload_file.gif)

- Para finalizar o processo, deve-se iniciar a tradução do arquivo. Neste momento, é de grande importância traduzir o modelo em formato RVT para gerar os ambientes. Você pode consultar mais detalhes [nesse passo-a-passo](https://aps.autodesk.com/en/docs/model-derivative/v2/tutorials/prep-roominfo4viewer/) e [nessa lightning talk](https://youtu.be/GgW9gBCRrWg?t=232). Para facilitar a tradução, pode-se usar o método customizado da nossa extensão

![upload file](../../assets/images/start_translation.gif)

Agora sim! Dessa forma, o modelo estará pronto para ser utilizado.

![model ready](../../assets/images/model_ready.gif)

[Próximo passo - Clonando o app base]({{ site.baseurl }}/cloning/home/){: .btn}
