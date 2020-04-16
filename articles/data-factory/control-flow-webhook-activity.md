---
title: Активность Webhook на фабрике данных Azure
description: Активность webhook не продолжает выполнение конвейера до тех пор, пока не проверит прикрепленный набор данных определенными критериями, которые определяет пользователь.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/25/2019
ms.openlocfilehash: 4056550ae0a71138d136878fc7e3aa5f6f8f4180
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417884"
---
# <a name="webhook-activity-in-azure-data-factory"></a>Активность Webhook на фабрике данных Azure

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Активность webhook может контролировать выполнение конвейеров через пользовательский код. С помощью активности webhook код клиентов может вызвать конечную точку и передать ей URL-адрес обратного вызова. Запуск конвейера ждет вызова вызова обратного вызова до его переступления в следующее действие.

## <a name="syntax"></a>Синтаксис

```json

{
    "name": "MyWebHookActivity",
    "type": "WebHook",
    "typeProperties": {
        "method": "POST",
        "url": "<URLEndpoint>",
        "headers": {
            "Content-Type": "application/json"
        },
        "body": {
            "key": "value"
        },
        "timeout": "00:03:00",
        "authentication": {
            "type": "ClientCertificate",
            "pfx": "****",
            "password": "****"
        }
    }
}

```

## <a name="type-properties"></a>Свойства типа

Свойство | Описание | Допустимые значения | Обязательно
-------- | ----------- | -------------- | --------
**name** | Название деятельности веб-крючка. | Строка | Да |
**тип** | Должен быть установлен на "WebHook". | Строка | Да |
**Метод** | Метод REST API для целевой точки. | Строка. Поддерживаемый тип —POST. | Да |
**url** | Целевая конечная точка и путь. | Строка или выражение с значением **resultType** строки. | Да |
**Заголовки** | Заголовки, которые отправляются в запрос. Вот пример, который устанавливает язык и тип `"headers" : { "Accept-Language": "en-us", "Content-Type": "application/json" }`по запросу: . | Строка или выражение с значением **resultType** строки. | Да. Заголовок, `Content-Type` `"headers":{ "Content-Type":"application/json"}` как не требуется. |
**Тела** | Представляет полезные данные, отправляемые конечной точке. | Действительное JSON или выражение с **значением resultType** JSON. [См. Схему полезной нагрузки Запроса](https://docs.microsoft.com/azure/data-factory/control-flow-web-activity#request-payload-schema) для схемы полезной нагрузки запроса. | Да |
**Проверки подлинности** | Метод проверки подлинности, используемый для вызова конечных точек. Поддерживаемые типы: "Основной" и "ClientCertificate". Дополнительные сведения см. в разделе [Authenticate to the Speech API](https://docs.microsoft.com/azure/data-factory/control-flow-web-activity#authentication) (Аутентификация в API речи). Если проверка подлинности не требуется, исключите это свойство. | Строка или выражение с значением **resultType** строки. | нет |
**Времени ожидания** | Как долго будет ждать сякобратно, указанное **callBackUri.** Значение по умолчанию составляет 10 минут (00:10:00). Значения имеют формат TimeSpan *d*. *hh*:*мм*:*ss*. | Строка | нет |
**Отчет о состоянии обратного вызова** | Позволяет пользователю сообщить о неисправном состоянии действия веб-крючка. | Логическое | нет |

## <a name="authentication"></a>Аутентификация

Активность webhook поддерживает следующие типы аутентификации.

### <a name="none"></a>None

Если проверка подлинности не требуется, не включите свойство **аутентификации.**

### <a name="basic"></a>Basic

Укажите имя пользователя и пароль для использования с базовой аутентификацией.

```json
"authentication":{
   "type":"Basic",
   "username":"****",
   "password":"****"
}
```

### <a name="client-certificate"></a>Сертификат клиента

Укажите закодированное содержимое файла PFX Base64 и пароль.

```json
"authentication":{
   "type":"ClientCertificate",
   "pfx":"****",
   "password":"****"
}
```

### <a name="managed-identity"></a>Управляемое удостоверение

Используйте управляемый итог фабрики данных, чтобы указать ресурс URI, для которого запрашивается токен доступа. Для вызова API управления ресурсами Azure используйте `https://management.azure.com/`. Для получения дополнительной информации о том, как работают управляемые идентификаторы, см. [managed identities for Azure resources overview](/azure/active-directory/managed-identities-azure-resources/overview)

```json
"authentication": {
    "type": "MSI",
    "resource": "https://management.azure.com/"
}
```

> [!NOTE]
> Если фабрика данных настроена с помощью репозитория Git, необходимо хранить учетные данные в Azure Key Vault, чтобы использовать базовую проверку подлинности сертификата клиента. Azure Data Factory не хранит пароли в Git.

## <a name="additional-notes"></a>Дополнительные замечания

Data Factory передает дополнительное свойство **callBackUri** в органе, отправленном в конечную точку URL. Data Factory ожидает, что этот URI будет вызываться до указанного значения тайм-аута. Если URI не вызывается, действие не удается со статусом "TimedOut".

Активность Webhook завершается сбой, когда вызов в пользовательскую конечную точку не удается. Любое сообщение об ошибке может быть добавлено в тело обратного вызова и использовано в более позднем действии.

Для каждого вызова REST API клиент выпадает, если конечная точка не отвечает в течение одной минуты. Такое поведение является стандартной наилучшей практикой HTTP. Чтобы решить эту проблему, реализуем шаблон 202. В текущем случае конечная точка возвращает 202 (принятые) и опросы клиентов.

Одноминутный тайм-аут по запросу не имеет ничего общего с тайм-аутом активности. Последний используется для ожидания обратного вызова, указанного **обратным вызовом.**

Тело, переданное обратно обратно в обратный вызов URI, должно быть действительным JSON. В качестве заголовка `Content-Type` установите `application/json`.

При использовании **статуса отчета в** свойстве обратного вызова необходимо добавить в тело следующий код при обратном вызове:

```json
{
    "Output": {
        // output object is used in activity output
        "testProp": "testPropValue"
    },
    "Error": {
        // Optional, set it when you want to fail the activity
        "ErrorCode": "testErrorCode",
        "Message": "error message to show in activity error"
    },
    "StatusCode": "403" // when status code is >=400, activity is marked as failed
}
```

## <a name="next-steps"></a>Дальнейшие действия

Ознакомьтесь со следующими действиями по управлению потоками, поддерживаемыми Data Factory:

- [Действие условия If](control-flow-if-condition-activity.md)
- [Действие выполнения конвейера](control-flow-execute-pipeline-activity.md)
- [Действие For Each](control-flow-for-each-activity.md)
- [Действие получения метаданных](control-flow-get-metadata-activity.md)
- [Действие поиска](control-flow-lookup-activity.md)
- [Веб-действие](control-flow-web-activity.md)
- [Действие Until](control-flow-until-activity.md)
