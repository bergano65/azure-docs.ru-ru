---
title: Общие сведения об использовании Swagger с Azure Digital Twins | Документация Майкрософт
description: Справочная документация об использовании Swagger с Azure Digital Twins.
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: v-adgera
ms.custom: seodec18
ms.openlocfilehash: c402b82b91b02f8d9619c851d09c689fd103c9fe
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67116434"
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

[![Верхней swagger](media/how-to-use-swagger/swagger_management_top.PNG)](media/how-to-use-swagger/swagger_management_top.PNG#lightbox)

Кроме того, перечислены модели объекта API управления.

[![Swagger моделей](media/how-to-use-swagger/swagger_management_models.PNG)](media/how-to-use-swagger/swagger_management_models.PNG#lightbox)

Можно выбрать каждую объектную модель объекта в списке, чтобы ознакомится с более подробным описанием атрибутов ключей.

[![Модель swagger](media/how-to-use-swagger/swagger_management_model.PNG)](media/how-to-use-swagger/swagger_management_model.PNG#lightbox)

С помощью созданных объектных моделей Swagger можно просмотреть все доступные [объекты и API](./concepts-objectmodel-spatialgraph.md) Azure Digital Twins. Этот ресурс используется разработчиками при построении решений на Azure Digital Twins.

## <a name="endpoint-summary"></a>Сводка конечных точек

Swagger также предоставляет полный обзор всех конечных точек, которые составляют API управления.

Каждая указанная конечная точка также содержит обязательную информацию о запросе, пример приведен ниже.

* Обязательные параметры.
* Типы данных обязательных параметров.
* Метод HTTP для получения доступа к ресурсу.

[![Конечные точки swagger](media/how-to-use-swagger/swagger_management_endpoints.PNG)](media/how-to-use-swagger/swagger_management_endpoints.PNG#lightbox)

Чтобы просмотреть более подробные сведения, выберите каждый ресурс.

## <a name="use-swagger-to-test-endpoints"></a>Тестирование конечных точек с помощью Swagger

Одной из эффективных функций, предоставляемых Swagger, является возможность протестировать или проверить конечную точку API непосредственно с помощью пользовательского интерфейса документации.

Выбрав конкретную конечную точку, вы увидите пункт **Попробовать**.

[![Попробуйте swagger](media/how-to-use-swagger/swagger_management_try.PNG)](media/how-to-use-swagger/swagger_management_try.PNG#lightbox)

После развертывания этого раздела появятся поля ввода для каждого обязательного и дополнительного параметров. Введите соответствующие значения и щелкните **Execute** (Выполнить).

[![Попытка swagger](media/how-to-use-swagger/swagger_management_tried.PNG)](media/how-to-use-swagger/swagger_management_tried.PNG#lightbox)

Выполнив тестирование, можно проверить данные ответа.

## <a name="swagger-response-data"></a>Данные ответа Swagger

Каждая указанная конечная точка также содержит данные текста ответа, необходимые для проверки вашей разработки и тестирования. Эти примеры включают коды нужного состояния и JSON, необходимые для успешного выполнения HTTP-запросов.

[![Ответ swagger](media/how-to-use-swagger/swagger_management_response.PNG)](media/how-to-use-swagger/swagger_management_response.PNG#lightbox)

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
    | Name  | Заменить на | Пример |
    |---------|---------|---------|
    | YOUR_SWAGGER_URL | URL-адрес документации по REST API управления, найти на портале  | `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/swagger` |

3. Предоставление разрешений вашему приложению доступ к цифровой двойниками Azure. В разделе **Требуемые разрешения** введите `Azure Digital Twins` и выберите **Делегированные разрешения**. Затем выберите **Предоставить разрешения**.

    ![API добавления регистраций приложений в Azure AD](../../includes/media/digital-twins-permissions/aad-app-req-permissions.png)

4. Настройте манифест приложения, чтобы разрешить неявный поток OAuth 2.0. Выберите **манифеста** открыть манифест приложения для вашего приложения. Для *oauth2AllowImplicitFlow* задайте значение `true`.

    ![Поток неявного предоставления разрешений Azure AD](../../includes/media/digital-twins-permissions/aad-app-allow-implicit-flow.png)

5. Скопируйте идентификатор приложения Azure AD.

После завершения регистрации Azure Active Directory:

6. Выберите **Authorize** кнопки на странице swagger.

    [![Кнопка "Авторизовать" выберите Swagger](media/how-to-use-swagger/swagger-select-authorize-btn.png)](media/how-to-use-swagger/swagger-select-authorize-btn.png#lightbox)

7. Вставьте идентификатор приложения в **client_id** поля.

    [![Поле client_id swagger](media/how-to-use-swagger/swagger-auth-form.png)](media/how-to-use-swagger/swagger-auth-form.png#lightbox)

8. Затем вы будете перенаправлены для следующих модальное успеха.

    [![Модальное окно перенаправления swagger](media/how-to-use-swagger/swagger_auth_redirect.PNG)](media/how-to-use-swagger/swagger_auth_redirect.PNG#lightbox)

## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения об объектных моделях и пространственном интеллектуальном графе Azure Digital Twins см. [здесь](./concepts-objectmodel-spatialgraph.md).

- Сведения об аутентификации с помощью API управления см. в статье [Подключение к API и аутентификация](./security-authenticating-apis.md).