---
title: Общие сведения об использовании Swagger с Azure Digital Twins | Документация Майкрософт
description: Справочная документация об использовании Swagger с Azure Digital Twins.
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 06/29/2019
ms.author: v-adgera
ms.custom: seodec18
ms.openlocfilehash: 9d4fd798309398dff38081cc66b4071c5dd1f5bf
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67670788"
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

[![Верхней swagger](media/how-to-use-swagger/swagger-management-top-img.png)](media/how-to-use-swagger/swagger-management-top-img.png#lightbox)

Кроме того, перечислены модели объекта API управления.

[![Swagger моделей](media/how-to-use-swagger/swagger-management-models-img.png)](media/how-to-use-swagger/swagger-management-models-img.png#lightbox)

Можно выбрать каждую объектную модель объекта в списке, чтобы ознакомится с более подробным описанием атрибутов ключей.

[![Модель swagger](media/how-to-use-swagger/swagger-management-model-img.png)](media/how-to-use-swagger/swagger-management-model-img.png#lightbox)

С помощью созданных объектных моделей Swagger можно просмотреть все доступные [объекты и API](./concepts-objectmodel-spatialgraph.md) Azure Digital Twins. Этот ресурс используется разработчиками при построении решений на Azure Digital Twins.

## <a name="endpoint-summary"></a>Сводка конечных точек

Swagger также предоставляет полный обзор всех конечных точек, которые составляют API управления.

Каждая указанная конечная точка также содержит обязательную информацию о запросе, пример приведен ниже.

* Обязательные параметры.
* Типы данных обязательных параметров.
* Метод HTTP для получения доступа к ресурсу.

[![Конечные точки swagger](media/how-to-use-swagger/swagger-management-endpoints-img.png)](media/how-to-use-swagger/swagger-management-endpoints-img.png#lightbox)

Чтобы просмотреть более подробные сведения, выберите каждый ресурс.

## <a name="use-swagger-to-test-endpoints"></a>Тестирование конечных точек с помощью Swagger

Одной из эффективных функций, предоставляемых Swagger, является возможность протестировать или проверить конечную точку API непосредственно с помощью пользовательского интерфейса документации.

Выбрав конкретную конечную точку, вы увидите пункт **Попробовать**.

[![Попробуйте swagger](media/how-to-use-swagger/swagger-management-try-img.png)](media/how-to-use-swagger/swagger-management-try-img.png#lightbox)

После развертывания этого раздела появятся поля ввода для каждого обязательного и дополнительного параметров. Введите соответствующие значения и щелкните **Execute** (Выполнить).

[![Попытка swagger](media/how-to-use-swagger/swagger-management-tried-img.png)](media/how-to-use-swagger/swagger-management-tried-img.png#lightbox)

Выполнив тестирование, можно проверить данные ответа.

## <a name="swagger-response-data"></a>Данные ответа Swagger

Каждая указанная конечная точка также содержит данные текста ответа, необходимые для проверки вашей разработки и тестирования. Эти примеры включают коды нужного состояния и JSON, необходимые для успешного выполнения HTTP-запросов.

[![Ответ swagger](media/how-to-use-swagger/swagger-management-response-img.png)](media/how-to-use-swagger/swagger-management-response-img.png#lightbox)

Примеры также включают коды ошибок, которые помогают отлаживать или улучшать неудачные тестирования.

## <a name="swagger-oauth-20-authorization"></a>Авторизация Swagger OAuth 2.0

> [!NOTE]
> * Участника-пользователя, создавшего ресурсов цифровой двойниками Azure будет иметь назначение ролей администратора пространства и будут иметь возможность создания дополнительных назначений ролей для других пользователей. Для вызова API можно авторизовать таких пользователей и их роли.

1. Выполните действия, описанные в [в этом кратком руководстве](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad) или [Регистрация приложения Azure цифровой Двойников с помощью Azure Active Directory прежних версий](./how-to-use-legacy-aad.md) для создания и настройки приложения Azure AD. Кроме того вы можете повторно использовать имеющуюся регистрацию приложения.

1. Добавьте следующий URL-адрес ответа для регистрации приложения:

    ```plaintext
    https://YOUR_SWAGGER_URL/ui/oauth2-redirect-html
    ```
    | ИМЯ  | Заменить на | Пример |
    |---------|---------|---------|
    | YOUR_SWAGGER_URL | URL-адрес документации по REST API управления, найти на портале  | `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/swagger` |

1. Скопируйте идентификатор приложения Azure AD.

После завершения регистрации Azure Active Directory:

1. Выберите **Authorize** кнопки на странице swagger.

    [![Кнопка "Авторизовать" выберите Swagger](media/how-to-use-swagger/swagger-select-authorize-btn.png)](media/how-to-use-swagger/swagger-select-authorize-btn.png#lightbox)

1. Вставьте идентификатор приложения в **client_id** поля.

    [![Поле client_id swagger](media/how-to-use-swagger/swagger-auth-form.png)](media/how-to-use-swagger/swagger-auth-form.png#lightbox)

1. Затем вы будете перенаправлены для следующих модальное успеха.

    [![Модальное окно перенаправления swagger](media/how-to-use-swagger/swagger-auth-redirect-img.png)](media/how-to-use-swagger/swagger-auth-redirect-img.png#lightbox)

Дополнительные сведения об интерактивном тестировании запросов, защищенных с помощью OAuth 2.0, см. в [официальной документации](https://swagger.io/docs/specification/authentication/oauth2/).

## <a name="next-steps"></a>Следующие шаги

- Дополнительные сведения об объектных моделях и пространственном интеллектуальном графе Azure Digital Twins см. [здесь](./concepts-objectmodel-spatialgraph.md).

- Сведения об аутентификации с помощью API управления см. в статье [Подключение к API и аутентификация](./security-authenticating-apis.md).