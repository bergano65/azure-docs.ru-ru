---
title: Использование модуля "службы"-Azure Maps | Документация Майкрософт
description: Узнайте, как использовать модуль служб Azure Maps Services.
author: rbrundritt
ms.author: richbrun
ms.date: 03/25/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.openlocfilehash: 3911d4e780e993fdd1c2945b34cd683d47fb884a
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827275"
---
# <a name="use-the-azure-maps-services-module"></a>Использование модуля Azure Maps Services

Веб-пакет SDK для Azure Maps предоставляет *Модуль служб*. Этот модуль представляет собой вспомогательную библиотеку, которая упрощает использование служб Azure Maps RESTFUL в веб-приложениях или в приложении Node. js с помощью JavaScript или TypeScript.

## <a name="use-the-services-module-in-a-webpage"></a>Использование модуля "службы" на веб-странице

1. Создайте HTML-файл.
1. Загрузите модуль служб Azure Maps Services. Загрузить его можно одним из двух способов:
    - Используйте глобально размещенную версию сети доставки содержимого Azure модуля Azure Maps Services. Добавьте ссылку на скрипт в элемент `<head>` файла:

        ```html
        <script src="https://atlas.microsoft.com/sdk/javascript/service/2/atlas-service.min.js"></script>
        ```

    - Кроме того, можно загрузить исходный код веб-пакета SDK Azure Maps локально с помощью пакета [Azure-Maps-](https://www.npmjs.com/package/azure-maps-rest) NPM, а затем разместить его в приложении. Этот пакет также включает определения TypeScript. Используйте следующую команду:
    
        > **NPM установка Azure-Maps-RESTful**
    
        Затем добавьте ссылку на скрипт в элемент `<head>` файла:

         ```html
        <script src="node_modules/azure-maps-rest/dist/js/atlas-service.min.js"></script>
         ```

1. Создайте конвейер проверки подлинности. Прежде чем можно будет инициализировать конечную точку клиента URL-адреса службы, необходимо создать конвейер. Используйте собственный ключ учетной записи Azure Maps или учетные данные Azure Active Directory (Azure AD) для проверки подлинности клиента службы поиска Azure Maps. В этом примере будет создан клиент URL-адреса службы поиска. 

    При использовании ключа подписки для проверки подлинности:

    ```javascript
    // Get an Azure Maps key at https://azure.com/maps.
    var subscriptionKey = '<Your Azure Maps Key>';

    // Use SubscriptionKeyCredential with a subscription key.
    var subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(subscriptionKey);

    // Use subscriptionKeyCredential to create a pipeline.
    var pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential, {
      retryOptions: { maxTries: 4 } // Retry options
    });

    // Create an instance of the SearchURL client.
    var searchURL = new atlas.service.SearchURL(pipeline);
    ```

    Если для проверки подлинности используется Azure AD:

    ```javascript
    // Enter your Azure AD client ID.
    var clientId = "<Your Azure Active Directory Client Id>";

    // Use TokenCredential with OAuth token (Azure AD or Anonymous).
    var aadToken = await getAadToken();
    var tokenCredential = new atlas.service.TokenCredential(clientId, aadToken);

    // Create a repeating time-out that will renew the Azure AD token.
    // This time-out must be cleared when the TokenCredential object is no longer needed.
    // If the time-out is not cleared, the memory used by the TokenCredential will never be reclaimed.
    var renewToken = async () => {
      try {
        console.log("Renewing token");
        var token = await getAadToken();
        tokenCredential.token = token;
        tokenRenewalTimer = setTimeout(renewToken, getExpiration(token));
      } catch (error) {
        console.log("Caught error when renewing token");
        clearTimeout(tokenRenewalTimer);
        throw error;
      }
    }
    tokenRenewalTimer = setTimeout(renewToken, getExpiration(aadToken));

    // Use tokenCredential to create a pipeline.
    var pipeline = atlas.service.MapsURL.newPipeline(tokenCredential, {
      retryOptions: { maxTries: 4 } // Retry options
    });

    // Create an instance of the SearchURL client.
    var searchURL = new atlas.service.SearchURL(pipeline);

    function getAadToken() {
      // Use the signed-in auth context to get a token.
      return new Promise((resolve, reject) => {
        // The resource should always be https://atlas.microsoft.com/.
        const resource = "https://atlas.microsoft.com/";
        authContext.acquireToken(resource, (error, token) => {
          if (error) {
            reject(error);
          } else {
            resolve(token);
          }
        });
      })
    }

    function getExpiration(jwtToken) {
      // Decode the JSON Web Token (JWT) to get the expiration time stamp.
      const json = atob(jwtToken.split(".")[1]);
      const decode = JSON.parse(json);

      // Return the milliseconds remaining until the token must be renewed.
      // Reduce the time until renewal by 5 minutes to avoid using an expired token.
      // The exp property is the time stamp of the expiration, in seconds.
      const renewSkew = 300000;
      return (1000 * decode.exp) - Date.now() - renewSkew;
    }
    ```

    Дополнительные сведения см. [в статье Проверка подлинности с помощью Azure Maps](azure-maps-authentication.md).

1. В следующем коде используется созданный клиент URL-адреса службы поиска Azure Maps для геокодирования адреса: "1 Microsoft Way, Redmond, WA". Код использует функцию `searchAddress` и отображает результаты в виде таблицы в тексте страницы.

    ```javascript
    // Search for "1 microsoft way, redmond, wa".
    searchURL.searchAddress(atlas.service.Aborter.timeout(10000), '1 microsoft way, redmond, wa')
      .then(response => {
        var html = [];

        // Display the total results.
        html.push('Total results: ', response.summary.numResults, '<br/><br/>');

        // Create a table of the results.
        html.push('<table><tr><td></td><td>Result</td><td>Latitude</td><td>Longitude</td></tr>');

        for(var i=0;i<response.results.length;i++){
          html.push('<tr><td>', (i+1), '.</td><td>', 
            response.results[i].address.freeformAddress, 
            '</td><td>', 
            response.results[i].position.lat,
            '</td><td>', 
            response.results[i].position.lon,
            '</td></tr>');
        }

        html.push('</table>');

        // Add the resulting HTML to the body of the page.
        document.body.innerHTML = html.join('');
    });
    ```

    Ниже приведен полный пример кода с кодом:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Использование модуля "службы"" src="//codepen.io/azuremaps/embed/zbXGMR/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Посмотрите перо <a href='https://codepen.io/azuremaps/pen/zbXGMR/'>с помощью модуля службы</a> , Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о классах и методах, которые используются в этой статье:

> [!div class="nextstepaction"]
> [мапсурл](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.mapsurl?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [сеарчурл](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [RouteURL](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.routeurl?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [субскриптионкэйкредентиал](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.subscriptionkeycredential?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [токенкредентиал](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.tokencredential?view=azure-maps-typescript-latest)

Дополнительные примеры кода, в которых используется модуль служб, см. в следующих статьях:

> [!div class="nextstepaction"]
> [Отображение результатов поиска на карте](./map-search-location.md)

> [!div class="nextstepaction"]
> [Получение сведений на основе координат](./map-get-information-from-coordinate.md)

> [!div class="nextstepaction"]
> [Отображение направлений от точки А до точки Б](./map-route.md)