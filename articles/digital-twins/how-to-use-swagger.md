---
title: Общие сведения об использовании Swagger с Azure Digital Twins | Документация Майкрософт
description: Справочная документация об использовании Swagger с Azure Digital Twins.
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 12/31/2018
ms.author: adgera
ms.custom: seodec18
ms.openlocfilehash: 1746e1d53be01e6c40b5d1948c666960970b75a0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60922996"
---
# <a name="azure-digital-twins-swagger-reference-documentation"></a>Справочная документация о Swagger с Azure Digital Twins

Каждый подготовленный экземпляр Azure Digital Twins содержит собственную автоматически созданную справочную документацию Swagger.

[Swagger](https://swagger.io/) (или[OpenAPI](https://www.openapis.org/)) объединяет сложную информацию об API в интерактивный и не зависящий от языка справочный ресурс. Swagger предоставляет критически важный справочный материал о том, какие полезные данные JSON, методы HTTP и конкретные конечные точки необходимо использовать для выполнения операций с API.

## <a name="swagger-summary"></a>Сводка по Swagger

Swagger предоставляет интерактивную сводку по вашему API, включая следующее.

* Сведения об API и модели объекта.
* Конечные точки REST API, определяющие требуемые полезные данные запроса, заголовки, параметры, пути контекста и методы HTTP.
* Тестирование функциональных возможностей API.
* Пример сведений ответа для проверки и подтверждения ответов HTTP.
* Сведения о коде ошибки.

Таким образом, Swagger является удобным средством для разработки и тестирования вызовов к API управления Azure Digital Twins.

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

## <a name="reference-material"></a>Справочные материалы

Автоматически сгенерированный справочный материал о Swagger содержит краткий обзор важных концепций, доступных конечных точек API управления и описание каждой объектной модели, помогающей в разработке и тестировании.

В краткой сводке описывается API.

![Верхняя панель Swagger][1]

Кроме того, перечислены модели объекта API управления.

![Модели Swagger][2]

Можно выбрать каждую объектную модель объекта в списке, чтобы ознакомится с более подробным описанием атрибутов ключей.

![Модель Swagger][3]

С помощью созданных объектных моделей Swagger можно просмотреть все доступные [объекты и API](./concepts-objectmodel-spatialgraph.md) Azure Digital Twins. Этот ресурс используется разработчиками при построении решений на Azure Digital Twins.

## <a name="endpoint-summary"></a>Сводка конечных точек

Swagger также предоставляет полный обзор всех конечных точек, которые составляют API управления.

Каждая указанная конечная точка также содержит обязательную информацию о запросе, пример приведен ниже.

* Обязательные параметры.
* Типы данных обязательных параметров.
* Метод HTTP для получения доступа к ресурсу.

![Конечные точки Swagger][4]

Чтобы просмотреть более подробные сведения, выберите каждый ресурс.

## <a name="use-swagger-to-test-endpoints"></a>Тестирование конечных точек с помощью Swagger

Одной из эффективных функций, предоставляемых Swagger, является возможность протестировать или проверить конечную точку API непосредственно с помощью пользовательского интерфейса документации.

Выбрав конкретную конечную точку, вы увидите пункт **Попробовать**.

![Пробное использование Swagger][5]

После развертывания этого раздела появятся поля ввода для каждого обязательного и дополнительного параметров. Введите соответствующие значения и щелкните **Execute** (Выполнить).

![Пробное использование Swagger][6]

Выполнив тестирование, можно проверить данные ответа.

## <a name="swagger-response-data"></a>Данные ответа Swagger

Каждая указанная конечная точка также содержит данные текста ответа, необходимые для проверки вашей разработки и тестирования. Эти примеры включают коды нужного состояния и JSON, необходимые для успешного выполнения HTTP-запросов.

![Ответ Swagger][7]

Примеры также включают коды ошибок, которые помогают отлаживать или улучшать неудачные тестирования.

## <a name="swagger-oauth-20-authorization"></a>Авторизация Swagger OAuth 2.0

Дополнительные сведения об интерактивном тестировании запросов, защищенных с помощью OAuth 2.0, см. в [официальной документации](https://swagger.io/docs/specification/authentication/oauth2/).

> [!NOTE]
> Участника-пользователя, создавшего ресурсов цифровой двойниками Azure будет иметь назначение ролей администратора пространства и будут иметь возможность создания дополнительных назначений ролей для других пользователей.

1. Выполните действия, описанные в [в этом кратком руководстве](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad) для создания приложения Azure AD типа ***веб-приложение или API***. Или вы можете повторно использовать имеющуюся регистрацию приложения.

2. Добавьте следующий URL-адрес ответа для регистрации приложения:

    ```plaintext
    https://YOUR_SWAGGER_URL/ui/oauth2-redirect-html
    ```
    | ИМЯ  | Заменить на | Пример |
    |---------|---------|---------|
    | YOUR_SWAGGER_URL | URL-адрес документации по REST API управления, найти на портале  | `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/swagger` |

3. Предоставление разрешений вашему приложению доступ к цифровой двойниками Azure. В разделе **Требуемые разрешения** введите `Azure Digital Twins` и выберите **Делегированные разрешения**. Затем выберите **Предоставить разрешения**.

    ![API добавления регистраций приложений в Azure AD](../../includes/media/digital-twins-permissions/aad-app-req-permissions.png)

4. Настройте манифест приложения, чтобы разрешить неявный поток OAuth 2.0. Щелкните **Манифест**, чтобы открыть манифест вашего приложения. Для *oauth2AllowImplicitFlow* задайте значение `true`.

    ![Поток неявного предоставления разрешений Azure AD](../../includes/media/digital-twins-permissions/aad-app-allow-implicit-flow.png)

5. Скопируйте идентификатор приложения Azure AD.

6. На странице swagger, нажмите кнопку Authorize.

    ![Кнопка "Авторизовать" swagger](../../includes/media/digital-twins-permissions/swagger-select-authorize-btn.png)

7. Вставьте идентификатор приложения в поле client_id.

    ![Поле client_id swagger](../../includes/media/digital-twins-permissions/swagger-auth-form.png)

    ![Swagger GRANT, предоставление разрешений приложения](../../includes/media/digital-twins-permissions/swagger-grant-application-permissions.png)

8. Теперь вы увидите носителю токена проверки подлинности, передаваемых в заголовке авторизации и удостоверение пользователя отображается в результатах.

    ![Результат маркера swagger](../../includes/media/digital-twins-permissions/swagger-token-example.png)

## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения об объектных моделях и пространственном интеллектуальном графе Azure Digital Twins см. [здесь](./concepts-objectmodel-spatialgraph.md).

- Сведения об аутентификации с помощью API управления см. в статье [Подключение к API и аутентификация](./security-authenticating-apis.md).

<!-- Images -->
[1]: media/how-to-use-swagger/swagger_management_top.PNG
[2]: media/how-to-use-swagger/swagger_management_models.PNG
[3]: media/how-to-use-swagger/swagger_management_model.PNG
[4]: media/how-to-use-swagger/swagger_management_endpoints.PNG
[5]: media/how-to-use-swagger/swagger_management_try.PNG
[6]: media/how-to-use-swagger/swagger_management_tried.PNG
[7]: media/how-to-use-swagger/swagger_management_response.PNG
