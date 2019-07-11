---
title: включение файла
description: включение файла
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: nacanuma
ms.custom: include file
ms.openlocfilehash: 5ce0f18c1ec7a0fcb6465ab20e774976552687f1
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67133270"
---
## <a name="set-up-your-web-server-or-project"></a>Настройка веб-сервера или проекта

> Хотите скачать этот пример проекта вместо указанного выше проекта? Выполните одно из приведенных ниже действий.
> 
> - Чтобы запустить проект на локальном веб-сервере, например Node.js, [скачайте файлы проекта](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip).
>
> - (Необязательно.) Чтобы запустить его с помощью сервера IIS, [скачайте проект Visual Studio](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip).
>
> Затем перейдите к [настройке](#register-your-application), чтобы настроить пример кода перед выполнением.

## <a name="prerequisites"></a>Предварительные требования

* Для работы с этим учебником требуется локальный веб-сервер, например [Node.js](https://nodejs.org/en/download/) или [.NET Core](https://www.microsoft.com/net/core), либо интеграция IIS Express с [Visual Studio 2017](https://www.visualstudio.com/downloads/).

* Если вы используете Node.js для запуска проекта, установите интегрированную среду разработки, например [Visual Studio Code](https://code.visualstudio.com/download), чтобы изменять файлы проекта.

* Инструкции в этом учебнике основаны на Node.js и Visual Studio 2017, но вы можете использовать любую другую среду разработки или веб-сервер.

## <a name="create-your-project"></a>Создание проекта

> ### <a name="option-1-nodejs-or-other-web-servers"></a>Вариант 1. Node.js или другие веб-серверы
> Убедитесь, что вы установили [Node.js](https://nodejs.org/en/download/), а затем сделайте следующее:
> - Создайте папку для размещения приложения.
>
> ### <a name="option-2-visual-studio"></a>Вариант 2. Visual Studio
> Если вы используете Visual Studio и создаете проект, сделайте следующее:
> 1. В Visual Studio выберите **Файл** > **Создать** > **Проект**.
> 1. В **Visual C#\Web** выберите **Веб-приложение ASP.NET (.NET Framework)** .
> 1. Введите имя приложения и нажмите кнопку **ОК**.
> 1. В разделе **Создание веб-приложения ASP.NET** выберите **Пусто**.

## <a name="create-the-spa-ui"></a>Создание пользовательского интерфейса одностраничного приложения
1. Создайте файл *index.html* для своего одностраничного приложения JavaScript. Если вы работаете с Visual Studio, выберите проект (корневую папку проекта), щелкните правой кнопкой мыши и выберите **Добавить** > **Новый элемент** > **HTML-страница**. Присвойте файлу имя *index.html*.

1. В файле *index.html* добавьте следующий код:

   ```html
   <!DOCTYPE html>
   <html>
   <head>
       <title>Quickstart for MSAL JS</title>
       <script src="https://cdnjs.cloudflare.com/ajax/libs/bluebird/3.3.4/bluebird.min.js"></script>
       <script src="https://secure.aadcdn.microsoftonline-p.com/lib/1.0.0/js/msal.js"></script>
   </head>
   <body>
       <h2>Welcome to MSAL.js Quickstart</h2><br/>
       <h4 id="WelcomeMessage"></h4>
       <button id="SignIn" onclick="signIn()">Sign In</button><br/><br/>
       <pre id="json"></pre>
       <script>
           //JS code
       </script>
   </body>
   </html>
   ```

   > [!TIP]
   > Приведенную в сценарии выше версию MSAL.js можно заменить последней выпущенной версией в разделе с [выпусками MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases).
