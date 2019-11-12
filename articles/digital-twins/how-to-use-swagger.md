---
title: Общие сведения об использовании Swagger с Azure Digital Twins | Документация Майкрософт
description: Справочная документация об использовании Swagger с Azure Digital Twins.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/03/2019
ms.custom: seodec18
ms.openlocfilehash: 6a99693c3f88e94c093910da6ba4a305c26fe39d
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/12/2019
ms.locfileid: "73931343"
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

[![в начало Swagger](media/how-to-use-swagger/swagger-management-top-img.png)](media/how-to-use-swagger/swagger-management-top-img.png#lightbox)

Кроме того, перечислены модели объекта API управления.

[![моделей Swagger](media/how-to-use-swagger/swagger-management-models-img.png)](media/how-to-use-swagger/swagger-management-models-img.png#lightbox)

Можно выбрать каждую объектную модель объекта в списке, чтобы ознакомится с более подробным описанием атрибутов ключей.

[![ная модель Swagger](media/how-to-use-swagger/swagger-management-model-img.png)](media/how-to-use-swagger/swagger-management-model-img.png#lightbox)

С помощью созданных объектных моделей Swagger можно просмотреть все доступные [объекты и API](./concepts-objectmodel-spatialgraph.md) Azure Digital Twins. Этот ресурс используется разработчиками при построении решений на Azure Digital Twins.

## <a name="endpoint-summary"></a>Сводка конечных точек

Swagger также предоставляет полный обзор всех конечных точек, которые составляют API управления.

Каждая указанная конечная точка также содержит обязательную информацию о запросе, пример приведен ниже.

* Обязательные параметры.
* Типы данных обязательных параметров.
* Метод HTTP для получения доступа к ресурсу.

[![конечных точек Swagger](media/how-to-use-swagger/swagger-management-endpoints-img.png)](media/how-to-use-swagger/swagger-management-endpoints-img.png#lightbox)

Чтобы просмотреть более подробные сведения, выберите каждый ресурс.

## <a name="use-swagger-to-test-endpoints"></a>Тестирование конечных точек с помощью Swagger

Одной из эффективных функций, предоставляемых Swagger, является возможность протестировать или проверить конечную точку API непосредственно с помощью пользовательского интерфейса документации.

Выбрав конкретную конечную точку, вы увидите пункт **Попробовать**.

[![Swagger try](media/how-to-use-swagger/swagger-management-try-img.png)](media/how-to-use-swagger/swagger-management-try-img.png#lightbox)

После развертывания этого раздела появятся поля ввода для каждого обязательного и дополнительного параметров. Введите соответствующие значения и щелкните **Execute** (Выполнить).

[![Swagger попытался](media/how-to-use-swagger/swagger-management-tried-img.png)](media/how-to-use-swagger/swagger-management-tried-img.png#lightbox)

Выполнив тестирование, можно проверить данные ответа.

## <a name="swagger-response-data"></a>Данные ответа Swagger

Каждая указанная конечная точка также содержит данные текста ответа, необходимые для проверки вашей разработки и тестирования. Эти примеры включают коды нужного состояния и JSON, необходимые для успешного выполнения HTTP-запросов.

[![ный ответ Swagger](media/how-to-use-swagger/swagger-management-response-img.png)](media/how-to-use-swagger/swagger-management-response-img.png#lightbox)

Примеры также включают коды ошибок, которые помогают отлаживать или улучшать неудачные тестирования.

## <a name="swagger-oauth-20-authorization"></a>Авторизация Swagger OAuth 2.0

> [!NOTE]
> * Субъект-пользователь, создавший ресурс Azure Digital двойников, будет иметь назначение роли администратора пространства и сможет создавать дополнительные назначения ролей для других пользователей. Такие пользователи и их роли могут быть разрешены для вызова API-интерфейсов.

1. Выполните действия, описанные в [этом кратком руководстве](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad) , чтобы создать и настроить приложение Azure AD. Кроме того, можно повторно использовать существующую регистрацию приложения.

1. Добавьте следующий URL-адрес ответа в регистрацию приложения:

    ```plaintext
    https://YOUR_SWAGGER_URL/ui/oauth2-redirect-html
    ```
    | имя  | Заменить на | Пример |
    |---------|---------|---------|
    | YOUR_SWAGGER_URL | На портале найден URL-адрес документации по REST API управления  | `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/swagger` |

1. Скопируйте идентификатор приложения Azure AD.

После завершения регистрации Azure Active Directory:

1. Нажмите кнопку **авторизовать** на странице Swagger.

    [![нажмите кнопку "авторизовать" Swagger](media/how-to-use-swagger/swagger-select-authorize-btn.png)](media/how-to-use-swagger/swagger-select-authorize-btn.png#lightbox)

1. Вставьте идентификатор приложения в поле **client_id** .

    [![поле Swagger client_id](media/how-to-use-swagger/swagger-auth-form.png)](media/how-to-use-swagger/swagger-auth-form.png#lightbox)

1. Затем вы будете перенаправлены к следующему модальному результату.

    [![модальное перенаправление Swagger](media/how-to-use-swagger/swagger-auth-redirect-img.png)](media/how-to-use-swagger/swagger-auth-redirect-img.png#lightbox)

Дополнительные сведения об интерактивном тестировании запросов, защищенных с помощью OAuth 2.0, см. в [официальной документации](https://swagger.io/docs/specification/authentication/oauth2/).

## <a name="next-steps"></a>Дополнительная информация

- Дополнительные сведения об объектных моделях и пространственном интеллектуальном графе Azure Digital Twins см. [здесь](./concepts-objectmodel-spatialgraph.md).

- Сведения об аутентификации с помощью API управления см. в статье [Подключение к API и аутентификация](./security-authenticating-apis.md).