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
ms.devlang: na
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: douglasl
ms.openlocfilehash: ef93c62a2e2084a43eeda578c889a568d04db4f1
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52855215"
---
# <a name="azure-function-activity-in-azure-data-factory"></a>Действие функции Azure в Фабрике данных Azure

Действие функции Azure позволяет запускать [Функции Azure](../azure-functions/functions-overview.md) в конвейере Фабрики данных. Чтобы запустить функцию Azure, необходимо создать подключение к связанной службе и действие, определяющее функцию Azure, которую вы планируете выполнить.

## <a name="azure-function-linked-service"></a>Связанные службы функции Azure

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
| текст  | Текст, который отправляется вместе с запросом для функции метода API  | Строка (или выражение с типом результата "строка").   | Необходимо для методов PUT или POST |
|   |   |   | |

Просмотрите схему полезных данных запроса в разделе  [Схема полезных данных запроса](control-flow-web-activity.md#request-payload-schema) .

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о действиях в Фабрике данных см. в статье [Конвейеры и действия в фабрике данных Azure](concepts-pipelines-activities.md).