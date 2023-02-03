---
layout: page
title: Handling elements and bars colors
nav_order: 2
has_children: false
parent: Connecting the chart with the model
permalink: /connecting/handlingcolors/
---

# Controlando a cor dos elementos

Para substituir as cores do modelo e das barras com base na configuração do gráfico de Gantt, precisamos de um método para calcular o status de uma tarefa, um campo no arquivo de configuração para definir as cores para cada status e uma função reagindo aos eventos acionados quando mudamos o gráfico.
Para sobrepor as cores dos elementos vamos utilizar o método [setThemingColor](https://forge.autodesk.com/en/docs/viewer/v7/reference/Viewing/GuiViewer3D/#setthemingcolor-dbid-color-model-recursive)
Vamos fazê-lo!
Primeiro precisamos incrementar o arquivo `config.js` adicionando os campos abaixo:

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
  "objects": {},
  "mapTaksNProps": {}
  ========START OF THE  ADDITIONAL CONTENT========
  "statusColors": {
    "finished": "31,246,14",
    "inProgress": "235,246,14",
    "late": "246,55,14",
    "notYetStarted": "",
    "advanced": "14,28,246"
  }
  ========END OF THE  ADDITIONAL CONTENT========
}
```

Observe que isso definirá a cor de cada status, e os números definem o valor dos componentes vermelho, verde e azul da cor a ser usada.
Agora precisamos da função para obter o status de cada tarefa. Vamos adicionar a função `checkStatus` dentro da classe `PhasingPanel`:

```js
...
addPropToMap(filterValue, taskId) {
  phasing_config.mapTaksNProps[filterValue] = taskId;
}
========START OF THE  ADDITIONAL CONTENT========
checkTaskStatus(task) {
  let currentDate = new Date();

  let taskStart = new Date(task._start);
  let taskEnd = new Date(task._end);

  let shouldHaveStarted = currentDate > taskStart;
  let shouldHaveEnded = currentDate > taskEnd;

  let taskProgress = parseInt(task.progress, 10);

  //We need to map finished, in progress, late, not yet started or advanced
  //finished should have started and ended and actually ended (progress 100%)
  //in progress should have started, not ended and progress should be greater than 0
  //late should have started and ended but progress is less than 100, or should have started not ended and progress is 0
  //not yet started should not have started nor ended and progress is 0
  //advanced should not have started and have progress greater than 0 or should not have ended and progress is 100

  if (shouldHaveStarted && shouldHaveEnded && taskProgress === 100)
    return 'finished';

  else if (shouldHaveStarted && !shouldHaveEnded) {
    switch (taskProgress) {
      case 100:
        return 'advanced';
      case 0:
        return 'late';
      default:
        return 'inProgress';
    }
  }

  else if (shouldHaveStarted && shouldHaveEnded && taskProgress < 100)
    return 'late';

  else if (!shouldHaveStarted && !shouldHaveEnded && taskProgress === 0)
    return 'notYetStarted';

  else if (!shouldHaveStarted && taskProgress > 0)
    return 'advanced';
}
========END OF THE  ADDITIONAL CONTENT========
```

Agora só precisamos chamar esse método sempre que o gráfico de Gantt for alterado. Para isso, podemos aproveitar os eventos `on_progress_change` e `on_date_change` da nossa biblioteca, modificando a instanciação do gráfico de Gantt mais uma vez:

```js
...
createGanttChart() {
  document.getElementById('phasing-container').innerHTML = `<svg id="phasing-container"></svg>`;

  let newGantt = new Gantt("#phasing-container", phasing_config.tasks, {
    on_click: this.barCLickEvent.bind(this),
    ========START OF THE  ADDITIONAL CONTENT========
    on_progress_change: this.handleColors.bind(this),
    on_date_change: this.handleColors.bind(this)
    ========END OF THE  ADDITIONAL CONTENT========
  });

  return newGantt;
}

========START OF THE  ADDITIONAL CONTENT========
handleColors() {
  this.handleElementsColor.call(this);
  this.handleBarsColor.call(this);
}

handleElementsColor() {
  const overrideCheckbox = document.getElementById('colormodel');
  if (overrideCheckbox.checked) {
    let tasksNStatusArray = this.gantt.tasks.map(this.checkTaskStatus);
    let mappeddbIds = [];
    for (let index = 0; index < this.gantt.tasks.length; index++) {
      const currentTaskId = this.gantt.tasks[index].id;
      const currentdbIds = phasing_config.objects[currentTaskId];
      const colorVector4 = this.fromRGB2Color(phasing_config.statusColors[tasksNStatusArray[index]]);
      currentdbIds.forEach(dbId => {
        if (colorVector4) {
          this.extension.viewer.setThemingColor(dbId, colorVector4)
        }
        else {
          this.extension.viewer.hide(dbId);
        }
      });
      mappeddbIds.push(...currentdbIds);
    }
    this.extension.viewer.isolate(mappeddbIds);
  }
  else {
    this.extension.viewer.clearThemingColors();
    this.extension.viewer.showAll();
  }
}

handleBarsColor() {
  this.gantt.bars.map(bar => {
    let tasksStatus = this.checkTaskStatus(bar.task);
    let barColor = phasing_config.statusColors[tasksStatus];
    bar.$bar.style = `fill: rgb(${barColor})`;
  })
}

fromRGB2Color(rgbString) {
  if (rgbString) {
    let colorsInt = rgbString.replaceAll(' ', '').split(',').map(colorString => parseInt(colorString, 10));
    return new THREE.Vector4(colorsInt[0] / 255, colorsInt[1] / 255, colorsInt[2] / 255, 0.5);
  }
  else {
    return null;
  }
}
========END OF THE  ADDITIONAL CONTENT========
...
```

To manage the bars colors as soon as we have the input loaded, we need to increment the `update` function from `PhasingPanel` class:

```js
...
update(model, dbids) {
  if (phasing_config.tasks.length === 0) {
    this.inputCSV();
  }
  model.getBulkProperties(dbids, { propFilter: phasing_config.propFilter }, (results) => {
    results.map((result => {
      this.updateObjects(result);
    }))
  }, (err) => {
    console.error(err);
  });
  if (phasing_config.tasks.length > 0) {
    this.gantt = this.createGanttChart();
    ========START OF THE  ADDITIONAL CONTENT========
    this.handleColors.call(this);
    ========END OF THE  ADDITIONAL CONTENT========
  }
}
...
```

Agora só falta uma parte. Como vamos controlar a cor dos elementos com base no status?

Para isso, podemos adicionar uma caixa de seleção em nosso painel.
Adicione o conteúdo abaixo dentro da função `initialize` da classe `PhasingPanel`:

```js
...
initialize() {
  this.title = this.createTitleBar(this.titleLabel || this.container.id);
  this.title.style.overflow = 'auto';
  this.initializeMoveHandlers(this.title);
  this.container.appendChild(this.title);

  this.div = document.createElement('div');
  this.container.appendChild(this.div);

  ========START OF THE  ADDITIONAL CONTENT========

  //Here we create a switch to control vision of the schedule based on the GANTT chart
  this.checkbox = document.createElement('input');
  this.checkbox.type = 'checkbox';
  this.checkbox.id = 'colormodel';
  this.checkbox.style.width = (this.options.checkboxWidth || 30) + 'px';
  this.checkbox.style.height = (this.options.checkboxHeight || 28) + 'px';
  this.checkbox.style.margin = '0 0 0 ' + (this.options.margin || 5) + 'px';
  this.checkbox.style.verticalAlign = (this.options.verticalAlign || 'middle');
  this.checkbox.style.backgroundColor = (this.options.backgroundColor || 'white');
  this.checkbox.style.borderRadius = (this.options.borderRadius || 8) + 'px';
  this.checkbox.style.borderStyle = (this.options.borderStyle || 'groove');

  this.checkbox.onchange = this.handleColors.bind(this);
  this.div.appendChild(this.checkbox);

  this.label = document.createElement('label');
  this.label.for = 'colormodel';
  this.label.innerHTML = 'Show Phases';
  this.label.style.fontSize = (this.options.fontSize || 18) + 'px';
  this.label.style.verticalAlign = (this.options.verticalAlign || 'middle');
  this.div.appendChild(this.label);

  ========END OF THE  ADDITIONAL CONTENT========

  //Here we add the svg for the GANTT chart
  this.content = document.createElement('div');
  this.content.style.backgroundColor = (this.options.backgroundColor || 'white');
  this.content.innerHTML = `<svg id="phasing-container"></svg>`;
  this.container.appendChild(this.content);

  this.updateTasks();
}
...
```

Com tudo definido, você deve conseguir ver o modelo colorido, como no gif abaixo:

![Second Step Result](../../assets/images/steptwo.gif)

[Próxima etapa - Otimizando a experiência]({{ site.baseurl }}/improving/home/){: .btn}