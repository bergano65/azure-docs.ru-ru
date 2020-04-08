---
title: Используйте модуль службы обслуживания Azure Maps Карты Microsoft Azure
description: В этой статье вы узнаете, как использовать службы Microsoft Azure Maps REST с помощью модуля служб Azure Maps.
author: rbrundritt
ms.author: richbrun
ms.date: 03/25/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.openlocfilehash: 6e9d1f35d021c3381f9c2887dfb1c150bb720871
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804662"
---
# <a name="use-the-azure-maps-services-module"></a>Используйте модуль служб ы Azure Maps

Azure Maps Web SDK предоставляет *модуль услуг.* Этот модуль является библиотекой помощников, которая упрощает использование служб Azure Maps REST в веб-приложениях или приложениях Node.js с помощью JavaScript или TypeScript.

## <a name="use-the-services-module-in-a-webpage"></a>Использование модуля услуг на веб-странице

1. Создайте HTML-файл.
1. Загрузите модуль служб Azure Maps. Вы можете загрузить его одним из двух способов:
    - Используйте глобально размещенную версию системы доставки содержимого Azure ВИО в модуле сервисов Azure Maps. Добавьте ссылку `<head>` на сценарий к элементу файла:

        ```html
        <script src="https://atlas.microsoft.com/sdk/javascript/service/2/atlas-service.min.js"></script>
        ```

    - Кроме того, загрузите модуль служб для исходного кода Azure Maps Web SDK локально, используя [пакет azure-maps-rest](https://www.npmjs.com/package/azure-maps-rest) npm, а затем разместите его с вашим приложением. Этот пакет также включает определения TypeScript. Выполните эту команду:
    
        > **npm install azure-maps-rest**
    
        Затем добавьте ссылку `<head>` на сценарий к элементу файла:

         ```html
        <script src="node_modules/azure-maps-rest/dist/atlas-service.min.js"></script>
         ```

1. Создание конвейера аутентификации. Конвейер должен быть создан, прежде чем вы сможете инициализировать конечную точку URL-адреса службы. Используйте свой собственный ключ учетной записи Azure Maps или учетные данные Active Directory (Azure AD) для проверки подлинности клиента службы поиска Azure Maps. В этом примере будет создан URL-клиент поисковой службы. 

    Если вы используете ключ подписки для проверки подлинности:

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

    Если вы используете Azure AD для проверки подлинности:

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

    Для получения дополнительной информации смотрите [аутентификацию с Azure Maps](azure-maps-authentication.md).

1. Следующий код использует недавно созданный url-клиент службы поиска Azure Maps для геокодирования адреса: "1 Microsoft Way, Redmond, WA". Код использует `searchAddress` функцию и отображает результаты в виде таблицы в теле страницы.

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

    Вот полный, работает образец кода:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Использование модуля услуг" src="//codepen.io/azuremaps/embed/zbXGMR/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Смотрите перо <a href='https://codepen.io/azuremaps/pen/zbXGMR/'>С помощью модуля услуг</a> по Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

## <a name="azure-government-cloud-support"></a>Поддержка облака правительства Azure

Веб-SDK Azure Maps поддерживает облако правительства Azure. Все URL-адреса JavaScript и CSS, используемые для доступа к Web SDK Azure Maps, остаются прежними, однако для подключения к облачной версии платформы Azure Maps необходимо будет выполнить следующие задачи.

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

Дополнительные сведения о классах и методах, которые используются в этой статье:

> [!div class="nextstepaction"]
> [КартыURL](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.mapsurl?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [SearchURL](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [RouteURL](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.routeurl?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [ПодпискаKeyCredential](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.subscriptionkeycredential?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [ТокенCredential](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.tokencredential?view=azure-maps-typescript-latest)

Дополнительные примеры кода, которые используют модуль служб, см.

> [!div class="nextstepaction"]
> [Отображение результатов поиска на карте](./map-search-location.md)

> [!div class="nextstepaction"]
> [Получение сведений на основе координат](./map-get-information-from-coordinate.md)

> [!div class="nextstepaction"]
> [Отображение направлений от точки А до точки Б](./map-route.md)
