---
title: Getting started with web map control in Azure Maps | Microsoft Docs
description: Learn how to use the Azure Maps map control client-side Javascript library.
author: walsehgal
ms.author: v-musehg
ms.date: 10/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: ff183261f67ff76f56fc034d8102e3aa3a4838a8
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/25/2019
ms.locfileid: "74480518"
---
# <a name="use-the-azure-maps-map-control"></a>Use the Azure Maps map control

Клиентская библиотека Javascript Map Control позволяет отображать карты и встроенные функциональные возможности службы "Карты Azure" в вашем мобильном или веб-приложении.

## <a name="create-a-new-map-in-a-web-page"></a>Создание карты на веб-странице

Вы можете встроить карту в веб-страницу с помощью клиентской библиотеки Javascript службы Map Control.

1. Создайте HTML-файл.

2. Загрузите в веб-пакет SDK Azure Maps. Это можно сделать с помощью одного из двух параметров.

    а) Используйте размещенную на глобальном уровне версию CDN веб-пакета SDK Azure Maps, добавив конечные точки URL-адреса в таблицу стилей и источник сценария в элементе `<head>` файла.

    ```HTML
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>
    ```

    б) Кроме того, загрузите исходный код веб-пакета SDK Azure Maps локально с помощью пакета NPM [azure-maps-control](https://www.npmjs.com/package/azure-maps-control) и разместите его в приложении. Этот пакет также включает определения TypeScript.

    > npm install azure-maps-control

    Затем добавьте ссылки на стили и источник сценария Azure Maps в элемент `<head>` файла.

    ```HTML
    <link rel="stylesheet" href="node_modules/azure-maps-control/dist/atlas.min.css" type="text/css"> 
    <script src="node_modules/azure-maps-control/dist/atlas.min.js"></script>
    ```

    >[!Note]
    > Typescript definitions can be imported into your application by adding:
    > ```Javascript
    > import * as atlas from 'azure-maps-control';
    > ```

3. Чтобы отобразить карту так, чтобы она занимала основную область страницы, добавьте следующий элемент `<style>` к элементу `<head>`.

    ```HTML
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

    ```HTML
    <body>
        <div id="myMap"></div>
    </body>
    ```

5. Для инициализации элемента управления картой определите новый раздел в тексте html и создайте сценарий. Pass in the `id` of the map `<div>` or an `HTMLElement` (for example, `document.getElementById('myMap')`) as the first parameter when creating an instance of the `Map` class. Используйте собственный ключ учетной записи Azure Maps или учетные данные Azure Active Directory (AAD), чтобы выполнить проверку подлинности сопоставления с помощью [параметров проверки подлинности](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.authenticationoptions). Если вам нужно создать учетную запись или найти ключ, см. статью об [управлении учетной записью и ключами в Azure Maps](how-to-manage-account-keys.md). Параметр **language** задает язык, который используется для метки схемы и элементов управления. Полный список поддерживаемых языков см. в разделе [Поддерживаемые языки](supported-languages.md). Если используется ключ подписки для проверки подлинности.

    ```HTML
    <script type="text/javascript">
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

    ```HTML
    <script type="text/javascript">
        var map = new atlas.Map('myMap', {
            center: [-122.33, 47.6],
            zoom: 12,
            language: 'en-US',
            authOptions: {
                authType: 'aad',
                clientId: '<Your AAD Client Id>',
                aadAppId: '<Your AAD App Id>',
                aadTenant: '<Your AAD Tenant Id>'
            }
        });
    </script>
    ```

    A list of samples showing how to integrate Azure Active Directory (AAD) with Azure Maps can be found [here](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples). 
    
    For more information, see the [Authentication with Azure Maps](azure-maps-authentication.md) document and also the [Azure Maps Azure AD authentication samples](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples).

6. При необходимости возможно добавление следующих элементов метатега в заголовок вашей страницы.

    ```HTML
    <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
    <meta http-equiv="x-ua-compatible" content="IE=Edge">

    <!-- Ensures the web page looks good on all screen sizes. -->
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
    ```

7. Putting it all together your HTML file should look something like the following code:

    ```HTML
    <!DOCTYPE html>
    <html>
    <head>
        <title></title>

        <meta charset="utf-8">

        <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
        <meta http-equiv="x-ua-compatible" content="IE=Edge">

        <!-- Ensures the web page looks good on all screen sizes. -->
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

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

        <script type="text/javascript">
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

8. Откройте файл в веб-браузере и вы увидите готовую для просмотра карту. It should look like the following code:

    <iframe height="700" style="width: 100%;" scrolling="no" title="Использование элемента управления картой" src="//codepen.io/azuremaps/embed/yZpEYL/?height=557&theme-id=0&default-tab=html,result" frameborder="no" allowtransparency="true" allowfullscreen="true">See the Pen <a href='https://codepen.io/azuremaps/pen/yZpEYL/'>How to use the map control</a> by Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>) on <a href='https://codepen.io'>CodePen</a>.
    </iframe>

## <a name="localizing-the-map"></a>Localizing the map

Azure Maps provides two different ways of setting the language and regional view of the map. The first option is to add this information to the global `atlas` namespace, which will result in all map control instances in your app defaulting to these settings. The following sets the language to French ("fr-FR") and the regional view to "Auto":

```javascript
atlas.setLanguage('fr-FR');
atlas.setView('Auto');
```

The second option is to pass this information into the map options when loading the map like:

```javascript
map = new atlas.Map('myMap', {
    language: 'fr-FR',
    view: 'Auto',

    authOptions: {
        authType: 'aad',
        clientId: '<Your AAD Client Id>',
        aadAppId: '<Your AAD App Id>',
        aadTenant: '<Your AAD Tenant Id>'
    }
});
```

> [!Note]
> With the Web SDK it is possible to load multiple map instances on the same page with different language and region settings. Additionally, these settings can be update after the map has loaded by using the `setStyle` function of the map. 

Here is an example of Azure Maps with the language set to "fr-FR" and the regional view set to "Auto".

![Map image showing labels in French](./media/how-to-use-map-control/websdk-localization.png)

A complete list of supported languages and regional views is documented [here](supported-languages.md).

## <a name="next-steps"></a>Дальнейшие действия

Узнайте, как создавать и взаимодействовать с картой:

> [!div class="nextstepaction"]
> [Create a map](map-create.md) (Создание карты)

Узнайте, как изменить стиль карты:

> [!div class="nextstepaction"]
> [Выбор стиля карты](choose-map-style.md)

To add more data to your map:

> [!div class="nextstepaction"]
> [Create a map](map-create.md) (Создание карты)

> [!div class="nextstepaction"]
> [Примеры кода](https://docs.microsoft.com/samples/browse/?products=azure-maps)

For a list of samples showing how to integrate Azure Active Directory (AAD) with Azure Maps, see:

> [!div class="nextstepaction"]
> [Azure AD authentication samples](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)