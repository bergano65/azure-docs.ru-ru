---
title: Начало работы с элементом управления Web Map в Azure Maps | Документация Майкрософт
description: Узнайте, как использовать библиотеку JavaScript Azure Maps Map Control Client на стороне клиента.
author: walsehgal
ms.author: v-musehg
ms.date: 10/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 4fd213a6550d045fe4bea280449ed228ba9eda35
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72550275"
---
# <a name="use-the-azure-maps-map-control"></a>Использование элемента управления картой Azure Maps

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

5. Для инициализации элемента управления картой определите новый раздел в тексте html и создайте сценарий. Передайте `id` `<div>` карты или `HTMLElement` (например, `document.getElementById('myMap')`) в качестве первого параметра при создании экземпляра класса `Map`. Используйте собственный ключ учетной записи Azure Maps или учетные данные Azure Active Directory (AAD), чтобы выполнить проверку подлинности сопоставления с помощью [параметров проверки подлинности](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.authenticationoptions). Если вам нужно создать учетную запись или найти ключ, см. статью об [управлении учетной записью и ключами в Azure Maps](how-to-manage-account-keys.md). Параметр **language** задает язык, который используется для метки схемы и элементов управления. Полный список поддерживаемых языков см. в разделе [Поддерживаемые языки](supported-languages.md). Если используется ключ подписки для проверки подлинности.

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

    Дополнительные сведения см. в статье [Проверка подлинности с помощью Azure Maps](azure-maps-authentication.md) документа.

6. При необходимости возможно добавление следующих элементов метатега в заголовок вашей страницы.

    ```HTML
    <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
    <meta http-equiv="x-ua-compatible" content="IE=Edge">

    <!-- Ensures the web page looks good on all screen sizes. -->
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
    ```

7. Все файлы HTML должны выглядеть примерно так, как в следующем коде:

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

8. Откройте файл в веб-браузере и вы увидите готовую для просмотра карту. Он должен выглядеть, как в следующем коде:

    <iframe height="700" style="width: 100%;" scrolling="no" title="Использование элемента управления картой" src="//codepen.io/azuremaps/embed/yZpEYL/?height=557&theme-id=0&default-tab=html,result" frameborder="no" allowtransparency="true" allowfullscreen="true">См. сведения <a href='https://codepen.io/azuremaps/pen/yZpEYL/'>об использовании элемента управления Map</a> с помощью Azure Maps (<a href='https://codepen.io/azuremaps'> @azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
    </iframe>

## <a name="localizing-the-map"></a>Локализация схемы

Azure Maps предоставляет два разных способа настройки языкового и регионального представления для схемы. Первый вариант — Добавить эту информацию в глобальное пространство имен `atlas`, что приведет к тому, что все экземпляры элементов управления картой в приложении будут по умолчанию иметь эти параметры. Следующий пример устанавливает для языка значение французский (fr-FR) и региональное представление "Auto":

```javascript
atlas.setLanguage('fr-FR');
atlas.setView('auto');
```

Второй вариант — передать эту информацию в параметры карты при загрузке карты следующим образом:

```javascript
map = new atlas.Map('myMap', {
    language: 'fr-FR',
    view: 'auto',

    authOptions: {
        authType: 'aad',
        clientId: '<Your AAD Client Id>',
        aadAppId: '<Your AAD App Id>',
        aadTenant: '<Your AAD Tenant Id>'
    }
});
```

> [!Note]
> С помощью веб-пакета SDK можно загрузить несколько экземпляров карт на одной странице с разными языками и региональными параметрами. Кроме того, эти параметры можно обновить после загрузки схемы с помощью функции `setStyle` на карте. 

Ниже приведен пример Azure Maps с языком, для которого задано значение "fr-FR", а для регионального представления задано значение "Авто".

![Изображение схемы, показывающее метки на французском языке](./media/how-to-use-map-control/websdk-localization.png)

Полный список поддерживаемых языков и региональных представлений приведен [здесь](supported-languages.md).

## <a name="next-steps"></a>Дальнейшие действия

Узнайте, как создавать и взаимодействовать с картой:

> [!div class="nextstepaction"]
> [Create a map](map-create.md) (Создание карты)

Узнайте, как изменить стиль карты:

> [!div class="nextstepaction"]
> [Выбор стиля карты](choose-map-style.md)

Чтобы добавить дополнительные данные на карту, выполните следующие действия.

> [!div class="nextstepaction"]
> [Create a map](map-create.md) (Создание карты)

> [!div class="nextstepaction"]
> [Примеры кода](https://docs.microsoft.com/samples/browse/?products=azure-maps)