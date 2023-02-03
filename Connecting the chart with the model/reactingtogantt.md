---
layout: page
title: Reacting to Gantt Chart
nav_order: 1
has_children: false
parent: Connecting the chart with the model
permalink: /connecting/reacting/
---

# Reagindo a eventos do gráfico

Com nossas tarefas definidas, precisamos mapear cada uma com o elemento pertinente renderizado no Viewer.
Para fazer isso, precisamos recuperar as propriedades dos elementos logo após a entrada do arquivo csv.

Esse é o nosso primeiro passo! Através do método [getbulkProperties](https://forge.autodesk.com/en/docs/viewer/v7/reference/Viewing/Model/#getbulkproperties-dbids-options-onsuccesscallback-onerrorcallback).
Para este exemplo, mapeamos tarefas e elementos com base no valor de uma propriedade definida, e essa propriedade será definida pelo arquivo `config.js`.
Vamos adicionar dois campos no arquivo de configuração:
 Um para armazenar os objetos e um para armazenar tarefas e propriedades, assim como o trecho abaixo:

```js
export const phasing_config = {
  "propFilter": "Type Name",
  "tasks": [],
  "requiredProps": {
    "id": "ID",
    "taskName": "NAME",
    "startDate": "START",
    "endDate": "END",
    "taskProgress": "PROGRESS",
    "dependencies": "DEPENDENCIES"
  },
  ========START OF THE  ADDITIONAL CONTENT========
  "objects": {},
  "mapTaksNProps": {}
  ========END OF THE  ADDITIONAL CONTENT========
}
```

Para esta demonstração, usaremos a propriedade `Type Name` para mapeamento.
Com isso, podemos definir o método para mapear tarefas e elementos.
Adicione o conteúdo abaixo dentro da função `update` da classe `PhasingPanel`:

```js
...
update(model, dbids) {
  if (phasing_config.tasks.length === 0) {
    this.inputCSV();
  }
  ========START OF THE  ADDITIONAL CONTENT========
  model.getBulkProperties(dbids, { propFilter: phasing_config.propFilter }, (results) => {
    results.map((result => {
      this.updateObjects(result);
    }))
  }, (err) => {
    console.error(err);
  });
  ========END OF THE  ADDITIONAL CONTENT========
  if (phasing_config.tasks.length > 0) {
    this.gantt = this.createGanttChart();
  }
}
========START OF THE  ADDITIONAL CONTENT========
updateObjects(result) {
  let currentTaskId = phasing_config.mapTaksNProps[result.properties[0].displayValue];
  if (!!currentTaskId) {
    if (!phasing_config.objects[currentTaskId])
      phasing_config.objects[currentTaskId] = [];

    phasing_config.objects[currentTaskId].push(result.dbId);
  }
}
========END OF THE  ADDITIONAL CONTENT========
...
```

E isso cobre os elementos, agora precisamos implementar o campo `mapTaksNProps` com os valores da nossa entrada.
Para isso, vamos aproveitar a função `addPropToMap` chamada toda vez que lemos o csv.

Adicione a função `addPropToMap` dentro da classe `PhasingPanel`:

```js
...
updateObjects(result) {
  let currentTaskId = phasing_config.mapTaksNProps[result.properties[0].displayValue];
  if (!!currentTaskId) {
    if (!phasing_config.objects[currentTaskId])
      phasing_config.objects[currentTaskId] = [];

    phasing_config.objects[currentTaskId].push(result.dbId);
  }
}
========START OF THE  ADDITIONAL CONTENT========
addPropToMap(filterValue, taskId) {
  phasing_config.mapTaksNProps[filterValue] = taskId;
}
========END OF THE  ADDITIONAL CONTENT========
```

E referenciá-lo dentro da função `lineToObject`:

```js
...
//This function converts a line from imported csv into an object to generate the GANTT chart
lineToObject(line, inputHeadersLine) {
  let parameters = line.split(',');
  let inputHeaders = inputHeadersLine.split(',');
  let newObject = {};
  Object.values(phasing_config.requiredProps).forEach(requiredProp => {
    newObject.id = parameters[inputHeaders.findIndex(h => h === phasing_config.requiredProps.id)];
    newObject.name = parameters[inputHeaders.findIndex(h => h === phasing_config.requiredProps.taskName)];
    newObject.start = parameters[inputHeaders.findIndex(h => h === phasing_config.requiredProps.startDate)];
    newObject.end = parameters[inputHeaders.findIndex(h => h === phasing_config.requiredProps.endDate)];
    newObject.progress = parameters[inputHeaders.findIndex(h => h === phasing_config.requiredProps.taskProgress)];
    newObject.dependencies = parameters[inputHeaders.findIndex(h => h === phasing_config.requiredProps.dependencies)];
    newObject.dependencies.replaceAll('-', ',');
  });
  ========START OF THE  ADDITIONAL CONTENT========
  this.addPropToMap(parameters[inputHeaders.findIndex(h => h === phasing_config.propFilter)], newObject.id);
  ========END OF THE  ADDITIONAL CONTENT========
  return newObject;
  }
```

Agora que temos nossa agenda com tarefas e elementos de modelo devidamente mapeados, podemos aproveitar os eventos acionados pelo [Frappe-Gantt](https://frappe.io/gantt).
O primeiro que podemos usar é o `on_click` de forma a isolar elementos apropriados quando uma tarefa é clicada.

Para conseguir isso, precisamos alterar `PhasingPanel.js` adicionando o conteúdo abaixo.

Primeiro, adicionamos o evento `on_click` na criação do gráfico de Gantt dentro da função `createGanttChart`:

```js
...
createGanttChart() {
  document.getElementById('phasing-container').innerHTML = `<svg id="phasing-container"></svg>`;

  let newGantt = new Gantt("#phasing-container", phasing_config.tasks, 
  ========START OF THE  ADDITIONAL CONTENT========
  {
    on_click: this.barCLickEvent.bind(this)
  }
  ========END OF THE  ADDITIONAL CONTENT========
  );
  return newGantt;
}
========START OF THE  ADDITIONAL CONTENT========
barCLickEvent(task) {
  this.extension.viewer.isolate(phasing_config.objects[task.id]);
}
========END OF THE  ADDITIONAL CONTENT========
...
```

E agora podemos isolar elementos vinculados clicando duas vezes em uma tarefa, assim como no gif abaixo:

![First Step Result](../../assets/images/doubleclick.gif)

No próximo passo, implementaremos métodos para lidar com a coloração de elementos com base em seu progresso.

[Próximo passo - Controlando a cor dos elementos]({{ site.baseurl }}/connecting/handlingcolors/){: .btn}