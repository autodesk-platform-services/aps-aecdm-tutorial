---
layout: page
title: Configuration File
nav_order: 3
has_children: false
parent: Building the Gantt chart
permalink: /building/configuration/
---

# Arquivo de Configuração

O arquivo de configuração define como nossa extensão fará a leitura do arquivo csv, para posterior organização e conexão dos dados com o nosso modelo.
Nesta primeira etapa, precisamos definir a propriedade que usaremos para filtrar, as propriedades necessárias esperadas do arquivo csv e as variáveis para armazenar tarefas.
Vamos melhorar nosso arquivo de configuração `config.js` em `wwwroot/extensions` com o conteúdo abaixo:

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
  }
}
```

Com isso, podemos verificar nosso novo gráfico de Gantt gerado a partir de nossa entrada csv.

![First Step Result](../../assets/images/stepone.gif)

[Próxima etapa - Conectando o gráfico com o modelo]({{ site.baseurl }}/connecting/home/){: .btn}