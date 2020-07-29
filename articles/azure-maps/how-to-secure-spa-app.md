---
title: Как защитить одностраничное приложение с помощью неинтерактивного входа
titleSuffix: Azure Maps
description: Настройка одностраничного приложения с помощью неинтерактивного контроля доступа на основе ролей Azure AD и Azure Maps веб-пакета SDK.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/12/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: devx-track-javascript
ms.openlocfilehash: 5b7f26a03c117620be7c16abaf689763e370e5ba
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87285702"
---
# <a name="how-to-secure-a-single-page-application-with-non-interactive-sign-in"></a>Как защитить одностраничное приложение с помощью неинтерактивного входа

Следующее руководством относится к приложению, которое использует Azure Active Directory (Azure AD) для предоставления маркера доступа для Azure Maps приложений, когда пользователь не может войти в Azure AD. Для этого потока требуется размещение веб-службы, которая должна быть защищена только для доступа к веб-приложению с одной страницей. Существует несколько реализаций, которые могут выполнить проверку подлинности в Azure AD. В этом руководством для получения маркеров доступа используется продукт, функция Azure.

[!INCLUDE [authentication details](./includes/view-authentication-details.md)]

> [!Tip]
> Карты Azure могут поддерживать маркеры доступа от пользователей, которые входят в интерактивные потоки. Интерактивные потоки обеспечивают более ограниченную область доступа и управление секретами.

## <a name="create-azure-function"></a>Создание Функций Azure

Создайте защищенное приложение веб-службы, которое отвечает за проверку подлинности в Azure AD. 

1. Создайте функцию в портал Azure. Дополнительные сведения см. в статье [Создание функции Azure](https://docs.microsoft.com/azure/azure-functions/functions-create-first-azure-function).

2. Настройте политику CORS для функции Azure, чтобы она была доступна для веб-приложения с одной страницей. Это обеспечит защиту клиентов браузера от разрешенных источников веб-приложения. См. раздел [Добавление функции CORS](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-rest-api#add-cors-functionality).

3. Добавьте в функцию Azure [удостоверение, назначенное системой](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet#add-a-system-assigned-identity) , чтобы разрешить создание субъекта-службы для проверки подлинности в Azure AD.  

4. Предоставьте учетной записи Azure Maps доступ на основе ролей для назначенного системой удостоверения. Дополнительные сведения см. в разделе [предоставление доступа на основе ролей](#grant-role-based-access) .

5. Напишите код для функции Azure, чтобы получить Azure Maps маркеры доступа, используя назначенное системой удостоверение с одним из поддерживаемых механизмов или протоколом RESTFUL. См. статью [получение маркеров для ресурсов Azure](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet#add-a-system-assigned-identity) .

    Пример протокола RESTFUL:

    ```http
    GET /MSI/token?resource=https://atlas.microsoft.com/&api-version=2019-08-01 HTTP/1.1
    Host: localhost:4141
    ```

    Пример ответа:

    ```http
    HTTP/1.1 200 OK
    Content-Type: application/json

    {
        "access_token": "eyJ0eXAi…",
        "expires_on": "1586984735",
        "resource": "https://atlas.microsoft.com/",
        "token_type": "Bearer",
        "client_id": "..."
    }
    ```

6. Настройка безопасности для функции Azure HttpTrigger

   * [Создание ключа доступа функции](https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook-trigger?tabs=csharp#authorization-keys)
   * [Защищенная конечная точка HTTP](https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook-trigger?tabs=csharp#secure-an-http-endpoint-in-production) для функции Azure в рабочей среде.
   
7. Настройте веб-пакет SDK для Azure Maps Web Application. 

    ```javascript
    //URL to custom endpoint to fetch Access token
    var url = 'https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?code=<API_KEY>';

    var map = new atlas.Map('myMap', {
                center: [-122.33, 47.6],
                zoom: 12,
                language: 'en-US',
                view: "Auto",
            authOptions: {
                authType: "anonymous",
                clientId: "<insert>", // azure map account client id
                getToken: function(resolve, reject, map) {
                    fetch(url).then(function(response) {
                        return response.text();
                    }).then(function(token) {
                        resolve(token);
                    });
                }
            }
        });

        // use the following events to debug, you can remove them at any time.
        map.events.add("tokenacquired", function () {
            console.log("token acquired");
        });
        map.events.add("error", function (err) {
            console.log(JSON.stringify(err.error));
        });
    ```

## <a name="grant-role-based-access"></a>Предоставление доступа на основе ролей

Вы предоставляете *Управление доступом на основе ролей* (RBAC), назначив назначенное системой удостоверение одному или нескольким определениям ролей Azure. Чтобы просмотреть определения ролей RBAC, доступные для Azure Maps, перейдите в раздел **Управление доступом (IAM)**. Выберите **роли**, а затем найдите роли, которые начинаются с *Azure Maps*.

1. Перейдите к **учетной записи Azure Maps**. Выберите назначение ролей **управления доступом (IAM)**  >  **Role assignment**.

    > [!div class="mx-imgBorder"]
    > ![Предоставление RBAC](./media/how-to-manage-authentication/how-to-grant-rbac.png)

2. На вкладке **назначения ролей** в разделе **роль**выберите встроенное определение роли Azure Maps, например **Azure Maps модуль чтения данных** или **участник данных Azure Maps**. В разделе **назначение доступа к**выберите **приложение-функция**. Выберите участника по имени. Нажмите кнопку **Сохранить**.

   * См. Дополнительные сведения о [добавлении и удалении назначений ролей](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).

> [!WARNING]
> Azure Maps определения встроенных ролей предоставляют очень большие разрешения на доступ к множеству Azure Maps интерфейсов API. Чтобы ограничить доступ через API к минимуму, см. раздел [Создание пользовательского определения роли и назначение назначенного системой удостоверения](https://docs.microsoft.com/azure/role-based-access-control/custom-roles) пользовательскому определению роли. Это обеспечит минимальный уровень привилегий, необходимых приложению для доступа к Azure Maps.

## <a name="next-steps"></a>Дальнейшие действия

Дальнейшее понимание сценария одностраничного приложения:
> [!div class="nextstepaction"]
> [Одностраничное приложение](https://docs.microsoft.com/azure/active-directory/develop/scenario-spa-overview)

Найдите метрики использования API для учетной записи Azure Maps:
> [!div class="nextstepaction"]
> [Просмотр метрик использования](how-to-view-api-usage.md)

Ознакомьтесь с другими примерами, в которых показано, как интегрировать Azure AD с Azure Maps.
> [!div class="nextstepaction"]
> [Примеры Azure Maps](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples/tree/master/src/ClientGrant)
