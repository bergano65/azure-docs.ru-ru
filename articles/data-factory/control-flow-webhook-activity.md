---
title: Действие веб-перехватчика в фабрике данных Azure | Документация Майкрософт
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
ms.openlocfilehash: c11fb800dba06ab5566647489f020f727860a7ff
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/29/2019
ms.locfileid: "70142410"
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



Свойство | Описание | Допустимые значения | Обязательное значение
-------- | ----------- | -------------- | --------
name | Имя действия веб-перехватчика | Строковое | Да |
type | Необходимо задать для **веб**-перехватчика. | Строковое | Да |
метод | Метод REST API для целевой конечной точки. | Строка. Поддерживаемые типы: БЛОГА | Да |
url | Целевая конечная точка и путь | Строка (или выражение с типом результата "строка"). | Да |
header | Заголовки, которые отправляются в запрос. Например, чтобы задать язык и тип запроса: "Headers": {"Accept-Language": "en-US", "Content-Type": "Application/JSON"}. | Строка (или выражение с типом результата "строка") | Да, требуется заголовок Content-type. "headers":{ "Content-Type":"application/json"} |
body | Представляет полезные данные, отправляемые конечной точке. | Текст, передаваемый обратно в URI обратного вызова, должен быть допустимым JSON. Просмотрите схему полезных данных запроса в разделе [Схема полезных данных запроса](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fdata-factory%2Fcontrol-flow-web-activity%23request-payload-schema&amp;data=02%7C01%7Cshlo%40microsoft.com%7Cde517eae4e7f4f2c408d08d6b167f6b1%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636891457414397501&amp;sdata=ljUZv5csQQux2TT3JtTU9ZU8e1uViRzuX5DSNYkL0uE%3D&amp;reserved=0). | Да |
проверка подлинности | Метод проверки подлинности, используемый для вызова конечной точки. Поддерживаются следующие типы: "базовый" или "ClientCertificate". Дополнительные сведения см. в разделе [Проверка подлинности](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fdata-factory%2Fcontrol-flow-web-activity%23authentication&amp;data=02%7C01%7Cshlo%40microsoft.com%7Cde517eae4e7f4f2c408d08d6b167f6b1%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636891457414397501&amp;sdata=GdA1%2Fh2pAD%2BSyWJHSW%2BSKucqoAXux%2F4L5Jgndd3YziM%3D&amp;reserved=0). Если проверка подлинности не требуется, исключите это свойство. | Строка (или выражение с типом результата "строка") | Нет |
время ожидания | Время, в течение которого действие будет ожидать &#39;вызова&#39; каллбаккури. Время, в течение которого действие будет ожидать вызова "Каллбаккури". Значение по умолчанию — 10mins ("00:10:00"). Формат — TimeSpan, т. е. чч: мм: СС. | Строковое | Нет |

## <a name="additional-notes"></a>Дополнительные замечания

Фабрика данных Azure передаст дополнительное свойство "Каллбаккури" в тексте в конечную точку URL-адреса и будет ждать вызова этого URI до указанного значения времени ожидания. Если URI не вызывается, действие завершится ошибкой с состоянием "TimedOut".

Действие веб-перехватчика завершается сбоем только в случае сбоя вызова пользовательской конечной точки. Любое сообщение об ошибке можно добавить в тело обратного вызова и использовать в последующем действии.

## <a name="next-steps"></a>Следующие шаги
Ознакомьтесь с другими действиями потока управления, которые поддерживаются фабрикой данных:

- [Действие условия If](control-flow-if-condition-activity.md)
- [Действие выполнения конвейера](control-flow-execute-pipeline-activity.md)
- [Действие ForEach](control-flow-for-each-activity.md)
- [Действие получения метаданных](control-flow-get-metadata-activity.md)
- [Действие поиска](control-flow-lookup-activity.md)
- [Веб-действие](control-flow-web-activity.md)
- [Действие Until](control-flow-until-activity.md)
