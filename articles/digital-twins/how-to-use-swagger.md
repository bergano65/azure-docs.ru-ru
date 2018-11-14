---
title: Общие сведения об использовании Swagger с Azure Digital Twins | Документация Майкрософт
description: Использование Swagger с Digital Twins
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/25/2018
ms.author: adgera
ms.openlocfilehash: 69b8f64574427a6b3d3d2cf0312eac88d86e5907
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/02/2018
ms.locfileid: "50960211"
---
# <a name="use-azure-digital-twins-swagger"></a>Использование Swagger с Digital Twins

Каждый подготовленный экземпляр Azure Digital Twins содержит собственную автоматически созданную справочную документацию Swagger.

[Swagger](https://swagger.io/) (или[OpenAPI](https://www.openapis.org/)) объединяет сложную информацию об API в интерактивный и не зависящий от языка справочный ресурс. Swagger предоставляет критически важный справочный материал о том, какие полезные данные JSON, методы HTTP и конкретные конечные точки необходимо использовать для выполнения операций с API.

## <a name="swagger-summary"></a>Сводка по Swagger

Swagger предоставляет интерактивную сводку по вашему API, включая следующее.

* Сведения об API и модели объекта.
* Конечные точки REST API, определяющие требуемые полезные данные запроса, заголовки, параметры, пути контекста и методы HTTP.
* Тестирование функциональных возможностей API.
* Пример сведений ответа для проверки и подтверждения ответов HTTP.
* Сведения о коде ошибки.

Таким образом, Swagger является удобным средством для разработки и тестирования вызовов к API управления.

> [!TIP]
> Для демонстрации набора функций API предоставляется краткий обзор предварительной версии Swagger.
> Он размещен по адресу [docs.westcentralus.azuresmartspaces.net/management/swagger](https://docs.westcentralus.azuresmartspaces.net/management/swagger).

Вы можете получить доступ к собственной созданной документации API управления Swagger по адресу, приведенному ниже.

```plaintext
https://yourInstanceName.yourLocation.azuresmartspaces.net/management/swagger
```

| Имя настраиваемого атрибута | Заменить на |
| --- | --- |
| *yourInstanceName* | Имя вашего экземпляра Azure Digital Twins |
| *yourLocation* | Регион сервера, в котором размещен ваш экземпляр |

## <a name="reference-material"></a>Справочные материалы

В автоматически созданном справочном материале объясняются критически важные понятия и модели объекта.

В краткой сводке описывается API.

![Верхняя панель Swagger][1]

Кроме того, перечислены модели объекта Core API.

![Модели Swagger][2]

Можно выбрать каждую объектную модель объекта в списке, чтобы ознакомится с более подробным описанием атрибутов ключей.

![Модель Swagger][3]

С помощью созданных объектных моделей Swagger можно просмотреть все доступные [объекты и API](./concepts-objectmodel-spatialgraph.md) Azure Digital Twins. Этот ресурс используется разработчиками при построении решений на Azure Digital Twins.

## <a name="endpoint-summary"></a>Сводка конечных точек

Swagger также предоставляет полный обзор всех конечных точек, которые составляют API.

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

После развертывания этого раздела появятся поля ввода для каждого обязательного и дополнительного параметров. Введите соответствующие значения и щелкните **Выполнить**.

![Пробное использование Swagger][6]

Выполнив тестирование, можно проверить данные ответа.

## <a name="swagger-response-data"></a>Данные ответа Swagger

Каждая указанная конечная точка также содержит данные текста ответа, необходимые для проверки вашей разработки и тестирования. Эти примеры включают коды нужного состояния и JSON, необходимые для успешного выполнения HTTP-запросов.

![Ответ Swagger][7]

Примеры также включают коды ошибок, которые помогают отлаживать или улучшать неудачные тестирования.

## <a name="swagger-oauth-20-authorization"></a>Авторизация Swagger OAuth 2.0

Сведения об интерактивном тестировании запросов ресурсов API, защищенных OAuth 2.0, см. в [официальной документации](https://swagger.io/docs/specification/authentication/oauth2/).

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения об объектных моделях и пространственном интеллектуальном графе Azure Digital Twins см. [здесь](./concepts-objectmodel-spatialgraph.md).

Сведения об аутентификации с помощью API управления см. в статье [Подключение к API и аутентификация](./security-authenticating-apis.md).

<!-- Images -->
[1]: media/how-to-use-swagger/swagger_management_top.PNG
[2]: media/how-to-use-swagger/swagger_management_models.PNG
[3]: media/how-to-use-swagger/swagger_management_model.PNG
[4]: media/how-to-use-swagger/swagger_management_endpoints.PNG
[5]: media/how-to-use-swagger/swagger_management_try.PNG
[6]: media/how-to-use-swagger/swagger_management_tried.PNG
[7]: media/how-to-use-swagger/swagger_management_response.PNG
