---
layout: page
title: Adaptando aos seus dados
nav_order: 4
has_children: true
permalink: /adapting/home/
---

# Adaptando aos seus dados

Agora vamos ajustar o exemplo aos nossos dados.

## Configurando model urn e view guid

Primeiro vamos adicionar nosso modelo, vista e data de início e fim.

Basta modificarmos o arquivo [public/config.js](https://github.com/autodesk-platform-services/aps-iot-extensions-demo/blob/master/public/config.js)

- `APS_MODEL_URN` deve conter o valor da urn do nosso modelo, que pode ser facilmente obtida com a ajuda da nossa extensão através do manifesto

![get model urn](../../assets/images/get_model_urn.gif)

- `APS_MODEL_VIEW` também pode ser encontrada no mesmo manifesto

![get view guid](../../assets/images/get_view_guid.gif)

- `APS_MODEL_DEFAULT_FLOOR_INDEX`, `DEFAULT_TIMERANGE_START` e `DEFAULT_TIMERANGE_END` devem ser modificadas em concordância com o seu modelo para comtrolar nível padrão a ser exibido e o intervalo dos dados dos sensores.

Uma vez definidas essas variáveis podemos iniciar o exemplo com o comando `yarn start`

![custom model](../../assets/images/custom_model.gif)

## Ajustando os sensores

Como foi possivel ver, os sensores estão localizados em posições aleatórias, sem fazer sentido no contexto do nosso modelo.

O nosso primeiro passo é determinar a posição dos sensores e os ambientes nos quais os mesmos estão localizados.

Aqui temos duas possibilidades:

1. Os sensores estão presentes na cena como elementos do modelo:

- Nesse caso precisamos obter a posição dos sensores na cena, e para isso podemos contar com a ajuda de um método presente no blog [Placing custom markup by dbId](https://aps.autodesk.com/blog/placing-custom-markup-dbid) (fique à vontade para lê-lo quando tiver um tempo). Esse blog apresenta uma extensão com oo passo a passo para sobrepor uma div na cena do modelo. Para isso ele conta com um método para obter a posição do centro de um elemento do modelo a partir de seu dbId. A partir dessa extensão chegamos à função abaixo que pode nos ajudar a encontrar essa posição:

```js
var getModifiedWorldBoundingBox = function(dbId) {
  var fragList = NOP_VIEWER.model.getFragmentList();
  const nodebBox = new THREE.Box3();

  let frags = [];
  const tree = NOP_VIEWER.model.getInstanceTree();
  tree.enumNodeFragments(dbId, function (fragId) {
      frags.push(fragId);
  });

  // for each fragId on the list, get the bounding box
  for (const fragId of frags) {
      const fragbBox = new THREE.Box3();
      fragList.getWorldBounds(fragId, fragbBox);
      nodebBox.union(fragbBox); // create a unifed bounding box
  }

  return nodebBox
}
```

Para obter a posição central de um elemento podemos seguir as etapas
  - copiar e colar essa função no console
  - selecionar um elemento  
  - digitar `NOP_VIEWER.getSelection()` para obter seu dbId
  - digitar `getModifiedWorldBoundingBox(dbId).center()` (subctituindo dbid pelo valor encontrado na etapa anterior)

Desse modo obteremos a posição do seu centro na cena

![element center](../../assets/images/element_center.gif)

Voltando ao outro caso possível:

2. Os sensores não estão representados no modelo:

- Nesse caso podemos reagir ao clique na tela do usuário para apresentar a posição equivalente no console.

```js
NOP_VIEWER.container.addEventListener('click', function (ev) {
  const result = NOP_VIEWER.clientToWorld(ev.clientX, ev.clientY);
  if (result) {
    console.log(result.point);
  }
});
```

Basta copiar e colar esse trecho no console e clicar na posição que você deseja colocar o seu sensor

![click position](../../assets/images/click_position.gif)

Para cada sensor, é necessário também definir o dbId do ambiente no qual o mesmo está localizado.

Para isso, basta selecionar o ambiente e digitar `NOP_VIEWER.getSelection()` no console

![room dbid](../../assets/images/room_dbid.gif)

## Adaptando iot.mocked.js

Por fim, devemos ajustar o arquivo `iot.mocked.js` com os valores encontrados anteriormente para as posições dos sensores e dbids dos ambientes

![iot mocked](../../assets/images/iot_mocked.gif)

Com isso teremos nosso modelo pronto com os sensores customizados.

A próxima etapa é adicionar os dados dos sensores em tempo real, porém essa parte não será coberta nesse tutorial.