---
title: Действие веб-перехватчика в фабрике данных Azure
description: Действие веб-перехватчика не позволяет продолжить выполнение конвейера до тех пор, пока не будет выполнена проверка присоединенного набора данных с определенным критерием, указанным пользователем.
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
ms.openlocfilehash: b2f7c35e6ddb3c6ed0a3032d7ea6d4c53043c17b
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/15/2019
ms.locfileid: "74122911"
---
# <a name="webhook-activity-in-azure-data-factory"></a>Действие веб-перехватчика в фабрике данных Azure
Действие веб-перехватчика можно использовать для управления выполнением конвейеров с помощью пользовательского кода. С помощью действия веб-перехватчика клиенты могут вызвать конечную точку и передать URL-адрес обратного вызова. Перед переходом к следующему действию конвейер выполняет ожидание вызова метода callback.

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



Свойство | ОПИСАНИЕ | Допустимые значения | обязательные
-------- | ----------- | -------------- | --------
Имя | Имя действия веб-перехватчика | Строка, | Yes |
type | Необходимо задать для **веб-перехватчика**. | Строка, | Yes |
метод | Метод REST API для целевой конечной точки. | Строка. Поддерживаемые типы: "POST" | Yes |
url | Целевая конечная точка и путь | Строка (или выражение с типом результата "строка"). | Yes |
headers | Заголовки, которые отправляются в запрос. Например, чтобы задать язык и тип запроса: "Headers": {"Accept-Language": "en-US", "Content-Type": "Application/JSON"}. | Строка (или выражение с типом результата "строка") | Да, требуется заголовок Content-type. "headers":{ "Content-Type":"application/json"} |
body | Представляет полезные данные, отправляемые конечной точке. | Допустимый формат JSON (или выражение с типом resultType для JSON). Просмотрите схему полезных данных запроса в разделе [Схема полезных данных запроса](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fdata-factory%2Fcontrol-flow-web-activity%23request-payload-schema&amp;data=02%7C01%7Cshlo%40microsoft.com%7Cde517eae4e7f4f2c408d08d6b167f6b1%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636891457414397501&amp;sdata=ljUZv5csQQux2TT3JtTU9ZU8e1uViRzuX5DSNYkL0uE%3D&amp;reserved=0). | Yes |
Аутентификация | Метод проверки подлинности, используемый для вызова конечной точки. Поддерживаются следующие типы: "базовый" или "ClientCertificate". Дополнительные сведения см. в разделе [Проверка подлинности](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fdata-factory%2Fcontrol-flow-web-activity%23authentication&amp;data=02%7C01%7Cshlo%40microsoft.com%7Cde517eae4e7f4f2c408d08d6b167f6b1%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636891457414397501&amp;sdata=GdA1%2Fh2pAD%2BSyWJHSW%2BSKucqoAXux%2F4L5Jgndd3YziM%3D&amp;reserved=0). Если проверка подлинности не требуется, исключите это свойство. | Строка (или выражение с типом результата "строка") | Нет |
timeout | Время, в течение которого действие будет ожидать &#39;вызова&#39; каллбаккури. Время, в течение которого действие будет ожидать вызова "Каллбаккури". Значение по умолчанию — 10mins ("00:10:00"). Формат — TimeSpan, т. е. чч: мм: СС. | Строка, | Нет |
Отчет о состоянии при обратном вызове | Разрешает пользователю сообщать о неудачном состоянии действия веб-перехватчика, которое помечает действие как неудачное | Логическое значение. | Нет |

## <a name="additional-notes"></a>Дополнительные замечания

Фабрика данных Azure передаст дополнительное свойство "Каллбаккури" в тексте в конечную точку URL-адреса и будет ждать вызова этого URI до указанного значения времени ожидания. Если URI не вызывается, действие завершится ошибкой с состоянием "TimedOut".

Действие веб-перехватчика завершается ошибкой при сбое вызова пользовательской конечной точки. Любое сообщение об ошибке можно добавить в тело обратного вызова и использовать в последующем действии.

Текст, переданный обратно в URI обратного вызова, должен быть допустимым JSON. Для заголовка Content-Type необходимо задать значение `application/json`.

При использовании параметра "сообщить о состоянии при обратном вызове" необходимо добавить следующий фрагмент кода в тело при выполнении обратного вызова:

```
{
    "Output": {
        // output object will be used in activity output
        "testProp": "testPropValue"
    },
    "Error": {
        // Optional, set it when you want to fail the activity
        "ErrorCode": "testErrorCode",
        "Message": "error message to show in activity error"
    },
    "StatusCode": "403" // when status code is >=400, activity will be marked as failed
}
```



## <a name="next-steps"></a>Дополнительная информация

Ознакомьтесь с другими действиями потока управления, которые поддерживаются фабрикой данных:

- [Действие условия If](control-flow-if-condition-activity.md)
- [Действие выполнения конвейера](control-flow-execute-pipeline-activity.md)
- [Действие ForEach](control-flow-for-each-activity.md)
- [Действие получения метаданных](control-flow-get-metadata-activity.md)
- [Действие поиска](control-flow-lookup-activity.md)
- [Веб-действие](control-flow-web-activity.md)
- [Действие Until](control-flow-until-activity.md)
