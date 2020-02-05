---
title: Как использовать справочную документацию по Swagger — Azure Digital двойников | Документация Майкрософт
description: Справочная документация об использовании Swagger с Azure Digital Twins.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 02/03/2020
ms.custom: seodec18
ms.openlocfilehash: 27874e5731bd6fb9821e7aeda9333adbdbb79099
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/05/2020
ms.locfileid: "77023299"
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

[![сводки Swagger и сведения об API](media/how-to-use-swagger/swagger-management-top-img.png)](media/how-to-use-swagger/swagger-management-top-img.png#lightbox)

Кроме того, перечислены модели объекта API управления.

[![моделей Swagger, перечисленных в нижней части пользовательского интерфейса Swagger](media/how-to-use-swagger/swagger-management-models-img.png)](media/how-to-use-swagger/swagger-management-models-img.png#lightbox)

Можно выбрать каждую объектную модель объекта в списке, чтобы ознакомится с более подробным описанием атрибутов ключей.

[![развернутые модели Swagger для чтения содержимого моделей](media/how-to-use-swagger/swagger-management-model-img.png)](media/how-to-use-swagger/swagger-management-model-img.png#lightbox)

Созданные модели объектов Swagger удобны для чтения всех доступных [объектов и API-интерфейсов](./concepts-objectmodel-spatialgraph.md)Azure Digital двойников. Этот ресурс используется разработчиками при построении решений на Azure Digital Twins.

## <a name="endpoint-summary"></a>Сводка конечных точек

Swagger также предоставляет полный обзор всех конечных точек, которые составляют API управления.

Каждая указанная конечная точка также содержит обязательную информацию о запросе, пример приведен ниже.

* Обязательные параметры.
* Типы данных обязательных параметров.
* Метод HTTP для получения доступа к ресурсу.

[![конечных точек Swagger, отображаемых в пользовательском интерфейсе Swagger](media/how-to-use-swagger/swagger-management-endpoints-img.png)](media/how-to-use-swagger/swagger-management-endpoints-img.png#lightbox)

Выберите каждый ресурс для просмотра его дополнительного содержимого, чтобы получить более подробный обзор.

## <a name="use-swagger-to-test-endpoints"></a>Тестирование конечных точек с помощью Swagger

Одной из эффективных функций, предоставляемых Swagger, является возможность протестировать или проверить конечную точку API непосредственно с помощью пользовательского интерфейса документации.

После выбора конкретной конечной точки появится кнопка **попробовать** .

[Кнопка ![Swagger try](media/how-to-use-swagger/swagger-management-try-img.png)](media/how-to-use-swagger/swagger-management-try-img.png#lightbox)

После развертывания этого раздела появятся поля ввода для каждого обязательного и дополнительного параметров. Введите соответствующие значения и щелкните **Execute** (Выполнить).

[Пример результата ![Swagger try](media/how-to-use-swagger/swagger-management-tried-img.png)](media/how-to-use-swagger/swagger-management-tried-img.png#lightbox)

Выполнив тестирование, можно проверить данные ответа.

## <a name="swagger-response-data"></a>Данные ответа Swagger

Каждая указанная конечная точка также содержит данные текста ответа, необходимые для проверки вашей разработки и тестирования. Эти примеры включают коды состояния и JSON для успешных HTTP-запросов.

[Пример ответа JSON для ![Swagger](media/how-to-use-swagger/swagger-management-response-img.png)](media/how-to-use-swagger/swagger-management-response-img.png#lightbox)

Примеры также включают коды ошибок, которые помогают отлаживать или улучшать неудачные тестирования.

## <a name="swagger-oauth-20-authorization"></a>Авторизация Swagger OAuth 2.0

> [!NOTE]
> * Субъект-пользователь, создавший ресурс Azure Digital двойников, будет иметь назначение роли администратора пространства и сможет создавать дополнительные назначения ролей для других пользователей. Эти пользователи и их роли могут быть разрешены для вызова API-интерфейсов.

1. Выполните действия, описанные в [кратком руководстве](quickstart-view-occupancy-dotnet.md#set-permissions-for-your-app) , чтобы создать и настроить приложение Azure Active Directory. Кроме того, можно повторно использовать существующую регистрацию приложения.

1. Добавьте следующий **URI перенаправления** в регистрацию приложения Azure Active Directory:

    [![зарегистрировать URL-адрес перенаправления Swagger в AAD](media/how-to-use-swagger/swagger-aad-redirect-url-registration.png)](media/how-to-use-swagger/swagger-aad-redirect-url-registration.png#lightbox)

    ```plaintext
    https://YOUR_SWAGGER_URL/ui/oauth2-redirect-html
    ```
    | Имя  | Заменить на | Пример |
    |---------|---------|---------|
    | YOUR_SWAGGER_URL | На портале найден URL-адрес документации по REST API управления  | `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/swagger` |

1. Установите флажок **неявное предоставление** > **маркеров доступа** , чтобы разрешить использование потока неявного предоставления OAuth 2,0. Выберите **Настройка**, а затем **сохранить**.

1. Скопируйте **идентификатор клиента** приложения Azure Active Directory.

После завершения регистрации Azure Active Directory:

1. Нажмите кнопку **авторизовать** на странице Swagger.

    [![нажмите кнопку "авторизовать" Swagger](media/how-to-use-swagger/swagger-select-authorize-btn.png)](media/how-to-use-swagger/swagger-select-authorize-btn.png#lightbox)

1. Вставьте идентификатор приложения в поле **client_id** .

    [![поле Swagger client_id](media/how-to-use-swagger/swagger-auth-form.png)](media/how-to-use-swagger/swagger-auth-form.png#lightbox)

1. Затем вы будете перенаправлены к следующему модальному результату.

    [![модальное перенаправление Swagger](media/how-to-use-swagger/swagger-auth-redirect-img.png)](media/how-to-use-swagger/swagger-auth-redirect-img.png#lightbox)

Дополнительные сведения о интерактивном тестировании запросов, защищенных с помощью OAuth 2,0, см. в [официальной документации](https://swagger.io/docs/specification/authentication/oauth2/).

## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения об объектных моделях и пространственном интеллектуальном графе Azure Digital Twins см. [здесь](./concepts-objectmodel-spatialgraph.md).

- Сведения об аутентификации с помощью API управления см. в статье [Подключение к API и аутентификация](./security-authenticating-apis.md).
