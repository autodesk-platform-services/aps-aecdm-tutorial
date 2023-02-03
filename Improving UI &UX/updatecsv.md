---
layout: page
title: Update CSV
nav_order: 3
has_children: false
parent: Improving UI & UX
permalink: /improving/csv/
---

# Atualizando o CSV

Perfeito, tudo ótimo!

Mas e se eu precisar atualizar meu gráfico com um csv diferente ou fiz alguns canges e preciso exportá-los?
Isso é o que vamos cobrir agora.

Basta copiar o conteúdo abaixo para a função `initialize` da classe `PhasingPanel`:

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
  //Here we add the button to update the csv
  this.importbutton = document.createElement('button');
  this.importbutton.innerHTML = 'IMPORT CSV';
  this.importbutton.style.width = (this.options.buttonWidth || 100) + 'px';
  this.importbutton.style.height = (this.options.buttonHeight || 24) + 'px';
  this.importbutton.style.margin = (this.options.margin || 5) + 'px';
  this.importbutton.style.verticalAlign = (this.options.verticalAlign || 'middle');
  this.importbutton.style.backgroundColor = (this.options.backgroundColor || 'white');
  this.importbutton.style.borderRadius = (this.options.borderRadius || 8) + 'px';
  this.importbutton.style.borderStyle = (this.options.borderStyle || 'groove');

  this.importbutton.onclick = this.importCSV.bind(this);
  this.div.appendChild(this.importbutton);

  //Here we add the button to export the Gantt as csv
  this.exportbutton = document.createElement('button');
  this.exportbutton.innerHTML = 'Export CSV';
  this.exportbutton.style.width = (this.options.buttonWidth || 100) + 'px';
  this.exportbutton.style.height = (this.options.buttonHeight || 24) + 'px';
  this.exportbutton.style.margin = (this.options.margin || 5) + 'px';
  this.exportbutton.style.verticalAlign = (this.options.verticalAlign || 'middle');
  this.exportbutton.style.backgroundColor = (this.options.backgroundColor || 'white');
  this.exportbutton.style.borderRadius = (this.options.borderRadius || 8) + 'px';
  this.exportbutton.style.borderStyle = (this.options.borderStyle || 'groove');

  this.exportbutton.onclick = this.exportCSV.bind(this);
  this.div.appendChild(this.exportbutton);
  ========END OF THE  ADDITIONAL CONTENT========

  //Here we create a dropdown to control vision of the GANTT
  this.dropdown = document.createElement('select');
  this.dropdown.style.width = (this.options.dropdownWidth || 100) + 'px';
  this.dropdown.style.height = (this.options.dropdownHeight || 28) + 'px';
  this.dropdown.style.margin = (this.options.margin || 5) + 'px';
  this.dropdown.style.verticalAlign = (this.options.verticalAlign || 'middle');
  this.dropdown.style.backgroundColor = (this.options.backgroundColor || 'white');
  this.dropdown.style.borderRadius = (this.options.borderRadius || 8) + 'px';
  this.dropdown.style.borderStyle = (this.options.borderStyle || 'groove');
  for (const viewMode of phasing_config.viewModes) {
    let currentOption = document.createElement('option');
    currentOption.value = viewMode;
    currentOption.innerHTML = viewMode;
    this.dropdown.appendChild(currentOption);
  }

  this.dropdown.onchange = this.changeViewMode.bind(this);
  this.div.appendChild(this.dropdown);

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

  //Here we add the svg for the GANTT chart
  this.content = document.createElement('div');
  this.content.style.backgroundColor = (this.options.backgroundColor || 'white');
  this.content.innerHTML = `<svg id="phasing-container"></svg>`;
  this.container.appendChild(this.content);

  this.dockleft = document.createElement('img');
  //https://icons8.com/icon/105589/left-docking
  this.dockleft.src = 'data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAADAAAAAwCAYAAABXAvmHAAAABmJLR0QA/wD/AP+gvaeTAAABGklEQVRoge2ZMQ6CQBBFn8bGxgNI7Rltbb2JFvbeiZgIjSUWsIYQdiMws8sm85IpDOuf/7MTNgAYhmGsmRPwAGqgWVg1cAeKmOZfAsaHVcYK8VAw7+oWI4DE2PjqLWVyE7jWTFj7D9J6AGwlRFJiAVKzm7B2OMOrIPsdyCXAZc6ftM4AV1PMzxrfuQ0l9Zz5LAP0zWcXYGg+qwBj5rMJ4DOfRYCQeW//NZ0DH2nBFCN0HlmXzQg5fCHUGmrojYVQbaihNwyh3lBDrx8iSkMNPRdilCkPNKm4AnvfRXsrkRoLkBoLkJrQbbQGDr3fEmeBo5ISCu3AU6pJZO0fBe3HiNBDxpwqgWOMAHSNbrRbvtR41WlFM28YhqHPF0NRPAWhEg4IAAAAAElFTkSuQmCC';
  this.dockleft.style.width = (this.options.imageWidth || 30) + 'px';
  this.dockleft.style.height = (this.options.imageHeight || 28) + 'px';
  this.dockleft.style.margin = '0 0 0 ' + (this.options.margin || 10) + 'px';
  this.dockleft.style.verticalAlign = (this.options.verticalAlign || 'middle');
  this.dockleft.onclick = this.toggleOrientation.bind(this, false);
  this.div.appendChild(this.dockleft);

  this.dockbottom = document.createElement('img');
  //https://icons8.com/icon/105606/bottom-docking
  this.dockbottom.src = 'data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAADAAAAAwCAYAAABXAvmHAAAABmJLR0QA/wD/AP+gvaeTAAAA+0lEQVRoge2XsQ6CMBCGP9RV4yyzz+hofBsc2F0dfBriAC+AAzQhBBp6plxJ7ktuIC30+3OQUjAMw9g6N6AV1l3BdxJJiGTkHSEhkpN3LAmRrLzDFyJ5ecdUiM3IO4YhosnvYz0Y+AAZ8AYeEdcxDMPDFSiBBvn/jrQa4Ank/8h/FcTHVUlDlAnIuyrmJDNPgAY4BoaORQ2cpwZ8AdqAuTFYtP5uBZGoWABtDgFzx+9kEmy+AxZAm5BvQHsfmGTzHbAA2lgAbSyANr59oAFOg2vNf6F6bsDXgVcEESkil5zuQK19Hq6AiyQA/Y0FXQvXFq/7tcXyhmEY8fkBDhLw1fWJwhgAAAAASUVORK5CYII=';
  this.dockbottom.style.width = (this.options.imageWidth || 30) + 'px';
  this.dockbottom.style.height = (this.options.imageHeight || 28) + 'px';
  this.dockbottom.style.margin = '0 0 0 ' + (this.options.margin || 10) + 'px';
  this.dockbottom.style.verticalAlign = (this.options.verticalAlign || 'middle');
  this.dockbottom.onclick = this.toggleOrientation.bind(this, true);
  this.div.appendChild(this.dockbottom);

  this.updateTasks();
}

changeViewMode(event) {
  const viewMode = event ? event.target.value : this.currentViewMode;
  this.gantt.change_view_mode(viewMode);
  this.handleColors.call(this);
  this.currentViewMode = viewMode;
}

toggleOrientation(isVertical) {
  const { left: startX, top: startY, right: endX, bottom: endY } = this.extension.viewer.impl.getCanvasBoundingClientRect();

  let defaultPanelHeigth = (this.options.height || 400);
  let defaultPanelWidth = (this.options.width || 500);

  if (isVertical) {
    this.container.style.width = (endX - startX - 20) + 'px';
    this.container.style.height = defaultPanelHeigth + 'px';
    this.container.style.left = (this.options.x || 0) + 'px';
    this.container.style.top = (endY - startY - defaultPanelHeigth - this.options.y) + 'px';
  }
  else {
    this.container.style.width = defaultPanelWidth + 'px';
    this.container.style.height = (endY - startY - 20) + 'px';
    this.container.style.left = (this.options.x || 0) + 'px';
    this.container.style.top = (this.options.y || 0) + 'px';
  }
}

========START OF THE  ADDITIONAL CONTENT========
async exportCSV() {
  let tasks = this.gantt.tasks.map(task => `${task.id},${task.name},${task._start.toISOString().split('T')[0]},${task._end.toISOString().split('T')[0]},${task.progress},${Object.keys(phasing_config.mapTaksNProps).find(key => phasing_config.mapTaksNProps[key] === task.id)},${task.dependencies.join('-')}`);

  let header = Object.values(phasing_config.requiredProps);
  header.splice(5, 0, phasing_config.propFilter);
  tasks.splice(0, 0, header.join(','));

  let csvString = tasks.join("%0A");
  let a = document.createElement('a');
  a.href = 'data:attachment/csv,' + csvString;
  a.target = '_blank';
  a.download = 'Construction_Phasing_' + (new Date()).getTime() + '.csv';
  document.body.appendChild(a);
  a.click();
}

async importCSV() {
  await this.inputCSV();
  this.gantt = this.createGanttChart();
  this.handleColors.call(this);
}
========END OF THE  ADDITIONAL CONTENT========
...
```

E agora temos nosso Gráfico conectado com o modelo. Espero que você tenha aproveitado!

![Update CSV](../../assets/images/csv.gif)