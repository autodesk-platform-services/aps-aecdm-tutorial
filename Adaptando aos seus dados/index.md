---
layout: page
title: Adaptando aos seus dados
nav_order: 4
has_children: true
permalink: /adapting/home/
---

# Adaptando aos seus dados

Finalmente, o exemplo será ajustado aos dados do modelo e sensores.

## Configurando model urn e view guid

Primeiro, deve-se adicionar o modelo, vista e datas de início e fim da série temporal.

Basta modificar o arquivo [public/config.js](https://github.com/autodesk-platform-services/aps-iot-extensions-demo/blob/master/public/config.js)

- `APS_MODEL_URN` deve conter o valor da urn do modelo, que pode ser facilmente obtida com a ajuda da nossa extensão através do manifesto

![get model urn](../../assets/images/get_model_urn.gif)

- `APS_MODEL_VIEW` também pode ser encontrada no mesmo manifesto

![get view guid](../../assets/images/get_view_guid.gif)

- `APS_MODEL_DEFAULT_FLOOR_INDEX`, `DEFAULT_TIMERANGE_START` e `DEFAULT_TIMERANGE_END` devem ser modificadas em concordância com o modelo para controlar nível padrão a ser exibido e o intervalo dos dados dos sensores.

Uma vez definidas essas variáveis, pode-se iniciar o exemplo com o comando `yarn start`

![custom model](../../assets/images/custom_model.gif)

## Ajustando os sensores

Como é possivel visualizar, os sensores estão localizados em posições aleatórias, ainda sem fazer sentido no contexto do modelo.

O primeiro passo é determinar a posição dos sensores e os ambientes os quais os mesmos estão localizados.

Neste ponto, há duas possibilidades:

1. Os sensores estão presentes na cena como elementos do modelo:

- Nesse caso, precisa-se obter a posição dos sensores na cena, e para isso pode-se utilizar como referência o seguinte método presente no blog [Placing custom markup by dbId](https://aps.autodesk.com/blog/placing-custom-markup-dbid) (sinta-se à vontade para lê-lo quando tiver tempo). Esse blog apresenta uma extensão com o passo a passo para sobrepor uma div na cena do modelo. Para isso, ele conta com um método para obter a posição do centro de um elemento do modelo a partir de seu dbId. A partir dessa extensão, chega-se à função abaixo que pode auxiliar a encontrar essa posição:

```js
var getModifiedWorldBoundingBox = function (dbId) {
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

  return nodebBox;
};
```

Para obter a posição central de um elemento, pode-se seguir as etapas

- copiar e colar essa função no console

![copy n paste](../../assets/images/copy_paste_function_console.gif)

- selecionar um elemento e digitar `NOP_VIEWER.getSelection()` no console para obter seu dbId

![selected element dbid](../../assets/images/selected_element_dbid.gif)

- digitar `getModifiedWorldBoundingBox(dbId).center()` (substituindo dbid pelo valor encontrado na etapa anterior)

![element center](../../assets/images/element_center.gif)

Desse modo, obtem-se a posição do seu centro na cena.

No caso deste exemplo seria `x: 102.20546340942383, y: -184.69332885742188, z: -14.37007999420166`

Voltando ao outro caso possível:

2. Os sensores não estão representados no modelo:

- Nesse caso pode-se reagir ao clique na tela do usuário para apresentar a posição equivalente no console.

```js
NOP_VIEWER.container.addEventListener("click", function (ev) {
  const result = NOP_VIEWER.clientToWorld(ev.clientX, ev.clientY);
  if (result) {
    console.log(result.point);
  }
});
```

Basta copiar e colar esse trecho no console e clicar na posição que você deseja posicionar o seu sensor

![click position](../../assets/images/click_position.gif)

Para cada sensor, é necessário também definir o dbId do ambiente no qual o mesmo está localizado.

Para isso, basta selecionar o ambiente e digitar `NOP_VIEWER.getSelection()` no console

![room dbid](../../assets/images/room_dbid.gif)

## Adaptando iot.mocked.js

Por fim, devemos ajustar o arquivo `iot.mocked.js` com os valores encontrados anteriormente para as posições dos sensores e dbids dos ambientes

No nosso caso, o arquivo ficou da seguite maneira

```js
const SENSORS = {
  "sensor-1": {
    name: "Sala TV/Jogos 39",
    description: "Sensor localizado na sala de jogos",
    groupName: "Level 1",
    location: {
      x: 109.73640074812874,
      y: -171.81602478027344,
      z: -14.284410060014096,
    },
    objectId: 50165,
  },
  "sensor-2": {
    name: "Quitinete Tipo 02 85",
    description: "Sensor localizado na quitinete 85",
    groupName: "Level 1",
    location: {
      x: 116.07907556072144,
      y: -148.61705993028409,
      z: -6.135171890258846,
    },
    objectId: 50800,
  },
  "sensor-3": {
    name: "Hall 45",
    description: "Sensor localizado no hall",
    groupName: "Level 1",
    location: {
      x: 106.51551963071785,
      y: -157.87245178222656,
      z: -13.44903700221414,
    },
    objectId: 50313,
  },
  "sensor-4": {
    name: "APTO 1D Tipo 02 141",
    description: "Sensor localizado no apartamento 141",
    groupName: "Level 2",
    location: {
      x: 83.50466918945312,
      y: -184.515209112987,
      z: -13.674205920613701,
    },
    objectId: 52421,
  },
};

const CHANNELS = {
  temp: {
    name: "Temperature",
    description: "External temperature in degrees Celsius.",
    type: "double",
    unit: "°C",
    min: 18.0,
    max: 28.0,
  },
  co2: {
    name: "CO₂",
    description: "Level of carbon dioxide.",
    type: "double",
    unit: "ppm",
    min: 482.81,
    max: 640.0,
  },
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
      "sensor-1": {
        temp: generateRandomValues(18.0, 28.0, resolution, 1.0),
        co2: generateRandomValues(540.0, 600.0, resolution, 5.0),
      },
      "sensor-2": {
        temp: generateRandomValues(20.0, 24.0, resolution, 1.0),
        co2: generateRandomValues(540.0, 600.0, resolution, 5.0),
      },
      "sensor-3": {
        temp: generateRandomValues(24.0, 28.0, resolution, 1.0),
        co2: generateRandomValues(500.0, 620.0, resolution, 5.0),
      },
      "sensor-4": {
        temp: generateRandomValues(20.0, 24.0, resolution, 1.0),
        co2: generateRandomValues(600.0, 640.0, resolution, 5.0),
      },
    },
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
  getSamples,
};
```

![iot mocked](../../assets/images/iot_mocked.gif)

Com isso, o modelo estará pronto e com os sensores customizados.

A próxima etapa consiste em adicionar os dados dos sensores IoT em tempo real, entretanto essa parte não será abordada nesse tutorial.

A integração com dados de sensores em tempo real depende de conexões com bancos de dados externos, variando de acordo com o provedor utilizado.

Abaixo segue um diagrama simplificado da arquitetura de uma integração com conexão em tempo real.

![system architecture simple](../../assets/images/system-architecture-simple.png)
