---
title: С помощью модуля службы - "карты Azure" | Документация Майкрософт
description: Узнайте, как использовать модуль службы "карты Azure".
author: rbrundritt
ms.author: richbrun
ms.date: 03/25/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.openlocfilehash: 3aa39568904cb0acc9b5b76ff7e07729bf99d6b1
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59278604"
---
# <a name="using-the-azure-maps-services-module"></a>С помощью модуля службы Azure Maps

SDK веб-карты Azure предоставляет службы модуля, который находится вспомогательную библиотеку, которая упрощает использование служб Azure Maps REST в или веб-приложений Node.js с помощью JavaScript или TypeScript.

## <a name="using-the-services-module-in-a-web-page"></a>С помощью модуля службы на веб-странице

1. Создайте HTML-файл.
2. Загрузить в модуле сопоставления служб Azure. Это можно сделать с помощью одного из двух параметров.

    1. Использовать CDN глобально размещенную версию модуля службы "карты Azure", добавив ссылку на скрипт <head> элемент файла:
    
    ```html
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas-service.min.js"></script>
    ```
    
    2. Кроме того, загрузить исходный код SDK веб-сопоставляет Azure локально с помощью [azure maps-rest](https://www.npmjs.com/package/azure-maps-rest) NPM упаковки и разместить его в приложении. Этот пакет также включает определения TypeScript.
    
    > npm install azure-maps-rest
    
    Затем добавьте ссылку на скрипт `<head>` элемент файла:
    
    ```html
    <script src="node_modules/azure-maps-rest/dist/js/atlas-service.min.js"></script>
    ```

3. Чтобы инициализировать клиент URL-адрес конечной точки службы, необходимо сначала создать конвейер с проверки подлинности. Используйте собственный ключ учетной записи Azure Maps или учетные данные Azure Active Directory (AAD) для проверки подлинности клиента службы поиска. В этом примере будет создан клиент URL-адрес службы поиска. Если вы используете ключ подписки для проверки подлинности:

    ```javascript
    //Get an Azure Maps key at https://azure.com/maps
    var subscriptionKey = '<Your Azure Maps Key>';
    
    //Use SubscriptionKeyCredential with a subscription key.
    var subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(subscriptionKey);
    
    //Use subscriptionKeyCredential to create a pipeline.
    var pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential, {
      retryOptions: { maxTries: 4 } // Retry options
    });
    
    //Create an instance of the SearchURL client.
    var searchURL = new atlas.service.SearchURL(pipeline);
    ```
    
    Если для проверки подлинности используется Azure Active Directory (AAD):

    ```javascript
    // Enter your Azure Actiuve Directory client ID.
    var clientId = "<Your Azure Active Directory Client Id>";
    
    // Use TokenCredential with OAuth token (AAD or Anonymous).
    var aadToken = await getAadToken();
    var tokenCredential = new atlas.service.TokenCredential(clientId, aadToken);
    
    // Create a repeating timeout that will renew the AAD token.
    // This timeout must be cleared once the TokenCredential object is no longer needed.
    // If the timeout is not cleared the memory used by the TokenCredential will never be reclaimed.
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
    
    // Use tokenCredential to create a pipeline
    var pipeline = atlas.service.MapsURL.newPipeline(tokenCredential, {
        retryOptions: { maxTries: 4 } // Retry options
    });
    
    //Create an instance of the SearchURL client.
    var searchURL = new atlas.service.SearchURL(pipeline);

    function getAadToken() {
        //Use the logged in auth context to get a token.
        return new Promise((resolve, reject) => {
            //The resource should always be https://atlas.microsoft.com/.
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
        //Decode the JWT token to get the expiration timestamp.
        const json = atob(jwtToken.split(".")[1]);
        const decode = JSON.parse(json);

        //Return the milliseconds until the token needs renewed.
        //Reduce the time until renew by 5 minutes to avoid using an expired token.
        //The exp property is the timestamp of the expiration in seconds.
        const renewSkew = 300000;
        return (1000 * decode.exp) - Date.now() - renewSkew;
    }
    ```

    Дополнительные сведения см. в разделе [проверки подлинности с помощью Azure Maps](azure-maps-authentication.md).

4. В следующем коде используется клиент URL-адрес службы поиска только что созданный выполним геокодирование это адрес, с помощью «1 Microsoft Way, Redmond, WA» `searchAddress` функции и отображения результатов в виде таблицы в основной области страницы. 

    ```javascript
    //Search for "1 microsoft way, redmond, wa".
    searchURL.searchAddress(atlas.service.Aborter.timeout(10000), '1 microsoft way, redmond, wa').then(response => {
      var html = [];
      
      //Display the total results.
      html.push('Total results: ', response.summary.numResults, '<br/><br/>');
     
      //Create a table of the results.
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
      
      //Add the result HTML to the body of the page.
      document.body.innerHTML = html.join('');
    });
    ```

    Ниже приведен полный запуск примера кода.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="С помощью модуля служб" src="//codepen.io/azuremaps/embed/zbXGMR/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
См. в разделе пера <a href='https://codepen.io/azuremaps/pen/zbXGMR/'>с помощью модуля службы</a> по Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о классах и методах, которые используются в этой статье:

> [!div class="nextstepaction"]
> [MapsURL](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.mapsurl?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [SearchURL](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [RouteURL](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.routeurl?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [SubscriptionKeyCredential](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.subscriptionkeycredential?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [tokenCredential](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.tokencredential?view=azure-iot-typescript-latest)

Ознакомьтесь со следующими статьями, Дополнительные примеры кода для использования модуля служб:

> [!div class="nextstepaction"]
> [Отображение результатов поиска на карте](./map-search-location.md)

> [!div class="nextstepaction"]
> [Получение сведений на основе координат](./map-get-information-from-coordinate.md)

> [!div class="nextstepaction"]
> [Отображение направлений от точки А до точки Б](./map-route.md)