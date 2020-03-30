---
title: Начало работы с управлением веб-картой Карты Microsoft Azure
description: Узнайте, как использовать систему управления картами Microsoft Azure Maps в клиентской javaScript библиотеке для визуализации карт и встроенных функций Azure Maps в веб-приложение или мобильное приложение.
author: philmea
ms.author: philmea
ms.date: 01/15/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 6becb504671c1fa380207fda9d7d553fca8ceddf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335250"
---
# <a name="use-the-azure-maps-map-control"></a>Используйте элемент управления картой Azure Maps

Библиотека JavaScript на стороне клиента Map Control позволяет визуализировать карты и внедрять функциональность Azure Maps в ваше веб-приложение или мобильное приложение.

## <a name="create-a-new-map-in-a-web-page"></a>Создание карты на веб-странице

Карту можно вставить на веб-страницу, используя клиентскую библиотеку JavaScript на стороне клиента Map Control.

1. Создайте HTML-файл.

2. Загрузите в веб-пакет SDK Azure Maps. Вы можете выбрать один из двух вариантов;

    * Используйте глобально размещенную версию CDN Web SDK Azure Maps, добавляя ссылки на JavaScript и таблицу стилей в `<head>` элемент html-файла:

        ```HTML
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>
        ```

    * Загрузите исходный код Azure Maps Web SDK локально, используя пакет NPM управления [Azure-maps,](https://www.npmjs.com/package/azure-maps-control) и разместите его с вашим приложением. Этот пакет также включает определения TypeScript.

        > **npm install azure-maps-control**

       Затем добавьте ссылки на стили и источник сценария Azure Maps в элемент `<head>` файла.

        ```HTML
        <link rel="stylesheet" href="node_modules/azure-maps-control/dist/atlas.min.css" type="text/css"> 
        <script src="node_modules/azure-maps-control/dist/atlas.min.js"></script>
        ```

    > [!Note]
    > Определения шрифта можно импортировать в приложение, добавив следующий код:
    >
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

4. В основной области страницы добавьте элемент `<div>` и присвойте ему `id`**myMap**.

   ```HTML
    <body>
        <div id="myMap"></div>
    </body>
   ```

5. Чтобы инициализировать элемент управления картой, определите новый тег скрипта в корпусе HTML. `id` Передайте на карту `<div>` или `HTMLElement` (например), `document.getElementById('myMap')`в качестве первого параметра `Map` при создании экземпляра класса. Используйте собственный ключ учетной записи Azure Maps или учетные данные Azure Active Directory (AAD), чтобы выполнить проверку подлинности сопоставления с помощью [параметров проверки подлинности](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.authenticationoptions). 

   Если вам нужно создать учетную запись или найти ключ, следуйте инструкциям в [Создать учетную запись](quick-demo-map-app.md#create-an-account-with-azure-maps) и [получить основной ключ](quick-demo-map-app.md#get-the-primary-key-for-your-account) . 

   Параметр **language** задает язык, который используется для метки схемы и элементов управления. Для получения дополнительной информации о поддерживаемых языках, [см.](supported-languages.md) Если вы используете ключ подписки для проверки подлинности, используйте следующее:

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

   Если вы используете Active Directory (AAD) для проверки подлинности, используйте следующее:

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

   Список образцов, показывающих, как интегрировать Активный каталог Azure (AAD) с Azure Maps можно найти [здесь.](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples) 
    
   Для получения дополнительной информации смотрите документ [«Аутентификация» с](azure-maps-authentication.md) помощью Azure Maps, а также [примеры аутентификации Azure Maps Azure AD.](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)

6. При необходимости возможно добавление следующих элементов метатега в заголовок вашей страницы.

   ```HTML
    <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
    <meta http-equiv="x-ua-compatible" content="IE=Edge">

    <!-- Ensures the web page looks good on all screen sizes. -->
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
   ```

7. Объединяя все это в своем HTML-файле, должен выглядеть как следующий код:

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

8. Откройте файл в веб-браузере и вы увидите готовую для просмотра карту. Он должен выглядеть как изображение ниже:

   ![Изображение карты, отображаемый результатом](./media/how-to-use-map-control/map-of-seattle.png)

## <a name="localizing-the-map"></a>Локализация карты

Azure Maps предоставляет два различных способа настройки представления языка и регионарного представления для отображаемых карт. Первый вариант заключается в добавлении этой информации в глобальное `atlas` пространство имен, что приведет к тому, что все экземпляры управления картами в приложении будут по умолчанию в этих настройках. Ниже приводится язык на французский язык ("fr-FR") и региональный вид на "Авто":

```javascript
atlas.setLanguage('fr-FR');
atlas.setView('Auto');
```

Второй вариант заключается в том, чтобы передать эту информацию в параметры карты при загрузке карты следующим образом:

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
> С помощью Web SDK можно загрузить несколько экземпляров карты на одной странице с различными настройками языка и области. Кроме того, эти параметры могут быть обновлены `setStyle` после загрузки карты с использованием функции карты. 

Вот пример Azure Maps с формулировкой "fr-FR" и региональным представлением, установленным на "Auto".

![Изображение карты, показывающей этикетки на французском языке](./media/how-to-use-map-control/websdk-localization.png)

Полный список поддерживаемых языков и региональных представлений приведен [здесь](supported-languages.md).

## <a name="azure-government-cloud-support"></a>Поддержка облака правительства Azure

Веб-SDK Azure Maps поддерживает облако правительства Azure. Все URL-адреса JavaScript и CSS, используемые для доступа к Web SDK Azure Maps, остаются прежними. Для подключения к облачной версии платформы Azure Maps необходимо выполнить следующие задачи.

При использовании интерактивного элемента управления картами добавьте `Map` следующую строку кода перед созданием экземпляра класса. 

```javascript
atlas.setDomain('atlas.azure.us');
```

При проверке подлинности карты и службы проверки подлинности Azure Maps используйте данные о проверке подлинности Azure Maps с облачной платформы Azure Government.

При использовании модуля служб домен для служб должен быть установлен при создании экземпляра конечной точки API URL. Например, следующий код создает `SearchURL` экземпляр класса и указывает домен в облако управления Azure.

```javascript
var searchURL = new atlas.service.SearchURL(pipeline, 'atlas.azure.us');
```

Если к службам Azure Maps REST был `atlas.azure.us`напрямую доступ к службам Azure Maps REST, измените домен URL на . Например, при использовании службы API поиска `https://atlas.microsoft.com/search/` `https://atlas.azure.us/search/`измените домен URL с в :

## <a name="next-steps"></a>Дальнейшие действия

Узнайте, как создавать и взаимодействовать с картой:

> [!div class="nextstepaction"]
> [Создание карты](map-create.md)

Узнайте, как изменить стиль карты:

> [!div class="nextstepaction"]
> [Выбор стиля карты](choose-map-style.md)

Чтобы добавить больше данных на карту:

> [!div class="nextstepaction"]
> [Создание карты](map-create.md)

> [!div class="nextstepaction"]
> [Примеры кода](https://docs.microsoft.com/samples/browse/?products=azure-maps)

Список примеров, показывающих, как интегрировать активный каталог Azure (AAD) с Картами Azure, см.:

> [!div class="nextstepaction"]
> [Образцы аутентификации Azure AD](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)
