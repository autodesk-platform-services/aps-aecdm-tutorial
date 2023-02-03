---
layout: page
title: Phasing Extension
nav_order: 1
has_children: false
parent: Building the Gantt chart
permalink: /building/phasingextension/
---

# Phasing Extension

Aqui adicionaremos o arquivo `PhasingExtension.js` ao nosso aplicativo. Esta extensão estenderá a classe `BaseExtension`.
Através dele, trataremos da criação do botão, painel e carregamento da biblioteca necessária (frappe-gantt).
Vamos começar criando nossa extensão  `PhasingExtension.js` na pasta `wwwroot/extensions` com o conteúdo abaixo:

```js
import { BaseExtension } from './BaseExtension.js';
import { PhasingPanel } from './PhasingPanel.js';

class PhasingExtension extends BaseExtension {
    constructor(viewer, options) {
        super(viewer, options);
        this._button = null;
        this._panel = null;
    }

    async load() {
        super.load();
        await Promise.all([
            this.loadScript('https://cdn.jsdelivr.net/npm/frappe-gantt@0.6.1/dist/frappe-gantt.js', 'FrappeGantt'),
            this.loadStylesheet('https://cdn.jsdelivr.net/npm/frappe-gantt@0.6.1/dist/frappe-gantt.css')
        ]);
        console.log('PhasingExtension loaded.');
        return true;
    }

    unload() {
        super.unload();
        if (this._button) {
            this.removeToolbarButton(this._button);
            this._button = null;
        }
        if (this._panel) {
            this._panel.setVisible(false);
            this._panel.uninitialize();
            this._panel = null;
        }
        this.viewer.clearThemingColors();
        this.viewer.showAll();
        console.log('PhasingExtension unloaded.');
        return true;
    }

    onToolbarCreated() {
        this._panel = new PhasingPanel(this, 'dashboard-phases-panel', 'Schedule', { x: 10, y: 10 });//
        this._button = this.createToolbarButton('dashboard-phases-button', 'https://img.icons8.com/small/32/000000/gantt-chart.png', 'Show Gantt Chart');//
        this._button.onClick = () => {
            this._panel.setVisible(!this._panel.isVisible());
            this._button.setState(this._panel.isVisible() ? Autodesk.Viewing.UI.Button.State.ACTIVE : Autodesk.Viewing.UI.Button.State.INACTIVE);
            if (this._panel.isVisible() && this.viewer.model) {
                this.update();
            }
        };
    }

    onModelLoaded(model) {
        super.onModelLoaded(model);
        if (this._panel && this._panel.isVisible()) {
            this.update();
        }
    }

    async update() {
        const dbids = await this.findLeafNodes(this.viewer.model);
        this._panel.update(this.viewer.model, dbids);
    }
}

Autodesk.Viewing.theExtensionManager.registerExtension('PhasingExtension', PhasingExtension);
```

Agora precisamos adicionar nossa extensão ao Viewer.

Adicione a linha abaixo ao seu arquivo js que está carregando o Viewer:

```
import './extensions/PhasingExtension.js';
```

Também precisamos adicionar nossa extensão

```
const config = {
    extensions: ['PhasingExtension']
};
const viewer = new Autodesk.Viewing.GuiViewer3D(container, config);
```

Você deve ter notado que esta extensão importa a classe `PhasingPanel`. Esse será o nosso próximo passo.

[Próximo passo - Phasing Panel]({{ site.baseurl }}/building/phasingpanel/){: .btn}
