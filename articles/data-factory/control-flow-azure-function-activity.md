---
title: Действие функции Azure в Фабрике данных Azure | Документация Майкрософт
description: Узнайте, как с помощью действия функции Azure запускать функцию Azure в конвейере Фабрики данных
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: douglasl
ms.openlocfilehash: 0fd9b679411dd06336fd65bcb400b152316146fa
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/10/2019
ms.locfileid: "54188646"
---
# <a name="azure-function-activity-in-azure-data-factory"></a>Действие функции Azure в Фабрике данных Azure

Действие функции Azure позволяет запускать [Функции Azure](../azure-functions/functions-overview.md) в конвейере Фабрики данных. Чтобы запустить функцию Azure, необходимо создать подключение к связанной службе и действие, определяющее функцию Azure, которую вы планируете выполнить.

## <a name="azure-function-linked-service"></a>Связанные службы функции Azure

Тип возвращаемого значения функции Azure должен быть допустимым объектом `JObject`. (Имейте в виду, что [JArray](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_Linq_JArray.htm)*не* является `JObject`.) При указании типов, отличных от `JObject`, происходит сбой и выдается общая пользовательская ошибка *Ошибка вызова конечной точки*.

| **Свойство** | **Описание** | **Обязательный** |
| --- | --- | --- |
| Тип   | Свойству type необходимо задать значение: **AzureFunction** | Да |
| function app url | URL-адрес для приложения-функции Azure. Формат – `https://<accountname>.azurewebsites.net`. Этот URL-адрес – это значение в разделе **URL** при просмотре приложения-функции на портале Azure  | Да |
| function key | Ключ доступа для функции Azure. Щелкните раздел **Управление** для соответствующей функции и скопируйте **Function Key** (ключ функции) или **Host key** (ключ хоста). Дополнительные сведения см. в статье [Триггеры и привязки HTTP в службе "Функции Azure"](../azure-functions/functions-bindings-http-webhook.md#authorization-keys) | Да |
|   |   |   |

## <a name="azure-function-activity"></a>Действие функции Azure

| **Свойство**  | **Описание** | **Допустимые значения** | **Обязательный** |
| --- | --- | --- | --- |
| name  | Имя действия в конвейере.  | Строка | Да |
| Тип  | Тип действия – "AzureFunctionActivity" | Строка | Да |
| linked service | Связанная служба функции Azure для соответствующего приложения-функции Azure  | Ссылка на связанную службу | Да |
| function name  | Имя функции, которую вызывает это действие в приложении-функции Azure | Строка | Да |
| метод  | Метод REST API для вызова функции | Поддерживаемые типы строки: "GET", "POST", "PUT"   | Да |
| Верхний колонтитул  | Заголовки, которые отправляются в запрос. Например, задать язык и тип в запросе: "headers": { "Accept-Language": "en-us", "Content-Type": "application/json" } | Строка (или выражение с типом результата "строка") | Нет  |
| текст  | Текст, который отправляется вместе с запросом для функции метода API  | Строка (или выражение с типом результата "строка") или объект.   | Необходимо для методов PUT или POST |
|   |   |   | |

Просмотрите схему полезных данных запроса в разделе  [Схема полезных данных запроса](control-flow-web-activity.md#request-payload-schema) .

## <a name="more-info"></a>Подробнее

Действие функции Azure поддерживает **маршрутизацию**. Например, если приложение использует маршрутизацию `https://functionAPP.azurewebsites.net/api/functionName/{value}?code=<secret>`, то `functionName` представляет собой `functionName/{value}`, которое можно параметризовать для получения нужного `functionName` во время выполнения.

Действие функции Azure поддерживает **запросы**. Запрос должен быть частью `functionName`, например `HttpTriggerCSharp2?name=hello`, где `function name` равно `HttpTriggerCSharp2`.

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о действиях в Фабрике данных см. в статье [Конвейеры и действия в фабрике данных Azure](concepts-pipelines-activities.md).
