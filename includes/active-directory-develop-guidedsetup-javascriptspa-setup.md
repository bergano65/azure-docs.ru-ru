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
ms.openlocfilehash: da2477b19327273fe922ac81f909233cb4ef8f06
ms.sourcegitcommit: 41015688dc94593fd9662a7f0ba0e72f044915d6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/11/2019
ms.locfileid: "59502703"
---
## <a name="setting-up-your-web-server-or-project"></a>Настройка веб-сервера или проекта

> Хотите скачать этот пример проекта вместо указанного выше проекта?
> - [Скачайте файлы проекта](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip) для локального веб-сервера, например Node.
>
> или
> - [Скачайте проект Visual Studio.](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip)
>
> Затем перейдите к [настройке](#register-your-application), чтобы настроить пример кода перед выполнением.

## <a name="prerequisites"></a>Предварительные требования
Для работы с этим руководством требуется локальный веб-сервер, например [Node.js](https://nodejs.org/en/download/) или [.NET Core](https://www.microsoft.com/net/core), либо интеграция IIS Express с [Visual Studio 2017](https://www.visualstudio.com/downloads/).

Если вы используете Node.js для запуска проекта, установите IDE, например [Visual Studio Code](https://code.visualstudio.com/download), чтобы изменять файлы проекта.

Инструкции в этом руководстве основаны на Node.js и Visual Studio 2017, но вы можете использовать любую другую среду разработки или веб-сервер.

## <a name="create-your-project"></a>Создание проекта

> ### <a name="option-1-node-other-web-servers"></a>Вариант 1. Node или другие веб-серверы
> Убедитесь, что вы установили [Node.js](https://nodejs.org/en/download/), а затем выполните следующие шаги:
> - Создайте папку для размещения приложения.

<p><!-- -->

> ### <a name="option-2-visual-studio"></a>Вариант 2. Visual Studio
> Если вы создаете новый проект, используя Visual Studio, то выполните следующие действия, чтобы создать новое решение Visual Studio:
> 1.    В Visual Studio:  **Файл > Создать > Проект**
> 2.    В разделе **Visual C#\Web** выберите **Веб-приложение ASP.NET (.NET Framework)**.
> 3.    Введите название приложения и нажмите кнопку **ОК**.
> 4.    В разделе **Создание веб-приложения ASP.NET** выберите **Пусто**

## <a name="create-your-single-page-applications-ui"></a>Создание пользовательского интерфейса одностраничного приложения
1. Создайте файл `index.html` для своего одностраничного приложения JavaScript. Если вы работаете с Visual Studio, выберите проект (корневую папку проекта), щелкните правой кнопкой мыши и выберите: **Добавить > Новый элемент > Страница HTML** и назовите ее index.html.

2. Добавьте в страницу следующий код:
   ```html
   <!DOCTYPE html>
   <html>
   <head>
       <title>Quickstart for MSAL JS</title>
       <script src="https://cdnjs.cloudflare.com/ajax/libs/bluebird/3.3.4/bluebird.min.js"></script>
       <script src="https://secure.aadcdn.microsoftonline-p.com/lib/0.2.4/js/msal.js"></script>
       <script src="https://code.jquery.com/jquery-3.2.1.min.js"></script>
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
