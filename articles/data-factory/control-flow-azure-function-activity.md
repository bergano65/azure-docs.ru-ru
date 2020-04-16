---
title: Функции функции Azure в фабрике данных Azure
description: Узнайте, как с помощью действия функции Azure запускать функцию Azure в конвейере Фабрики данных
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.openlocfilehash: ee2e59e794cf34a8fd5043a56867a81c2537f1ae
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415310"
---
# <a name="azure-function-activity-in-azure-data-factory"></a>Действие функции Azure в Фабрике данных Azure
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]
Действие функции Azure позволяет запускать [Функции Azure](../azure-functions/functions-overview.md) в конвейере Фабрики данных. Чтобы запустить функцию Azure, необходимо создать подключение к связанной службе и действие, определяющее функцию Azure, которую вы планируете выполнить.

Общие сведения об этой функции и ее демонстрацию см. в следующем 8-минутном видео:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Run-Azure-Functions-from-Azure-Data-Factory-pipelines/player]

## <a name="azure-function-linked-service"></a>Связанные службы функции Azure

Тип возвращаемого значения функции Azure должен быть допустимым объектом `JObject`. (Имейте в виду, *not* что `JObject` [JArray](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_Linq_JArray.htm) не .) Любой тип `JObject` возврата, кроме сбоев и повышает пользовательскую ошибку *Response Content, не является допустимым JObject.*

| **Свойство** | **Описание** | **Обязательно** |
| --- | --- | --- |
| type   | Свойство типа должно быть установлено: **AzureFunction** | да |
| function app url | URL-адрес для приложения-функции Azure. Формат – `https://<accountname>.azurewebsites.net`. Этот URL-адрес – это значение в разделе **URL** при просмотре приложения-функции на портале Azure  | да |
| function key | Ключ доступа для функции Azure. Щелкните раздел **Управление** для соответствующей функции и скопируйте **Function Key** (ключ функции) или **Host key** (ключ хоста). Узнайте больше здесь: [Azure Функции HTTP триггеры и привязки](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) | да |
|   |   |   |

## <a name="azure-function-activity"></a>Действие функции Azure

| **Свойство**  | **Описание** | **Разрешенные значения** | **Обязательно** |
| --- | --- | --- | --- |
| name  | Имя действия в конвейере.  | Строка | да |
| type  | Тип действия – "AzureFunctionActivity" | Строка | да |
| linked service | Связанная служба функции Azure для соответствующего приложения-функции Azure  | Ссылка на связанную службу | да |
| имя функции  | Имя функции, которую вызывает это действие в приложении-функции Azure | Строка | да |
| method  | Метод REST API для вызова функции | Типы поддержки строки: "GET", "POST", "PUT"   | да |
| Верхний колонтитул  | Заголовки, которые отправляются в запрос. Например, задать язык и тип в запросе: "headers": { "Accept-Language": "en-us", "Content-Type": "application/json" } | Строка (или выражение с типом результата "строка") | нет |
| текст  | Текст, который отправляется вместе с запросом для функции метода API  | Строка (или выражение с типом результата "строка") или объект.   | Необходимо для методов PUT или POST |
|   |   |   | |

Просмотреть схему полезной нагрузки запроса в разделе [Схема полезной нагрузки Запроса.](control-flow-web-activity.md#request-payload-schema) 

## <a name="routing-and-queries"></a>Реуктораинг и запросы

Действие функции Azure поддерживает **маршрутизацию**. Например, если функция Azure имеет `https://functionAPP.azurewebsites.net/api/<functionName>/<value>?code=<secret>`конечную `functionName` точку, то для `<functionName>/<value>`использования в функции Azure — это средство. Можно параметризировать эту функцию, чтобы обеспечить желаемое `functionName` во время выполнения.

Действие функции Azure поддерживает **запросы**. Запрос должен быть включен в состав `functionName`. Например, когда имя `HttpTriggerCSharp` функции и `name=hello`запрос, который вы хотите включить, `functionName` таков, можно `HttpTriggerCSharp?name=hello`построить функцию функции Azure как Эта функция может быть параметризирована, так что значение может быть определено во время выполнения.

## <a name="timeout-and-long-running-functions"></a>Тайм-аут и длительные функции

Функции Azure выходят через 230 секунд, независимо от `functionTimeout` настройки, настроенной в настройках. Дополнительные сведения см. в [этой статье](../azure-functions/functions-versions.md#timeout). Чтобы обойти это поведение, следуйте шаблону async или используйте долгосрочные функции. Преимущество функций durable заключается в том, что они предлагают свой собственный механизм отслеживания состояния, так что вам не придется реализовывать свой собственный.

Подробнее о функциях длительного пользования читайте в [этой статье](../azure-functions/durable/durable-functions-overview.md). Можно настроить функцию функции Azure для вызова функции durable, которая вернет ответ с другим URI, [например, в этом примере.](../azure-functions/durable/durable-functions-http-features.md#http-api-url-discovery) Поскольку `statusQueryGetUri` при запуске функции HTTP Status 202 можно опросить состояние функции с помощью Web Activity. Просто навяжните `url` веб-активность с набором `@activity('<AzureFunctionActivityName>').output.statusQueryGetUri`поля. Когда функция durable завершается, выход функции будет выходом Web-активности.


## <a name="sample"></a>Пример

Вы можете найти образец фабрики данных, которая использует функцию Azure для извлечения содержимого файла смолы [здесь.](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV2/UntarAzureFilesWithAzureFunction)

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о действиях в Фабрике данных см. в статье [Конвейеры и действия в фабрике данных Azure](concepts-pipelines-activities.md).
