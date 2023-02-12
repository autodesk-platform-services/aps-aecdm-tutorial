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

![copy n paste](../../assets/images/copy_paste_function_console.gif)

  - selecionar um elemento  e digitar `NOP_VIEWER.getSelection()` no console para obter seu dbId

![selected element dbid](../../assets/images/selected_element_dbid.gif)

  - digitar `getModifiedWorldBoundingBox(dbId).center()` (subctituindo dbid pelo valor encontrado na etapa anterior)

![element center](../../assets/images/element_center.gif)

Desse modo obteremos a posição do seu centro na cena.

No caso do nosso exemplo seria `x: 102.20546340942383, y: -184.69332885742188, z: -14.37007999420166`

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

No nosso caso, o arquivo ficou da seguite maneira

```js
const SENSORS = {
  'sensor-1': {
    name: 'Living Room',
    description: 'Basic sensor in the middle of the living room.',
    groupName: 'Level 1',
    location: {
      x: 31.92,
      y: 11.49,
      z: -12.97
    },
    objectId: 4124
  },
  'sensor-2': {
    name: 'Dining Table',
    description: 'Basic sensor at the dining table.',
    groupName: 'Level 1',
    location: {
      x: -10,
      y: 41.64,
      z: -12.15
    },
    objectId: 4111
  },
  'sensor-3': {
    name: 'Kitchen',
    description: 'Basic sensor in the kitchen.',
    groupName: 'Level 1',
    location: {
      x: 10,
      y: 41.64,
      z: -12.15
    },
    objectId: 4111
  },
  'sensor-4': {
    name: 'Bedroom',
    description: 'Basic sensor in the bedroom.',
    groupName: 'Level 2',
    location: {
      x: -7.46,
      y: 41.47,
      z: 2.97
    },
    objectId: 4085
  }
};

const CHANNELS = {
  'temp': {
    name: 'Temperature',
    description: 'External temperature in degrees Celsius.',
    type: 'double',
    unit: '°C',
    min: 18.0,
    max: 28.0
  },
  'co2': {
    name: 'CO₂',
    description: 'Level of carbon dioxide.',
    type: 'double',
    unit: 'ppm',
    min: 482.81,
    max: 640.00
  }
};

async function getSensors() {
    return SENSORS;
}

async function getChannels() {
    return CHANNELS;
}

async function getSamples(timerange, resolution = 32) {
    return {
        count: resolution,
        timestamps: generateTimestamps(timerange.start, timerange.end, resolution),
        data: {
            'sensor-1': {
                'temp': generateRandomValues(18.0, 28.0, resolution, 1.0),
                'co2': generateRandomValues(540.0, 600.0, resolution, 5.0)
            },
            'sensor-2': {
                'temp': generateRandomValues(20.0, 24.0, resolution, 1.0),
                'co2': generateRandomValues(540.0, 600.0, resolution, 5.0)
            },
            'sensor-3': {
                'temp': generateRandomValues(24.0, 28.0, resolution, 1.0),
                'co2': generateRandomValues(500.0, 620.0, resolution, 5.0)
            },
            'sensor-4': {
                'temp': generateRandomValues(20.0, 24.0, resolution, 1.0),
                'co2': generateRandomValues(600.0, 640.0, resolution, 5.0)
            }
        }
    };
}

function generateTimestamps(start, end, count) {
    const delta = Math.floor((end.getTime() - start.getTime()) / (count - 1));
    const timestamps = [];
    for (let i = 0; i < count; i++) {
        timestamps.push(new Date(start.getTime() + i * delta));
    }
    return timestamps;
}

function generateRandomValues(min, max, count, maxDelta) {
    const values = [];
    let lastValue = min + Math.random() * (max - min);
    for (let i = 0; i < count; i++) {
        values.push(lastValue);
        lastValue += (Math.random() - 0.5) * 2.0 * maxDelta;
        if (lastValue > max) {
            lastValue = max;
        }
        if (lastValue < min) {
            lastValue = min;
        }
    }
    return values;
}

module.exports = {
    getSensors,
    getChannels,
    getSamples
};
```

![iot mocked](../../assets/images/iot_mocked.gif)

Com isso teremos nosso modelo pronto com os sensores customizados.

A próxima etapa é adicionar os dados dos sensores em tempo real, porém essa parte não será coberta nesse tutorial.