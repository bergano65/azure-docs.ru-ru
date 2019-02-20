---
title: Как использовать библиотеку Map Control в службе "Карты Azure" | Документация Майкрософт
description: Узнайте, как использовать клиентскую библиотеку Javascript Map Control в службе "Карты Azure".
author: dsk-2015
ms.author: dkshir
ms.date: 10/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 877393de20788b0aa1c76084b121a82f12715cd3
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/12/2019
ms.locfileid: "56118077"
---
# <a name="use-the-azure-maps-map-control"></a>Использование библиотеки Map Control в службе Azure Maps

Клиентская библиотека Javascript Map Control позволяет отображать карты и встроенные функциональные возможности службы "Карты Azure" в вашем мобильном или веб-приложении.

## <a name="create-a-new-map-in-a-web-page"></a>Создание карты на веб-странице

Вы можете встроить карту в веб-страницу с помощью клиентской библиотеки Javascript службы Map Control.

1. Создайте HTML-файл.

2. Загрузите в веб-пакет SDK Azure Maps. Это можно сделать с помощью одного из двух параметров.
    
    a. Используйте размещенную на глобальном уровне версию CDN веб-пакета SDK Azure Maps, добавив конечные точки URL-адреса в таблицу стилей и источник сценария в элементе `<head>` файла.

    ```html
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=2" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/js/atlas.min.js?api-version=2"></script>
    ```

    б) Кроме того, загрузите исходный код веб-пакета SDK Azure Maps локально с помощью пакета NPM [azure-maps-control](https://www.npmjs.com/package/azure-maps-control) и разместите его в приложении. Этот пакет также включает определения TypeScript.

    > npm install azure-maps-control

    Затем добавьте ссылки на стили и источник сценария Azure Maps в элемент `<head>` файла.

    ```html
    <link rel="stylesheet" href="node_modules/azure-maps-control/dist/css/atlas.min.css" type="text/css" />
    <script src="node_modules/azure-maps-control/dist/js/atlas.min.js"></script>
    ```

3. Чтобы отобразить карту так, чтобы она занимала основную область страницы, добавьте следующий элемент `<style>` к элементу `<head>`.

    ```html
    <style>
        html, body {
            margin: 0;
        }
    
        #myMap {
            height: 100vh;
            width: 100vw;
        }
    </style>
    ```

4. В основной области страницы добавьте элемент `<div>` и присвойте ему `id` **myMap**. 

    ```html
    <body>
        <div id="myMap"></div>
    </body>
    ```

5. Для инициализации элемента управления картой определите новый раздел в тексте html и создайте сценарий. Используйте собственный ключ учетной записи Azure Maps или учетные данные Azure Active Directory (AAD), чтобы выполнить проверку подлинности сопоставления с помощью [параметров проверки подлинности](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.atlas.authenticationoptions). Если вам нужно создать учетную запись или найти ключ, см. статью об [управлении учетной записью и ключами в Azure Maps](how-to-manage-account-keys.md). Параметр **language** задает язык, который используется для метки схемы и элементов управления. Полный список поддерживаемых языков см. в разделе [Поддерживаемые языки](supported-languages.md). Если используется ключ подписки для проверки подлинности.

    ```html
    <script type='text/javascript'>
        var map = new atlas.Map('myMap', {
            center: [-122.33, 47.6],
            zoom: 12,
            language: 'en-US',
            authOptions: {
                authType: 'subscriptionKey',
                subscriptionKey: '<Your Azure Maps Key>'
            }
        });
    </script>
    ```

    Если для проверки подлинности используется Azure Active Directory (AAD):

    ```html
    <script type='text/javascript'>
        var map = new atlas.Map('myMap', {
            center: [-122.33, 47.6],
            zoom: 12,
            language: 'en-US',
            authOptions: {
                authType: 'aad',
                clientId: '<Your AAD Client Id>',
                aadAppId: '<Your AAD App Id',
                aadTenant: 'msft.ccsctp.net'
            }
        });
    </script>
    ```

6. При необходимости возможно добавление следующих элементов метатега в заголовок вашей страницы.

    ```html
    <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    
    <!-- Ensures the web page looks good on all screen sizes. -->
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />
    ```

7. Сборка вашего HTML-файла должна выглядеть следующим образом.

    ```html
    <!DOCTYPE html>
    <html>
    <head>
        <title></title>
    
        <meta charset="utf-8" />
        
        <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
        <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    
        <!-- Ensures the web page looks good on all screen sizes. -->
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />
    
        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=2" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/js/atlas.min.js?api-version=2"></script>
    
        <style>
            html, body {
                margin: 0;
            }
        
            #myMap {
                height: 100vh;
                width: 100vw;
            }
        </style>
    </head>
    <body>
        <div id="myMap"></div>
        
        <script type='text/javascript'>
            //Create an instance of the map control and set some options.
            var map = new atlas.Map('myMap', {
                center: [-122.33, 47.6],
                zoom: 12,
                language: 'en-US',
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });
        </script>
    </body>
    </html>
    ```

8. Откройте файл в веб-браузере и вы увидите готовую для просмотра карту. Он должен выглядеть примерно так:

    <iframe height="700" style="width: 100%;" scrolling="no" title="Использование элемента управления картой" src="//codepen.io/azuremaps/embed/yZpEYL/?height=557&theme-id=0&default-tab=html,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Просмотрите перо <a href='https://codepen.io/azuremaps/pen/yZpEYL/'>How to use the map control</a> (Использование элемента управления картой) с помощью Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на сайте <a href='https://codepen.io'>CodePen</a>.
    </iframe>

## <a name="next-steps"></a>Дополнительная информация

Узнайте, как создавать и взаимодействовать с картой:

> [!div class="nextstepaction"]
> [Create a map](map-create.md) (Создание карты)

Узнайте, как изменить стиль карты:

> [!div class="nextstepaction"]
> [Выбор стиля карты](choose-map-style.md)
