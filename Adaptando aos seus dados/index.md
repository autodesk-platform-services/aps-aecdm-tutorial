---
layout: page
title: Adaptando aos seus dados
nav_order: 4
has_children: true
permalink: /adapting/home/
---

# Adaptando aos seus dados

Agora vamos ajustar o exemplo aos nossos dados.

Primeiro vamos adicionar nosso modelo, vista e data de início e fim.

Basta modificarmos o arquivo [public/config.js](https://github.com/autodesk-platform-services/aps-iot-extensions-demo/blob/master/public/config.js)

- `APS_MODEL_URN` deve conter o valor da urn do nosso modelo, que pode ser facilmente obtida com a ajuda da nossa extensão através do manifesto

![get model urn](../../assets/images/get_model_urn.gif)

- `APS_MODEL_VIEW` também pode ser encontrada no mesmo manifesto

![get view guid](../../assets/images/get_view_guid.gif)

- `APS_MODEL_DEFAULT_FLOOR_INDEX`, `DEFAULT_TIMERANGE_START` e `DEFAULT_TIMERANGE_END` devem ser modificadas em concordância com o seu modelo para comtrolar nível padrão a ser exibido e o intervalo dos dados dos sensores.

Uma vez definidas essas variáveis podemos iniciar o exemplo com o comando `yarn start`

![custom model](../../assets/images/custom_model.gif)

Porém, como é possivel ver, os sensores estão localizados em posições aleatórias, sem fazer sentido no contexto do nosso modelo.

