---
title: Действие функции Azure в фабрике данных Azure
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
ms.openlocfilehash: 781c5a579fa0cd0383e95b79df1f81f74008111c
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73679955"
---
# <a name="azure-function-activity-in-azure-data-factory"></a>Действие функции Azure в Фабрике данных Azure

Действие функции Azure позволяет запускать [Функции Azure](../azure-functions/functions-overview.md) в конвейере Фабрики данных. Чтобы запустить функцию Azure, необходимо создать подключение к связанной службе и действие, определяющее функцию Azure, которую вы планируете выполнить.

Общие сведения об этой функции и ее демонстрацию см. в следующем 8-минутном видео:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Run-Azure-Functions-from-Azure-Data-Factory-pipelines/player]

## <a name="azure-function-linked-service"></a>Связанные службы функции Azure

Тип возвращаемого значения функции Azure должен быть допустимым объектом `JObject`. (Помните, что [жаррай](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_Linq_JArray.htm) *не является* `JObject`ом.) Любой тип возвращаемого значения, отличный от `JObject`, завершается ошибкой и создает содержимое ответа об ошибке пользователя *не является допустимым JObject*.

| **Свойство** | **Описание** | **Обязательный** |
| --- | --- | --- |
| type   | Для свойства Type необходимо задать значение **азурефунктион** . | Да |
| function app url | URL-адрес для приложения-функции Azure. Формат – `https://<accountname>.azurewebsites.net`. Этот URL-адрес – это значение в разделе **URL** при просмотре приложения-функции на портале Azure  | Да |
| function key | Ключ доступа для функции Azure. Щелкните раздел **Управление** для соответствующей функции и скопируйте **Function Key** (ключ функции) или **Host key** (ключ хоста). Дополнительные сведения см. здесь: [триггеры и привязки HTTP для функций Azure](../azure-functions/functions-bindings-http-webhook.md#authorization-keys) . | Да |
|   |   |   |

## <a name="azure-function-activity"></a>Действие функции Azure

| **Свойство**  | **Описание** | **Допустимые значения** | **Обязательный** |
| --- | --- | --- | --- |
| name  | Имя действия в конвейере.  | string | Да |
| type  | Тип действия – "AzureFunctionActivity" | string | Да |
| linked service | Связанная служба функции Azure для соответствующего приложения-функции Azure  | Ссылка на связанную службу | Да |
| function name  | Имя функции, которую вызывает это действие в приложении-функции Azure | string | Да |
| метод  | Метод REST API для вызова функции | Поддерживаемые строковые типы: "GET", "POST", "постановка"   | Да |
| Верхний колонтитул  | Заголовки, которые отправляются в запрос. Например, задать язык и тип в запросе: "headers": { "Accept-Language": "en-us", "Content-Type": "application/json" } | Строка (или выражение с типом результата "строка") | Нет |
| текст  | Текст, который отправляется вместе с запросом для функции метода API  | Строка (или выражение с типом результата "строка") или объект.   | Необходимо для методов PUT или POST |
|   |   |   | |

Просмотрите схему полезных данных запроса в разделе  [Схема полезных данных запроса](control-flow-web-activity.md#request-payload-schema) .

## <a name="routing-and-queries"></a>Маршрутизация и запросы

Действие функции Azure поддерживает **маршрутизацию**. Например, если у функции Azure есть конечная точка `https://functionAPP.azurewebsites.net/api/<functionName>/<value>?code=<secret>`, то `functionName`, используемое в действии функции Azure, будет `<functionName>/<value>`. Эту функцию можно параметризовать для предоставления требуемого `functionName` во время выполнения.

Действие функции Azure поддерживает **запросы**. Запрос должен быть включен в состав `functionName`. Например, если имя функции — `HttpTriggerCSharp` а запрос, который требуется включить, `name=hello`, то можно создать `functionName` в действии функции Azure, как `HttpTriggerCSharp?name=hello`. Эта функция может быть параметризована, чтобы значение можно было определить во время выполнения.

## <a name="timeout-and-long-running-functions"></a>Время ожидания и длительные функции

Время ожидания функций Azure истекает через 230 секунд независимо от параметра `functionTimeout`, настроенного в параметрах. Дополнительные сведения см. в [этой статье](../azure-functions/functions-versions.md#timeout). Чтобы обойти это поведение, выполните шаблон асинхронного режима или используйте Устойчивые функции. Преимуществом Устойчивые функции является то, что они предлагают собственный механизм отслеживания состояния, поэтому вам не придется реализовывать собственные.

Дополнительные сведения о Устойчивые функции см. в [этой статье](../azure-functions/durable/durable-functions-overview.md). Вы можете настроить действие функции Azure для вызова устойчивой функции, которая вернет ответ с другим URI, например в [этом примере](../azure-functions/durable/durable-functions-http-features.md#http-api-url-discovery). Поскольку `statusQueryGetUri` возвращает состояние HTTP 202 во время выполнения функции, можно опросить состояние функции с помощью веб-действия. Просто настройте веб-действие, указав для поля `url` значение `@activity('<AzureFunctionActivityName>').output.statusQueryGetUri`. После завершения устойчивой функции выходные данные функции будут выходом веб-действия.


## <a name="sample"></a>Образец

Образец фабрики данных, который использует функцию Azure для извлечения содержимого tar-файла, можно найти [здесь](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV2/UntarAzureFilesWithAzureFunction).

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о действиях в Фабрике данных см. в статье [Конвейеры и действия в фабрике данных Azure](concepts-pipelines-activities.md).
