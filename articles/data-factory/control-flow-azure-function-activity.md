---
title: Действие функции Azure в Фабрике данных Azure | Документация Майкрософт
description: Узнайте, как с помощью действия функции Azure запускать функцию Azure в конвейере Фабрики данных
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/09/2019
author: sharonlo101
ms.author: shlo
manager: craigg
ms.openlocfilehash: dfdfb9e38f16d0077175587933b0800b87cc1931
ms.sourcegitcommit: 22c97298aa0e8bd848ff949f2886c8ad538c1473
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/14/2019
ms.locfileid: "67144126"
---
# <a name="azure-function-activity-in-azure-data-factory"></a>Действие функции Azure в Фабрике данных Azure

Действие функции Azure позволяет запускать [Функции Azure](../azure-functions/functions-overview.md) в конвейере Фабрики данных. Чтобы запустить функцию Azure, необходимо создать подключение к связанной службе и действие, определяющее функцию Azure, которую вы планируете выполнить.

Общие сведения об этой функции и ее демонстрацию см. в следующем 8-минутном видео:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Run-Azure-Functions-from-Azure-Data-Factory-pipelines/player]

## <a name="azure-function-linked-service"></a>Связанные службы функции Azure

Тип возвращаемого значения функции Azure должен быть допустимым объектом `JObject`. (Имейте в виду, что [JArray](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_Linq_JArray.htm)*не* является `JObject`.) Тип возвращаемого значения отличные от `JObject` завершается ошибкой и происходит ошибка пользователя *содержимое ответа не является допустимым JObject*.

| **Свойство** | **Описание** | **Обязательный** |
| --- | --- | --- |
| type   | Свойству type необходимо задать значение: **AzureFunction** | Да |
| function app url | URL-адрес для приложения-функции Azure. Формат – `https://<accountname>.azurewebsites.net`. Этот URL-адрес – это значение в разделе **URL** при просмотре приложения-функции на портале Azure  | Да |
| function key | Ключ доступа для функции Azure. Щелкните раздел **Управление** для соответствующей функции и скопируйте **Function Key** (ключ функции) или **Host key** (ключ хоста). Дополнительные сведения см. в статье [Триггеры и привязки HTTP в службе "Функции Azure"](../azure-functions/functions-bindings-http-webhook.md#authorization-keys) | Да |
|   |   |   |

## <a name="azure-function-activity"></a>Действие функции Azure

| **Свойство**  | **Описание** | **Допустимые значения** | **Обязательный** |
| --- | --- | --- | --- |
| name  | Имя действия в конвейере.  | String | Да |
| type  | Тип действия – "AzureFunctionActivity" | String | Да |
| linked service | Связанная служба функции Azure для соответствующего приложения-функции Azure  | Ссылка на связанную службу | Да |
| function name  | Имя функции, которую вызывает это действие в приложении-функции Azure | String | Да |
| метод  | Метод REST API для вызова функции | Поддерживаемые типы строки: "GET", "POST", "PUT"   | Да |
| Верхний колонтитул  | Заголовки, которые отправляются в запрос. Например, задать язык и тип в запросе: "headers": { "Accept-Language": "en-us", "Content-Type": "application/json" } | Строка (или выражение с типом результата "строка") | Нет |
| текст  | Текст, который отправляется вместе с запросом для функции метода API  | Строка (или выражение с типом результата "строка") или объект.   | Необходимо для методов PUT или POST |
|   |   |   | |

Просмотрите схему полезных данных запроса в разделе  [Схема полезных данных запроса](control-flow-web-activity.md#request-payload-schema) .

## <a name="routing-and-queries"></a>Маршрутизация и запросы

Действие функции Azure поддерживает **маршрутизацию**. Например, если функция Azure есть конечная точка `https://functionAPP.azurewebsites.net/api/<functionName>/<value>?code=<secret>`, а затем `functionName` для использования в активности функции Azure — `<functionName>/<value>`. Позволяют задавать параметры для этой функции для обеспечения нужной `functionName` во время выполнения.

Действие функции Azure поддерживает **запросы**. Запрос должен быть частью `functionName`. Например, если имя функции является `HttpTriggerCSharp` и является запрос, который требуется включить `name=hello`, а затем можно создать `functionName` в активности функции Azure, как `HttpTriggerCSharp?name=hello`. Эта функция может быть параметризовано, поэтому значение может быть определено во время выполнения.

## <a name="timeout-and-long-running-functions"></a>Время ожидания и долго выполняющиеся функции

Azure функции времени ожидания после 230 секунд, вне зависимости от `functionTimeout` параметр настроен в параметрах. Дополнительные сведения см. в [этой статье](../azure-functions/functions-versions.md#timeout). Чтобы обойти это поведение, выполните это асинхронный шаблон или использовать устойчивые функции. Преимуществом устойчивых функций является, они предоставляют собственный механизм отслеживания состояния, поэтому вам не придется реализовать собственный.

Дополнительные сведения об устойчивых функций в [в этой статье](../azure-functions/durable/durable-functions-overview.md). Действие функции Azure можно настроить для вызова устойчивой функции, которая вернет ответ с другой идентификатор URI, таких как [в этом примере](../azure-functions/durable/durable-functions-http-api.md#http-api-url-discovery). Так как `statusQueryGetUri` возвращает HTTP 202 состояния, тогда как функция работает, можно запросить состояние функции с помощью веб-действие. Просто настройте веб-действие с `url` поле "значение" `@activity('<AzureFunctionActivityName>').output.statusQueryGetUri`. По завершении устойчивой функции выходные данные функции будут выходные данные веб-действия.


## <a name="sample"></a>Образец

Пример из фабрики данных, которая использует функцию Azure, чтобы извлечь содержимое tar-файл можно найти [здесь](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV2/UntarAzureFilesWithAzureFunction).

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о действиях в Фабрике данных см. в статье [Конвейеры и действия в фабрике данных Azure](concepts-pipelines-activities.md).
