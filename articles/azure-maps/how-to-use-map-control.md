---
title: Начало работы с элементом управления веб-карт | Карты Microsoft Azure
description: Узнайте, как использовать клиентскую библиотеку JavaScript с картой Microsoft Azure Maps для отображения карт и встроенных функций Azure Maps в веб-приложения или мобильное приложение.
author: walsehgal
ms.author: v-musehg
ms.date: 01/15/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 90cd752ddd46999627de538abe3ca6b1926005aa
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/17/2020
ms.locfileid: "76264137"
---
# <a name="use-the-azure-maps-map-control"></a>Использование элемента управления картой Azure Maps

Клиентская библиотека Javascript Map Control позволяет отображать карты и встроенные функциональные возможности службы "Карты Azure" в вашем мобильном или веб-приложении.

## <a name="create-a-new-map-in-a-web-page"></a>Создание карты на веб-странице

Вы можете встроить карту в веб-страницу с помощью клиентской библиотеки Javascript службы Map Control.

1. Создайте HTML-файл.

2. Загрузите в веб-пакет SDK Azure Maps. Можно выбрать один из двух параметров.

а. Используйте размещенную на глобальном уровне версию CDN веб-пакета SDK Azure Maps, добавив конечные точки URL-адреса в таблицу стилей и источник сценария в элементе `<head>` файла.

```HTML
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>
```

b. Загрузите исходный код веб-пакета SDK Azure Maps локально с помощью пакета [Azure-Maps-Control](https://www.npmjs.com/package/azure-maps-control) NPM и разместите его в своем приложении. Этот пакет также включает определения TypeScript.

> **NPM. Установка Azure-Maps-Control**

Затем добавьте ссылки на стили и источник сценария Azure Maps в элемент `<head>` файла.

```HTML
    <link rel="stylesheet" href="node_modules/azure-maps-control/dist/atlas.min.css" type="text/css"> 
    <script src="node_modules/azure-maps-control/dist/atlas.min.js"></script>
```

    >[!Note]
    > Typescript definitions can be imported into your application by adding the following code:
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

5. Чтобы инициализировать элемент управления картой, определите новый тег скрипта в тексте HTML. Передайте `id` `<div>` карты или `HTMLElement` (например, `document.getElementById('myMap')`) в качестве первого параметра при создании экземпляра класса `Map`. Используйте собственный ключ учетной записи Azure Maps или учетные данные Azure Active Directory (AAD), чтобы выполнить проверку подлинности сопоставления с помощью [параметров проверки подлинности](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.authenticationoptions). 

Если вам нужно создать учетную запись или найти свой ключ, следуйте инструкциям в разделе [Создание учетной записи](quick-demo-map-app.md#create-an-account-with-azure-maps) и [Получение первичного ключа](quick-demo-map-app.md#get-the-primary-key-for-your-account) . 

Параметр **language** задает язык, который используется для метки схемы и элементов управления. Дополнительные сведения о поддерживаемых языках см. в разделе [Поддерживаемые языки](supported-languages.md). Если для проверки подлинности используется ключ подписки, используйте следующую команду:

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

Если для проверки подлинности используется Azure Active Directory (AAD), используйте следующую команду:

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

Список примеров, демонстрирующих интеграцию Azure Active Directory (AAD) с Azure Maps, можно найти [здесь](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples). 
    
Дополнительные сведения см. в статье [Проверка подлинности с помощью Azure Maps](azure-maps-authentication.md) документа, а также в [Azure Maps примерах проверки подлинности Azure AD](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples).

6. при необходимости в заголовке страницы можно добавить следующие элементы тега meta:

```HTML
    <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
    <meta http-equiv="x-ua-compatible" content="IE=Edge">

    <!-- Ensures the web page looks good on all screen sizes. -->
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
```

7. размещение всех файлов HTML должно выглядеть примерно так, как в следующем коде:

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

8. Откройте файл в веб-браузере и вы увидите готовую для просмотра карту. Оно должно выглядеть, как показано ниже:

![Изображение схемы, показывающее результат визуализации](./media/how-to-use-map-control/map-of-seattle.png)

## <a name="localizing-the-map"></a>Локализация схемы

Azure Maps предоставляет два разных способа настройки языкового и регионального представления для отображаемой схемы. Первый вариант — Добавить эту информацию в глобальное пространство имен `atlas`, что приведет к тому, что все экземпляры элементов управления картой в приложении будут по умолчанию иметь эти параметры. Следующий пример устанавливает для языка значение французский (fr-FR) и региональное представление "Auto":

```javascript
atlas.setLanguage('fr-FR');
atlas.setView('Auto');
```

Второй вариант заключается в передаче этой информации в параметры Map при загрузке карты следующим образом:

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
> С помощью веб-пакета SDK можно загрузить несколько экземпляров карт на одной странице с разными языками и региональными параметрами. Кроме того, эти параметры можно обновить после загрузки карт с помощью функции `setStyle` на карте. 

Ниже приведен пример Azure Maps с языком, для которого задано значение "fr-FR", а для регионального представления задано значение "Авто".

![Изображение схемы, показывающее метки на французском языке](./media/how-to-use-map-control/websdk-localization.png)

Полный список поддерживаемых языков и региональных представлений приведен [здесь](supported-languages.md).

## <a name="azure-government-cloud-support"></a>Облачная поддержка Azure для государственных организаций

Azure Maps веб-пакет SDK поддерживает облако Azure для государственных организаций. Все URL-адреса JavaScript и CSS, используемые для доступа к Azure Maps Web SDK, остаются неизменными. Для подключения к облачной версии Azure Maps платформы Azure для государственных организаций необходимо выполнить следующие задачи.

При использовании интерактивного элемента управления картой добавьте следующую строку кода перед созданием экземпляра класса `Map`. 

```javascript
atlas.setDomain('atlas.azure.us');
```

При проверке подлинности карт и служб обязательно используйте сведения о проверке подлинности Azure Maps на облачной платформе Azure для государственных организаций.

При использовании модуля служб необходимо задать домен для служб при создании экземпляра конечной точки URL-адреса API. Например, следующий код создает экземпляр класса `SearchURL` и указывает домен в облаке Azure для государственных организаций.

```javascript
var searchURL = new atlas.service.SearchURL(pipeline, 'atlas.azure.us');
```

При непосредственном доступе к службам Azure Maps RESTFUL измените домен URL-адресов на `atlas.azure.us`. Например, при использовании службы API поиска измените домен URL-адреса с `https://atlas.microsoft.com/search/` на `https://atlas.azure.us/search/`.

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

Список примеров, демонстрирующих интеграцию Azure Active Directory (AAD) с Azure Maps, см. в следующих статьях:

> [!div class="nextstepaction"]
> [Примеры проверки подлинности Azure AD](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)