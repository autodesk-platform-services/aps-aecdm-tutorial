---
layout: page
title: Input, Libraries and Configuration file
permalink: /input/index
nav_order: 2
---

# Introdução

Para gerar o gráfico de Gantt conectado aos nossos modelos, precisamos de uma biblioteca e uma forma de implementar informações adicionais relacionadas às tarefas que cada elemento fará parte.

Para a biblioteca, escolhemos [Frappe/Gantt](https://frappe.io/gantt) pela sua simplicidade e flexibilidade. <br /> Esta biblioteca requer o seguinte objeto para definir uma tarefa:

```
{
  id: 'Task id',
  name: 'Task name',
  start: 'Task start',
  end: 'Task end',
  progress: Task_progress,
  dependencies: 'Tasks dependencies'
}
```

Precisamos de todas essas informações para gerar cada tarefa e também precisamos de uma maneira de mapear as tarefas e os elementos do nosso modelo. Neste exemplo, usaremos uma propriedade para fazer esse mapeamento (controlado pelo arquivo de configuração `config.js`). <br /> Ao final, teremos a seguinte configuração para nosso csv:

| ID  | NAME          | START      | END        | PROGRESS | Type Name              | DEPENDENCIES |
| --- | ------------- | ---------- | ---------- | -------- | ---------------------- | ------------ |
| 123 | Timber Floors | 2022-03-10 | 2022-04-04 | 100      | Timber Suspended Floor | 121-122      |

Com este csv temos todas as informações exigidas pela biblioteca e a propriedade utilizada para conectar com o modelo (**Type Name**).

Você pode encontrar um exemplo de input csv [aqui](https://raw.githubusercontent.com/JoaoMartins-Forge/construction-phasing-tutorial-docs/main/assets/samples/sampleinput.csv)

Também precisamos do arquivo de configuração (`config.js`). A configuração contém informações que usaremos em diferentes seções deste tutorial e abordaremos cada parte aqui. Por enquanto, vamos nos concentrar na propriedade usada para mapear tarefas e elementos. Isso é controlado pelo campo `propFilter`.

Por enquanto, você pode criar um arquivo `config.js` na pasta `wwwroot/extensions` com o conteúdo abaixo:

```js
export const phasing_config = {
  propFilter: "Type Name",
};
```

Teremos uma ideia de como isso vai funcionar no decorrer do tutorial, pois continuaremos melhorando o `config.js` e nossa extensão.

Como dito na página inicial, você também precisará do arquivo `BaseExtension.js` para que este exemplo funcione.
Adicione o arquivo na pasta `extensions` do seu projeto com o conteúdo abaixo.

```js
export class BaseExtension extends Autodesk.Viewing.Extension {
  constructor(viewer, options) {
    super(viewer, options);
    this._onObjectTreeCreated = (ev) => this.onModelLoaded(ev.model);
    this._onSelectionChanged = (ev) => this.onSelectionChanged(ev.model, ev.dbIdArray);
    this._onIsolationChanged = (ev) => this.onIsolationChanged(ev.model, ev.nodeIdArray);
  }

  load() {
    this.viewer.addEventListener(Autodesk.Viewing.OBJECT_TREE_CREATED_EVENT, this._onObjectTreeCreated);
    this.viewer.addEventListener(Autodesk.Viewing.SELECTION_CHANGED_EVENT, this._onSelectionChanged);
    this.viewer.addEventListener(Autodesk.Viewing.ISOLATE_EVENT, this._onIsolationChanged);
    return true;
  }

  unload() {
    this.viewer.removeEventListener(Autodesk.Viewing.OBJECT_TREE_CREATED_EVENT, this._onObjectTreeCreated);
    this.viewer.removeEventListener(Autodesk.Viewing.SELECTION_CHANGED_EVENT, this._onSelectionChanged);
    this.viewer.removeEventListener(Autodesk.Viewing.ISOLATE_EVENT, this._onIsolationChanged);
    return true;
  }

  onModelLoaded(model) {}

  onSelectionChanged(model, dbids) {}

  onIsolationChanged(model, dbids) {}

  findLeafNodes(model) {
    return new Promise(function (resolve, reject) {
      model.getObjectTree(function (tree) {
        let leaves = [];
        tree.enumNodeChildren(tree.getRootId(), function (dbid) {
          if (tree.getChildCount(dbid) === 0) {
            leaves.push(dbid);
          }
        }, true);
        resolve(leaves);
      }, reject);
    });
  }

  async findPropertyNames(model) {
    const dbids = await this.findLeafNodes(model);
    return new Promise(function (resolve, reject) {
      model.getBulkProperties(dbids, {}, function (results) {
        let propNames = new Set();
        for (const result of results) {
          for (const prop of result.properties) {
            propNames.add(prop.displayName);
          }
        }
        resolve(Array.from(propNames.values()));
      }, reject);
    });
  }

  createToolbarButton(buttonId, buttonIconUrl, buttonTooltip) {
    let group = this.viewer.toolbar.getControl('dashboard-toolbar-group');
    if (!group) {
      group = new Autodesk.Viewing.UI.ControlGroup('dashboard-toolbar-group');
      this.viewer.toolbar.addControl(group);
    }
    const button = new Autodesk.Viewing.UI.Button(buttonId);
    button.setToolTip(buttonTooltip);
    group.addControl(button);
    const icon = button.container.querySelector('.adsk-button-icon');
    if (icon) {
      icon.style.backgroundImage = `url(${buttonIconUrl})`; 
      icon.style.backgroundSize = `24px`; 
      icon.style.backgroundRepeat = `no-repeat`; 
      icon.style.backgroundPosition = `center`; 
    }
    return button;
  }

  removeToolbarButton(button) {
    const group = this.viewer.toolbar.getControl('dashboard-toolbar-group');
    group.removeControl(button);
  }

  loadScript(url, namespace) {
    if (window[namespace] !== undefined) {
      return Promise.resolve();
    }
    return new Promise(function (resolve, reject) {
      const el = document.createElement('script');
      el.src = url;
      el.onload = resolve;
      el.onerror = reject;
      document.head.appendChild(el);
    });
  }

  loadStylesheet(url) {
    return new Promise(function (resolve, reject) {
      const el = document.createElement('link');
      el.rel = 'stylesheet';
      el.href = url;
      el.onload = resolve;
      el.onerror = reject;
      document.head.appendChild(el);
    });
  }
}
```

Para gerenciar os inputs (CSV) no nosso exemplo, estamos usando a biblioteca [SweetAlert2]().
Para usá-la basta adicionar sua referência copiando a linha abaixo no arquivo `index.html`:
```jsx
...
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  ========START OF THE  ADDITIONAL CONTENT========
  <script src="https://cdn.jsdelivr.net/npm/sweetalert2@11"></script>
  ========END OF THE  ADDITIONAL CONTENT========
  <link rel="stylesheet" href="https://developer.api.autodesk.com/modelderivative/v2/viewers/7.*/style.css">
  <title>Autodesk Forge: Construction Pasing Sample</title>
</head>
...
```

Agora que sabemos como vai funcionar e as dependências do projeto, podemos começar a construir nosso exemplo.

[Próxima etapa - Adicionando o Gráfico de Gantt]({{ site.baseurl }}/building/home){: .btn}
