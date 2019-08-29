---
title: Действие функции Azure в Фабрике данных Azure | Документация Майкрософт
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
ms.openlocfilehash: 292fe858b85faef69b9df2dbdf54e7061ed56fa2
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/29/2019
ms.locfileid: "70142508"
---
# <a name="azure-function-activity-in-azure-data-factory"></a>Действие функции Azure в Фабрике данных Azure

Действие функции Azure позволяет запускать [Функции Azure](../azure-functions/functions-overview.md) в конвейере Фабрики данных. Чтобы запустить функцию Azure, необходимо создать подключение к связанной службе и действие, определяющее функцию Azure, которую вы планируете выполнить.

Общие сведения об этой функции и ее демонстрацию см. в следующем 8-минутном видео:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Run-Azure-Functions-from-Azure-Data-Factory-pipelines/player]

## <a name="azure-function-linked-service"></a>Связанные службы функции Azure

Тип возвращаемого значения функции Azure должен быть допустимым объектом `JObject`. (Имейте в виду, что [JArray](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_Linq_JArray.htm)*не* является `JObject`.) Любой тип возвращаемого значения `JObject` , отличный от, не дает ошибки и создает содержимое ответа об ошибке пользователя *, не является допустимым JObject*.

| **Property** | **Описание** | **Обязательный** |
| --- | --- | --- |
| type   | Свойству type необходимо задать значение: **AzureFunction** | да |
| function app url | URL-адрес для приложения-функции Azure. Формат – `https://<accountname>.azurewebsites.net`. Этот URL-адрес – это значение в разделе **URL** при просмотре приложения-функции на портале Azure  | да |
| function key | Ключ доступа для функции Azure. Щелкните раздел **Управление** для соответствующей функции и скопируйте **Function Key** (ключ функции) или **Host key** (ключ хоста). Дополнительные сведения см. в статье [Триггеры и привязки HTTP в службе "Функции Azure"](../azure-functions/functions-bindings-http-webhook.md#authorization-keys) | да |
|   |   |   |

## <a name="azure-function-activity"></a>Действие функции Azure

| **Property**  | **Описание** | **Допустимые значения** | **Обязательный** |
| --- | --- | --- | --- |
| name  | Имя действия в конвейере.  | Строковое | да |
| type  | Тип действия – "AzureFunctionActivity" | Строковое | да |
| linked service | Связанная служба функции Azure для соответствующего приложения-функции Azure  | Ссылка на связанную службу | да |
| function name  | Имя функции, которую вызывает это действие в приложении-функции Azure | Строковое | да |
| метод  | Метод REST API для вызова функции | Поддерживаемые типы строки: "GET", "POST", "PUT"   | да |
| Верхний колонтитул  | Заголовки, которые отправляются в запрос. Например, задать язык и тип в запросе: "headers": { "Accept-Language": "en-us", "Content-Type": "application/json" } | Строка (или выражение с типом результата "строка") | Нет |
| body  | Текст, который отправляется вместе с запросом для функции метода API  | Строка (или выражение с типом результата "строка") или объект.   | Необходимо для методов PUT или POST |
|   |   |   | |

Просмотрите схему полезных данных запроса в разделе  [Схема полезных данных запроса](control-flow-web-activity.md#request-payload-schema) .

## <a name="routing-and-queries"></a>Маршрутизация и запросы

Действие функции Azure поддерживает **маршрутизацию**. Например, если у функции Azure есть конечная точка `https://functionAPP.azurewebsites.net/api/<functionName>/<value>?code=<secret>`, то `functionName` для использования в действии функции Azure используется `<functionName>/<value>`. Эту функцию можно параметризовать, чтобы обеспечить требуемую `functionName` среду выполнения.

Действие функции Azure поддерживает **запросы**. Запрос должен быть включен в состав `functionName`. Например, `HttpTriggerCSharp` если имя функции —, а запрос, который требуется включить, — `name=hello`, то можно создать `functionName` в действии функции Azure как `HttpTriggerCSharp?name=hello`. Эта функция может быть параметризована, чтобы значение можно было определить во время выполнения.

## <a name="timeout-and-long-running-functions"></a>Время ожидания и длительные функции

Время ожидания функций Azure истекает через 230 секунд независимо `functionTimeout` от параметра, настроенного в параметрах. Дополнительные сведения см. в [этой статье](../azure-functions/functions-versions.md#timeout). Чтобы обойти это поведение, выполните шаблон асинхронного режима или используйте Устойчивые функции. Преимуществом Устойчивые функции является то, что они предлагают собственный механизм отслеживания состояния, поэтому вам не придется реализовывать собственные.

Дополнительные сведения о Устойчивые функции см. в [этой статье](../azure-functions/durable/durable-functions-overview.md). Вы можете настроить действие функции Azure для вызова устойчивой функции, которая вернет ответ с другим URI, например в [этом примере](../azure-functions/durable/durable-functions-http-api.md#http-api-url-discovery). Так `statusQueryGetUri` как функция возвращает состояние HTTP 202 во время выполнения функции, можно опросить состояние функции с помощью веб-действия. Просто настройте веб-действие с полем, `url` для `@activity('<AzureFunctionActivityName>').output.statusQueryGetUri`которого задано значение. После завершения устойчивой функции выходные данные функции будут выходом веб-действия.


## <a name="sample"></a>Пример

Образец фабрики данных, который использует функцию Azure для извлечения содержимого tar-файла, можно найти [здесь](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV2/UntarAzureFilesWithAzureFunction).

## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения о действиях в Фабрике данных см. в статье [Конвейеры и действия в фабрике данных Azure](concepts-pipelines-activities.md).
