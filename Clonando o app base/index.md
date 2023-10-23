---
layout: page
title: Clonando o app base
nav_order: 3
has_children: true
permalink: /cloning/home/
---

# Clonando o app base

Agora que o modelo está preparado, pode-se trabalhar com o nosso exemplo de app.

O app base de referência que será utilizado está em um repositório público: [autodesk-platform-services/aps-iot-extensions-demo](https://github.com/autodesk-platform-services/aps-iot-extensions-demo)

Para começar a trabalhar com ele, há opções como clonar ou fazer download como zip, por exemplo.

Para simplificar o gerenciamento e armazenar as eventuais alterações, recomenda-se trabalhar com um fork desse repo

![Fork IOT Sample](../../assets/images/fork_sample.gif)

A partir da própria versão configurada, pode-se seguir o passo a passo do [README](https://github.com/JoaoMartins-callmeJohn/aps-iot-extensions-demo#running-locally) e clonar o exemplo (no gif abaixo foi utilizado o [GitHub Desktop](https://desktop.github.com))

![clone IOT Sample](../../assets/images/clone_app.gif)

Basicamente, deve-se:

- Instalar as dependências com `yarn install`

![install IOT Sample](../../assets/images/yarn_install.gif)

- Definir as variáveis de ambiente de acordo com seu app APS (`APS_CLIENT_ID` e `APS_CLIENT_SECRET`). A forma mais fácil é alterando os valores do arquivo `.env.example` e renomeando o mesmo como .env

![env variables](../../assets/images/env_vars.gif)

- Iniciar o exemplo com o comando usando a ferramenta de debug do VS Code

Dessa forma, será possível verificar o app navegando até http://localhost:3000

![localhost 3000](../../assets/images/localhost_3000.gif)

O exemplo está funcionando, porém ainda é necessário ajustar aos dados do (modelo e sensores).

Esse será o objetivo da próxima etapa

[Próximo passo - Adaptando aos seus dados]({{ site.baseurl }}/adapting/home/){: .btn}
