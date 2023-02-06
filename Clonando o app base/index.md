---
layout: page
title: Clonando o app base
nav_order: 3
has_children: true
permalink: /cloning/home/
---

# Clonando o app base

Agora que já temos o modelo preparado podemos trabalhar com o nosso exemplo.

O app base que iremos utilizar está em um repo público: https://github.com/autodesk-platform-services/aps-iot-extensions-demo

Para começar a trabalhar com ele temos opções como clonar ou fazer download como zip, por exemplo.

Para simplificar o gerenciamento armazenar nossas alterações, vamos trabalhar com um fork desse repo

![Fork IOT Sample](../../assets/images/fork_sample.gif)

Com a nossa própria versão configurada, podemos seguir o passo a passo do [README](https://github.com/JoaoMartins-callmeJohn/aps-iot-extensions-demo#running-locally) e rodar a extensão localmente.

Basicamente vamos:

- Instalar as dependências com `yarn install`

- Definir as variáveis de ambiente de acordo com seu app APS (`APS_CLIENT_ID` e `APS_CLIENT_SECRET`)

- Iniciar o exemplo com o comando `yarn start`

Com isso deve ser possível verificar o modelo padrão com http://localhost:3000

Obtemos esse resultado através dos passos descritos abaixo:

1. [Reagindo a eventos do gráfico]({{ site.baseurl }}/connecting/reacting/)
1. [Controlando a cor dos elementos]({{ site.baseurl }}/connecting/handlingcolors/)